---
layout: single
title: NEMBV 16 게시판 댓글 만들기
category: nembv
tag: [nembv,api,mongoose,vue,bootstrap,bootstrap-vue]
comments: true
sidebar:
  nav: "nembv"
---

> 이 강좌는 종료되었습니다.  
새로운 강좌로 시작하세요~  
[모던웹(NEMV) 제작 강좌](/nemv/){: .btn .btn--success}  

게시판에 댓글을 추가할 수 있게 만들어보겠다.

지난번에 구현했던 내용(company, group)과 거의 같기 때문에 소스를 확인 하면 충분히 이해할 수 있을 것이다. 

{% include toc %}

{% raw %}

# api 추가

**routes/api/data/index.js**  
```javascript
const router = require('express').Router();
const company = require('./company');
const group = require('./group');
const board = require('./board');
const comment = require('./comment'); // add

router.use('/company', company);
router.use('/group', group);
router.use('/board', board);
router.use('/comment', comment); // add

router.all('*', (req, res) => {
    res.status(404).send({ success: false, msg: `unknown uri ${req.path}` });
});

module.exports = router;
```

- comment 추가

## comment api

**routes/api/data/comment/index.js**  
```javascript
const router = require('express').Router();
const ctrl = require('./ctrls');

router.get('/', ctrl.list);
router.post('/', ctrl.add);
router.put('/', ctrl.mod);
router.delete('/', ctrl.del);

router.all('*', (req, res) => {
    res.status(404).send({ success: false, msg: `unknown uri ${req.path}` });
});

module.exports = router;
```

**routes/api/data/comment/ctrls.js**  
```javascript
const Board = require('../../../../models/boards');
const Comment = require('../../../../models/comments');

exports.list = (req, res) => {
  return res.send({ success: false, msg: '댓글만 보고 싶을때 기능 추후 추가'});
};

exports.add = (req, res) => {
  const { bd_id, id,  content } = req.body;

  if (!id) res.send({ success: false, msg : 'id not exists' });
  if (!content) res.send({ success: false, msg : 'content not exists' });

  const cmt = new Comment({
    bd_id: bd_id,
    id: id,
    content: content,
    ip: req.ip,
  });
  cmt.save()
    .then((r) => {
      const f = { _id: r.bd_id };
      const s = { $addToSet: { cmt_ids: r._id }};
      return Board.updateOne(f, s);
    })
    .then((r) => {
      if (!r.nModified) return res.send({ success: false, msg : 'already Board' });
      res.send({ success: true });
    })
    .catch((err) => {
      res.send({ success: false, msg : err.message });
    });
};

exports.mod = (req, res) => {
  const set = req.body;

  if (!Object.keys(set).length) return res.send({ success: false, msg: 'body not set' });
  if (!set._id) return res.send({ success: false, msg: 'id not exists' });
  set.ut = new Date();
  set.ip = req.ip;

  const f = { _id: set._id };
  const s = { $set: set };

  Comment.findOneAndUpdate(f, s)
    .then(() => {
      res.send({ success: true });
    })
    .catch((err) => {
      res.send({ success: false, msg: err.message });
    });
};

exports.del = (req, res) => {
  const _id = req.query._id;
  if (!_id) return res.send({ success: false, msg : 'param id not exists' });
  Comment.findOne({_id:_id})
    .then((r) => {
      if (!r) throw new Error('group not exists');
      const f = { _id: r.bd_id };
      const s = { $pull: { cmt_ids: r._id }};
      return Board.updateOne(f, s);
    })
    .then(() => { // { n: 1, nModified: 1, ok: 1 }
      return Comment.remove({ _id: _id });
    })
    .then(() => { // { n: 1, ok: 1 }
      res.send({ success: true });
    })
    .catch((err) => {
      res.send({ success: false, msg : err.message });
    });
}
```

- list, add, mod, del api 추가

- list는 추후 commant만 따로 볼 수 있는 페이지에서 사용하면 된다.

# front-end

