<template>
	<Header></Header>
	<Loading :showLoading="showLoading" />
	<div>
		<!-- 页面内容 -->
		<div class="container">
      <div class="banner">
				<img src="../assets/images/banner.png" mode=""></img>
				<p>{{ appData.title }}</p>
				<p>{{ appData.subtitle }}</p>
      </div>


      <div class="uni-form-item uni-column">
        <div class="title">选择你喜欢的猫猫类型</div>
        <div class="templateCon" v-if="templateList.length > 0">
          <div v-for="(item, index) in templateList"
               :class="`template_item ${Number(item.id) === templateId ? 'templateActive' : ''}`"
               @click="selectTemplate(Number(item.id), item.image, item.prompt)" :key="index">
            <img :src="item.image" alt="" />
            <div :class="`isActiveIcon ${Number(item.id) === templateId ? 'active' : ''}`">
              <img src="../assets/icons/selectIcon.png" alt="" v-if="Number(item.id) === templateId">
            </div>
          </div>
        </div>
      </div>



			<div class="bottom_btn">
        <div class="spendCount">
          <img class="icon" src="../assets/icons/money.png" mode=""></img>
          <text>-8</text>
        </div>
				<button class="submitBtn" @click="formSubmit">
					<text>生成图片</text>
				</button>
			</div>


		</div>
	</div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { showToast } from 'vant';
import { useRouter } from 'vue-router'
import { getTemplate } from '../lib/getTemplate'
import { AI } from 'aonweb'

import 'vant/lib/index.css';
import Header from '../components/Header.vue';
import Loading from '../components/Loading.vue';

const router = useRouter()
const showLoading = ref(false);
const templateList = ref([]);
const templateId = ref(1);
const prompt = ref('');
const appData = process.env?.appData || {}

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
        "width": 1152,
        "height": 1152,
        "prompt": prompt.value,
        "output_format": "png",
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


async function getTemplateList() {
  try {
    const list = await getTemplate()
    templateList.value = list
    prompt.value = list[0].prompt
  } catch (error) {
    console.log(error)
  }
}

function selectTemplate(id, imageUrl, prompt_) {
  templateId.value = id
  prompt.value = prompt_
}

onMounted(() => {
  getTemplateList()
})
</script>

<style scoped>
.banner {
	width: 100%;
	height: 27.73vw;
	margin-top: 8.53vw;
	margin-bottom: 8.53vw;
	position: relative;
	padding: 4.27vw;
}

.banner img {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
}

.banner p {
	position: relative;
	z-index: 10;
	font-family: Roboto-Black;
	font-weight: 900;
	font-size: 6.4vw;
	color: #FFFFFF;
	text-align: left;
	font-style: normal;
	text-transform: none;
}

.banner p:last-child {
	width: 49.07vw;
	font-family: Roboto-Regular;
	font-weight: 400;
	font-size: 2.4vw;
	color: #FFFFFF;
	line-height: 4.27vw;
	text-align: left;
	font-style: normal;
	text-transform: none;
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
.error-text{
  width: 86.67vw;
  position: fixed;
  bottom: 21.6vw;

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

.templateCon {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  flex-wrap: wrap;
  height: auto;
  background: transparent;
}

.template_item {
  width: 24.8vw;
  height: 44vw;
  background: #F1F1F1;
  border-radius: 1.07vw;
  margin-bottom: 4.53vw;
  position: relative;
  overflow: hidden;
  box-sizing: border-box;

}

.template_item img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.templateActive {
  border: .27vw solid #000;

}

.isActiveIcon {
  position: absolute;
  bottom: 1.6vw;
  right: 1.6vw;
  width: 3.2vw;
  height: 3.2vw;
  background: #FFFFFF;
  border: .27vw solid #000000;
  border-radius: 50%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.isActiveIcon img{
  height: 2.13vw;
  width: 2.13vw;
}

.active{
  background: #EBCC2F;
}
</style>
