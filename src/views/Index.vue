<template>
	<Header></Header>
	<Loading :showLoading="showLoading" />
	<div>
		<!-- 页面内容 -->
		<div class="container">
			<img class="banner" src="../assets/images/banner.png" mode=""></img>
			<div class="uni-form-item uni-column">
				<div class="title">Upload your photos</div>

				<div class="content">
					<div class="upload upload-done" v-if="imgUrl">
						<img class="upload-res" :src="imgUrl" mode=""></img>
						<img class="deleteIcon" @click="deleteImg" src="../assets/icons/delete.png" mode=""></img>
					</div>
					<van-uploader v-else style="width: 100%" :max-size="maxSize" @oversize="onOversize"
						:after-read="afterRead">

						<div class="upload upload-before">
							<img class="uploadIcon" src="../assets/icons/uploadImg.png" mode=""></img>
							<text>limit 30MB per file</text>
						</div>
					</van-uploader>


				</div>

			</div>

			<div class="uni-form-item uni-column">
				<div class="title">Customize your clothing logo</div>
				<div class="content">
					<input v-model="logoText" name="input" placeholder="a zombie in a fire, burning flames behind him" />
				</div>
			</div>

			<div class="uni-form-item error-text" v-if="showError">
				<div class="content">Please Upload your photos</div>
			</div>
			<div class="bottom_btn">

				<button class="submitBtn" @click="formSubmit">
					<text>Generate img</text>
				</button>
			</div>


		</div>
	</div>
</template>

<script setup>
import { ref } from 'vue';
import { showToast } from 'vant';
import { useRouter } from 'vue-router'

import { AI } from 'aonweb'

import 'vant/lib/index.css';
import Header from '../components/Header.vue';
import Loading from '../components/Loading.vue';

const router = useRouter()

const showLoading = ref(false);
const showError = ref(false);
const logoText = ref('');
const imgUrl = ref('');
const submitImgUrl = ref('');

const maxSize = 30 * 1024 * 1024;
function goToComplete(url) {
	const query = { url: url }
	router.push({
		path: '/created',
		query
	})
}

const onOversize = (file) => {
	console.log(file);
	showToast('文件大小不能超过 30MB');
};

function afterRead(file) {
	const formData = new FormData();
	formData.append('file', file.file);
	console.log(formData, file.file)

	// 调用上传接口
	uploadFile(formData).then(res => {
		console.log(res);
		if (res.code == 200 && res.data && res.data.length) {
			submitImgUrl.value = res.data
		}

	}).catch(err => {
		showToast('image upload failed');
		console.log(err);
	});

	imgUrl.value = URL.createObjectURL(file.file);

}
// 上传接口
const uploadFile = async (formData) => {
	const response = await fetch('https://tmp-file.aigic.ai/api/v1/upload?expires=1800&type=image/png', {
		method: 'POST',
		body: formData
	});

	const data = await response.json();
	console.log(data);
	return data;
};

function deleteImg() {
	if (imgUrl.value) {
		const formData = new FormData();
		formData.append('file', imgUrl.value);

		// 删除文件
		formData.delete('file');
		imgUrl.value = null;
		submitImgUrl.value = null

		console.log('File deleted:', formData.get('file'));
	} else {
		console.log('No file to delete')
	}
}


const formSubmit = async () => {
	if (!logoText.value || !imgUrl.value || !submitImgUrl.value) {
		showError.value = true

		setTimeout(() => {
			showError.value = false
		}, 3000)
		return
	}
	showLoading.value = true
	try {
		// AI 使用方法
		const ai_options = {
			//Please replace with your own API key or jwt token.
			auth: "Rbhpcp0FKNrYNA1nZkrwrIbD0YSSRlVG",
			// host: "http://localhost:8080"
		}

		const aonet = new AI(ai_options)

		const data = {
			input: {
				"image": submitImgUrl.value,
				"style": "Clay",
				"prompt": logoText.value,
				"negative_prompt": "",
				"prompt_strength": 4.5,
				"denoising_strength": 1,
				"instant_id_strength": 0.8
			}
		}
		console.log("formSubmit data", data)
		let response = await aonet.prediction("/predictions/ai/face-to-many", data)
		console.log("test", response)
		if (response.task.exec_code == 200 && response.task.is_success) {
			showLoading.value = false

			let url = response.output
			if (Array.isArray(response.output)) {
				url = response.output && response.output.length && response.output[0]
			}
			if (typeof url == 'object' || typeof url == 'Object') {
				return
			}

			goToComplete(url)
		} else {
			showLoading.value = false
			showToast('AI processing failed')
		}
	} catch (error) {
		showLoading.value = false
		showToast('AI processing failed')
	}

}

</script>

<style scoped>
.banner {
	width: 100%;
	height: 27.73vw;
	margin-top: 8.53vw;

	margin-bottom: 8.53vw;
}

.uni-form-item .title {
	font-family: Roboto-Bold;
	font-weight: bold;
	font-size: 3.73vw;
	color: #000000;
	text-align: left;
	font-style: normal;
	text-transform: none;
	margin-bottom: 2.13vw;
}

.uni-form-item {
	margin-bottom: 8.53vw;
}

.uni-form-item .content {
	width: 100%;
	height: 14.93vw;
	background: #F1F1F1;
	border-radius: 1.07vw;
	display: flex;
	align-items: center;
	padding: 0 3.2vw;
	box-sizing: border-box;

}

.error-text .content {
	background-color: #F3A32B;
	font-size: 3.2vw;
	color: #fff;
}

.uni-form-item .content input {
	width: 100%;
	font-size: 3.2vw;
	font-family: Roboto-Regular;
	border: none;
	outline: none;
	background-color: #F1F1F1;
}

.upload {
	width: 100%;
	display: flex;
	align-items: center;
}

.upload-before text {
	color: #575757;
	font-size: 3.2vw;
	font-family: Roboto-Regular;
}

.upload-done {
	justify-content: space-between;
}

.uploadIcon {
	width: 6.4vw;
	height: 6.4vw;
	margin-right: 2.13vw;
}

.upload-res {
	width: auto;
	max-height: 8.53vw;
}

.deleteIcon {
	height: 5.07vw;
	width: 5.07vw;
}


.bottom_btn .spendCount {
	width: 19.2vw;
	height: 9.07vw;
	background: #F1F1F1;
	border-radius: 1.07vw;
	display: flex;
	justify-content: center;
	align-items: center;
}

.bottom_btn .spendCount .icon {
	height: 6.4vw;
	width: 6.4vw;
	margin-right: 1.07vw;
}

.bottom_btn .submitBtn {
	width: 64.8vw;
	height: 9.07vw;
	background: #000000;
	box-shadow: 1.07vw 1.07vw 2.13vw .13vw rgba(0, 0, 0, 0.32);
	border-radius: 1.07vw;
	display: flex;
	justify-content: center;
	align-items: center;

}

.bottom_btn .submitBtn text {
	font-size: 3.73vw;
	font-weight: bold;
	background: linear-gradient(182deg, #2F54EB 0%, #FF26A8 100%);
	-webkit-background-clip: text;
	color: transparent;
	background-clip: text;
}
</style>
