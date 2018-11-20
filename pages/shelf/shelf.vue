<template>
	<view class="uni-list">
		<view class="uni-list-cell" v-for="(product,key) in productList" :key="key" @tap="goInfo(product)">
			<view class="uni-media-list">
				<image class="uni-media-list-logo" :src="product.cover" mode="aspectFill"></image>
				<view class="uni-media-list-body">
					<view class="uni-media-list-text-top">{{product.title}}</view>
					<view class="uni-media-list-text-bottom">
						<text>已读{{product.rate}}%</text>
						<text>{{product.created_at}}</text>
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
            this.get_history(readerInfo.openid);
        },
        methods: {
            get_history() {
                uni.request({
                	url: this.$requestUrl+'get_history_info',
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
                    }
                });
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

</style>