**fe/src/components/page/board/talk.vue**  
```vue
<template>
  <div>
    <b-row class="mb-4">
      <b-col cols="6">
        <input type="text" v-model="filter" class="form-control" id="input-text" placeholder="검색 제목">
      </b-col>
      <b-col cols="6">
        <b-form-group>
          <b-form-select :options="pageOptions" v-model="perPage" />
        </b-form-group>
      </b-col>
    </b-row>

    <b-table
      id="tt"
      ref="table"
      show-empty
      stacked="md"
      :items="list"
      :fields="fields"
      :current-page="currentPage"
      :per-page="perPage"
      :busy.sync="isBusy"
      :sort-by.sync="sortBy"
      :sort-desc.sync="sortDesc"
      :filter="filter"
      no-local-sorting
    >
      <!--@sort-changed="sortingChanged"-->
      <!--:no-local-sorting="true"-->
      <template slot="_id" slot-scope="row">
        <b-badge variant="info">
          <!--{{ row.item._id }}-->
          {{ id2time(row.item._id) }}
        </b-badge>
      </template>
      <template slot="ut" slot-scope="row">
        <b-badge variant="info">
          {{ ago(row.item.ut) }}
        </b-badge>
      </template>
      <template slot="cmt_ids" slot-scope="row">
        <b-badge pill variant="success">
          {{ row.item.cmt_ids.length }}
        </b-badge>
      </template>
      <template slot="actions" slot-scope="row">
        <!-- We use @click.stop here to prevent a 'row-clicked' event from also happening -->
        <b-button size="sm" variant="primary" @click.stop="row.toggleDetails" @click="read(row)">
          {{ row.detailsShowing ? '숨기기' : '보기' }}
        </b-button>
        <!--<b-btn>{{row.item._id}}</b-btn>-->
      </template>
      <template slot="row-details" slot-scope="row">
        <b-card no-body
                :title="row.item.title"
        >
          <b-card-body>
            <p class="card-text" style="white-space: pre;">{{row.item.content}}</p>
          </b-card-body>

          <b-list-group flush>
            <b-list-group-item v-for="(cmt) in row.item.cmt_ids" :key="cmt._id">
              <b-row>
                <b-col cols="2">
                  <b-badge>{{ cmt.id }}</b-badge>
                </b-col>
                <b-col cols="6">
                  <span style="white-space: pre;">  {{ cmt.content}}</span>
                </b-col>
                <b-col cols="2">
                  <small class="text-muted"> {{ cmt.ip }} | {{ ago(cmt.ut) }}</small>
                </b-col>
                <b-col cols="2">
                  <b-button-group class="float-right" size="sm">
                    <b-btn variant="outline-warning" @click="mdModCmtOpen(cmt)"><icon name="pencil"></icon></b-btn>
                    <b-btn variant="outline-danger" @click="delCmt(cmt)"><icon name="trash"></icon></b-btn>
                  </b-button-group>
                </b-col>
              </b-row>


            </b-list-group-item>
            <b-list-group-item>
              <span> 새 댓글 작성 </span>
              <b-button-group class="float-right" size="sm">
                <b-btn variant="outline-success" @click="mdAddCmtOpen(row.item)"><icon name="plus"></icon></b-btn>
              </b-button-group>
            </b-list-group-item>

          </b-list-group>

          <b-card-footer>
            <small class="text-muted">{{ ago(row.item.ut) }}</small>
            <b-button-group class="float-right">
              <b-btn variant="outline-warning" @click="mdModOpen(row.item)"><icon name="pencil"></icon></b-btn>
              <b-btn variant="outline-danger" @click="del(row.item)"><icon name="trash"></icon></b-btn>
            </b-button-group>
          </b-card-footer>
        </b-card>
      </template>
    </b-table>
    <!--<b-table id="my-table" show-empty :busy.sync="isBusy" :items="list"></b-table>-->


    <b-row>
      <b-col>
        <b-btn variant="info" @click="refresh">새로고침</b-btn>
        <b-btn variant="success" @click="mdAddOpen" >글쓰기</b-btn>
      </b-col>
      <b-col>
        <b-pagination
          align="right"
          size="md"
          :total-rows="totalRows"
          v-model="currentPage"
          :per-page="perPage"
          >
        </b-pagination>
      </b-col>
    </b-row>

    <b-modal ref="mdAdd" hide-footer title="새로운 글 작성">
      <b-form @submit="add">
        <b-form-group label="이름:"
                      label-for="f-a-id">
          <b-form-input id="f-a-id"
                        type="text"
                        v-model="form.id"
                        required
                        placeholder="홍길동">
          </b-form-input>
        </b-form-group>

        <b-form-group label="제목:"
                      label-for="f-a-title">
          <b-form-input id="f-a-title"
                        type="text"
                        v-model="form.title"
                        required
                        placeholder="아무거나">
          </b-form-input>
        </b-form-group>

        <b-form-group label="글"
                      label-for="f-a-content">
          <b-form-textarea id="f-a-content"
                           v-model="form.content"
                           placeholder="재미있는 글"
                           :rows="10"
                           :max-rows="20">
          </b-form-textarea>
        </b-form-group>

        <b-btn type="submit" variant="primary" class="float-right">글 쓰기</b-btn>
      </b-form>
    </b-modal>

    <b-modal ref="mdMod" hide-footer title="글 수정하기">
      <b-form @submit="mod">
        <b-form-group label="이름:"
                      label-for="f-m-id">
          <b-form-input id="f-m-id"
                        type="text"
                        v-model="form.id"
                        required
                        placeholder="홍길동">
          </b-form-input>
        </b-form-group>

        <b-form-group label="제목:"
                      label-for="f-m-title">
          <b-form-input id="f-m-title"
                        type="text"
                        v-model="form.title"
                        required
                        placeholder="아무거나">
          </b-form-input>
        </b-form-group>

        <b-form-group label="글"
                      label-for="f-m-content">
          <b-form-textarea id="f-m-content"
                           v-model="form.content"
                           placeholder="재미있는 글"
                           :rows="10"
                           :max-rows="20">
          </b-form-textarea>
        </b-form-group>

        <b-btn type="submit" variant="warning" class="float-right">글 수정</b-btn>
      </b-form>
    </b-modal>

    <b-modal ref="mdAddCmt" hide-footer title="댓글 작성">
      <b-form @submit="addCmt">
        <b-form-group label="이름:"
                      label-for="f-a-c-id">
          <b-form-input id="f-a-c-cid"
                        type="text"
                        v-model="formCmt.id"
                        required
                        placeholder="홍길동">
          </b-form-input>
        </b-form-group>

        <b-form-group label="글"
                      label-for="f-a-c-content">
          <b-form-textarea id="f-a-c-content"
                           v-model="formCmt.content"
                           placeholder="재미있는 글"
                           :rows="10"
                           :max-rows="20">
          </b-form-textarea>
        </b-form-group>

        <b-btn type="submit" variant="primary" class="float-right">글 쓰기</b-btn>
      </b-form>
    </b-modal>

    <b-modal ref="mdModCmt" hide-footer title="댓글 수정하기">
      <b-form @submit="modCmt">
        <b-form-group label="이름:"
                      label-for="f-m-c-id">
          <b-form-input id="f-m-c-id"
                        type="text"
                        v-model="formCmt.id"
                        required
                        placeholder="홍길동">
          </b-form-input>
        </b-form-group>

        <b-form-group label="글"
                      label-for="f-m-c-ontent">
          <b-form-textarea id="f-m-c-ontent"
                           v-model="formCmt.content"
                           placeholder="재미있는 글"
                           :rows="10"
                           :max-rows="20">
          </b-form-textarea>
        </b-form-group>

        <b-btn type="submit" variant="warning" class="float-right">글 수정</b-btn>
      </b-form>
    </b-modal>

  </div>
</template>

<script>

  export default {
    name: 'talk',
    data() {
      return {
        fields: [
          {
            key: '_id',
            label: '등록일',
            sortable: true,
          },
          // {
          //   key: 'ut',
          //   label: '수정날짜',
          //   sortable: true,
          // },
          {
            key: 'id',
            label: '작성자',
            sortable: true,
          },
          {
            key: 'title',
            label: '제목',
            sortable: true,
          },
          {
            key: 'cntView',
            label: '조회',
            sortable: true,
          },
          {
            key: 'cmt_ids',
            label: '댓글',
            sortable: true,
          },
          {
            key: 'actions',
            label: '내용',
            sortable: true,
          },
        ],
        isBusy: false,
        items: [],
        currentPage: 1,
        perPage: 5,
        totalRows: 0,
        pageOptions: [5, 10, 15],
        sortBy: 'ut',
        sortDesc: false,
        filter: '',
        draw: 0,
        form: {
          _id: '',
          id: '',
          title: '',
          content: '',
        },
        formCmt: {
          bd_id: '',
          _id: '',
          id: '',
          content: '',
        },
      };
    },
    // props: ['items'],
    mounted() {
      // this.list();
      // this.test();
    },
    computed: {
      setSkip() {
        if (this.currentPage <= 0) return 0;
        return (this.currentPage - 1) * this.perPage;
      },
      setSort() {
        return this.sortDesc ? -1 : 1;
      },
    },
    methods: {
      swalSuccess(msg) {
        return this.$swal({
          icon: 'success',
          title: '성공',
          text: msg,
          timer: 2000,
        });
      },
      swalWarning(msg) {
        return this.$swal({
          icon: 'warning',
          title: '실패',
          text: msg,
          timer: 2000,
        });
      },
      swalError(msg) {
        return this.$swal({
          icon: 'error',
          title: '에러',
          text: msg,
          timer: 2000,
        });
      },
      mdAddOpen() {
        this.form._id = '';
        this.form.id = '';
        this.form.title = '';
        this.form.content = '';
        this.$refs.mdAdd.show();
      },
      mdModOpen(v) {
        this.form._id = v._id;
        this.form.id = v.id;
        this.form.title = v.title;
        this.form.content = v.content;
        this.$refs.mdMod.show();
      },
      mdAddCmtOpen(v) {
        this.formCmt.bd_id = v._id;
        this.formCmt._id = '';
        this.formCmt.id = '';
        this.formCmt.content = '';
        this.$refs.mdAddCmt.show();
      },
      mdModCmtOpen(v) {
        this.formCmt.bd_id = v._id;
        this.formCmt._id = v._id;
        this.formCmt.id = v.id;
        this.formCmt.content = v.content;
        this.$refs.mdModCmt.show();
      },
      ago(t) {
        return this.$moment(t).fromNow();
      },
      id2time(_id) {
        return new Date(parseInt(_id.substring(0, 8), 16) * 1000).toLocaleString();
      },
      refresh() {
        this.$refs.table.refresh();
      },
      sortingChanged(ctx) {
        this.sortBy = ctx.sortBy;
        this.sortDesc = ctx.sortDesc;
        // if (ctx.sortDesc) this.s.order = -1;
        // else this.s.order = 1;
        this.list();
        // console.log(ctx);
      },
      list(ctx) {
        this.sortBy = ctx.sortBy;
        this.sortDesc = ctx.sortDesc;
        this.isBusy = true;
        const p = this.$axios.get(`${this.$cfg.path.api}data/board`, {
          params: {
            draw: (this.draw += 1),
            search: this.filter,
            skip: this.setSkip,
            limit: this.perPage,
            order: this.sortBy,
            sort: this.setSort,
          },
        });
        return p.then((res) => {
          if (!res.data.success) throw new Error(res.data.msg);
          this.totalRows = res.data.d.cnt;
          this.isBusy = false;
          // const items = res.data.d.ds;
          return res.data.d.ds;
        })
          .catch((err) => {
            this.isBusy = false;
            this.swalError(err.message);
            return [];
          });
      },
      read(r) {
        if (r.detailsShowing) return;
        const _id = r.item._id;
        this.isBusy = true;
        this.$axios.get(`${this.$cfg.path.api}data/board/${_id}`)
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            r.item.cntView = res.data.d.cntView;
            r.item.content = res.data.d.content;
            r.item.cmt_ids = res.data.d.cmt_ids;
            // console.log(res.data.d);
            // console.log(r.item);
            this.isBusy = false;
          })
          .catch((err) => {
            this.isBusy = false;
            this.swalError(err.message);
          });
      },
      add(evt) {
        evt.preventDefault();
        this.$axios.post(`${this.$cfg.path.api}data/board`, this.form)
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            return this.swalSuccess('글 작성 완료');
          })
          .then(() => {
            this.$refs.mdAdd.hide();
            this.refresh();
          })
          .catch((err) => {
            this.swalError(err.message);
          });
      },
      mod() {
        this.$swal({
          title: '작성한 글을 수정하시겠습니까?',
          dangerMode: true,
          buttons: {
            cancel: {
              text: '취소',
              visible: true,
            },
            confirm: {
              text: '수정',
            },
          },
        })
          .then((sv) => {
            if (!sv) throw new Error('');
            return this.$axios.put(`${this.$cfg.path.api}data/board`, this.form);
          })
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            return this.swalSuccess('글 수정 완료');
          })
          .then(() => {
            this.$refs.mdMod.hide();
            this.refresh();
            // this.list(); // todo: instead one article..
          })
          .catch((err) => {
            if (err.message) return this.swalError(err.message);
            this.swalWarning('글 수정 취소');
          });
      },
      del(v) {
        this.$swal({
          title: '글 삭제',
          dangerMode: true,
          buttons: {
            cancel: {
              text: '취소',
              visible: true,
            },
            confirm: {
              text: '삭제',
            },
          },
        })
          .then((sv) => {
            if (!sv) throw new Error('');
            return this.$axios.delete(`${this.$cfg.path.api}data/board`, {
              params: { _id: v._id },
            });
          })
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            return this.swalSuccess('글 삭제 완료');
          })
          .then(() => {
            this.refresh();
          })
          .catch((err) => {
            if (err.message) return this.swalError(err.message);
            this.swalWarning('글 삭제 취소');
          });
      },
      addCmt(evt) {
        evt.preventDefault();
        this.$axios.post(`${this.$cfg.path.api}data/comment`, this.formCmt)
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            return this.swalSuccess('댓글 추가 완료');
          })
          .then(() => {
            this.$refs.mdAddCmt.hide();
            this.refresh();
          })
          .catch((err) => {
            this.swalError(err.message);
          });
      },
      modCmt() {
        this.$swal({
          title: '댓글 수정 변경',
          dangerMode: true,
          buttons: {
            cancel: {
              text: '취소',
              visible: true,
            },
            confirm: {
              text: '수정',
            },
          },
        })
          .then((res) => {
            if (!res) throw new Error('');
            return this.$axios.put(`${this.$cfg.path.api}data/comment`, this.formCmt);
          })
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            return this.swalSuccess('댓글 수정 완료');
          })
          .then(() => {
            this.$refs.mdModCmt.hide();
            this.refresh();
          })
          .catch((err) => {
            if (err.message) this.swalError(err.message);
            else this.swalWarning('댓글 수정 취소');
          });
      },
      delCmt(cmt) {
        this.$swal({
          title: '댓글 삭제',
          dangerMode: true,
          buttons: {
            cancel: {
              text: '취소',
              visible: true,
            },
            confirm: {
              text: '삭제',
            },
          },
        })
          .then((res) => {
            if (!res) throw new Error('');
            return this.$axios.delete(`${this.$cfg.path.api}data/comment`, {
              params: { _id: cmt._id },
            });
          })
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            return this.swalSuccess('댓글 삭제 완료');
          })
          .then(() => {
            this.refresh();
          })
          .catch((err) => {
            if (err.message) return this.swalError(err.message);
            this.swalWarning('댓글 삭제 취소');
          });
      },
    },
  };
</script>

<style scoped>
</style>
```
           
{% endraw %}

# 구현된 화면

{% include video id="muvEky4tkCM" provider="youtube" %}    

# 기타

댓글 소트는 따로 이벤트 핸들러를 만들어 줘야한다. 현재 단순 array에 _id들만 들어있기 때문.

# 전체 소스

[https://github.com/fkkmemi/nembv](https://github.com/fkkmemi/nembv)

# 결론

화면은 후지지만.. bootstrap-vue table data provider 응용 화면은 이것으로 끝났다.

bootstrap은 사실 배치가 중요한 것인데 정신없이 코딩하다보니 모양이 좋진 않다..

다음번엔 invisible recaptcha로 로봇들이 쓰지 못하게 최전방 수비수를 구현해보겠다.

