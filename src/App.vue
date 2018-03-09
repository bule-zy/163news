<template>
	<div id="app">
		<view-box>
			<x-header :left-options="{showBack:false}" class="header">
				<div slot="left">直播</div>
				<div>新闻</div>
				<div slot="right">搜索</div>
			</x-header>
			<scroller :lock-y="true">
				<div class="tab">
					<tab>
						<tab-item selected>推荐</tab-item>
						<tab-item>要闻</tab-item>
						<tab-item>娱乐</tab-item>
						<tab-item>游戏</tab-item>
						<tab-item>体育</tab-item>
						<tab-item>互联网</tab-item>
					</tab>
				</div>
			</scroller>
			<swiper :list="swiperList" v-model="swiperIndex" :loop="true"></swiper>
			<marquee class="m-marquee" :list="marqueeList">
				<marquee-item v-for="list in marqueeList">{{list.title}}</marquee-item>
			</marquee>
			<panel :list="dataList"></panel>
			<tabbar slot="bottom">
				<tabbar-item>
					<img src="./assets/icon_nav_button.png" slot="icon"/>
					<span slot="label">主页</span>
				</tabbar-item>
				<tabbar-item>
					<img src="./assets/icon_nav_article.png" slot="icon"/>
					<span slot="label">推荐</span>
				</tabbar-item>
				<tabbar-item>
					<img src="./assets/icon_nav_cell.png" slot="icon"/>
					<span slot="label">我的</span>
				</tabbar-item>
			</tabbar>
		</view-box>
		<router-view></router-view>
	</div>
</template>

<script>
	import { ViewBox, XHeader,Tabbar,TabbarItem, Tab, TabItem,Scroller,Swiper,Marquee, MarqueeItem,Panel } from 'vux';
	export default {
		name: 'App',
		components:{
			ViewBox,
			XHeader,
			Tabbar, 
			TabbarItem,
			Tab, 
			TabItem,
			Scroller,
			Swiper,
			Marquee, 
			MarqueeItem,
			Panel 
		},
		created(){
//			轮播图
			this.$jsonp("https://3g.163.com/touch/jsonp/sy/recommend/0-9.html").then(
				data => {
					this.swiperList = data.focus.filter(item=>{
						return item.addata === null;
					}).map(item =>{
						return{
							url:item.link,
							img:item.picInfo[0].url,
							title:item.title
						}
					})
				});
//			首屏新闻页
			this.$jsonp("https://3g.163.com/touch/jsonp/sy/recommend/0-9.html").then(
			data => {
				this.dataList = data.list.filter(item=>{
					return item.addata === null;
				}).map(item =>{
					return{
						url:item.link,
						src:item.picInfo[0].url,
						title:item.title,
						desc:item.digest
					}
				})
			});
//			滚动新闻列表
			this.$jsonp("https://3g.163.com/touch/jsonp/sy/recommend/0-9.html").then(
			data => {
				this.marqueeList = data.list.filter(item=>{
					return item.addata === null;
				}).map(item =>{
					return{
						title:item.title
					}
				})
			});
		},
		data(){
//			var dataList = [];
//			for(var i=0;i<10;i++){
//				dataList.push({
//		          	src: 'http://somedomain.somdomain/x.jpg',
//			        fallbackSrc: 'http://placeholder.qiniudn.com/60x60/3cc51f/ffffff',
//			        title: '标题一',
//			        desc: '由各种物质组成的巨型球状天体，叫做星球。星球有一定的形状，有自己的运行轨道。',
//			        url: '/component/cell'
//		      	})
//			}
			return{
				swiperList:[],
				swiperIndex:0,
				dataList:[],
				marqueeList:[]
			}
		}
	}
</script>

<style lang="less">
	@import '~vux/src/styles/reset.less';
	html,body{
		margin: 0;
		width: 100%;
		height: 100%;
		overflow-x: hidden;
	}
	#app{
		height: 100%;
		.header{
			position: absolute;
			left: 0;
			top: 0;
			width: 100%;
			height: 46px;
			z-index: 9;
		}
		.tab{
			margin-top: 46px;
			width: 600px;
		}
		.m-marquee{
			margin: 10px;
		}
	}
</style>
