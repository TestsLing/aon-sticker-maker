<template>
	<Header></Header>
	<Loading :showLoading="showLoading" />
	<div>
		<!-- 页面内容 -->
		<div class="container">
			<img class="banner" src="../assets/images/banner.png" mode=""></img>


			<div class="uni-form-item uni-column">
				<div class="title">选择你想生成的猫贴纸</div>
				<div class="radio-wrap">

          <div>
            <input type="radio" id="lh"   v-model="catType" name="cat" value="dragon-li" />
            <label class="margin-left-5" for="lh">狸花猫</label>
          </div>
          <div>
            <input type="radio" id="Ginger"  v-model="catType" name="cat" value="Ginger cat" />
            <label class="margin-left-5" for="Ginger">橘猫</label>
          </div>
          <div>
            <input type="radio" id="Cow"  v-model="catType" name="cat" value="Cow Cat" />
            <label class="margin-left-5" for="Cow">奶牛猫</label>
          </div>

          <div>
            <input type="radio" id="Three"  v-model="catType" name="cat" value="Three Flower Cat" />
            <label class="margin-left-5" for="Three">三花猫</label>
          </div>


          <div>
            <input type="radio" id="Ragdoll"  v-model="catType" name="cat" value="Ragdoll" />
            <label class="margin-left-5" for="Ragdoll">布偶猫</label>
          </div>

          <div>
            <input type="radio" id="Persian"  v-model="catType" name="cat" value="Persian cat" />
            <label class="margin-left-5" for="Persian">波斯猫</label>
          </div>

          <div>
            <input type="radio" id="short"  v-model="catType" name="cat" value="English short cat" />
            <label class="margin-left-5" for="short">英短猫</label>
          </div>

          <div>
            <input type="radio" id="American"  v-model="catType" name="cat" value="American Short Cat" />
            <label class="margin-left-5" for="American">美短猫</label>
          </div>

          <div>
            <input type="radio" id="Maine"  v-model="catType" name="cat" value="Maine cat" />
            <label class="margin-left-5" for="Maine">缅因猫</label>
          </div>

          <div>
            <input type="radio" id="Siamese"  v-model="catType" name="cat" value="Siamese cat" />
            <label class="margin-left-5" for="Siamese">暹罗猫</label>
          </div>
            <div>
            <input type="radio" id="Hairless"  v-model="catType" name="cat" value="Hairless cat" />
            <label class="margin-left-5" for="Hairless">无毛猫</label>
          </div>

          <div>
            <input type="radio" id="Garfield"  v-model="catType" name="cat" value="Garfield cat" />
            <label class="margin-left-5" for="Garfield">加菲猫</label>
          </div>

				</div>

			</div>

			<div class="bottom_btn">

				<button class="submitBtn" @click="formSubmit">
					<text>生成图片</text>
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
const catType  = ref("dragon-li")
function goToComplete(url) {
	const query = { url: url }
	router.push({
		path: '/created',
		query
	})
}

const formSubmit = async () => {

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
        "steps": 17,
        "width": 300,
        "height": 300,
        "prompt": "a cute cat,its breed is " + catType.value ,
        "output_format": "jpg",
        "output_quality": 100,
        "negative_prompt": "",
        "number_of_images": 1
			}
		}
		console.log("formSubmit data", data)
		let response = await aonet.prediction("/predictions/ai/sticker-maker", data)
		console.log("res", response)
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
			showToast('AI 处理失败')
		}
	} catch (error) {
		showLoading.value = false
		showToast('AI 处理失败')
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
.flex{
  display: flex;
}

.radio-wrap{
  display: flex;
  flex-wrap: wrap;
  gap: 10px 10px;
}

.margin-left-5{
  margin-left: 5px;
}
</style>
