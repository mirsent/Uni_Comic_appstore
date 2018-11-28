<template>
	<view>
		<view class="timeline">
			<view class="timeline-item" 
                :class="[{'timeline-first-item': index == 0}, {'timeline-last-item': index == noteData.length-1}]"
                v-for="(note, index) in noteData" :key="index">
				<view class="timeline-item-divider"></view>
				<view class="timeline-item-content bottom-border">
					<view>
						{{note.content}}
					</view>
					<view class="datetime">
                        <text>{{note.create_at}}</text>
						<text class="cost">-{{note.points}}金币</text> 
					</view>

				</view>
			</view>
            <text v-if="noteData.length == 0">暂无记录</text>
		</view>
	</view>
</template>

<script>
    import service from '../../service.js';
	export default {
		data() {
			return {
                openid: '',
                noteData: []
			};
		},
        onLoad(e) {
        	let readerInfo = service.getUsers();
        	this.openid = readerInfo.openid;
            
            this.getNote();
        },
        methods: {
            getNote() {
                uni.request({
                	url: this.$requestUrl+'Reader/get_recharge_note',
                	method: 'GET',
                	data: {
                        openid: this.openid
                    },
                	success: res => {
                        this.noteData = res.data.data;
                    },
                	fail: () => {},
                	complete: () => {}
                });
            }
        }
	}
</script>

<style>
    page{
        background-color: #FFF;
    }
    
	.timeline {
		padding: 35upx;
		display: flex;
		flex-direction: column;
		position: relative;
	}


	.timeline-item {
		display: flex;
		flex-direction: row;
		position: relative;
		padding-bottom: 20upx;
		box-sizing: border-box;
		overflow: hidden;

	}

	.timeline-item .timeline-item-divider {
		flex-shrink: 0;
		position: relative;
		width: 20upx;
		height: 20upx;
		top: 15upx;
		border-radius: 50%;
		background-color: #bbb;
	}



	.timeline-item-divider::before,
	.timeline-item-divider::after {
		position: absolute;
		left: 10upx;
		width: 1upx;
		height: 100vh;
		content: '';
		background: inherit;
	}

	.timeline-item-divider::before {
		bottom: 100%;
	}

	.timeline-item-divider::after {
		top: 100%;
	}


	.timeline-last-item .timeline-item-divider:after {
		display: none;
	}

	.timeline-first-item .timeline-item-divider:before {
		display: none;
	}

	.timeline-item .timeline-item-content {
        width: 100%;
		padding-left: 20upx;
	}

	.bottom-border::after {
		position: absolute;
		left: 40upx;
		right: 0;
		height: 1px;
		content: '';
		background-color: #bbb;
	}

	.timeline-last-item .bottom-border::after {
		display: none;
	}

	.timeline-item-content .datetime {
		color: #888;
        line-height: 2em;
        display: flex;
        justify-content: space-between;
	}

	/* 自定义节点颜色 */
	.timeline-first-item .timeline-item-divider {
		background-color: #E27C6B;
	}
    
    .cost{
        color: #e60340;
    }
</style>
