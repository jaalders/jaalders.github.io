---
layout: post
title: Gripes go up, s#!t rolls down!
date: 2024-01-05
categories: blog
excerpt: <img src="/images/fulls/spr.jpg" class="image fit"> Along the way since I have been learning Vue I have been dealing with the concepts of emits and props. These are definitely new to me and it's something that I have been trying to actively apply in my code to allow me to have two-way communication between components. My brain works in weird ways, but the correlation between how Vue operates and how this scene illistrates flow of hierarchy. At least you can feel good knowing this was wrote by a human with a very, very weird thought process.
---

<img src="/images/thumbs/spr.jpg" class="image fit" title="Image taken from Saving Private Ryan - DreamWorks / Paramount Pictures - 1998"> 


> Gripes go up, shit rolls down, or in this case, emits go up and props roll down.

You ever watch Saving Private Ryan? Remember that scene when the company is complaining to Tom Hanks and they ask him why he never gripes, and he says the following:

Along the way since I have been learning Vue I have been dealing with the concepts of emits and props. These are definitely new to me and it's something that I have been trying to actively apply in my code to allow me to have two-way communication between components. My brain works in weird ways, but the correlation between how Vue operates and how this scene illistrates flow of hierarchy. At least you can feel good knowing this was wrote by a human with a very, very weird thought process.

This example I am providing assumes you're on Vue3 using composition API. It's fairly simple all things considered, and I am sure I could optimize thing further (perhaps I will revisit this post again in the future with an example 2.0) but for now this will show what is possible. I will also say, this code will work as is, but ideally you will have Vue DevTools installed or some other add-on that will allow you to see the props update as you click through to better illustrate this example.

Lets get into the examples!

## Props: Passing Data Downwards

Props allow you to pass data from a parent component to a child component. In our example, the ParentComponent has a boolean `toggle` ref() variable, which we want to pass down to the ChildComponent. Let's break down the moving parts:

```
<template>
  <div>
    <button @click="openWindow">Click Button</button>
    <ChildComponent :toggle="toggle" @closeWindow="closeWindow" />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import ChildComponent from './ChildComponent.vue';

const toggle = ref(false);

const openWindow = () => {
  toggle.value = true;
};

const closeWindow = (newValue: boolean) => {
  toggle.value = newValue;
};
</script>
```

Here, `toggle` is a boolean variable in the ParentComponent, and it is passed down to ChildComponent using the `:toggle` syntax. This establishes a one-way data flow from parent to child.

## Emits: Communicating Changes Upwards

Emits provide a way for a child component to trigger events that the parent component can listen to. In our case, the ChildComponent emits a `closeWindow` event when the window is closed back to the ParentComponent.

```
<template>
  <div v-if="isOpen" class="dialog-overlay">
    <div class="dialog-content">
      <p>Some Content Here</p>
      <button @click="closeWindow">Close Window</button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue';

const props = defineProps(['toggle']);
const emits = defineEmits(['closeWindow']);

const isOpen = ref(false);

const closeWindow = () => {
  isOpen.value = false;
  emits('closeWindow', false);
};

watch(() => props.toggle, (newValue: boolean) => {
  isOpen.value = newValue;
});

</script>

<style scoped>
.dialog-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.dialog-content {
  background: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
}
</style>
```

The ChildComponent defines a `toggle` prop using defineProps, and it uses the watch function to reactively update the `isOpen` ref() value whenever the `toggle` prop changes.

In the ParentComponent, the `closeWindow` method is defined to listen for the `closeWindow` by prefixing it with the `@` symbol. When the event is received, it updates the toggle value accordingly with the parameter passed from the ChildComponent.

In summary, this is just a very quick example about how emits and props work. I would love to hear your comments about the matter. If you are reading this and you have some comments good or bad, by all means reach out! I am always interested in getting new perspectives or further knowledge about how to better my code.

Remember the chain of command! The grips go up, not down. Always up.

See you in the next one!
