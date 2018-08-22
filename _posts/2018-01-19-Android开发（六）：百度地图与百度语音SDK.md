---
title: Android开发（六）：百度地图与百度语音SDK
key: 20180119
tags: Android
---

## 百度地图SDK ##

[百度地图开放平台](http://lbsyun.baidu.com/index.php?title=%E9%A6%96%E9%A1%B5)上的功能种类多，但是文档并不是很靠谱。建议尝试文档详尽的高德地图API哈哈。

首先注册／登录一个百度账号，在控制台创建你的应用。

![屏幕快照 2018-01-17 21.09.07.png](https://i.loli.net/2018/08/22/5b7d01eda4f69.png)

访问应用（AK）是接入百度地图SDK的重要密钥。

![屏幕快照 2018-01-17 21.11.14.png](https://i.loli.net/2018/08/22/5b7d01f2aab7d.png)

勾选你所需要启用的服务，填好项目的包名，最重要的是获取发布版SHA1和开发版SHA1。

这篇非常详细地指导了如何在IDE中获得：[Android Studio 和 Eclipse 中获取SHA1详解](http://bbs.lbsyun.baidu.com/forum.php?mod=viewthread&tid=112007)

下载自定义SDK：[SDK下载](http://lbsyun.baidu.com/index.php?title=sdk/download&action#selected=mapsdk_basicmap,mapsdk_searchfunction,mapsdk_lbscloudsearch,mapsdk_calculationtool,mapsdk_radar)

关于在项目中如何接入百度地图SDK，文档还是比较良心。[Android 地图SDK](http://lbsyun.baidu.com/index.php?title=androidsdk/guide/create-project/ak)

当遇到接入后不显示地图而显示网格的问题，我也出现过，可以排查下自己的AK是否正确，申请权限是否写清楚了，发布版和开发版SHA1是否都正确填写，还有网络是否可用。

<!--more-->

 1. 显示某经纬度位置的N级地图
    
	  
	    TextureMapView mMapView;
	    BaiduMap mBaiduMap;
	    
	    //获取地图控件引用
	    mMapView = (TextureMapView)v.findViewById(R.id.mTexturemap);
	    mBaiduMap = mMapView.getMap();
	
	    // 开启定位图层
	    mBaiduMap.setMyLocationEnabled(true);
	
	    // map type
	    mBaiduMap.setMapType(BaiduMap.MAP_TYPE_NORMAL);
	
	    // 定位点为陕西历史博物馆纬度，经度，放大级数为19级        mBaiduMap.setMapStatus(MapStatusUpdateFactory.zoomTo((float)19.0));
	    getLocationByLL(34.230637, 108.961711);
	    
	    public void getLocationByLL(double la, double lg){
	        //地理坐标的数据结构
	        LatLng latLng = new LatLng(la, lg);
	        //描述地图状态将要发生的变化,通过当前经纬度来使地图显示到该位置
	        MapStatusUpdate msu = MapStatusUpdateFactory.newLatLng(latLng);
	        mBaiduMap.animateMapStatus(msu);
	    }
	
	![app.jpeg](https://i.loli.net/2018/08/22/5b7d01e8d52dc.jpeg)
	
2. 实现定位（带方向传感器）

	    
	    LocationClient mLocationClient = new LocationClient(this);
	    MyLocationListener mLocationListener = new MyLocationListener();
	    mLocationClient.registerLocationListener(mLocationListener);
	
	    LocationClientOption option = new LocationClientOption();
	    option.setCoorType("bd09ll");
	    option.setIsNeedAddress(true);
	    option.setOpenGps(true);
	    option.setScanSpan(1000);
	    mLocationClient.setLocOption(option);
	
	    myOrientationListener = new MyOrientationListener(MainActivity.this);
	    myOrientationListener.setOnOrientationListener(new    MyOrientationListener.OnOrientationListener() {
	        @Override
	        public void OnOrientationChanged(float x) {
	            mCurrentX = x;
	        }
	    });
	
	    // 开启定位
	    if(!mLocationClient.isStarted())
	        mLocationClient.start();
	
	    // 开启方向传感器
	    myOrientationListener.start();
	    
	    
	    public class LocateClickListener implements View.OnClickListener {
	        @Override
	        public void onClick(View v) {
	            BitmapDescriptor mMarker = BitmapDescriptorFactory.fromResource(R.drawable.point);
	            MyLocationConfiguration configuration = new MyLocationConfiguration(MyLocationConfiguration.LocationMode.NORMAL, true, mMarker);
	            mBaiduMap.setMyLocationConfiguration(configuration);
	            MapStatusUpdate msu = MapStatusUpdateFactory.zoomTo((float)16.0);
	            mBaiduMap.setMapStatus(msu);
	
	            getLocationByLL(mLatitude, mLongtitude);
	        }
	    }
	
	    private class MyLocationListener implements BDLocationListener{
	        // 定位后的回调
	        @Override
	        public void onReceiveLocation(BDLocation location) {
	
	            MyLocationData data = new MyLocationData.Builder()
	                    .direction(mCurrentX)
	                    .accuracy(location.getRadius())
	                    .latitude(location.getLatitude())
	                    .longitude(location.getLongitude()).build();
	
	            mBaiduMap.setMyLocationData(data);
	
	            mLatitude = location.getLatitude();
	            mLongtitude = location.getLongitude();
	        }
	    }
	
	![map4.jpeg](https://i.loli.net/2018/08/22/5b7d01e219f52.jpeg)

3. 显示自定义地图标注

	
	    //创建OverlayOptions的集合
	    List<OverlayOptions> options = new ArrayList<OverlayOptions>();
	    //设置坐标点
	    LatLng point1 = new LatLng(34.230159, 108.961697);
	    LatLng point2 = new LatLng(34.2305, 108.962425);
	    LatLng point3 = new LatLng(34.230772, 108.961724);
	    LatLng point4 = new LatLng(34.230697, 108.961059);
	    LatLng point5 = new LatLng(34.230123, 108.961032);
	    //创建OverlayOptions属性
	    BitmapDescriptor ex1 = BitmapDescriptorFactory.fromResource(R.drawable.ex1);
	    BitmapDescriptor ex2 = BitmapDescriptorFactory.fromResource(R.drawable.ex2);
	    BitmapDescriptor ex3 = BitmapDescriptorFactory.fromResource(R.drawable.ex3);
	    BitmapDescriptor ex4 = BitmapDescriptorFactory.fromResource(R.drawable.ex4);
	    BitmapDescriptor ex5 = BitmapDescriptorFactory.fromResource(R.drawable.ex5);
	    OverlayOptions option1 = new MarkerOptions()
	            .position(point1)
	            .title("first")
	            .icon(ex1);
	    OverlayOptions option2 = new MarkerOptions()
	            .position(point2)
	            .title("second")
	            .icon(ex2);
	    OverlayOptions option3 = new MarkerOptions()
	            .position(point3)
	            .title("third")
	            .icon(ex3);
	    OverlayOptions option4 = new MarkerOptions()
	            .position(point4)
	            .title("fourth")
	            .icon(ex4);
	    OverlayOptions option5 = new MarkerOptions()
	            .position(point5)
	            .title("fifth")
	            .icon(ex5);
	    //将OverlayOptions添加到list
	    options.add(option1);
	    options.add(option2);
	    options.add(option3);
	    options.add(option4);
	    options.add(option5);
	
	    //在地图上批量添加
	    mBaiduMap.addOverlays(options);
	    mBaiduMap.setOnMarkerClickListener(new MarkerClickListener());
	
	    public class MarkerClickListener implements BaiduMap.OnMarkerClickListener {
	        @Override
	        public boolean onMarkerClick(Marker marker) {
	            String title = marker.getTitle();
	            Intent intent = new    Intent(MainActivity.this,secondActivity.class);
	            intent.putExtra("list", title);
	            startActivity(intent);
	            return true;
	        }
	    }
	
	并为标注物添加点击事件。
	
	![map1.jpeg](https://i.loli.net/2018/08/22/5b7d01eb4f027.jpeg)

4. 静态图

	用于馆内路线规划：[静态图](http://lbsyun.baidu.com/index.php?title=static)
	
	    //定义Ground的显示地理范围
	    LatLng southwest = new LatLng(34.229085, 108.959793);
	    LatLng northeast = new LatLng(34.2321, 108.962955);
	    LatLngBounds bounds = new LatLngBounds.Builder()
	        .include(northeast)
	        .include(southwest)
	        .build();
	
	    //定义Ground显示的图片
	    BitmapDescriptor bdGround = BitmapDescriptorFactory
	        .fromResource(R.drawable.musume);
	
	    //定义Ground覆盖物选项
	    OverlayOptions ooGround = new GroundOverlayOptions()
	        .positionFromBounds(bounds)
	        .image(bdGround)
	        .transparency(1.0f);
	
	     //在地图中添加Ground覆盖物
	     mBaiduMap.addOverlay(ooGround);
	
	![map.jpeg][11]

5. 步行路线规划


	    RoutePlanSearch mSearch = RoutePlanSearch.newInstance();
	    RouteLine route = null;
	    // 陕西历史博物馆附近
	    final LatLng stPoint = new LatLng(34.230037, 108.961011);
	    final LatLng enPoint = new LatLng(34.230637, 108.961711);
	
	    mSearch.setOnGetRoutePlanResultListener(new OnGetRoutePlanResultListener() {
	        @Override
	        public void onGetWalkingRouteResult(WalkingRouteResult walkingRouteResult) {
	            if (walkingRouteResult == null || walkingRouteResult.error != SearchResult.ERRORNO.NO_ERROR) {
	                Toast.makeText(MainActivity.this, "抱歉，规划路线失败", Toast.LENGTH_SHORT).show();
	             }
	             if (walkingRouteResult.error == SearchResult.ERRORNO.AMBIGUOUS_ROURE_ADDR) {
	             // 起终点或途经点地址有岐义，通过以下接口获取建议查询信息
	             // result.getSuggestAddrInfo()
	             return;
	             }
	             if (walkingRouteResult.error == SearchResult.ERRORNO.NO_ERROR) {
	                WalkingRouteOverlay overlay = new WalkingRouteOverlay(mBaiduMap);
	                mBaiduMap.setOnMarkerClickListener(overlay);
	        overlay.setData(walkingRouteResult.getRouteLines().get(0));
	        overlay.addToMap();
	        overlay.zoomToSpan();
	
	        double distance = DistanceUtil. getDistance(stPoint, enPoint);
	        Toast.makeText(MainActivity.this, "温馨提示您：您距离陕西历史博物馆" + String.format("%.1f", distance) + "米", Toast.LENGTH_SHORT).show();
	             }
	        }
	
	        @Override
	        public void onGetTransitRouteResult(TransitRouteResult transitRouteResult) {}
	
	         @Override
	         public void onGetMassTransitRouteResult(MassTransitRouteResult massTransitRouteResult) {}
	
	         @Override
	         public void onGetDrivingRouteResult(DrivingRouteResult drivingRouteResult) {}
	
	         @Override
	         public void onGetIndoorRouteResult(IndoorRouteResult indoorRouteResult) {}
	
	         @Override
	         public void onGetBikingRouteResult(BikingRouteResult bikingRouteResult) {}
	    });
	
	    PlanNode startPlanNode = PlanNode.withLocation(stPoint);
	    PlanNode endPlanNode = PlanNode.withLocation(enPoint);
	
	    mSearch.walkingSearch((new WalkingRoutePlanOption())
	        .from(startPlanNode)
	        .to(endPlanNode));
	
	这里为方便显示效果将起始位置设置在了博物馆的附近。
	
	![map2.jpeg](https://i.loli.net/2018/08/22/5b7d01ee9428b.jpeg)

----------

## 百度语音SDK ##

因为app是手机导览系统，所以需要在介绍展览和文物时的语音讲解，由于语音资料有限，所以选择了使用百度语音的语音合成服务。[百度语音开放平台](http://yuyin.baidu.com/)

![屏幕快照 2018-01-17 09.39.21.png](https://i.loli.net/2018/08/22/5b7d01dcf330f.png)

由于之前已经注册成为了百度地图的开发者，所以这里不需要第一步。接下来就创建应用，选择语音合成服务，而不是语音识别或是语音唤醒服务。下载好离在线融合SDK后，根据文档进行开发。

注意将下载的Demo包中文件夹下附带SDK的库放入自己的项目下的相同文件夹下，没有的就新建文件夹。

![yuyin.jpeg](https://i.loli.net/2018/08/22/5b7d01ef0262e.jpeg)

使用了两个按钮用于播放，暂停与继续播放。
