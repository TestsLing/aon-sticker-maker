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
          <van-cell-group>
            <van-radio-group v-model="catType" >
            <van-cell title="狸花猫" clickable @click="catType = 'dragon-li'">
              <template #right-icon>
                <van-radio name="dragon-li" />
              </template>
            </van-cell>


              <van-cell title="橘猫" clickable @click="catType = 'ginger cat'">
                <template #right-icon>
                  <van-radio name="ginger cat" />
                </template>
              </van-cell>

             <van-cell title="奶牛猫" clickable @click="catType = 'cow cat'">
                <template #right-icon>
                  <van-radio name="cow cat" />
                </template>
              </van-cell>

              <van-cell title="三花猫" clickable @click="catType = 'three flower cat'">
                <template #right-icon>
                  <van-radio name="three flower cat" />
                </template>
              </van-cell>

              <van-cell title="布偶猫" clickable @click="catType = 'ragdoll'">
                <template #right-icon>
                  <van-radio name="ragdoll" />
                </template>
              </van-cell>

              <van-cell title="波斯猫" clickable @click="catType = 'persian'">
                <template #right-icon>
                  <van-radio name="persian" />
                </template>
              </van-cell>

              <van-cell title="英短猫" clickable @click="catType = 'english short cat'">
                <template #right-icon>
                  <van-radio name="english short cat" />
                </template>
              </van-cell>

              <van-cell title="美短猫" clickable @click="catType = 'american short cat'">
                <template #right-icon>
                  <van-radio name="american short cat" />
                </template>
              </van-cell>

              <van-cell title="缅因猫" clickable @click="catType = 'maine cat'">
                <template #right-icon>
                  <van-radio name="maine cat" />
                </template>
              </van-cell>

              <van-cell title="暹罗猫" clickable @click="catType = 'siamese cat'">
                <template #right-icon>
                  <van-radio name="siamese cat" />
                </template>
              </van-cell>

              <van-cell title="无毛猫" clickable @click="catType = 'hairless cat'">
                <template #right-icon>
                  <van-radio name="hairless cat" />
                </template>
              </van-cell>

              <van-cell title="加菲猫" clickable @click="catType = 'garfield cat'">
                <template #right-icon>
                  <van-radio name="garfield cat" />
                </template>
              </van-cell>


            </van-radio-group>
          </van-cell-group>
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
  console.log(catType.value);return
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
        "prompt": "a cute cat ,its breed is " + catType.value ,
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


.radio-wrap{
  height: 400px;
  overflow: scroll;

}


</style>
