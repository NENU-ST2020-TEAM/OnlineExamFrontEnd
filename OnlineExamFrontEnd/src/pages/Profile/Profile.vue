<template>
  <div class="profile">
    <section class="profile-number">
      <div id="title">
        <a href="http://localhost:8090/#/home">
          <h1>在线考试系统</h1>
          <span>TESTONLINE</span>
        </a>
      </div>
      <a href="javascript:void(0)" class="profile-link" @click="personInfor=true">
        <div class="user-info">
          <p class="user-info-top" >{{userInfor.username}}</p>
        </div>
        <div class="profile_image">
          <img :src="require('../../assets/头像.jpg')" alt="学生头像">
          <!--<img src="../../common/imgs/profile.jpg" alt="头像" v-else>-->
        </div>
      </a>
    </section>

    <div id="navigation">
      <el-menu
      default-active="1"
      class="el-menu-vertical-demo"
      background-color="#f5f5f5"
      active-text-color="#4ab8a1">
        <el-menu-item index="1">
          <i class="el-icon-menu"></i>
          <span slot="title" @click="isTest=true; myScore=false;">我的考试</span>
        </el-menu-item>
        <el-menu-item index="2" @click="isTest=false; myScore=true; sureTest=false">
          <i class="el-icon-document"></i>
          <span slot="title">查看成绩</span>
        </el-menu-item>
      </el-menu>
    </div>

    <!-- 考试 -->
    <!-- 提示 -->
    <el-card v-show="isTest" id="testOrNot">
      <div slot="header" class="clearfix" style="text-align:center">
        <span>考试通知</span>
      </div>
      <div>
        <p>
          {{userInfor.username}}同学：你好！
        </p>
        <br>
        <p style="line-height: 20px">
          &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;考试通道已开启，请合理安排时间完成考试！
        </p>
        <br>
      </div>
      <div style="text-align: center">
        <el-button type="success" plain @click="isTest=false;sureTest=true">选择试卷</el-button>
      </div>
    </el-card>

    <!-- 试卷列表 -->
    <el-card v-show="sureTest" id="testYes">
      <el-table :data="tableData2.slice((currentPage2-1)*pagesize,currentPage2*pagesize)" style="width: 100%">
          <el-table-column align="center" prop="paperId" label="试卷编号"></el-table-column>
          <el-table-column align="center" prop="paperName" label="试卷名称"></el-table-column>
          <el-table-column align="center" prop="operation" label="操作">
            <template slot-scope="scope">
              <el-button type="success" 
                icon="el-icon-edit" 
                title="开始考试" 
                circle 
                plain
                @click="beginTest(scope.$index, scope.row); start()"></el-button>
          </template>
                        <!-- <el-button type="danger" icon="el-icon-delete" title="删除" @click="dele" plain>删除</el-button> -->
                    </el-table-column>
          <div class="pagination">
            <el-pagination background layout="prev, pager, next" :total="total2" @current-change="current_change2" class="paginate"></el-pagination>
          </div>
      </el-table>
    </el-card>

    <!-- 考试页面 -->
    <el-card id="answer" v-show="testing">
      <div slot="header" class="clearfix" style="text-align:center">
        <span>答题卡</span>
        <br><br>
        <p>用时：{{str}}</p>
        <el-button type="success" 
                  icon="el-icon-check" 
                  style="float: right; padding: 3px 0" 
                  size="small"
                  plain 
                  @click="submiteAnwser()">确认提交</el-button>
      </div>
      <div v-for="item in question" :key="item.id">
        <p><span>{{item.exerciseId}}. </span><span>{{item.content}}</span></p>
        <el-radio-group 
            v-model="item.index"
            @change="handleRadioChange(item.index,item.exerciseId)"
            v-show="item.exerciseType='单选题'">
            <el-radio label="A">A.{{item.typeA}}</el-radio>
            <el-radio label="B">B.{{item.typeB}}</el-radio>
            <el-radio label="C">C.{{item.typeC}}</el-radio>
            <el-radio label="D">D.{{item.typeD}}</el-radio>
        </el-radio-group>
        <!-- <el-checkbox-group
            v-model="item.index"
            @change="handleCheckChange(item.index,item.exerciseId)"
            v-show="item.exerciseType='多选题'">
            <el-checkbox label="A">A.{{item.typeA}}</el-checkbox>
            <el-checkbox label="B">B.{{item.typeB}}</el-checkbox>
            <el-checkbox label="C">C.{{item.typeC}}</el-checkbox>
            <el-checkbox label="D">D.{{item.typeD}}</el-checkbox>
        </el-checkbox-group> -->
      </div>
    </el-card>
    
    <!-- 查看成绩 -->
    <el-card v-show="myScore" id="score">
      <el-table :data="tableData1.slice((currentPage1-1)*pagesize,currentPage1*pagesize)" style="width: 100%" id="peoTable">
          <el-table-column align="center" prop="paperId" label="试卷编号"></el-table-column>
          <el-table-column align="center" prop="paperName" label="试卷名称"></el-table-column>
          <el-table-column align="center" prop="mark" label="得分"></el-table-column>
          <div class="pagination">
            <el-pagination background layout="prev, pager, next" :total="total1" @current-change="current_change1" class="paginate"></el-pagination>
          </div>
      </el-table>
    </el-card>

    <el-dialog title="个人信息" v-model="userInfor" :visible.sync="personInfor" :close-on-click-modal="false">
      <img id="userHead" src="../../assets/头像.jpg" width="100px" height="100px" style="border-radius: 50px" />
      <el-form  ref="userInfor" label-width="150px">
        <el-form-item label="用户ID：">
          <el-col :span="8">
             <span>{{userInfor.userId}}</span>
          </el-col>
        </el-form-item>
        <el-form-item label="用户名：">
          <el-col :span="8">
            <span>{{userInfor.username}}</span>
          </el-col>
        </el-form-item>
        <el-form-item label="性别：">
          <el-col :span="8">
            <span>{{userInfor.sex}}</span>
          </el-col>
        </el-form-item>
        <el-form-item label="生日：">
          <el-col :span="8">
            <span>{{userInfor.birthday}}</span>
          </el-col>
        </el-form-item>
        <el-form-item label="权限：">
          <el-col :span="8">
            <span>{{userInfor.power}}</span>
          </el-col>
        </el-form-item>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
  import HeaderTop from '../../components/HeaderTop/HeaderTop.vue'
  import ProfileItem from '../../components/ProfileItem/ProfileItem.vue'
  import { mapState, mapGetters } from 'vuex'
  import { MessageBox, Toast } from 'mint-ui';
  import {reqUpdateFeedbackStatus, reqFeedbackInfo} from '../../api'
  export default {
    name: "",
    data() {
      return {
        sno:this.$store.state.userInfo.sno,
        isSelect:false,

        //在线考试
        //试卷id
        paperID: '',
        isTest: true,
        sureTest: false,
        tableData2: [],
        radioAnswer: [],
        question: [],
        currentPage2: 1,
        total2: 0,
        h: 0,
        m: 0,
        s: 0,
        time: 0,
        str: '00:00:00',
        radio: '',
        radioShow: true,
        checkShow: false,
        testing: false,
        //分页
        pagesize: 10,
        //查看成绩
        myScore: false,
        tableData1: [],
        currentPage1: 1,
        total1: 0,

        //personInfor
        personInfor: false,

        //公告
        title: '',
        content: '',

        //用户信息
        userInfor: [],
      }
    },
    computed:{
      ...mapState(['userInfo']),
      ...mapGetters(['unreadMsgCount'])
    },
    created(){
      //请求用户信息
      this.$ajax({
          method: "post",
          url: "http://120.26.186.88:8080/user/listUserById",
          dataType: "json",
          crossDomain: true,
          cache: false,
      }).then(resolve => {
        console.log(resolve.data[0]);
        this.userInfor = resolve.data[0];
      }, reject => {
          // this.peoLoading = true;
          console.log(reject);
      });
      //所有试卷加载
      this.$ajax({
          method: "post",
          url: "http://47.103.10.220:8010/paper/listAllPaper",
          dataType: "json",
          crossDomain: true,
          cache: false,
      }).then(resolve => {
          this.tableData2 = resolve.data;
          //获取数组长度赋值给total
          this.total2 = resolve.data.length;
          // this.peoLoading = false;
          console.log(this.total2);
          console.log(resolve.data);
      }, reject => {
          // this.peoLoading = true;
          console.log(reject);
      });

      //个人成绩
      this.$ajax({
          method: "post",
          url: "http://47.103.10.220:8010/score/queryScoreByUser",
          dataType: "json",
          crossDomain: true,
          cache: false,
      }).then(resolve => {
          this.tableData1 = resolve.data;
          //获取数组长度赋值给total
          this.total1 = resolve.data.length;
          // this.peoLoading = false;
          console.log(resolve.data);
      }, reject => {
          // this.peoLoading = true;
          console.log(reject);
      });

      const h = this.$createElement;
      this.$notify({
        title: '17级软件工程导论通知',
        message: h('i', { style: 'color: teal'}, '本周二将进行软件工程导论考试，请同学们抓紧时间复习！')
      });
    },
    methods: {
      //开始考试
      beginTest(index, row) {
        this.testing = true;
        this. sureTest = false;
        this.paperID = row.paperId;
        console.log(row.paperId);
        this.$ajax({
            method: "post",
            url: "http://47.103.10.220:8010/paperDetail/listPaperDetailByPaperId",
            data: {
              paperIdStr: row.paperId,
            },
            dataType: "json",
            crossDomain: true,
            cache: false,
            transformRequest(obj){
                    var str = [];
                    for(var p in obj){
                        str.push(encodeURIComponent(p) + "=" + encodeURIComponent(obj[p]));
                    }
                    return str.join("&");
                },
        }).then(resolve => {
          console.log(resolve);
          this.question = resolve.data;
          // this.radioModel = resolve.data
        }, reject => {
            // this.peoLoading = true;
            console.log(reject);
        });
      },

      //查看成绩
      current_change1(currentPage) {
        this.currentPage1 = currentPage;
      },
      //考试
      current_change2(currentPage) {
        this.currentPage2 = currentPage;
      },
      //选择
      handleRadioChange(chose, id){
        this.radioAnswer[id] = {index:id, value:chose};
        console.log(this.radioAnswer);
      },
      //交卷
      submiteAnwser() {
        clearInterval(this.time);
        this.$alert('请前往“我的成绩”中查询你的成绩', '提交成功！', {
            confirmButtonText: '确定',
            callback: action => {
              this.testing = false;
              this.myScore = true;
              this.$ajax({
                method: "post",
                url: "http://47.103.10.220:8010/score/queryScoreByUser",
                dataType: "json",
                crossDomain: true,
                cache: false,
            }).then(resolve => {
                this.tableData1 = resolve.data;
                //获取数组长度赋值给total
                this.total1 = resolve.data.length;
                // this.peoLoading = false;
                console.log(resolve.data);
            }, reject => {
                // this.peoLoading = true;
                console.log(reject);
            });
          }
        });
        //算分
        let right = 0;
        for(let p=0; p<this.question.length; p++){
          for(let q=0; q<this.radioAnswer.length; q++){
            if(this.radioAnswer[q]!=undefined){
              if(this.question[p].exerciseId == this.radioAnswer[q].index){
                if(this.question[p].answer == this.radioAnswer[q].value){
                  right += this.question[p].score;
                }
              }
            }
          }
        }
        console.log(right);
        this.testing = true;
        this. sureTest = false;
        this.$ajax({
            method: "post",
            url: "http://47.103.10.220:8010/score/addScore",
            data: {
              paperIdStr: this.paperID,
              markStr: right,
            },
            dataType: "json",
            crossDomain: true,
            cache: false,
            transformRequest(obj){
                    var str = [];
                    for(var p in obj){
                        str.push(encodeURIComponent(p) + "=" + encodeURIComponent(obj[p]));
                    }
                    return str.join("&");
                },
        }).then(resolve => {
          console.log(resolve.data);
        }, reject => {
            console.log(reject);
        });
      },
      //倒计时
      timer(){
      //秒针走动
        this.s=this.s+1;
        //秒进位分，秒归零
        if(this.s>=60){
          this.m=this.m+1;
          this.s=0;
        }
        //分进位时，分归零
        if(this.m>=60){
          this.h=this.h+1;
          this.m=0;
        }
        this.str=this.addZero(this.h)+":"+this.addZero(this.m)+":"+this.addZero(this.s);
        // console.log(this.str);
        if(this.str == "02:00:00"){
          this.$alert('请停止答题，前往“我的成绩”中查询你的成绩', '考试时间到！', {
            confirmButtonText: '确定',
            callback: action => {
              submiteAnwser();
              this.testing = false;
              this.myScore = true;
            }
          });
          clearInterval(this.time);
        }
      },
      //补零
      addZero(t){
        if(t<10){
          return ("0"+t);
        }
        else{
          return t;
        }
      },
      //开始计时
      start(){
        //1秒=1000毫秒
        this.time=setInterval(this.timer,1000);
      },
    },
    components:{
      HeaderTop,
      ProfileItem
    }
  }
