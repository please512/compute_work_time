<template>
    <div class="table_box">
        <table style="width:1000px;">
            <thead>
                <tr>
                    <th>日期(月-日)</th>
                    <th>时间（小时）</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item,index) in list" :key="index">
                    <td width='50%'>{{item.date}}</td>
                    <td width='50%'>{{item.time}}</td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
import Bus from './eventBus'
export default {
  name: 'TableShow',
  data () {
    return {
      list:[]
    }
  },
  mounted(){
      this.getList()
      Bus.$on('compute',()=>{
          console.log('重新计算')
          this.list=[]
          this.getList()
      })
  },
  methods:{
    getList(){
        document.cookie.split(';').forEach(s=>{
            if(s.indexOf('-')!=-1 && !s.includes('sensorsdata')){
                let obj={
                    date:'',
                    time:''
                }
                obj.date=s.split('=')[0]
                obj.time=s.split('=')[1]
                this.list.push(obj)
            }
        })
        if(this.list.length){
            var timelist=[]
            this.list.forEach(s=>{
                timelist.push(Number(s.time))
            })
            var sum=0
            sum=timelist.reduce((total,currentValue)=>{
                return total+currentValue
            })
        }
        this.list.push({
            date:`共计${this.list.length}天`,
            time:sum
        })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.table_box{
    width: 100%;
}
table{
    border-collapse: collapse;
    margin:0 auto;
}
th,td{
    text-align: center;
    border: 1px black solid;
    padding:10px 0;
}
/* th td{
    border: 1px black solid;
    font-size: 12px;
    width:100px;
}
tr td{
    border: 1px black solid;
    font-size: 12px;
} */
</style>
