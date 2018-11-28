<template>
	<view class="uni-list">
		<view class="uni-list-cell" v-for="(product,key) in productList" :key="key" @tap="reading(product)">
			<view class="uni-media-list">
				<image class="uni-media-list-logo" :src="product.cover" mode="aspectFill"></image>
				<view class="uni-media-list-body">
					<view class="uni-media-list-text-top">
                        <view class="title">{{product.title}}</view>
                        <view class="brief">{{product.last_time}}</view>
                    </view>
					<view class="uni-media-list-text-bottom">
                        <view class="rate">已读{{product.rate}}%</view>
                        <progress :percent="product.rate" stroke-width="12" activeColor="#35352B" />
					</view>
				</view>
			</view>
		</view>
		<view class="none" v-if="productList.length == 0">
			
		</view>
	</view>
</template>

<script>
    import service from '../../service.js';
    
	export default {
		data() {
			return {
                openid: '',
				productList: []
			};
		},
        onLoad(e) {
            uni.showLoading();
            let readerInfo = service.getUsers();
            this.openid = readerInfo.openid;
            this.get_history();
        },
        onPullDownRefresh() {
            this.get_history();
        },
        methods: {
            get_history() {
                uni.request({
                	url: this.$requestUrl+'Comic/get_history_info',
                	method: 'GET',
                	data: {
                        openid: this.openid
                    },
                	success: res => {
                        this.productList = res.data.data;
                    },
                	fail: () => {},
                	complete: () => {
                        uni.hideLoading();
                        uni.stopPullDownRefresh();
                    }
                });
            },
            reading(e) {
                console.log(e);
            	uni.request({
            		url: this.$requestUrl+'Comic/get_reading_chapter',
            		method: 'GET',
            		data: {
            			comic_id: e.comic_id,
            			openid: this.openid
            		},
            		success: res => {
            			let detail = {
            				comic_id: e.comic_id,
            				title: e.title,
            				cover: e.cover,
            				chapter: res.data.data
            			}
            			uni.navigateTo({
            				url: "../comic-detail/comic-detail?detailData=" + JSON.stringify(detail)
            			})
            		},
            		fail: () => {},
            		complete: () => {}
            	});
            }
        },
        onShareAppMessage() {
        	return {
        		title: '漫画',
        		path: '/pages/index/index'
        	}
        },
	}
</script>

<style>
    page{
        background-color: #FFF;
    }
    .uni-list{
        padding: 20px;
        box-sizing: border-box;
    }
    .uni-list:before,
    .uni-list:after{
    	height: 0upx;
    }
    .uni-list-cell{
        margin-bottom: 12px;
        border: 2px solid #35352B;
        border-radius: 5px;
        background-color: #FBF5E7;
        box-shadow: 2px 2px 2px 0 rgba(0,0,0,.9);
    }
    .uni-media-list-logo{
        width: 150upx;
        height: 220upx;
        border: 2px solid #35352B;
        border-radius: 3px;
    }
    .uni-media-list-body{
        height: 220upx;
        padding: 0 10px;
    }
    .uni-media-list-text-top{
        margin: 15upx 0;
    }
    .uni-media-list-text-top .title{
        font-size: 32upx;
        line-height: 2em;
    }
    .uni-media-list-text-top .brief{
        font-size: 26upx;
    }
    .uni-media-list-text-bottom{
        margin-bottom: 20upx;
    }
    .uni-media-list-text-bottom .rate{
        font-size: 26upx;
        line-height: 2em;
    }
</style>
