 <template>
	<div class="footer bgcolor flex-fix" >
        <div class="color-ban" ref="coloer"></div>
        <div :class="['footer-item',item.classon]" v-for="(item,index) of classList" @click="bottomclick(index,item.name)">
           <router-link :to="item.path">   
            <span :class="['iconfont',item.icon]"></span>
            <p>{{item.name}}</p>
           </router-link> 
        </div>            
      </div>
</template>
<script type="text/javascript">
 import axios from "axios"
	export default {
       name:"kcalfooter",
       data:function(){
        return{
         classList:[
                     {name:"热量计算",classon:{footeronchoose:true},icon:"icon-jisuan",path:"/"},
                     {name:"健身技巧",classon:{footeronchoose:false},icon:"icon-jianshenqicai",path:"/train"},
                     {name:"日常打卡",classon:{footeronchoose:false},icon:"icon-jianshenquan",path:"/card"},
                     {name:"个人中心",classon:{footeronchoose:false},icon:"icon-geren11",path:"/mine"}],
         leftDistance:0,
         timer:null,
         nowrouter:0
        }
       },
       watch:{
        $route:function(){
         this.routerchang();
        }
       },
       beforeMount:function(){
       var that=this;
        axios.get("./api/lf/checkcookie.php").then(function(re){
          if(re.data===0){
            that.classList[3].path="/login";
             that.classList[2].path="/login";
          }
          else{
            that.classList[3].path="/mine";
             that.classList[2].path="/card";
          }          
         });        
       },
       mounted:function(){
        var that=this;
         this.hub.$on("loginfooter",function(){
         that.classList[3].path="/mine";
         that.classList[2].path="/card";
        });
          this.routerchang();  
       },
       methods:{
         bottomclick:function(num,name){
            this.move(num);
         },
         move:function(num){
           var that=this;
           clearTimeout(that.timer);
           var targetLeft=25*num;
           var movedistance=targetLeft-this.leftDistance>0?1:-1;
           if(targetLeft!=this.leftDistance){
             that.timer=setInterval(function(){
             that.leftDistance+=movedistance;
             that.$refs.coloer.style.left=that.leftDistance+"%";
           if(that.leftDistance===targetLeft){
            clearTimeout(that.timer);
            that.leftDistance=targetLeft;
           }
           }, 1)
           }
         },
         routerchang:function(){
          var _nowrouter=this.$route.path;
          var num=0;
          var that=this;

           function _change(item,index){
                item.classon.footeronchoose=true;
               num=index;
           }

           if(_nowrouter=="/login"||_nowrouter=="/reg"){
              num=3;
              this.classList[3].classon.footeronchoose=true;
              this.classList[3].path="/login";
              this.classList[2].path="/login";
            }
            else{

          this.classList.forEach(function(item,index){
            item.classon.footeronchoose=false;
            if(item.path===_nowrouter){
                _change(item,index);
            }
            else if(_nowrouter.indexOf(item.path)!=-1&&item.path!="/"){
                _change(item,index);
            }
          });
            }
          this.move(num); 
         }
       }
	}
</script>
<style type="text/css">
      .footer{
            display: flex;
            position: fixed;
            bottom: 0px;
            width: 100%;
            z-index: 99;
      }
      .footer-item{
            flex-grow: 1;
             padding: 4px 0px;
            text-align: center;
            font-size: 12px;
            border-right: 1px solid white;
            width:100%;
      }
      .footer-item span{
            font-size:24px;
            color: white;
      }
      .footer-item p{
         color: white;
      }
      .footer-item.footeronchoose{
            background: rgba(255,255,255,0);
      }
      .color-ban{
        box-sizing: border-box;
        height: 48px;

        width: 25%;
        position: absolute;
        top: 0;
        left: 0;
        z-index: -999;
        background-color: #524F4F;
      }
</style>