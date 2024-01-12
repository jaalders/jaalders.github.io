---
layout: post
title: Samwise the coder. Defender of Frodo and defender of DRY!
date: 2024-01-05
categories: blog
excerpt: <img src="/images/fulls/frodo.jpg" class="image fit"> Alright, in all seriousness I wanted to actually continue the Lord of the Rings theme throughout the blog post, but I figured it would diverge from the meat and "po-tay-toes" of the post. (last one I swear)
---

<img src="/images/thumbs/frodo.jpg" class="image fit" title="Image taken from Lord of the Rings - New Line Cinema - 2001"> 


>  Samwise the Coder: saving the Ring and preventing redundant code since the Third Age!"

Alright, in all seriousness I wanted to actually continue the Lord of the Rings theme throughout the blog post, but I figured it would diverge from the meat and "po-tay-toes" of the post. (last one I swear)

Essentially in Vue, you will learn about the concepts of reusable components. A simple Don't Repeat Yourself philosophy for sure. They allow you to write code once, and then place that whole component within other areas in your app with a single custom formed tag. Usually this will contain three sections. template, script, and style.

What if we're looking to just add in some additional logic that we can just reuse regardless of needing a template or not? Well in Vue 2 mixins were a thing sometimes used to do this, but in Vue 3 with composition API mixins have been more so phased out in favour for composables. 

If you go to the Vue documentation website, you'll see a very simple concept that leverages JavaScript to showcase how composables can be used, but of course since Vue promotes TypeScript, we'll build our examples in TypeScript!

Here's a very simple component App.vue file we'll use for the example:

App.vue

```
<script setup lang="ts">
import ButtonOne from './ButtonOne.vue';
import ButtonTwo from './ButtonTwo.vue';
import { clickListener } from './clickCapture.ts';

const listClicks = clickListener();

</script>

<template>
  <div class="container" style="">
    <ButtonOne class="button" />
    <ButtonTwo class="button"/>
    <button id="ButtonThree" class="button">Button Three</button>
    <p>Click anywhere to begin!</p>
  </div>
  <div>
    <p v-if="listClicks.length > 0">{{ listClicks }}</p>
  </div>
</template>

<style scoped>
  .container {
    width: 600px; 
    background-color: #ccc;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .button {
    margin: 10px;
  }
</style>
```

Our boring button component (you'll have to copy and paste it to make the second button.)

```
<script setup></script>

<template>
  <div>
    <button id="buttonOne">Button One</button>
  </div>
</template>
```

And then our reusable composables clickCapture.ts

```
import { Ref, ref, onMounted, onUnmounted } from 'vue';

export function clickListener(): Ref<string[]> {
  const clickHistory = ref<string[]>([]);

  function clickCapture(event: MouseEvent): void {
    clickHistory.value.push(
      event.target instanceof Element
        ? event.target.id || event.target.tagName
        : ''
    );
  }

  onMounted(() => {
    window.addEventListener('click', clickCapture);
  });

  onUnmounted(() => {
    window.removeEventListener('click', clickCapture);
  });

  return clickHistory;
}
```

Once you get these files up and running you should see something like this:

<img src="/images/fulls/starter.png" class="image fit">

To test our code we can begin by clicking on any parts of the example. You'll see the element clicks get captured in a `<p>` tag below.

So in our clickCapture.ts file we have the following moving parts: 

A `Ref` is a reactive generic type in Vue.js. Here, we use `ref<string[]>([])` to create a reactive reference to an empty array that will store our click history.

The `clickCapture` function is called on every click event, identifying the clicked element and adding it to our click history.

we use `onMounted` and `onUnmounted` hooks. On component mount, we add an event listener to the window to capture clicks. On component unmount, we ensure the event listener is removed to avoid any potential chances of a crash due to memory leak.

I am sure you have a couple of questions. Like for instance. is there a reason why `const listClicks = clickListener();` isn't within the `onBeforeMount`?

`const listClicks = clickListener();` is placed outside of the `onBeforeMount` hook to ensure that `listClicks` is created once when the component is initialized and is available throughout the component's lifecycle. If we moved it inside the `onBeforeMount` hook, it would mean that `listClicks` is created every time before the component is mounted, and it will not persist between re-renders or updates.

Or perhaps you're wondering is there a reason why `const listClicks = clickListener();` doesn't have to be within a `ref()`?

`const listClicks = clickListener();` doesn't need to be wrapped in a `ref()`. The `clickListener` function itself already returns a `Ref<string[]>`. In our `clickListener` function, we use `ref()` to create a reactive reference (clickHistory) and return it. Therefore, when we call `clickListener()`, we are directly getting a reference to the reactive data (clickHistory) without the need for an additional `ref()` wrapper.

I understand I've been doing a slew of explaining and certainly even more typing. So, what I have done is create a vue sandbox with the code involved above so you can test this code right away and see if piques your interest further!

[Vue clickCapture Sandbox](https://play.vuejs.org/#eNqtVVFvmzAQ/is39pBMSkmnSnugaaa1irRNWzt13VPpA4WDuAUb2YYkSvPfd7aBBBR1m7Q8tPjuu/P3fdjH1vtUln5doRd4MxVLVmpQqKsS8ohnF6GnVejNQ86KUkgNl5XWgt9whFSKAkb+tIuYJqPzAfJuJQZIigyQW4hzFj9/Y0ojRwm7rsLGr6JSVxJ9rUxNyGPBlYac0FcmreCiXz9+Z2GzqVND3GmhsSjzSCOtAGYJq6kmUor0UTsdMaoLPVB6kyPFrGKg32yvt8U/2giBp32MUTrEdBAXAJZQsoEvJSJ1GZbMXRpsfjZ14bZNObeSIeKb1RIlghbwiBnjb2bT0kmbkrZOZFcH9QlLaZe9a36OPNNLmMMpbbrdHhq62w3bzaYHBtLSGgUqFiUmFuh3NsLWbbpiiV4G8OH0tFyfg4s9RvFzJkXFk5NY5EIG8DaOY3pdJpkwRTtsAkhzXDexKGcZP2G0uQogRq5RNpmnSmmWbqgPBbnuZXeWUeN5Q6eIJPkUwHtDpwHRETE6SIA3oXNOrVKW+U9KcLoMts4cj6JkOcqbUjM6eKEXtB1DL8pzsfpqY1pWOGnj8RLj5yPxJ7U2sdD7IVGhrOn9dzlN/FC79OLnNa7puUsWIqlyQr+SvEUl8spwdLBLMploH+As2y/2yjGe3anFmoxTrShD1Npi8aFHV/TqFel7umf+WWPnjlwc3Fjysbvkt5hOQJo/gn+nM6AxMY+/eOEW3b1vpkPIcW1L04rHhsHwmgemJR1FSXLuH+aOnJsOFvmZkEJuaD7Qrnvc+P7BTQgYdG5oj7G2B4o4KlyYZ9qpFixp1R829+sor9AvK7UcuyyArfedQ8CITsRjFCksciwo08IAPvagPu3w8tIP6Si7jgr7YtwvgNHIrYwGa7v51zk6Hr+Di8YKcwd5IlZ+lCRWR2fdyEoYTXq6m4atNwdv5mhTiYWonT//1FfSx0U2jjcmUoZ00OnpfUuGH6T5Hyb6sVnrnqnhfrDSojdWj464jkvztfp/XKjhwZBfib/gsvsNCKiY2A==)

As always, I am open to any feedback. If there is something I can do to better explain the code or other ways to refactor my logic to optimize further, by all means please reach out!

See you in the next one!