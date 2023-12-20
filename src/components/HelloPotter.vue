<template>
  <div class="wrapper">
    <img class="main-img" :src="mainImg" alt="unsplash img" />
    <div class="bottom-text" :class="{ start: start }">
      <div class="text-area">
        <p v-if="round > 1">role:{{role.name}} health:{{ role.health }} attack:{{ role.attack }}</p>
        <p>{{ story }}</p>
        <ul>
          <li v-for="option in options" :key="option.id">
            <label :for="option.content"
              ><input
                v-model="selectedOption"
                :id="option.index"
                :value="option.content"
                type="radio"
              />{{ option.content }}</label
            >
          </li>
        </ul>
      </div>
      <button class="start-btn" @click="startGame()">Click to Start</button>
      <button
        v-if="round && round < 10  && !story.includes('正在思考中')"
        class="round-btn"
        @click="nextRound"
      >
        Next Round
      </button>
      <button v-if="isEnd" class="restart-btn" @click="restartGame()">
        我要重來
      </button>
    </div>
  </div>
</template>
<script setup>
import { ref, computed, watch } from "vue";
import axios from "axios";
import charactersData from "../assets/characters.json";

const characters = charactersData;
const start = ref(false);
const isEnd = ref(false);
const mainImg = ref("");
const rawOption = ref([]);
let options = ref([]);
let selectedOption = ref([]);
let story = ref("");
let round = ref(0);
let theEvent = 2;

const role = ref({
  name: "",
  skill: "",
  attack: 0,
  health: 0,
  team: "",
});

mainImg.value =
  "https://images.unsplash.com/photo-1518709268805-4e9042af9f23?crop=entropy&cs=srgb&fm=jpg&ixid=M3w1MzYzMDB8MHwxfHNlYXJjaHwxfHxtYWdpYyUyMHdvcmxkfGVufDB8fHx8MTcwMTgxNzg5OHww&ixlib=rb-4.0.3&q=85";

const events = [
  "",
  "",
  "在禁忌森林裡面遭遇半人馬群包圍",
  "",
  "遭遇了一場激戰後，前往海格的小屋休息，這時候海格拿出一杯液體",
  "",
  "一年一度魔法世界的魁地奇比賽到了，賽場上的你看了一眼觀眾群後施展魔法",
  "",
  "今早醒來發現自己魔杖被壓斷了，前往Ollivanders魔杖店，挑選適合的魔杖",
  "",
  "在斜角巷撿到一顆水晶球，水晶球裡看到一個人影",
  "",
  "遇到一位強大的對手，還沒反應過來就被施打索命咒",
  "",
  "在前往霍格華茲的列車上撿到隱形斗篷，披上它後聽到了關於魔法部的重大消息",
  "",
  "貓頭鷹送來魔法部寄出違反巫師法的信，被帶往魔法部巫師法庭",
  "",
  "在弗倫德和喬治的愚人產品商店開幕，好奇前往購買商品",
  "",
  "穿巷口鑰，瞬間被傳送到一個陌生的地方",
  "",
  "遠方看到一群模糊的人影，發現是催狂魔群",
  "",
  "突然一輛騎士公車停在你眼前，乘務員說可以將陷入困境的你帶去任何地方",
  "",
  "在霍格華茲的教授家裡，發現了一顆魔法樹",
  "",
  "走在霍格華茲走廊，有求必應室突然出現在你面前",
  "",
  "憑著直覺來到狄恩森林，在冰冷湖面下發現葛來芬多寶劍",
  "",
  "在有求必應室看到雷文克勞皇冠，突然衝出一位鬼魂驚叫走開",
  "",
  "在月圓之夜喝下幸運藥水，出門卻遇到狼人在對滿月嚎叫",
  "",
  "想體驗麻瓜生活找到停在院子裡的飛天魔法汽車並開去麻瓜世界上空",
];

const getPrompt = () => {
  return (
    `我在玩哈利波特冒險遊戲，我的角色數據是` +
    `{name:"${role.value.name}",description:"${role.value.description}", attack:"${role.value.attack}", health:"${role.value.health}"}` +
    `，發生了一個事件「${
      events[getRandomEvenNumber(2, 36)]
    }」，請提出三個對於事件產生的反應，以及對角色數據的影響，只回傳JSON格式，` +
    `格式為 [{"event"://10到30字的事件反應,"result"://選擇這個事件發生了什麼事情以及為什麼會對攻擊力和生命值造成影響, "attack"://對攻擊力的影響正3到負3之間,"health"://對生命值的影響正20到負20之間 },...]` +
    `，除了JSON不要回答其他內容，事件敘述請用中文回答`
  );
};

async function getStory() {
  const baseUrl = "https://api.openai.com/v1/chat/completions";
  const MODEL = "gpt-3.5-turbo";
  const token = window.location.search.replace("?token=", "")
  options.value = [];
  const response = await axios.post(
    baseUrl,
    {
      messages: [
        {
          role: "user",
          content: getPrompt(),
        },
      ],
      model: MODEL,
      max_tokens: 720,
      temperature: 0.7,
      top_p: 1,
      n: 1,
    },
    {
      headers: {
        "Content-Type": "application/json",
        Authorization: `Bearer ${token}`,
      },
    }
  );

  story.value = events[theEvent];
  const array = JSON.parse(response?.data?.choices[0]?.message.content);
  rawOption.value = array;
  options.value = array.map(({ event }, index) => ({ content: event, index }));
}