</script>

<style lang="stylus" type="text/stylus" rel="stylesheet/stylus" scoped>
  @import "../../common/stylus/mixins.styl"
  .profile
    width 100%
    overflow hidden
    padding-bottom 56px
    .profile-number
      // margin-top 45.5px
      &:active
        opacity 0.8
      .profile-link
        clearFix()
        position relative
        display block
        background lightslategrey
        padding 20px 10px
        .profile_image
          float right
          width 60px
          height 60px
          border-radius 50%
          overflow hidden
          vertical-align top
          img
            height 100%
            width 100%
          .icon-yonghuming
            background #e4e4e4
            font-size 62px
        .user-info
          float right
          margin-top 23px
          margin-left 15px
          p
            font-weight: 700
            font-size 18px
            color #fff
            &.user-info-top
              padding-bottom 8px
            .icon-msnui-tel
              font-size 14px
              color #fff
        .arrow
          position absolute
          right 15px
          top 40%
          .iconjiantou
            color #fff
            font-size 30px
    .profile-items
      .login_out
        height 50px
        display flex
        justify-content center
        align-items center
        background-color lightslategrey
        margin-top 6px
        color #fff
        //box-shadow 0px 0px 1px rgba(0,0,0,.5)
        &.opacity
          opacity 0.4
          background lightslategrey
  #title
    position absolute 
    // border 1px solid red
    z-index 1000
    left 2%
    top 4.5%
    line-height 25px
    text-align center
  #title a {
    color white
    font-weight 300
  }
  #title h1 {
    font-size 20px
  }
  #navigation {
    margin-top 1%
    width 13%
    background-color rgba(255,255,255,0)
  }

  //考试
  #testOrNot {
    width 50%;
    position absolute 
    top 150px
    left 27%
  }
  #testYes
    position absolute 
    width 83%
    top 112px
    left 15%
  #answer
    position absolute 
    width 83%
    top 112px
    left 15%
  // 查看成绩
  #score {
    position absolute 
    width 83%
    top 112px
    left 15%
  }
  //头像
  #userHead {
    position absolute
    top 12%
    right 15%;
  }
</style>
