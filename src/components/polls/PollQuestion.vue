<template>
  <li class="question__container">
    <h3>{{ question.text }}</h3>
    <IconButton v-if="isPreview" @click="removeQuestion" name="Remove Question" />
    <form>
      <textarea v-if="question.type === 'open'" />
      <div class="question__choices" v-if="question.type !== 'open'">
        <div v-if="question.options.isYesNo">
          <div class="question__yesno">
            <input id="yes" type="radio" :value="true" v-model="value[qIndex].answer" />
            <label for="yes">Yes</label>
            <input id="no" type="radio" :value="false" v-model="value[qIndex].answer" />
            <label for="no">No</label>
          </div>
        </div>
        <div
          class="question__choice"
          v-for="(choice, index) in question.options.choices"
          :key="index"
        >
          <input
            v-if="question.options.oneAnswerOnly"
            :id="choice"
            type="radio"
            :value="choice"
            v-model="value.answer"
          />
          <input
            v-else
            :id="choice"
            type="checkbox"
            :value="choice"
            v-model="value[qIndex].answer"
          />
          <label :for="choice">{{ choice }}</label>
        </div>
        <div v-if="question.options.customAnswer" class="question__choice">
          <input id="custom-answer" type="checkbox" :checked="value[qIndex].customAnswer" />
          <input type="text" v-model="value[qIndex].customAnswer" />
        </div>
        <textarea v-if="question.options.withText" v-model="value[qIndex].text" />
      </div>
      <ul class="question__scale" v-if="question.type === 'scale'">
        <li>{{ question.options.startValue }}</li>
        <input
          type="radio"
          :value="n"
          v-model="value[qIndex].answer"
          v-for="n in parseInt(question.options.scaleSteps)"
          :key="n"
        />
        <li>{{ question.options.endValue }}</li>
      </ul>
    </form>
  </li>
</template>

<script>
import IconButton from '../basic/Buttons/IconButton.vue';

export default {
  components: {
    IconButton,
  },
  props: {
    question: Object,
    isPreview: Boolean,
    qIndex: Number,
    value: Array, // value Prop is generate by v-model
  },
  watch: {
    value() {
      this.$emit('input', this.value);
    },
  },
  methods: {
    removeQuestion() {
      this.$emit('removeQuestion', this.question.id);
    },
  },
};
</script>

<style lang="scss" scoped>
.question {
  &__container {
    background-color: $primary-light;
    margin: 10px;
    padding: 20px;
  }
  h3 {
    margin-top: 0;
  }
  textarea {
    width: 90%;
    height: 100px;
    border-radius: 5px;
    resize: none;
  }
  &__choices {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
  }
  &__choice {
    margin-right: 10px;
    margin-bottom: 10px;
    display: flex;
    align-items: center;
    white-space: nowrap;
  }
  &__scale {
    display: flex;
    justify-content: center;
    list-style-type: none;
  }
  &__container {
    transition: all 0.3s;
    &.v-enter,
    &.v-leave-to {
      transform: translateX(-120%);
    }
    &.v-enter-to,
    &.v-leave {
      transform: translateX(0);
    }
    &.v-enter-active,
    &.v-leave-active,
    &.v-move {
      transition: all 0.5s ease-out;
    }
  }
}
</style>
