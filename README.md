# wzwzwz555
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="initial-scale=1.0, user-scalable=no, width=device-width">
    <title>下属行政区查询</title>
    <link rel="stylesheet" href="https://a.amap.com/jsapi_demos/static/demo-center/css/demo-center.css"/>
    <style type="text/css">
        html,body,#container{
            height:100%;
        }
    </style>
</head>
<body>
<div id="container"></div>

<div class="input-card">
    <h4>下属行政区查询</h4>
    <div class="input-item">
        <div class="input-item-prepend"><span class="input-item-text" >省市区</span></div>
        <select id='province' style="width:100px" onchange='search(this)'></select>
    </div>
    <div class="input-item">
        <div class="input-item-prepend"><span class="input-item-text" >地级市</span></div>
        <select id='city' style="width:100px" onchange='search(this)'></select>
    </div>
    <div class="input-item">
        <div class="input-item-prepend"><span class="input-item-text" >区县</span></div>
        <select id='district' style="width:100px" onchange='search(this)'></select>
    </div>
    <div class="input-item">
        <div class="input-item-prepend"><span class="input-item-text" >街道</span></div>
        <select id='street' style="width:100px" onchange='setCenter(this)'></select>
    </div>
    <img src="" alt="">
                    
                    	<div class="imgBox">
                    		<img src="" style="width:100%;" />
                    	</div>
                    </div><img id="leftLate" src="/Content/images/small_left.png" />
                    <div style="width: 200px; float: left;overflow: hidden;height: 60px;"><div class="infoImg" style="width:' + infoImgLen * 70 + 'px">
                    	'<img src=' + data.BridgeImage[j].ImagePath[i].ImageUrl + ' title=' + title + ' />'
                    </div></div><img id="rightLate" src="/Content/images/small_right.png" />

</div>
<script type="text/javascript" src="https://webapi.amap.com/maps?v=1.4.15&key=147251588ca93141a5efd74af8470475&plugin=AMap.DistrictSearch"></script>
<script type="text/javascript">
    var map, district, polygons = [];
    var citySelect = document.getElementById('city');
    var districtSelect = document.getElementById('district');
    var areaSelect = document.getElementById('street');

    map = new AMap.Map('container', {
        resizeEnable: true,
        center: [116.30946, 39.937629],
        zoom: 3
    });
    //行政区划查询
    var opts = {
        subdistrict: 1,   //返回下一级行政区
        showbiz:false  //最后一级返回街道信息
    };
    district = new AMap.DistrictSearch(opts);//注意：需要使用插件同步下发功能才能这样直接使用
    district.search('中国', function(status, result) {
        if(status=='complete'){
            getData(result.districtList[0]);
            
        }
    });




    function getData(data,level) {
        var bounds = data.boundaries;
        if (bounds) {
            for (var i = 0, l = bounds.length; i < l; i++) {
                var polygon = new AMap.Polygon({
                    map: map,
                    strokeWeight: 1,
                    strokeColor: '#0091ea',
                    fillColor: '#80d8ff',
                    fillOpacity: 0.2,
                    path: bounds[i]
                });
                polygons.push(polygon);
            }
            map.setFitView();//地图自适应
        }
       
        //清空下一级别的下拉列表
        if (level === 'province') {
            citySelect.innerHTML = '';
            districtSelect.innerHTML = '';
            areaSelect.innerHTML = '';
        } else if (level === 'city') {
            districtSelect.innerHTML = '';
            areaSelect.innerHTML = '';
        } else if (level === 'district') {
            areaSelect.innerHTML = '';
        }

        var subList = data.districtList;
        debugger
        if (subList) {
            var contentSub = new Option('--请选择--');
            var curlevel = subList[0].level;
            var curList =  document.querySelector('#' + curlevel);
            curList.add(contentSub);
            for (var i = 0, l = subList.length; i < l; i++) {
                var name = subList[i].name;
                var levelSub = subList[i].level;
                var cityCode = subList[i].citycode;
                contentSub = new Option(name);
                contentSub.setAttribute("value", levelSub);
                contentSub.center = subList[i].center;
                contentSub.adcode = subList[i].adcode;
                curList.add(contentSub);
            }
        }
        
    }
    
    


    
    function search(obj) {
        debugger
        //清除地图上所有覆盖物
        for (var i = 0, l = polygons.length; i < l; i++) {
            polygons[i].setMap(null);
        }
        var option = obj[obj.options.selectedIndex];
        var keyword = option.text; //关键字
        var adcode = option.adcode;
        district.setLevel(option.value); //行政区级别
        district.setExtensions('all');
        //行政区查询
        //按照adcode进行查询可以保证数据返回的唯一性
        district.search(adcode, function(status, result) {
            if(status === 'complete'){
                getData(result.districtList[0],obj.id);
            }
        });
    }




    function setCenter(obj){

        debugger
        map.setCenter(obj[obj.options.selectedIndex].center)
    }
</script>
<script type="text/javascript" src="https://webapi.amap.com/demos/js/liteToolbar.js"></script>

</body>
</html>
