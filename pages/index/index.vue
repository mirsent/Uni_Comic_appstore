<template>
	<view class="container">
		<!-- <swiper :indicator-dots="indicatorDots" :indicator-active-color="indicatorColor" :autoplay="autoplay" :interval="interval"
		 :duration="duration">
			<swiper-item v-for="(banner, index) in bannerData" :key="index">
				<view class="swiper-item">
					<image :src="banner.banner_url"></image>
				</view>
			</swiper-item>
		</swiper> -->

		<view class="list">
			<view class="item" v-for="(comic, index) in comicData" :key="index"
                :class="{up : index == 0}"
                @tap="goInfo(comic)">
				<image class="cover" :src="comic.cover" mode="aspectFill"></image>
                <view class="item-c">
                	<view class="title">{{comic.title}}</view>
                	<view class="brief uni-ellipsis">{{comic.brief}}</view>
                </view>
			</view>
		</view>
        
        <button class="login-btn" open-type="getUserInfo" v-if="!authed" @getuserinfo="login"></button>
	</view>
</template>

<script>
    import service from '../../service.js';
	export default {
		data() {
			return {
                authed: false,
				indicatorDots: true,
				indicatorColor: '#007aff',
				autoplay: true,
				interval: 5000,
				duration: 500,
                readerData: [],
				bannerData: [],
				comicData: [],
                
                proxyOpenid: ''
			};
		},
		onLoad(e) {
			uni.showLoading();
            
            if (e.detailData) {
            	// 判断是否代理推广
            	let shareInfo = JSON.parse(e.detailData);
            	this.proxyOpenid = shareInfo.proxy_openid;
            }
            
            // #ifdef MP-WEIXIN
            let _this = this;
            wx.getSetting({
            	success (res){
            		if (res.authSetting['scope.userInfo']) {
            			_this.authed = true;
            		}
            	}
            })
            this.code_2_session();
            // #endif
            
			// this.getBanner();
			this.getProduct();
		},
		methods: {
            login(e) {
                if (e.detail.errMsg == 'getUserInfo:ok') {
                	uni.request({
                		url: this.$requestUrl+'Comic/edit_reader',
                		method: 'POST',
                		header: {
                			'content-type': 'application/x-www-form-urlencoded'
                		},
                		data: {
                			openid: this.openid,
                			nickname: e.detail.userInfo.nickName,
                			head: e.detail.userInfo.avatarUrl
                		},
                		success: res => {
                			this.authed = true;
                			this.readerData = res.data.data;
                			uni.showToast({
                				title: '登录成功',
                				duration: 1500
                			});
                		}
                	});
                }
            },
			getBanner() {
				uni.request({
					url: this.$requestUrl+'Comic/get_comic_banner',
					method: 'GET',
					success: res => {
						this.bannerData = res.data.data;
					},
					fail: () => {},
					complete: () => {}
				});
			},
			getProduct() {
				uni.request({
					url: this.$requestUrl+'Comic/get_comic_list',
					method: 'GET',
                    data: {
                        type: '-1'
                    },
					success: res => {
						this.comicData = res.data.data;
					},
					fail: () => {},
					complete: () => {
						uni.hideLoading();
					}
				});
			},
			goList(e) {
				let detail = {
					release_type_id: e.release_type_id,
					release_type_name: e.release_type_name
				}
				uni.navigateTo({
					url: "../release/release?detailData=" + JSON.stringify(detail)
				})
			},
			goInfo(e) {
				let detail = {
					comic_id: e.comic_id,
					title: e.title
				}
				uni.navigateTo({
					url: "../comic-info/comic-info?detailData=" + JSON.stringify(detail)
				})
			},
            code_2_session() {
                uni.login({
                	provider: 'weixin',
                	success: res => {
                        uni.request({
                        	url: this.$requestUrl+'Comic/code_2_session',
                        	method: 'POST',
                            header: {
                            	'content-type': 'application/x-www-form-urlencoded'
                            },
                        	data: {
                                js_code: res.code,
                                proxy_openid: this.proxyOpenid
                            },
                        	success: res => {
                                let readerInfo = res.data.data;
                                service.addUser(readerInfo);
                                this.readerData = readerInfo;
                            },
                        	fail: () => {},
                        	complete: () => {}
                        });
                    }
                });
            }
		},
		onShareAppMessage() {
            let detail = {};
            let isProxy = this.readerData.is_proxy;
            if (isProxy) {
            	detail.proxy_openid = this.readerData.openid;
            }
			return {
				title: '漫画',
				path: '/pages/index/index?detailData=' + JSON.stringify(detail)
			}
		},
	}
</script>

<style>
	page {
		background: #F6F6F6;
	}

	swiper {
		height: 450upx;
	}

	.swiper-item image {
		width: 100%;
	}

    
    .list{
        padding: 0 20px;
    }
	.list .item{
        width: 100%;
        margin-bottom: 15px;
        background-color: #FFF;
        overflow: hidden;
        position: relative;
        border: 1px solid #EEE;
        border-radius: 10px;
        display: flex;
        flex-direction: column;
        box-shadow: 0 4px 8px 0 rgba(0,0,0,0.12),
                    0 2px 4px 0 rgba(0,0,0,0.08);
    }
    .list .cover{
        width: 100%;
        height: 460upx;
    }
    .list .item-c{
        padding: 8px 15px;
        background-color: rgba(255, 255, 255, .9);
    }
    .list .title{
        font-size: 42upx;
        font-weight: bold;
        line-height: 2em;
    }
    .list .brief{
        color: #888;
    }
    
    .list .up .cover{
        height: 600upx;
    }
    .list .up .item-c{
    	position: absolute;
    	bottom: 0;
    	left: 0;
    	right: 0;
        background-color: transparent;
        background-image: linear-gradient(to bottom, rgba(60,103,130,0), #333);
        color: #FFF;
    }
    .list .up .title{
        line-height: 1.5em;
    }
    .list .up .brief{
        color: #FFF;
    }


    .login-btn{
        position: fixed;
        top: 0;
        bottom: 0;
        left: 0;
        right: 0;
        background: none;
    }
    .login-btn:after{
        border: 0;
    }
</style>
