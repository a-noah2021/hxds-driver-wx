<template>
	<view class="page">
		<view class="notice">
			<u-icon name="info-circle-fill" color="#AC9146" size="30" top="3" />
			<text>准确填写个人信息，可享受每份代驾订单人身意外险</text>
		</view>
		<view class="credentials-container">
			<view class="credentials">
				<image :src="cardBackground[0]" class="bg"></image>
				<view class="cover">
					<image src="../static/filling/card.png" mode="widthFix" class="card"></image>
					<text class="desc">身份证正面</text>
					<ocr-navigator @onSuccess="scanIdcardFront" certificateType="idCard" :opposite="false">
						<button class="camera"></button>
					</ocr-navigator>
				</view>
			</view>
			<view class="credentials">
				<image :src="cardBackground[1]" class="bg"></image>
				<view class="cover">
					<image src="../static/filling/card.png" mode="widthFix" class="card"></image>
					<text class="desc">身份证背面</text>
					<ocr-navigator @onSuccess="scanIdcardBack" certificateType="idCard" :opposite="true">
						<button class="camera"></button>
					</ocr-navigator>
				</view>
			</view>
			<view class="credentials">
				<image :src="cardBackground[2]" class="bg"></image>
				<view class="cover">
					<image src="../static/filling/card.png" mode="widthFix" class="card"></image>
					<text class="desc">手持身份证</text>
					<button class="camera" @tap="takePhoto('idcardHolding')"></button>
				</view>
			</view>
			<view class="credentials">
				<image :src="cardBackground[3]" class="bg"></image>
				<view class="cover">
					<image src="../static/filling/card.png" mode="widthFix" class="card"></image>
					<text class="desc">驾驶证正面</text>
					<ocr-navigator @onSuccess="scanDrcardFront" certificateType="driverslicense">
						<button class="camera"></button>
					</ocr-navigator>
				</view>
			</view>
			<view class="credentials">
				<image :src="cardBackground[4]" class="bg"></image>
				<view class="cover">
					<image src="../static/filling/card.png" mode="widthFix" class="card"></image>
					<text class="desc">驾驶证背面</text>
					<button class="camera" @tap="takePhoto('drcardBack')"></button>
				</view>
			</view>
			<view class="credentials">
				<image :src="cardBackground[5]" class="bg"></image>
				<view class="cover">
					<image src="../static/filling/card.png" mode="widthFix" class="card"></image>
					<text class="desc">手持驾驶证</text>
					<button class="camera" @tap="takePhoto('drcardHolding')"></button>
				</view>
			</view>
		</view>

		<view class="title">个人信息</view>
		<view class="list">
			<u-cell-group border="false">
				<u-cell-item title="真实姓名" :value="idcard.name" :value-style="style" :arrow="false" />
				<u-cell-item title="性别" :value="idcard.sex" :value-style="style" :arrow="false" />
				<u-cell-item title="生日" :value="idcard.birthday" :value-style="style" :arrow="false" />
				<u-cell-item title="身份证号" :value="idcard.pid" :value-style="style" :arrow="false" />
				<u-cell-item title="身份证地址" :value="idcard.shortAddress" :value-style="style" @click="showAddressContent" />
				<u-cell-item title="身份证有效期" :value="idcard.expiration" :value-style="style" :arrow="false" />
			</u-cell-group>
		</view>
		<view class="title">联系方式</view>
		<view class="list">
			<u-cell-group border="false">
				<u-cell-item title="手机号码" :value="contact.tel" :value-style="style" @click="enterContent('手机号码', 'tel')" />
				<u-cell-item title="电子信箱" :value="contact.shortEmail" :value-style="style" @click="enterContent('电子信箱', 'email')" />
				<u-cell-item
					title="收信地址"
					:value="contact.shortMailAddress"
					:value-style="style"
					@click="enterContent('收信地址', 'mailAddress')"
				/>
				<u-cell-item
					title="紧急联系人"
					:value="contact.contactName"
					:value-style="style"
					@click="enterContent('紧急联系人', 'contactName')"
				/>
				<u-cell-item
					title="紧急联系人电话"
					:value="contact.contactTel"
					:value-style="style"
					@click="enterContent('紧急联系人电话', 'contactTel')"
				/>
			</u-cell-group>
		</view>
		<view class="title">驾驶证</view>
		<view class="list">
			<u-cell-group border="false">
				<u-cell-item title="驾驶证类型" :value="drcard.carClass" :value-style="style" :arrow="false" />
				<u-cell-item title="驾驶证有效期" :value="drcard.validTo" :value-style="style" :arrow="false" />
				<u-cell-item title="初次领证日期" :value="drcard.issueDate" :value-style="style" :arrow="false" />
			</u-cell-group>
		</view>

		<button class="btn" @tap="save" :disabled="realAuth == 3">保存信息</button>
		<view class="remark">您每次提交实名信息之后，都需要工作人员严格审查，请等候1~3天，这期间您将无法接单，特此声明！</view>
		<u-toast ref="uToast" />
	</view>
