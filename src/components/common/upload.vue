<template>
  <div class="upload-wrap">
    <!--color="#696969" rippleOpacity="'0.3'"-->
    <div class="guess">猜你喜欢</div>
    <div v-for="book in uploadData" class="book-list clearfix" @click="goDetail(book.id)">
      <a :href="book.shareUrl" class="link" @click.prevent="">
        <div class="avatar">
          <img v-lazy="book.images[0].imgUrl" width="104" height="80" class="icon">
          <span class="vip" v-if="book.novel_type !== 1"></span>
        </div>
        <div class="desc">
          <h2 class="name">{{book.title}}</h2>
          <p class="des">{{book.des}}</p>
          <div class="info"><span class="author"><img v-lazy="book.user.icon" class="icon" width="13" height="13">{{book.user.name}}</span>
            <span class="tag" v-if="book.tags[0]">{{book.tags[0].name}}</span>
            <span class="tag" v-if="book.tags[1]">{{book.tags[1].name}}</span>
          </div>
        </div>
      </a>
    </div>
    <mu-raised-button :label="progressShow?'正在努力加载中，请稍等...':'点击加载更多'" class="upload-btn"
                      @click="upload" :rippleOpacity="0.2" fullWidth/>
    <mu-linear-progress :size="8" v-if="progressShow" color="#d500f9"/>
  </div>
</template>

<script type="text/ecmascript-6">
  import * as types from '../../store/mutationsTypes';
  import {mapState} from 'vuex';
  export default {
    data(){
      return {
        uploadData: [],
        progressShow: false //进度条显示状态
      }
    },
    computed: {
      ...mapState(['guessContent'])
    },
    methods: {
      upload() {
        if (this.progressShow) { //防止多次次点击加载按钮
          console.log(this.progressShow);
          return;
        }
        this.progressShow = true;
        this.$store.dispatch(types.UPLOAD_GUESS).then(() => {
          this.guessContent.list.forEach((item) => {
            this.uploadData.push(item);
          });
          this.progressShow = false;
        });
//        guessApi.then((res)=> {
//          if (res.data.status === ERRNO) {
//            console.log(res.data);
//            res.data.data.list.forEach((item) => {
//              this.uploadData.push(item);
//            });
//          }
//        });
      },
      goDetail(bookId) {
        this.$router.push({path: '/detail', params: {bookId}});
        this.$store.commit(types.SET_BOOK_ID, bookId);
      }
    },
    created() {
      this.$store.dispatch(types.UPLOAD_GUESS).then(() => {
        this.guessContent.list.forEach((item) => {
          this.uploadData.push(item);
        });
      });
    }
  }
</script>


<style lang="less" rel="stylesheet/less" scoped>
  @import "../../../static/less/index";

  .upload-wrap {
    background-color: #fff;
    .guess {
      height: 26px;
      line-height: 38px;
      font-size: 13px;
      color: rgb(105, 105, 105);
    }
    .book-list + .book-list {
      border-top: 1px solid #e6e6e6;
    }
    .book-list {
      height: 92px;
      min-height: 80px;
      margin: 8px 0;
      padding-top: 8px;
      &:last-of-type {
        padding-bottom: 0px;
        margin-bottom: 10px;
      }
      .link {
        display: flex;
        text-decoration: none;
      }
      .avatar {
        position: relative;
        flex: none;
        width: 104px;
        margin-right: 12px;
        .icon {
          border-radius: 5px;
        }
        .vip {
          position: absolute;
          top: 0;
          left: 0;
          height: 24px;
          width: 24px;
          background-image: url('../../../static/img/img_vip_ribbon@3x.png');
          background-size: 100% 100%;
        }
      }
      .desc {
        flex: 1;
        .name {
          line-height: 14px;
          font-size: 14px;
          color: #101010;
          margin-bottom: 6px;
        }
        .des {
          overflow: hidden;
          /*height: 46px;*/
          font-size: 12px;
          line-height: 16px;
          color: #929292;
          white-space: pre-wrap;
          word-wrap: break-word;
          display: -webkit-box;
          -webkit-box-orient: vertical;
          overflow: hidden;
          text-overflow: ellipsis;
          -webkit-line-clamp: 2;
          margin-bottom: 6px;
        }
        .author {
          color: #666;
          vertical-align: top;
          .icon {
            vertical-align: top;
            margin-right: 3px;
            display: inline-block;
            border-radius: 50%;
          }
        }
        .tag {
          color: #999;
          margin-right: 4px;
          float: right;
          border: 1px solid #b3b3b3;
          line-height: 10px;
          font-size: 10px;
          padding: 2px;
        }
      }
    }
  }

  .upload-btn {
    height: 40px;
    border-radius: 6px;
  }
</style>
