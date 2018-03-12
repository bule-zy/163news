<template>
	<div id="app">
		<view-box>
			<x-header :left-options="{showBack:false}" class="header">
				<div slot="left">直播</div>
				<div>新闻</div>
				<div slot="right">搜索</div>
			</x-header>
			<ac :lock-y="true">
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
			</ac>
			<scroller class="my-scroller" :on-refresh="refresh"  :on-infinite="infinite" ref="Mref">
				<swiper :list="swiperList" v-model="swiperIndex" :loop="true"></swiper>
				<marquee class="m-marquee" :list="marqueeList">
					<marquee-item v-for="item in marqueeList">{{item.title}}</marquee-item>
				</marquee>
				<panel :list="dataList"></panel>
				<panel :list="moreDataList"></panel>
			</scroller>
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
	var keyrefresh = ['A', 'B01', 'B02', 'B03', 'B04', 'B05', 'B06', 'B07', 'B08', 'B09', 'B010'];
	var key = 0;
	var star = 0;
	var end = star+9;
	var keyValue = "A";
	var initload = false;
	function getkeyRefresh(){
		key++;
		if(key == keyrefresh){
			key = 0;
		}
		keyValue = keyrefresh[key];
	}
	import { ViewBox, XHeader,Tabbar,TabbarItem, Tab, TabItem,Scroller as ac,Swiper,Marquee, MarqueeItem,Panel } from 'vux';
	export default {
		name: 'App',
		components:{
			ViewBox,
			XHeader,
			Tabbar, 
			TabbarItem,
			Tab, 
			TabItem,
			ac,
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
						return item.addata === null && item.picInfo[0];
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
					return item.addata === null && item.picInfo[0];
				}).map(item =>{
					return{
						url:item.link,
						src:item.picInfo[0].url,
						title:item.title,
						desc:item.digest
					}
				})
				initload = true;
			});
//			滚动新闻列表
			this.$jsonp("https://3g.163.com/touch/jsonp/sy/recommend/0-9.html").then(
			data => {
				this.marqueeList = data.list.filter(item=>{
					return item.addata === null && item.picInfo[0];
				}).map(item =>{
					return{
						title:item.title
					}
				})
			});
		},
		data(){
			return{
				swiperList:[],
				swiperIndex:0,
				dataList:[],
				marqueeList:[],
				moreDataList:[]
			}
		},
		methods:{
			refresh(){
				getkeyRefresh();
				this.$jsonp("https://3g.163.com/touch/jsonp/sy/recommend/0-9.html",{
					miss:"0",
					refresh:keyValue
				}).then(
				data => {
//					console.log(data)
//					console.log(this.$refs.Mref);
					this.dataList = data.list.filter(item=>{
						return item.addata === null && item.picInfo[0];
					}).map(item =>{
						return{
							url:item.link,
							src:item.picInfo[0].url,
							title:item.title,
							desc:item.digest
						}
					});
					this.$refs.Mref.finishPullToRefresh();
					this.$vux.toast.text(`当前一共刷新${this.dataList.length}条数据`, 'top')
				});
			},
			infinite(){
				
				if(!initload){
					this.$refs.Mref.finishInfinite();
					return;
				}
//				console.log(2);
				this.$jsonp(`https://3g.163.com/touch/jsonp/sy/recommend/${star}-${end}.html`,{
					miss:"0",
					refresh:keyValue
				}).then(data =>{
					setTimeout(() =>{
						var dataList= data.list.filter(item=>{
							return item.addata === null && item.picInfo[0];
						}).map(item =>{
							return{
								url:item.link,
								src:item.picInfo[0].url,
								title:item.title,
								desc:item.digest
							}
						});
						this.moreDataList = this.moreDataList.concat(dataList);
						star +=10;
						end = star + 9;
						this.$refs.Mref.finishInfinite();
					},1000);
				});
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
		.my-scroller{
			top: 90px;
		}
		.tab{
			margin-top: 46px;
			width: 600px;
		}
		.m-marquee{
			margin: 10px;
		}
		.weui-media-box__hd,
		.weui-media-box__hd img{
			width: 102px;
			height: 78px;
		}
		.weui-media-box__bd{
			height: 78px;
		}
	}
</style>
