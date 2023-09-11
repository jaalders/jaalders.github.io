---
layout: post
title: Big O Notation
date: 2023-09-10
categories: blog
excerpt: <img src="/images/fulls/bigbang.jpg" class="image fit"> So a while back I was asked if I knew what "Big O notation" was, or pretty much any real sorting algorithms. I guess this is where my lack of degree in Computer Science kind of starts to reel it's head. I don't have a degree in computer science and I feel like this would have been one of those things that would have been discussed; alongside C++ which I kind of cringe at. Not cause of the language, more so for the fact that I would probably end up cooking a computer from memory mismanagement.
---

<img src="/images/fulls/bigbang.jpg" class="image fit">

> A universe is just a giant dataset right???

## The Big O ... There is a joke that comes to mind...

Anyways, lemme pick my brain out of the gutter and get to the actual post.

So a while back I was asked if I knew what "Big O notation" was, or pretty much any real sorting algorithms. I guess this is where my lack of degree in Computer Science kind of starts to reel it's head. I feel like this would have been one of those topics that would have been discussed; alongside C++ which I kind of cringe at. Not cause of the language, more so for the fact that I would probably end up cooking a computer from memory mismanagement.

Anyways, I was tasked with taking some time to go over Big O and at least understand the concepts of where it can be applied and what's the limitations for it. (we'll come back to the limitations later on.)

Here's some very basic code.

```
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const searchValue = 7;

function search(arr: number[], target: number): number | null {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === target) {
      return i; // We found the element, return the index.
    }
  }
  return null;
}

const result = search(numbers, searchValue);

if (result !== null) {
  console.log(`Found our searchValue ${searchValue} at index of ${result}`);
} else {
  console.log(`${searchValue} not found in the array.`);
}
```

Okay, lets break this down as best as I can. `function search` takes in two params, the first is the dataset we will be looping through, in this instance it's just the array of `numbers` the second is just our search value aka `searchValue`. The returned value will be either a number or a null depending on the search value provided.

So because this search is linear, it's simple, but maybe not ideal. Lets say in this scenario we were to pad out the dataset with a couple of extra zeros, not as in at the end of the array, but adding a couple of zeros the length of the dataset. aka taking the array from 10 rows to 100, or 100,000, etc, etc.

If your dataset is in memory via table view or some caching service such as redis, things are a little easier, but what if you were looking at a database and you've already loaded too many rows into memory and are forced to go to paged memory, what if the paged cache contains the record you're looking for at the very last position of the cached dataset?

So, what can you do?

Introducing Binary Search!

So, in our previous code example, we implemented a algorithm of this type (O(n))

> The (O(n)) algorithm searches for the target element by iterating through each element in the array one by one until it finds a match or reaches the end of the array.

But in this instance, we'll be using (O(log n))

> This algorithm is more efficient and works only on **sorted arrays** \*(NOTE). It repeatedly divides the search range in half, which reduces the search space significantly with each iteration.

Okay, so lets take a look at another code example, but with a little more complexity to it. We'll use some of the code from our previous example to help identify the changes between the two examples.

```
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const searchValue = 7;

function search(arr: number[], target: number): number | null {
  let left = 0;
  let right = arr.length - 1;

  while (left <= right) {
    const mid = Math.floor((left + right) / 2);

    if (arr[mid] === target) {
      return mid; // We found the element, return the index.
    } else if (arr[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }

  return null;
}

const result = search(numbers, searchValue);

if (result !== null) {
  console.log(`Found our searchValue ${searchValue} at index of ${result}`);
} else {
  console.log(`${searchValue} not found in the array.`);
}
```

Okay, this one is a little more in depth. It's still the same in principal, we're still taking in the array of `numbers` and our `searchValue` as params for the function but the operations are going to be slightly different. So, right off the bat, we're going to look at the number in the middle of the array first. The `Math.floor` just ensures that we're on a whole number when checking.

If the middle number is smaller than our search value, it knows that the target number must be in the second half of the array (to the right of the middle number). So, it discards the first half and repeats the process with the second half.

If the middle number is larger than our search value, it knows that the target number must be in the first half of the list (to the left of the middle number). So, it discards the second half and repeats the process with the first half.

This beauty of this algorithm is that once you have the data sorted in such a matter, the algorithm can literally drop half the result set immediately once it figured which half of the array contains the `searchValue` and it will continue to go through and repeat the process until the `searchValue` is matched.

Are there some limitations to this? Yes, like for example, if you code is mostly sorted, or very small, it would not make sense to use this form of algorithm, but when dealing with large results, once sorted. This algorithm really shines.
