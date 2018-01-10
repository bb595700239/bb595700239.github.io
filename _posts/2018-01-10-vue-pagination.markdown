---
layout:     post
title:      "VUE结合elementUI(Pagination)进行前端分页"
subtitle:   "生活"
date:       2018-01-10
author:     "Joubn"
header-img: "img/hello2017.jpg"
header-mask: 0.3
catalog: true
tags:
    - 微码
---

> “You are the butter to my bread, you are the breath to my life. ”

项目中我们经常会遇到后台把列表的所有数据一次传过来我们前端进行分页的情况，这里说一下基本思路，就拿第二页(假设总页数大于两页)来说，第二页的数据就是第一页最后一条（不包括）到第三页的第一条（不包括），还有一种情况就是刚好两页，那么第二页的数据就是第一页最后一条（不包括）到最后一条（包括）。如果只有一页你却翻到了第二页那就出事啦！！！

优点：
- 切换效果更流畅
- 当删除一条数据时filter会重新计算当页显示数据

缺点：
- 数据量大时浪费流量

## filters
我们应该先创建一个filter来过滤列表数据，这里有三个参数`array`（列表数据）、`pageNo`（当前第几页）、`pageSize`（每页条数）

```js
filters:{
      pagination(array,pageNo,pageSize){
        let offset = (pageNo - 1) * pageSize;//当前页第一条的索引
        let data=(offset + pageSize >= array.length) ? array.slice(offset, array.length) : array.slice(offset, offset + pageSize);
        return data
      }
    }
```

## 完整代码

```html
<template>
    <el-table :data="tableData | pagination(pageNo,pageSize)">
      <el-table-column label="ID">
          <template slot-scope="scope">
            {{scope.$index}}  
          </template>
      </el-table-column>
    </el-table>
    <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange" :current-page="pageNo" :page-size="pageSize" layout=" prev, pager, next, sizes, jumper" :total="tableData.length"></el-pagination>
</template>
<script>
  export default{
    data(){
      return {
        tableData: [1,2,3,4],
        pageNo: 1,
        pageSize:10
      }
    },
    mounted(){
    },
    methods:{
      handleSizeChange(val) {
        this.pageSize=val;
      },
      handleCurrentChange(val) {
        this.pageNo = val;
      }
    },
    filters:{
      pagination(array,pageNo,pageSize){
        let offset = (pageNo - 1) * pageSize;
        let data=(offset + pageSize >= array.length) ? array.slice(offset, array.length) : array.slice(offset, offset + pageSize);
        return data
      }
    }
  }
</script>
```




