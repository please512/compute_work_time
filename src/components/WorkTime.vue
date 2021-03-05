<template>
    <div class="form_box">
      工作日期<input type="date" class="inp" id="date">
      开始时间<input type="time" class="inp" id="start">
      结束时间<input type="time" class="inp" id="end">
      共计时长<input type="text" class="inp res" disabled id="show">
      <button class="inp btn" id="btn">COMPUTE</button>
    </div>
</template>

<script>
import Bus from './eventBus'
export default {
  name: 'WorkTime',
  data () {
    return {
      
    }
  },
  mounted(){
    var ostart=document.getElementById('start')
    var oend=document.getElementById('end')
    var oshow=document.getElementById('show')
    var obtn=document.getElementById('btn')
    var odate=document.getElementById('date')
    window.localStorage.removeItem('date')
    window.localStorage.removeItem('week')
    function delCookie(name){
      document.cookie = name+"=;expires="+(new Date(0)).toGMTString();
    }
    function writeCookie(){
      var time=[]
      document.cookie.split(';').forEach((item)=>{
      if(item.split('=')[0].indexOf('-')!=-1){
        time.push(Number(item.split('=')[1]))
        }
      })
      var all=time.reduce((total,currentValue)=>{
        return total+currentValue
      })
      // alert(`当前共计加班时长${all}小时`)
      Bus.$emit('compute','')
    }
    function setCookie (value) {
        var days = 31; //定义一天
        var exp = new Date();
        exp.setTime(exp.getTime() + days * 24 * 60 * 60 * 1000);
        // 写入Cookie, toGMTString将时间转换成字符串
        let name=window.localStorage.getItem('date')
        document.cookie = name + "=" + escape(value) + ";expires=" + exp.toGMTString();
        writeCookie()
    };
    // function setContent(workTime){
    //     var name=(new Date().getMonth()+1)+'-'+(new Date().getDate())
    //     setCookie(name,workTime)
    // }
    function compute(end,start){
      let week=window.localStorage.getItem('week')
      if(week==0 || week==6){
        console.log('周末')
        var workMinutes=(Number(end.split(':')[0])*60+Number(end.split(':')[1]))-(Number(start.split(':')[0])*60+Number(start.split(':')[1]))
        var workTime=workMinutes/60
        oshow.value=workTime
      }else{
        console.log('工作日')
        var allTime=(Number(end.split(':')[0])*60+Number(end.split(':')[1]))-(Number(start.split(':')[0])*60+Number(start.split(':')[1]))
        var standTime=10*60
        var workMinutes=allTime-standTime
        var workTime=workMinutes/60
        oshow.value=workTime<0.5?0:workTime
      }
        setCookie(oshow.value)
    }
    function formatTime(end,start){
        if(end.split(':')[0]<start.split(':')[0]){
            alert('结束时间早于开始时间')
            return
        }
        if(end.split(':')[0]==start.split(':')[0]){
            if(end.split(':')[1]<start.split(':')[1]){
                alert('结束时间早于开始时间')
                return
            }
            if(end.split(':')[1]==start.split(':')[1]){
                alert('结束时间不能等于开始时间')
                return
            }
            if(end.split(':')[1]>start.split(':')[1]){
                compute(end,start)
            }
        }
        if(end.split(':')[0]>start.split(':')[0]){
            compute(end,start)
        }
    }
    odate.addEventListener('change',()=>{
      if(!odate.value){
        alert('请选择工作日期')
        return
      }else{
        let t=odate.value.replace(/2020-/g,'')
        let d=Number(t.split('-')[0])+'-'+Number(t.split('-')[1])
        window.localStorage.setItem('date',d)
        window.localStorage.setItem('week',new Date(odate.value).getDay())
      }
    })
    obtn.addEventListener('click',()=>{
        if(!window.localStorage.getItem('date')){
          alert('请选择工作日期')
          return
        }
        if(!ostart.value){
            alert('请输入开始时间')
            return
        }
        if(!oend.value){
            alert('请输入结束时间')
            return
        }
        formatTime(oend.value,ostart.value)
    })
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.form_box{
  display: flex;
  margin:50px 0;
}
.inp{
  width:130px;
  border: 1px lightcoral solid;
  border-radius: 3px;
  outline: none;
  margin:0 20px;
  padding:5px 10px;
}
.res{
  width:100px;
  height: 20px;
  color: darkblue;
}
.btn{
  width:80px;
  height: 32px;
  font-size: 12px;
  cursor: pointer;
  background: lightcoral;
  color: #fff;
}
.btn:active{
  background: darkorange;
  border: none;
  color: #fff;
}
</style>
