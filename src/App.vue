<template>
 <form @submit.prevent="text_change">
	<div class="container mb-5">
<nav class="p-3 d-flex justify-content-between align-items-center">
<div>
	<span class="google-text h3 google_g text-primary">G</span>
<span class="google-text h3 google_o text-danger">o</span>
<span class="google-text h3 google_goo text-warning">o</span>
<span class="google-text h3 google_gg text-primary">g</span>
<span class="google-text h3 google_l text-success">l</span>
<span class="google-text h3 google_e text-danger">e</span>

<span :class="dark ?'text-light':'night_color'" class="translate_text">Translate</span>
</div>
<div>
	<span class="cur_pointer" @click="dark_mode">
		
	<span v-if="dark" ><i class="far fa-moon"></i></span>
	<span v-else><i class="fas fa-sun text-primary"></i></span>

	</span>
</div>
</nav>


<main class="ms-2 container">
	<div class="row mt-4">
		<div class="col-12 col-md-12 mt-2 col-lg-6 col-sm-12">
			<ul class="langs">
				<li @click="left = 'uz'" :class="left == 'uz' ? 'active':''">
					<span :class="dark ?'text-light bg-dark':''">
					Uzbek
				</span>
				</li>
				<li @click="left = 'ru'" :class="left == 'ru' ? 'active':''">
					<span :class="dark ?'text-light bg-dark':''">
						Russian
					</span>
				</li>
				<li @click="left = 'en'" :class="left == 'en' ? 'active':''">
					<span :class="dark ?'text-light bg-dark':''">
						English
				</span>
				</li>
				<li>
					<select v-model="left" name="" :class="dark ?'text-light bg-dark':''" id="" class="form-control">
						<option v-for="(i,j) in lang" :key="j" :value="lang_item[j]">{{ i }}</option>
					</select>
				</li>

			</ul>
			<div class="text">
				<textarea  :maxlength="maxlength" v-model="text" name="" class="form-control" :class="dark ?'text-light bg-dark':''" id="" cols="30" rows="10">
				</textarea>
				<span @click="text =''" v-if="text" class="clear" title="Tozalash">
					<i :class="dark ?'text-light bg-dark':''" class="fas fa-xmark"></i>
				</span>
				<span>
					<i :class="dark ?'text-light bg-dark':''" class="far fa-clipboard" @click="paste" title="Qo'yish"></i>
				</span>
				<span  v-if="text" :class="text.length == maxlength?'text-danger fw-600':''" class="hisobla u">
					<span :class="dark ?'text-light bg-dark':''">
					{{ text.length  }} / {{ maxlength }}

</span>
				</span>
			</div>
		</div>
		<div class="col-12 col-md-12 mt-2 col-lg-6 col-sm-12">
			<ul class="langs">
				<li @click="right = 'uz'" :class="right == 'uz'?'active':''">
					<span :class="dark ?'text-light bg-dark':''">
					Uzbek
				</span>
				</li>
				<li @click="right = 'ru'" :class="right == 'ru'?'active':''">
					<span :class="dark ?'text-light bg-dark':''">
					Russian
				</span>
				</li>
				<li @click="right = 'en'" :class="right == 'en'?'active':''">
					<span :class="dark ?'text-light bg-dark':''">
							English
				</span>
				</li>
				<li>
					<select :class="dark ?'text-light bg-dark':''" v-model="right" name="" id="" class="form-control   d-block">
						<option v-for="(i,j) in lang" :key="j" :value="lang_item[j]">{{ i }}</option>
					</select>
				</li>

			</ul>
			<div class="text">
				<textarea :class="dark ?'text-light bg-dark':''" :readonly="true" name="" v-model="translate" class="form-control" id="" cols="30" rows="10"></textarea>
				<i @click="copy" v-if="translate" :class="dark ?'text-light bg-dark':''" class="far fa-clone"></i>
				<div v-if="load" class="loading d-flex align-items-center justify-content-center">
					<div class="spinner-border text-primary" role="status">
          <span class="visually-hidden">Loading...</span>
        </div>
				</div>

			</div>
		</div>
	</div>
	<button :disabled="!text?true:false" type="submit" class="btn btn-outline-primary w-75 m-auto d-block mt-3">
		<span v-if="!load">
		Tarjima qilish

</span>
<span v-else>
	<i class="fas fa-rotate"></i>
</span>
	</button>
</main>
 </div>
 </form>
