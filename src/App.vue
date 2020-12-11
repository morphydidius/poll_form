<template>
  <div class="poll">
    <div class="poll__form">
      <div class="poll__form__title">{{ question }}</div>
      <div class="poll__form__type">{{ type }}</div>
      <div
        v-for="(answer, index) in answers"
        :key="index"
        :class="[
          'poll__form__answer',
          {
            'disabled': isAnswerGiven
          }
        ]"
        @click="chooseAnswer(answer)">
          <span>{{ answer.text }}</span>
          <span
            v-if="isAnswerGiven && answer.count"
            class="poll__form__answer__count"
          > &#183; {{ answer.count }}</span>
          <transition name="fade">
            <div
              v-show="isAnswerGiven"
              class="poll__form__answer__scale"
              :style="{
                width: getWidth(answer)
              }"
            ></div>
          </transition>
          <div
            v-if="isAnswerGiven"
            :class="[
              'poll__form__answer__percent',
              {
                'checked': answer.checked
              }
            ]"
          >{{ getUserPercent(answer) }} %
          </div>
      </div>
      <div
        v-if="isAnswerGiven"
        class="poll__form__revote"
        @click="reset"
      >
        Переголосовать
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'App',
  components: {},
  data() {
    return {
      question: 'Кто Вы?',
      type: 'Публичный опрос',
      answers: [
        {
          text: 'Экстраверт',
          checked: false,
          count: 6
        },
        {
          text: 'Интроверт',
          checked: false,
          count: 4
        },
        {
          text: 'Не знаю',
          checked: false,
          count: 2
        }
      ],
      isAnswerGiven: false
    };
  },
  computed: {
    totalCount() {
      return this.answers.reduce((acc, answer) => acc += answer.count, 0);
    }
  },
  methods: {
    chooseAnswer(answer) {
      if (!this.isAnswerGiven) {
        this.isAnswerGiven = true;
        answer.count += 1;
        answer.checked = true;
      }
    },
    getUserPercent(answer) {
      return (answer.count * 100 / this.totalCount).toFixed(1);
    },
    getWidth(answer) {
      return `${this.getUserPercent(answer)}%`;
    },
    reset() {
      this.answers.forEach((answer) => {
        if (answer.checked) {
          answer.count -= 1;
        }
        answer.checked = false;
      });
      this.isAnswerGiven = false;
    }
  }
}
</script>
<style lang="scss">
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  padding: 0;
}
</style>

<style lang="scss" scoped>
.fade-enter-active {
  transition: transform .5s;
}

.fade-enter {
  transform: translateX(-100%);
}

.fade-leave {
  transform: none;
}

.poll {
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
  font: 16px/24px 'Roboto';

  &__form {
    width: 510px;
    min-height: 400px;
    border-radius: 8px;
    padding: 60px 20px 20px;
    color: #fff;
    background: linear-gradient(135deg, #2eb3b3 0%, #2552b3 100%);
    display: flex;
    flex-direction: column;
    justify-content: flex-start;

    &__title {
      font: bold 30px/36px 'Roboto';
      margin-bottom: 12px;
    }

    &__type {
      color: rgba(255,255,255,.84);
      margin-bottom: 60px;
    }

    &__answer {
      background-color: rgba(255,255,255,.1);
      text-align: left;
      padding: 6px 75px 8px 10px;
      font: 14px/18px 'Roboto';
      border-radius: 4px;
      transition: background-color .1s ease-in-out;
      position: relative;
      overflow: hidden;
      margin-bottom: 10px;
      cursor: pointer;

      &:hover {
        background-color: rgba(255,255,255,.2);
      }

      &.disabled {
        cursor: auto;

        &:hover {
          background-color: rgba(255,255,255,.1);
        }
      }

      &__count {
        color: rgba(255,255,255,.4);
      }

      &__scale {
        position: absolute;
        top: 0;
        left: 0;
        height: 100%;
        background-color: rgba(255,255,255,.3);
        // animation-name: slide;
        // animation-duration: 1s;
        // animation-fill-mode: forwards;
      }

      &__percent {
        position: absolute;
        width: 50px;
        top: 50%;
        right: 10px;
        transform: translateY(-50%);
        text-align: right;

        &.checked::after {
          content: '\2713';
          position: absolute;
          left: -10px;
          top: 50%;
          transform: translateY(-50%);
        }
      }
    }

    &__revote {
      margin-top: 20px;
      cursor: pointer;
    }
  }
}
</style>