</template>

<script>
let dayjs = require('dayjs');
export default {
	data() {
		return {
			mode: 'fill',
			style: {
				color: '#FF9900'
			},
			cardBackground: [
				'../static/filling/credentials-bg.jpg',
				'../static/filling/credentials-bg.jpg',
				'../static/filling/credentials-bg.jpg',
				'../static/filling/credentials-bg.jpg',
				'../static/filling/credentials-bg.jpg',
				'../static/filling/credentials-bg.jpg'
			],
			idcard: {
				pid: '',
				name: '',
				sex: '',
				address: '',
				shortAddress: '',
				birthday: '',
				expiration: '',
				idcardFront: '',
				idcardBack: '',
				idcardHolding: ''
			},
			contact: {
				tel: '',
				email: '',
				shortEmail: '',
				mailAddress: '',
				shortMailAddress: '',
				contactName: '',
				contactTel: ''
			},
			drcard: {
				issueDate: '',
				carClass: '',
				validFrom: '',
				validTo: '',
				drcardFront: '',
				drcardBack: '',
				drcardHolding: ''
			},
			cosImg: [],
			currentImg: {},
			realAuth: uni.getStorageSync('realAuth')
		};
	},
	methods: {
		scanIdcardFront: function(resp) {
			let that = this;
			let detail = resp.detail;
			that.idcard.pid = detail.id.text;
			that.idcard.name = detail.name.text;
			that.idcard.sex = detail.gender.text;
			that.idcard.address = detail.address.text;
			//需要缩略身份证地址，文字太长页面显示不了
			that.idcard.shortAddress = detail.address.text.substr(0, 15) + '...';
			that.idcard.birthday = detail.birth.text;
			//OCR插件拍摄到的身份证正面照片存储地址
			that.idcard.idcardFront = detail.image_path;
			//让身份证View标签加载身份证正面照片
			that.cardBackground[0] = detail.image_path;
			that.uploadCos(that.url.uploadCosPrivateFile, detail.image_path, 'driverAuth', function(resp) {
				let data = JSON.parse(resp.data);
				let path = data.path;
				that.currentImg['idcardFront'] = path;
				that.cosImg.push(path);
			});
		},
		scanIdcardBack: function(resp) {
			let that = this;
			let detail = resp.detail;
			//OCR插件拍摄到的身份证背面照片存储地址
			that.idcard.idcardBack = detail.image_path;
			//View标签加载身份证背面照片
			that.cardBackground[1] = detail.image_path;
			let validDate = detail.valid_date.text.split('-')[1];
			that.idcard.expiration = dayjs(validDate, 'YYYYMMDD').format('YYYY-MM-DD');
			that.uploadCos(that.url.uploadCosPrivateFile, detail.image_path, 'driverAuth', function(resp) {
				let data = JSON.parse(resp.data);
				let path = data.path;
				that.currentImg['idcardBack'] = path;
				that.cosImg.push(path);
			});
		},
		scanDrcardFront: function(resp) {
			let that = this;
			let detail = resp.detail;
			that.drcard.issueDate = detail.issue_date.text; //初次领证日期
			that.drcard.carClass = detail.car_class.text; //准驾车型
			that.drcard.validFrom = detail.valid_from.text; //驾驶证起始有效期
			that.drcard.validTo = detail.valid_to.text; //驾驶证截止有效期
			that.drcard.drcardFront = detail.image_path;
			that.cardBackground[3] = detail.image_path;
			that.uploadCos(that.url.uploadCosPrivateFile, detail.image_path, 'driverAuth', function(resp) {
				let data = JSON.parse(resp.data);
				let path = data.path;
				that.currentImg['drcardFront'] = path;
				that.cosImg.push(path);
			});
		},
		updatePhoto: function(type, path) {
			let that = this;
			that.uploadCos(that.url.uploadCosPrivateFile, path, 'driverAuth', function(resp) {
				let data = JSON.parse(resp.data);
				that.cosImg.push(data.path);
				if (type == 'idcardHolding') {
					that.cardBackground[2] = path;
					that.currentImg['idcardHolding'] = data.path;
					that.idcard.idcardHolding = data.path;
				} else if (type == 'drcardBack') {
					that.cardBackground[4] = path;
					that.currentImg['drcardBack'] = data.path;
					that.idcard.drcardBack = data.path;
				} else if (type == 'drcardHolding') {
					that.cardBackground[5] = path;
					that.currentImg['drcardHolding'] = data.path;
					that.idcard.drcardHolding = data.path;
				}
			});
			that.$forceUpdate();  //强制刷新视图层
		},
		takePhoto: function(type) {
			uni.navigateTo({
				url: '../identity_camera/identity_camera?type=' + type
			});
		},
		enterContent: function(title, key) {
			let that = this;
			uni.showModal({
				title: title,
				editable: true,
				content: that.contact[key],
				success: function(resp) {
					if (resp.confirm) {
						if (key == 'mailAddress') {
							that.contact['shortMailAddress'] = resp.content.substr(0, 15) + (resp.content.length > 15 ? '...' : '');
						} else if (key == 'email') {
							that.contact['shortEmail'] = resp.content.substr(0, 25) + (resp.content.length > 25 ? '...' : '');
						}
						that.contact[key] = resp.content;
					}
				}
			});
		},
		save: function() {
			let that = this;
			//判断是否设置了6张照片
			if (Object.keys(that.currentImg).length != 6) {
				that.$refs.uToast.show({
					title: '证件上传不完整',
					type: 'error'
				});
			}
			//执行前端验证
			else if (
				that.checkValidTel(that.contact.tel, '手机号码') &&
				that.checkValidEmail(that.contact.email, '电子信箱') &&
				that.checkValidAddress(that.contact.mailAddress, '收信地址') &&
				that.checkValidName(that.contact.contactName, '联系人') &&
				that.checkValidTel(that.contact.contactTel, '联系人电话')
			) {
				uni.showModal({
					title: '提示信息',
					content: '确认提交实名资料？',
					success: function(resp) {
						if (resp.confirm) {
							//比较哪些照片需要删除
							let temp = [];
							let values = [];
							//从JSON中获取6张证件照片的云端存储地址
							for (let key in that.currentImg) {
								let path = that.currentImg[key];
								values.push(path);
							}
							//判断cosImg数组里面哪些图片的云端地址不是6张图片的，这些图片要在云端删除
							for (let one of that.cosImg) {
								if (!values.includes(one)) {
									temp.push(one);
								}
							}
							if (temp.length > 0) {
								//删除云端文件
								that.ajax(that.url.deleteCosPrivateFile, 'POST', JSON.stringify({ pathes: temp }), function() {
									console.log('文件删除成功');
								});
							}
							//需要上传的实名认证数据
							let data = {
								pid: that.idcard.pid,
								name: that.idcard.name,
								sex: that.idcard.sex,
								birthday: that.idcard.birthday,
								tel: that.contact.tel,
								email: that.contact.email,
								mailAddress: that.contact.mailAddress,
								contactName: that.contact.contactName,
								contactTel: that.contact.contactTel,
								idcardAddress: that.idcard.address,
								idcardFront: that.currentImg.idcardFront,
								idcardBack: that.currentImg.idcardBack,
								idcardHolding: that.currentImg.idcardHolding,
								idcardExpiration: that.idcard.expiration,
								drcardType: that.drcard.carClass,
								drcardExpiration: that.drcard.validTo,
								drcardIssueDate: that.drcard.issueDate,
								drcardFront: that.currentImg.drcardFront,
								drcardBack: that.currentImg.drcardBack,
								drcardHolding: that.currentImg.drcardHolding
							};
							//提交Ajax请求，上传数据
							that.ajax(that.url.updateDriverAuth, 'POST', data, function(resp) {
								console.log('更新成功');
								that.$refs.uToast.show({
									title: '资料提交成功',
									type: 'success',
									callback: function() {
										uni.setStorageSync('realAuth', 3); //更新小程序Storage
										that.realAuth = 3; //更新模型层
										if (that.mode == 'create') {
											//提示新注册的司机采集面部数据
											uni.navigateTo({
												url:"../face_camera/face_camera?mode=create"
											})
										} else {
											//跳转到工作台页面
											uni.switchTab({
												url: '../../pages/workbench/workbench'
											});
										}
									}
								});
							});
						}
					}
				});
			}
		},
		showAddressContent: function() {
			if (this.idcard.address.length > 0) {
				uni.showModal({
					title: '身份证地址',
					content: this.idcard.address,
					showCancel: false
				});
			}
		}
	},
	onLoad: function(options) {
	    let that = this;
	    that.mode = options.mode;
	    if (uni.getStorageSync('realAuth') == 1) {
	        uni.showModal({
	            title: '提示信息',
	            content: '新注册的代驾司机请填写实名认证信息，并且上传相关证件照片',
	            showCancel: false
	        });
	    } else {
	        that.ajax(that.url.searchDriverAuth, 'GET', null, function(resp) {
	            let json = resp.data.result;
	            that.idcard.pid = json.pid;
	            that.idcard.name = json.name;
	            that.idcard.sex = json.sex;
	            that.idcard.birthday = json.birthday;
	            that.idcard.address = json.idcardAddress;
	            that.idcard.shortAddress = json.idcardAddress.substr(0, 15) + (json.idcardAddress.length > 15 ? '...' : '');
	            that.idcard.expiration = json.idcardExpiration;
	            that.idcard.idcardFront = json.idcardFront;
	            if (json.idcardFrontUrl.length > 0) {
	                that.cardBackground[0] = json.idcardFrontUrl;
	            }
	            that.idcard.idcardBack = json.idcardBack;
	            if (json.idcardBackUrl.length > 0) {
	                that.cardBackground[1] = json.idcardBackUrl;
	            }
	            that.idcard.idcardHolding = json.idcardHolding;
	            if (json.idcardHoldingUrl.length > 0) {
	                that.cardBackground[2] = json.idcardHoldingUrl;
	            }
	            that.contact.tel = json.tel;
	            that.contact.email = json.email;
	            that.contact.shortEmail = json.email.substr(0, 25) + (json.email.length > 25 ? '...' : '');
	            that.contact.mailAddress = json.mailAddress;
	            that.contact.shortMailAddress = json.mailAddress.substr(0, 15) + (json.mailAddress.length > 15 ? '...' : '');
	            that.contact.contactName = json.contactName;
	            that.contact.contactTel = json.contactTel;
	            that.drcard.carClass = json.drcardType;
	            that.drcard.validTo = json.drcardExpiration;
	            that.drcard.issueDate = json.drcardIssueDate;
	            that.drcard.drcardFront = json.drcardFront;
	            if (json.drcardFrontUrl.length > 0) {
	                that.cardBackground[3] = json.drcardFrontUrl;
	            }
	            that.drcard.drcardBack = json.drcardBack;
	            if (json.drcardBackUrl.length > 0) {
	                that.cardBackground[4] = json.drcardBackUrl;
	            }
	            that.drcard.drcardHolding = json.drcardHolding;
	            if (json.drcardHoldingUrl.length > 0) {
	                that.cardBackground[5] = json.drcardHoldingUrl;
	            }
	            if (that.idcard.idcardFront.length > 0) {
	                that.cosImg.push(that.idcard.idcardFront);
	                that.currentImg['idcardFront'] = that.idcard.idcardFront;
	            }
	            if (that.idcard.idcardBack.length > 0) {
	                that.cosImg.push(that.idcard.idcardBack);
	                that.currentImg['idcardBack'] = that.idcard.idcardBack;
	            }
	            if (that.idcard.idcardHolding.length > 0) {
	                that.cosImg.push(that.idcard.idcardHolding);
	                that.currentImg['idcardHolding'] = that.idcard.idcardHolding;
	            }
	            if (that.drcard.drcardFront.length > 0) {
	                that.cosImg.push(that.drcard.drcardFront);
	                that.currentImg['drcardFront'] = that.drcard.drcardFront;
	            }
	            if (that.drcard.drcardBack.length > 0) {
	                that.cosImg.push(that.drcard.drcardBack);
	                that.currentImg['drcardBack'] = that.drcard.drcardBack;
	            }
	            if (that.drcard.drcardHolding.length > 0) {
	                that.cosImg.push(that.drcard.drcardHolding);
	                that.currentImg['drcardHolding'] = that.drcard.drcardHolding;
	            }
	        });
	    }
	}
};
</script>

<style lang="less">
@import url('filling.less');
</style>
