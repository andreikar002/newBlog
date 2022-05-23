<template>
  <div class="flex flex-col mt-3 publication">
    <template v-if="!isChanging">
      <div class="flex-col gap-1">
        <h2 class="text-2xl">{{ publication.header }}</h2>
        <p>{{ publication.text }}</p>
        <p class="text-xl">{{ publication.author }}</p>
        <template v-if="isUser">
          <div class="flex flex-row gap-2 mt-1">
            <button @click="$emit('deletePublication', idx)">Delete</button>
            <button @click="isChanging = !isChanging">Change</button>
          </div>
        </template>
      </div>
    </template>

    <template v-if="isChanging">
      <div class="flex flex-col">
        <div class="flex flex-row gap-1">
          <p>Name:</p>
          <input v-model="changedPublication.header" class="input-name" />
        </div>
        <div class="flex flex-row gap-1">
          <p>Text:</p>
          <textarea
            v-model="changedPublication.text"
            class="textarea-change"
          ></textarea>
        </div>
        <div class="flex flex-row gap-1">
          <p>Author:</p>
          <input v-model="changedPublication.author" />
        </div>
        <div class="flex flex-row gap-1">
          <button @click="isChanging = false">Cancel</button>

          <button
            @click="
              $emit('changePublication', changedPublication, idx);
              isChanging = !isChanging;
            "
          >
            Change
          </button>
        </div>
      </div>
    </template>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  props: {
    publication: {
      type: Object,
      required: false,
    },
    idx: {
      required: false,
      type: Number,
    },
    isUser: {
      type: Boolean,
      required: true,
    },
  },
  data() {
    return {
      isChanging: false,
      changedPublication: {
        oldPublication: this.publication,
        header: this.publication.header,
        text: this.publication.text,
        author: this.publication.author,
      },
    };
  },
};
</script>