async function getEnd() {
  const baseUrl = "https://api.openai.com/v1/chat/completions";
  const MODEL = "gpt-3.5-turbo";
  const token = "";
  const response = await axios.post(
    baseUrl,
    {
      messages: [
        {
          role: "user",
          content: `這是一個哈利波特的世界，請創造屬於${role.value.name}的故事結局，不要超過5行字`,
        },
      ],
      model: MODEL,
      max_tokens: 720,
      temperature: 0.7,
      top_p: 1,
      n: 1,
    },
    {
      headers: {
        "Content-Type": "application/json",
        Authorization: `Bearer ${token}`,
      },
    }
  );
  story.value = response?.data?.choices[0]?.message.content;
}

const teams = computed(() => {
  return [...new Set(characters.map((character) => character.team))];
});
selectedOption.value = options.value.filter(
  (option) => option.isSelected === true
);

const teamHarry = computed(() => {
  return characters.filter((character) => character.team === "成為鳳凰會");
});

const teamVoldemort = computed(() => {
  return characters.filter((character) => character.team === "成為食死徒");
});

const nextRound = () => {
  round.value = round.value + 1;
};

function startGame() {
  start.value = true;
  round.value = 0;
  story.value = "請選擇成為食死徒或是鳳凰會";
  setOptions(teams.value);
}
function restartGame() {
  location.reload();
}

function setOptions(arr) {
  let temp = [];
  arr.forEach((item, index) => {
    temp.push({
      id: index,
      content: item,
    });
  });
  options.value = temp;
}

function getRandomElements(arr, count) {
  const shuffled = arr.slice().sort(() => Math.random() - 0.5);
  return shuffled.slice(0, count);
}

const getRandomEvenNumber = (min, max) => {
  let randomNumber = Math.floor(Math.random() * (max - min + 1)) + min;
  if (randomNumber % 2 !== 0) {
    randomNumber += 1;
  }
  theEvent = randomNumber;
  return randomNumber;
};

const getPictures = async ({ keyword }) => {
  const baseUrl = "https://api.unsplash.com/search/photos";
  const response = await axios.get(
    `${baseUrl}?query=${keyword}&client_id=lpzI5L4f2klqot43me98raxpI6FVeWHCbkSQuo9LM9Q`
  );
  mainImg.value = response.data.results[getRandomNumber(0, 9)].urls.regular;
};

const getRandomNumber = (min, max) => {
  return Math.floor(Math.random() * (max - min + 1)) + min;
};
window.getRandomNumber = getRandomNumber;

watch(
  () => selectedOption,
  ({ value }) => {
    if (!value) return;
    if (round.value === 0) {
      if (value === "成為食死徒") {
        let arrV = teamVoldemort.value.map((character) => character.name);
        arrV = getRandomElements(arrV, 3);
        setOptions(arrV);
      } else {
        let arrH = teamHarry.value.map((character) => character.name);
        arrH = getRandomElements(arrH, 3);
        setOptions(arrH);
      }
      round.value++;
    }

    if (round.value === 1) {
      story.value = "請選擇你的角色";
      const theCharacter = charactersData.find(({ name }) => name === value);

      if (theCharacter) {
        story.value = theCharacter?.description ?? "";
        role.value = {
          name: theCharacter?.name,
          description: theCharacter?.description,
          health: theCharacter?.health,
          attack: theCharacter?.min_attack,
          team: theCharacter?.team,
        };
      }
    }
  },
  { deep: true }
);

watch(
  () => round,
  ({ value }) => {
    if (value > 1) {
      if (value % 2) {
        getPictures({ keyword: "hogwarts express" });
        const answer = rawOption.value.find(
          ({ event }) => event === selectedOption.value
        );
        story.value = answer.result;
        role.value.health += +answer.health;
        role.value.attack += +answer.attack;
        options.value = [];
      } else {
        if (role.value.health <= 0) {
          story.value = "你已經死亡";
          options.value = [];
          round.value = 0;
          isEnd.value = true;
          return;
        } else if (role.value.attack <= 2) {
          story.value = "魔法能力過於低弱，回家吧";
          options.value = [];
          round.value = 0;
          isEnd.value = true;
          return;
        } else {
          getPictures({ keyword: "harry potter studio london" });
          story.value = `chatGPT正在思考中`;
          getStory();
        }
      }
    }
    if (value === 10) {
      getEnd();
      isEnd.value = true;
      options.value = []
    }
  },
  { deep: true }
);
</script>
<style>
  ul {
list-style-type: none;
padding: 0;
margin: 0;
width: 100%;
}
li {
  display: inline-block;
  width: 100%;
}
li:not(:last-child) {
  margin-bottom: 8px;
}

.wrapper {
  width: 800px;
  height: 600px;
  display: flex;
  gap: 12px;
  flex-flow: column nowrap;
  justify-content: center;
  align-items: center;
}
.bottom-text {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: space-evenly;
}
.text-area {
  display: none;
}
.start-btn,
.round-btn,
.restart-btn {
  background-color: #000;
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  font-size: 16px;
  border-radius: 12px;
  cursor: pointer;
}

.start-btn {
  background-color: #000;
  border: none;
  color: white;
  padding: 15px 32px;
  text-align: center;
  font-size: 16px;
  border-radius: 12px;
  cursor: pointer;
}
.start {
  display: flex;
}
.start .start-btn {
  display: none;
}
.start .text-area {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 12px;
}
.main-img {
  display: inline-block;
  width: 600px;
  height: 450px;
  object-fit: cover;
}
</style>