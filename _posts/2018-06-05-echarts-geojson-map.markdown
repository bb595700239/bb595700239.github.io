---
layout:     post
title:      "Echarts加载geoJson数据,实现省市地图下钻"
subtitle:   "geoJson"
date:       2018-06-05
author:     "Joubn"
header-img: "img/contact-bg.jpg"
header-mask: 0.3
catalog: true
tags:
    - js
---

> “He who seize the right moment, is the right man. ”

## 序言

这两年数据可视化被越来越多的人提起。主要原因还是大数据的兴起。那我们免不了要用一些数据可视化组件了。主要有[d3](https://d3js.org/)、[highcharts](https://www.highcharts.com/)、[echarts](http://echarts.baidu.com/).
这里我主要想聊一下`echarts`加载`geoJson`地图数据的实现

### 认识geoJson

>GeoJSON是一种对各种地理数据结构进行编码的格式，基于Javascript对象表示法的地理空间信息数据交换格式。GeoJSON对象可以表示几何、特征或者特征集合。GeoJSON支持下面几何类型：点、线、面、多点、多线、多面和几何集合。GeoJSON里的特征包含一个几何对象和其他属性，特征集合表示一系列特征。

这里官方的解释，我们先来看一下庐山真面目：

```js
{
  "type": "Feature",
  "geometry": {
    "type": "Point",
    "coordinates": [125.6, 10.1]
  },
  "properties": {
    "name": "Dinagat Islands"
  }
}
```

了解更多：[geojson](http://geojson.org/)官网

### Echarts加载geoJson数据

你看完上面可能你会想，什么鬼，我要这数据有什么用。这个可是非常有用的。有了这个数据就可以转成`topojson`在D3里用了或者转成SVG放浏览器里直观感受了。

这里我先准备一份图家-省份-市区的geojson的数据[省市geojson](http://static.joubn.com/2018-06-05-echarts-geojson-map/mapdata.rar).
有了这份数据我们就可以通过echarts轻松把地图信息展示到页面上去了。

示例：
<iframe frameborder="0" width="100%" height="400px" src="http://static.joubn.com/2018-06-05-echarts-geojson-map/index.html"></iframe>

js代码：
```js
    (function(echarts) {
        var map = echarts.init(document.getElementById('map'))
        var reset = function() {
            map = echarts.init(document.getElementById('map'))
            map.setOption({
                geo: {
                    map: 'mapCity',
                    label: {
                        emphasis: {
                            show: true,
                            color: '#fff',
                        }
                    },
                    roam: true, //允许拖动
                    itemStyle: {
                        normal: {
                            areaColor: '#00405b',
                            borderColor: '#02334a',
                            borderWidth: 1,
                            shadowColor: 'rgba(0, 189, 249, 0.5)',
                            shadowBlur: 2,
                            shadowOffsetY: 0,
                            shadowOffsetX: 0,
                        },
                        emphasis: {
                            areaColor: '#006387'
                        }
                    }
                }
            })
        }



        var level = 1
        var country = function(){
            level = 1
            $.ajax({
                url: 'mapdata/china.json',
                type: 'get',
                success: function(res) {
                   echarts.registerMap("mapCity", res);
                    map.dispose()
                    reset() 
                    map.on('click', (params) => {
                        const provinceCode=JSON.parse(res).features.filter((val) => {
                            if(val.properties.name.indexOf(params.name)!==-1){
                                return true
                            }
                        });
                        province(provinceCode[0].properties.id,params.name)

                    });
                }
            })
        }

        var province = function(id,name){
            level = 2
            $.ajax({
                url: 'mapdata/geometryProvince/'+id+'.json',
                type: 'get',
                success: function(res) {
                   echarts.registerMap("mapCity", res);
                   map.dispose()
                    reset() 
                    map.on('click', (params) => {
                        const provinceCode=JSON.parse(res).features.filter((val) => {
                            if(val.properties.name.indexOf(params.name)!==-1){
                                return true
                            }
                        });

                        city(provinceCode[0].properties.id,params.name)
                    });
                }
            })
        }
        var city = function(id,name){
            level = 3
            $.ajax({
                url: 'mapdata/geometryCouties/'+id+'00.json',
                type: 'get',
                success: function(res) {
                    echarts.registerMap("mapCity", res);
                    map.dispose()
                    reset() 
                    map.on('click', (params) => {
                        alert(params.name)
                    });
                }
            })
        }
        country()

    })(echarts)
    
```

html代码：
```html
<div id="map"></div>
```


### svg转geoJson

是的，看到这里你可能还在想，这个geoJson这么好用那怎么去写出来呀，这么一大坨。是的让我手写也不可能。但是SVG会画吧，我们可以直接画好SVG然后再转成我们想要的geoJson数据。当然SVG本身也是很好用的。在d3里体现的淋漓尽致。
这里我用的工具是[SVG 2 GeoJSON](https://github.com/Phrogz/svg2geojson)

示例：

svg:

<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1" id="图层_1" x="0px" y="0px" width="1200px" height="600px" viewBox="0 0 800 600" enable-background="new 0 0 800 600" xml:space="preserve" style="width: 100%;height: 100%;">
	<path d="M-33.52,392.276c0,19.539-4.147,33.977-12.442,43.316c-8.294,9.338-20.431,14.009-36.404,14.009   c-10.815,0-20.155-0.553-28.018-1.658v-29.124c3.318,0.613,7.065,0.922,11.244,0.922c8.848,0,14.898-2.612,18.156-7.834   c3.255-5.225,4.885-13.119,4.885-23.686V187.858h42.58V392.276z M-76.1,145.095v-36.312h42.395v36.312H-76.1z"/>
	<path d="M204.445,281.865c0,30.846-8.571,54.993-25.713,72.44c-17.143,17.448-41.197,26.175-72.164,26.175   c-29.985,0-53.455-8.848-70.413-26.543s-25.437-41.718-25.437-72.072c0-30.843,8.508-54.866,25.529-72.071   c17.019-17.203,40.828-25.806,71.426-25.806c31.581,0,55.604,8.387,72.072,25.161C196.211,225.922,204.445,250.161,204.445,281.865   z M160.022,281.865c0-44.359-17.142-66.542-51.427-66.542c-35.639,0-53.455,22.183-53.455,66.542   c0,21.75,4.36,38.404,13.087,49.953c8.724,11.552,21.382,17.326,37.971,17.326C142.08,349.144,160.022,326.72,160.022,281.865z"/>
	<path d="M288.129,187.858v105.619c0,19.17,3.01,32.934,9.032,41.289c6.019,8.358,15.42,12.534,28.202,12.534   c13.761,0,24.7-5.253,32.81-15.76s12.166-25.161,12.166-43.962v-99.721h42.395v145.249c0,14.746,0.553,29.309,1.659,43.686h-40.368   c-1.354-18.678-2.027-30.843-2.027-36.497h-0.737c-7.373,14.502-15.884,24.821-25.529,30.968   c-9.648,6.143-22.027,9.216-37.142,9.216c-20.893,0-36.589-6.238-47.096-18.709c-10.506-12.471-15.76-31.612-15.76-57.418V187.858   H288.129z"/>
	<path d="M651.806,281.312c0,31.335-7.097,55.637-21.29,72.901s-34.256,25.898-60.183,25.898c-27.896,0-47.619-11.429-59.169-34.285   h-0.737c0,10.322-0.737,20.645-2.212,30.967h-41.289c1.106-11.428,1.659-25.497,1.659-42.211V108.783h42.396v81.656   c0,4.917-0.063,9.801-0.185,14.654c-0.124,4.855-0.309,9.616-0.553,14.285h0.737c5.774-11.428,13.576-20.212,23.409-26.359   c9.83-6.143,22.732-9.216,38.708-9.216c25.189,0,44.607,8.45,58.247,25.345C644.985,226.046,651.806,250.1,651.806,281.312z    M607.567,281.312c0-44.359-15.607-66.542-46.819-66.542c-16.589,0-29.155,5.838-37.694,17.511   c-8.543,11.676-12.811,28.51-12.811,50.505c0,21.505,4.176,37.971,12.534,49.399c8.354,11.429,20.889,17.143,37.603,17.143   C591.836,349.328,607.567,326.656,607.567,281.312z"/>
	<path d="M820.279,376.793V271.174c0-19.17-2.981-32.931-8.94-41.289c-5.962-8.355-15.391-12.534-28.294-12.534   c-13.888,0-24.792,5.346-32.718,16.037s-11.889,25.192-11.889,43.501v99.905h-42.396V231.359c0-14.622-0.553-29.124-1.659-43.501   h40.368c1.351,18.433,2.027,30.598,2.027,36.497h0.737c7.249-14.129,15.668-24.392,25.253-30.782   c9.585-6.388,21.935-9.585,37.049-9.585c21.014,0,36.773,6.207,47.28,18.617c10.507,12.414,15.76,31.583,15.76,57.51v116.679   H820.279z"/>
</svg>

可拖拽、缩放（兼容手机）：


<iframe frameborder="0" width="100%" height="400px" src="http://static.joubn.com/2018-06-05-echarts-geojson-map/joubn.html"></iframe>


### 结束语

其实到这一步应该可以想象它的应用还是很广。如果觉得不够炫再加上3D效果（GL）试试。😏

<iframe frameborder="0" width="100%" height="400px" src="http://static.joubn.com/2018-06-05-echarts-geojson-map/joubn-gl.html"></iframe>







