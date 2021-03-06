# BannerLayoutSimple

支持图片无限轮播的控件

##支持功能

- 支持网络本地加载数据

- 可自定义小圆点状态，左 中 右

- 可自定义title状态，左 中 右

- 可自定义提示栏状态，上 中 下

- 可自定义小圆点

- 可自定义是否自动轮播

- 支持List 、数组 两种数据格式

- 支持点击事件

- 支持设置轮播速度

- 支持是否显示小圆点，title,或者整个提示栏

- 支持加载时和加载失败时图片显示状态

- 支持选择暂停 恢复 轮播状态

- 支持自定义提示栏

##使用效果
![](http://i.imgur.com/UXFFMDx.gif)



##基础使用方法

>项目中引用 

		compile 'com.ydevelop:bannerlayout:0.0.4'

>更新状态
	
	0.0.4： 支持自定义Model类，自定的需要继承BaseModel，修改0.0.3不能点击的bug
		
	0.0.3： 添加上次忘记添加的一些方法，支持自定义提示栏，如果自定义提示栏请不要初始化initRound()

	0.0.2： 修改部分代码，加载图片可选择自定义加载框架或者使用默认的Glide

	0.0.1：提交项目


>如果是网络加载图片 记得添加

	<uses-permission android:name="android.permission.INTERNET" />

1.数组方式

        BannerLayout bannerLayout = (BannerLayout) findViewById(R.id.bannerLayout);
        int[] mImage = new int[]{R.drawable.banner1, R.drawable.banner2, R.drawable.banner3};
        String[] mTitle = new String[]{"bannerl", "banner2", "banner3"};
        bannerLayout
                .initImageArrayResources(mImage, mTitle)
                .initAdapter()
                .initRound(true, true, true)
                .start(true);

2.List集合

        BannerLayout bannerLayout = (BannerLayout) findViewById(R.id.bannerLayout);
        List<BannerModel> mDatas = new ArrayList<>();
        mDatas.add(new BannerModel("http://ww2.sinaimg.cn/bmiddle/0060lm7Tgw1f94c6kxwh0j30dw099ta3.jpg", "那个时候刚恋爱，这个时候放分手"));
        mDatas.add(new BannerModel("http://ww4.sinaimg.cn/bmiddle/0060lm7Tgw1f94c6qyhzgj30dw07t75g.jpg", "羞羞呢～"));
        mDatas.add(new BannerModel("http://ww1.sinaimg.cn/bmiddle/0060lm7Tgw1f94c6f7f26j30dw0ii76k.jpg", "腿不长 但细"));
        mDatas.add(new BannerModel("http://ww4.sinaimg.cn/bmiddle/0060lm7Tgw1f94c63dfjxj30dw0hjjtn.jpg", "深夜了"));
        bannerLayout
                .initImageListResources(mDatas)
                .initAdapter()
                .initRound()
                .start(true);	

3.点击事件，也可以自己单独写，如果是list集合 返回的是当前model对象，如果是数组，返回的object就是当前数组对象,自定义的model如果获取的json图片命名不是image，请自行实现	ImageLoaderManage，返回的object里面获取网络url


		bannerLayout
                .initImageListResources(list) 
                .initAdapter()
                .initRound()
                .start(true)
                .setOnBannerClickListener(new BannerLayout.OnBannerClickListener() {
                    @Override
                    public void onBannerClick(int position, Object model) {
                        ImageModel imageModel = (ImageModel) model;
                        Toast.makeText(getApplicationContext(), imageModel.getTestText(), Toast.LENGTH_SHORT).show();
                        //如果是array  返回的object是传入的数组对象
	//                        int[] image = (int[]) model;
	//                        Toast.makeText(getApplicationContext(), image[position], Toast.LENGTH_SHORT).show();

                    }
                })
                });

4.提示栏及小圆点、title位置的改变

	想要改变位置在initRound()方法中实现几种不同的状态，不需要的可以直接传null 有默认的参数
	
	代码中提供了三个枚举
	
	- BANNER_ROUND_CONTAINER_POSITION 	 提示栏在布局中的位置，TOP,BUTTOM,CENTERED三种可选 
	- BANNER_ROUND_POSITION  	小圆点在提示栏的位置，LEFT,CENTERED,RIGHT三种可选 
	- BANNER_TITLE_POSITION  	title在提示栏的位置，LEFT,CENTERED,RIGHT三种可选 