</template>
<script setup>
import axios from 'axios';
import { ref } from 'vue';
let langs_backend = ["Afrikaans","af","Albanian","sq","Amharic","am","Arabic","ar","Armenian","hy","Azerbaijani","az","Basque","eu","Belarusian","be","Bengali","bn","Bosnian","bs","Bulgarian","bg","Catalan","ca","Cebuano","ceb","Chichewa","ny","Chinese (Simplified)","zh-CN","Chinese (Traditional)","zh-TW","Corsican","co","Croatian","hr","Czech","cs","Danish","da","Dutch","nl","English","en","Esperanto","eo","Estonian","et","Filipino","tl","Finnish","fi","French","fr","Frisian","fy","Galician","gl","Georgian","ka","German","de","Greek","el","Gujarati","gu","Haitian Creole","ht","Hausa","ha","Hawaiian","haw","Hebrew","iw","Hindi","hi","Hmong","hmn","Hungarian","hu","Icelandic","is","Igbo","ig","Indonesian","id","Irish","ga","Italian","it","Japanese","ja","Javanese","jw","Kannada","kn","Kazakh","kk","Khmer","km","Kinyarwanda","rw","Korean","ko","Kurdish (Kurmanji)","ku","Kyrgyz","ky","Lao","lo","Latin","la","Latvian","lv","Lithuanian","lt","Luxembourgish","lb","Macedonian","mk","Malagasy","mg","Malay","ms","Malayalam","ml","Maltese","mt","Maori","mi","Marathi","mr","Mongolian","mn","Myanmar (Burmese)","my","Nepali","ne","Norwegian","no","Odia (Oriya)","or","Pashto","ps","Persian","fa","Polish","pl","Portuguese","pt","Punjabi","pa","Romanian","ro","Russian","ru","Samoan","sm","Scots Gaelic","gd","Serbian","sr","Sesotho","st","Shona","sn","Sindhi","sd","Sinhala","si","Slovak","sk","Slovenian","sl","Somali","so","Spanish","es","Sundanese","su","Swahili","sw","Swedish","sv","Tajik","tg","Tamil","ta","Tatar","tt","Telugu","te","Thai","th","Turkish","tr","Turkmen","tk","Ukrainian","uk","Urdu","ur","Uyghur","ug","Uzbek","uz","Vietnamese","vi","Welsh","cy","Xhosa","xh","Yiddish","yi","Yoruba","yo","Zulu","zu","Hebrew","he","Chinese (Simplified)","zh"]
let lang_item = langs_backend.filter((i,j)=>{return j % 2});
let lang = langs_backend.filter((i,j)=>{return j % 2 == 0});



let left = ref("uz");
let right = ref("ru");

let text = ref("");
let translate = ref("");
let load = ref(false);
let maxlength = ref(500);
let dark = ref(false);


let text_change = async ()=>{
load.value = true;
// console.log(left.value, right.value);
  const url = 'https://text-translator2.p.rapidapi.com/translate';
const options = {
	method: 'POST',
	headers: {
		'content-type': 'application/x-www-form-urlencoded',
		'X-RapidAPI-Key': '0c0a2b9cecmsh03918711380315bp1921d6jsn69a311209e72',
		'X-RapidAPI-Host': 'text-translator2.p.rapidapi.com'
	},
	body: new URLSearchParams({
		source_language: left.value,
		target_language: right.value,
		text: text.value
	})
};

try {
	const response = await fetch(url, options);
	const result = await response.text();
  // result = 
//   console.log(JSON.parse(result).data);
  translate.value = JSON.parse(result).data.translatedText;
} catch (error) {
	alert(error);
	load.value = false;

  
}



	load.value = false;
	

}
let dark_mode = ()=>{
dark.value = !dark.value;
(dark.value)?document.body.classList.value="bg-dark text-light":document.body.classList.value="";

}
let paste = async ()=>{
	try {
    const clipboardText = await navigator.clipboard.readText();
    text.value += clipboardText;
  } catch (error) {
   alert('Buferga ruxsat berilmagan:', error);
  }
}
let copy = async ()=>{
	try {
    await navigator.clipboard.writeText(translate.value);
  } catch (error) {
    alert('Nusxalashga ruxsat yo`q :', error);
  }
};


</script>
<style>

nav{
	width: 100%;
	height: 60px;
	/* background-color: red; */
	border-bottom: 1px solid rgb(231, 227, 227);
}
.translate_text{
	font-size: 30px;
	font-size: 22px;
	display:inline-block;
	margin-left: 10px;
}
.night_color{
	color: #5f6368;
}
.langs{
	display: flex;
	max-width: 500px;
	justify-content: space-around;
	font-weight: 600;
	color: #5f6368;
	font-size: 18px;
	align-items: center;
}
.langs li{
	list-style: none;
	border-bottom: 2px solid transparent;
	cursor: pointer;

}
.active{
	color: #0d6efd;
	border-bottom: 2px solid #0d6efd!important;;
}
textarea{
	resize: none!important;
}
.text{
	position: relative;
}
.clear{
	position: absolute;
	top:10px;
	font-size: 17px;
	color: #5f6368;
	cursor: pointer;
	right: 20px;
	transition: 1s ease color;
}
/* .clear,.fa-clone:hover{
color: black;

} */
.fa-clone,.fa-clipboard{
	position: absolute;
	bottom:10px;
	font-size: 17px;
	color: #5f6368;
	cursor: pointer;
	right: 20px;
	transition: 1s ease color;
}
.hisobla{
	position: absolute;
	bottom: 10px;
	right: 70px;
	color: black;

}
.fw-600{
	font-weight: 600;
}
.loading{
	width: 100%;
	height: 100%;
	position: absolute;
	top: 0;
	left: 0;
	background: transparent;
	backdrop-filter: blur(2px);
}
.cur_pointer{
	cursor: pointer;
}
</style>