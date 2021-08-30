<template>
  <div id="neon" class="flex flex-col">
    <div class="control flex flex-col items-center justify-center text-xl bg-blue-dark cursor-pointer h-12 active:bg-gray-dark active:text-white"  @click="refurbishFrame">
      <div class="items-center justify-center h-8">add index</div>
    </div>
    <div id="neon_matrix" class="mt-12"></div>
    <div class="info text-white text-sm text-center">row:{{block_row_num}}  column:{{block_column_num}}</div>
  </div>
</template>

<script>
import * as d3 from "d3"
var BezierEasing = require('bezier-easing')
var Mock = require('mockjs')
export default {
  name: "NeonMatrixLayout",
  data(){
    return {
      height:400,
      width:250,
      text_index:1,
      gap:2,
      delay:150,
      layout_matrix:[],
      now_index:0,
      block_row_num:0,
      block_column_num:0,
    }
  },
  props:['dataset'],
  methods:{
    initMatrix:function(event){
      let layout=d3.select('#neon_matrix').insert('svg').attr('height',this.$data.height).attr('width',this.$data.width)
      // .insert('rect').attr('height',this.$data.height).attr('width',this.$data.width).attr('class','layout').attr('x','0').attr('y','0')
      let layout_matrix=[]
      this.$data.block_row_num=this.dataset[this.$data.text_index].length
      this.$data.block_column_num=this.dataset[this.$data.text_index][0].length
      let avg_height=Number.parseInt(this.$data.height/this.$data.block_row_num)
      let avg_width=Number.parseInt(this.$data.width/this.$data.block_column_num)
      let rect_length_max=avg_height>avg_width?avg_width:avg_height
      let rect_length=rect_length_max-this.$data.gap
      console.log("block_row_num/block_column_num: "+this.$data.block_row_num+"/"+this.$data.block_column_num)
      console.log("avg_height/avg_width: "+avg_height+'/'+avg_width)
      console.log("rect_length_max:"+rect_length_max+"; rect_length:"+rect_length)
      for(let i=0;i<this.$data.block_row_num;i++){
        let matrix_column=[]
        for(let j=0;j<this.$data.block_column_num;j++){
          matrix_column.push(layout.insert('rect').attr('height',rect_length).attr('width',rect_length)
              .attr('y',i*(rect_length+this.$data.gap)).attr('x',j*(rect_length+this.$data.gap))
              .attr('rx','4').attr('fill','#1f2d3d'))
        }
        layout_matrix.push(matrix_column)
      }
      this.$data.layout_matrix=layout_matrix
    },
    refurbishFrame:function(event){
      // alert('refurbish!')
      this.clearFrame()
      this.addIndex()
      for(let i=0;i<this.$data.block_row_num;i++){
        for(let j=0;j<this.$data.block_column_num;j++){
          if(this.dataset[this.$data.now_index][i][j]==1)this.$data.layout_matrix[i][j].transition().delay(this.$data.delay+Mock.Random.integer(0,this.$data.delay)).attr('fill','orange')
        }
      }
    },
    clearFrame:function(event){
      for(let i=0;i<this.$data.block_row_num;i++){
        for(let j=0;j<this.$data.block_column_num;j++){
          this.$data.layout_matrix[i][j].transition().delay(Mock.Random.integer(0,this.$data.delay)).attr('fill','#1f2d3d')
        }
      }
    },
    addIndex:function(event){
      this.$data.now_index=this.$data.now_index==this.dataset.length-1?0:this.$data.now_index+1
    }
  },
  mounted() {
    var easing = BezierEasing(0, 0, 1, 0.5);
    console.log(easing(0.5));
    this.initMatrix()
    this.refurbishFrame()
  }
}
</script>

<style scoped>
.info,.control{
  font-family: Expansiva;
}
</style>