5.使用自定义加载图片框架
	  
	默认使用Glide加载图片，如果不喜欢的继承 ImageLoaderManage 然后在代码中 setImageLoaderManage.

	 bannerLayout
                .initImageListResources(mBanner)
                .setImageLoaderManage(new ImageLoader()) //自己定义加载图片的方式
                .initAdapter()
                .initRound(true, true, false)
                .start(true);

	Glide默认就算是本地的资源文件也可以加载，但是Picasso加载时不行，如果使用Picasso加载图片请把url强转成int类型，其他的没有试过。

	

	    public class ImageLoader implements ImageLoaderManage {
	
	        @Override
	        public void display(Context context, ImageView imageView, Object url, Object model) {
				//如果是list集合 返回的model就是当前Model对象，如果是数组，返回的model就是当前数组对象
	            Picasso.with(context).load((int) url).placeholder(R.mipmap.ic_launcher).error(R.mipmap.ic_launcher).into(imageView);
	        }
	    }


6.自定义提示栏

>自定义提示栏不建议使用，为了简便才封装，如果使用自定义提示栏就违背初衷，所以没有什么能快速设置的功能请尽量提[lssues](https://github.com/7449/BannerLayoutSimple/issues),除非是非常奇葩的需求，再使用自定义提示栏吧

        bannerLayout
                .initImageListResources(mDatas)
                .setTitleSetting(ContextCompat.getColor(this, R.color.colorPrimary), -1)
                .addOnBannerPageChangeListener(new BannerOnPage())
				.initPromptBar(new PromptBarView(getBaseContext())) //自定义提示栏view initAdapter之前调用生效
                .initAdapter()
                .start(true);

     /**
     * 接管viewpager的onPage方法
     */
    public class BannerOnPage implements BannerLayout.OnBannerPageChangeListener {

        @Override
        public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

        }

        @Override
        public void onPageSelected(int position) {
            Log.i(getClass().getSimpleName(), position + "");
        }

        @Override
        public void onPageScrollStateChanged(int state) {

        }
    }
	


>最后调用start（）的时候可以决定是否开启自动轮播，不管在fragment还是activity里面，应该在合适的生命周期里选择暂停或者恢复轮播（如果开启了自动轮播），BannerLayout已经提供了方法，使用者直接调用就可以了，如果使用List数据，请使用BannerModel



## 自定义参数详解

属性名								|说明  						|属性值
---    								|---   						|---
delay_time   						|轮播时间					|默认2s
start_rotation   					|是否开启自动轮播				|true 开启，默认不开启
view_pager_touch_mode   			|viewpager是否可以手动滑动	|true禁止滑动,false可以滑动，默认可以滑动
round_selector   					|小圆点状态选择器				|可参考自带的
round_container_background_switch   |是否显示提示控件的背景		|true 显示，默认不显示
round_left_margin   				|小圆点的marginLeft			|默认10	
round_right_margin   				|小圆点的marginRight			|默认10	
title_left_margin   				|title marginLeft			|默认10	
title_right_margin   				|title marginRight			|默认10	
round_width   						|小圆点width					|默认15
round_height   						|小圆点height				|默认15
round_container_background   		|BannerRound背景色			|默认半透明色
round_container_width   			|BannerRound宽度				|填充屏幕
round_container_height 				|BannerRound高度				|默认50
glide_error_image  					|glide加载错误占位符			|默认android自带图标
glide_place_image  					|glide加载中占位符			|默认android自带图标
banner_round_visible  				|是否显示小圆点				|默认显示
banner_title_visible  				|是否显示title				|默认不显示
banner_title_size   				|字体大小					|默认12
banner_title_color 					|字体颜色					|默认黄色
banner_title_width 					|字体width					|默认自适应
banner_title_height 				|字体height					|默认自适应

#最后
	
BannerLayout这个类里面的注释我感觉已经很详细了，如果上面的设置有不懂得可以看BannerLayout。
我一个人肯定测不出来所有bug，所以现在我也不知道哪里还有问题，基本的使用暂时没发现问题。
如果有人在使用的过程中出现未知或者莫名其妙的bug，欢迎提 [lssues](https://github.com/7449/BannerLayoutSimple/issues),
至于图片加载我直接是内置了Glide来加载图片。不管本地或者网络的图片都可以，但是要记得添加网络权限


License
--
    Copyright (C) 2016 yuezhaoyang7449@163.com

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
