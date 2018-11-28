<template>
	<view>
		<view class="info">
            <image class="logo" src="../../static/image/brand.png" mode="widthFix"></image>
            <view class="member">
                <view class="title">欢迎您</view>
                <view class="content" v-if="authed">
                	<image :src="readerInfo.head" class="head"></image>
                	<view class="nickname">{{readerInfo.nickname}}</view>
                </view>
                <view class="login" v-else>
                	<button class="login-btn" open-type="getUserInfo" @getuserinfo="login">
                		登录
                	</button>
                </view>
            </view>
		</view>
		
		<view class="uni-list">
            <view class="uni-list-cell">
            	<view class="uni-list-cell-navigate">
            		<view class="left">
            			<image src="../../static/image/score.png" class="icon"></image>
            			积分
            		</view>
                    <view class="right">
                    	{{readerInfo.balance}}
                    </view>
            	</view>
            </view>
			<view class="uni-list-cell" v-for="(list,index) in listData" :key="index">
				<view class="uni-list-cell-navigate uni-navigate-right" @tap="custom(list.event)">
					<view class="left">
						<image :src="list.icon" class="icon"></image>
						{{list.title}}
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
    import service from '../../service.js';
    
	export default {
		data() {
			return {
                authed: false,
                openid: '',
                readerInfo: {
                    head: '../../static/image/user.png',
                },
                listData: [
                    {
                    	title: '充值',
                    	icon: '../../static/image/recharge.png',
                        event: 'recharge'
                    },
                    {
                    	title: '充值记录',
                    	icon: '../../static/image/pay.png',
                        event: 'getRechargeNote'
                    },
                    {
                    	title: '消费记录',
                    	icon: '../../static/image/pay.png',
                        event: 'getConsumeNote'
                    }
                ]
			};
		},
        onLoad(e) {
            let _this = this;
            let readerInfo = service.getUsers();
            this.openid = readerInfo.openid;
            wx.getSetting({
            	// 检查权限
            	success (res){
            		if (res.authSetting['scope.userInfo']) {
                        _this.authed = true;
            			_this.readerInfo = readerInfo;
            		}
            	}
            })
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
                    		this.readerInfo = res.data.data;
                    		uni.showToast({
                    			title: '登录成功',
                    			duration: 1500
                    		});
                    	},
                    	fail: () => {},
                    	complete: () => {}
                    });
                }
            },
            custom(e) {
                this[e]()
            },
            recharge() {
                uni.showToast({
                    icon: 'none',
                	title: '期待吗'
                })
            },
            integral() {
                uni.showToast({
                	icon: 'none',
                	title: '期待吗'
                })
            },
            getRechargeNote() {
                uni.navigateTo({
                	url: "../note-recharge/note-recharge"
                })
            },
            getConsumeNote() {
                uni.navigateTo({
                	url: "../note-consume/note-consume"
                })
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
    .info{
        height: 300upx;
        background-color: #E27C6B;
        position: relative;
        margin-bottom: 150upx;
        display: flex;
        justify-content: center;
    }
    .info .logo{
        width: 300upx;
    }
    .info .member{
        width: 600upx;
        height: 250upx;
        border-radius: 5px;
        background-color: #FFF;
        position: absolute;
        bottom: -125upx;
        box-shadow: 0 2px 4px 0 rgba(0,0,0,0.10);
        display: flex;
        flex-direction: column;
        justify-content: space-around;
        align-items: center;
    }
    .info .member .content{
        display: flex;
        align-items: center;
    }
    .info .member .head{
        width: 120upx;
        height: 120upx;
        margin-right: 30upx;
    }
    
    .login-btn{
        font-size: 32upx;
        width: 300upx;
        height: 80upx;
        line-height: 80upx;
        margin: 0;
        border-radius: 80upx;
        color: #FFF;
        background-color: #E27C6B;
    }
    .login-btn:after{
        border: none;
    }
    
    .uni-list:before,
    .uni-list:after{
    	height: 0upx;
    }
    .uni-list-cell-navigate {
        line-height: 2em;
    }
    .uni-list .left{
        display: flex;
        align-items: center;
        font-size: 30upx;
    }
    .uni-list .icon{
        width: 30px;
        height: 30px;
        margin-right: 10px;
    }
</style>
