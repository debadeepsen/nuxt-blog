---
title: The truth about setTimeout
description: Learn how to use @nuxt/content.
---
# The truth about `setTimeout`

So, you're a JavaScript programmer. You write code. And generally speaking, things work. So, naturally, you feel like you know things. But some of the things you think you know might not quite be the way they actually are.

## A function misunderstood by many

So, tell me, how do you think the parameters of the function `setTimeout` work?

It's easy, it's like `setTimeout(callback, delay)`, right? So it means that the `callback` function will be executed after `delay` milliseconds, right? Right?

It could be. It probably will be. But it's not that simple. Not always.

>If you know details of the event loop, you know what I'm about to talk about here and can skip this post. If you don't, take a seat, grab a coffee, and let me enlighten you.

## TLDR section
There is no guarantee that the callback function will run exactly after the specified delay. It does, however, guarantee that it won't run _before_ that time.

As an example, `setTimeout(function() { alert(Date()) }, 200)` means that it will wait _at least_ 200 milliseconds before showing today's date in an alert.

This is also why `setTimeout(callback, 0)` does not guarantee immediate execution, but rather to be executed "as soon as possible" (Check out https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Timeouts_and_intervals).

## But why?

Glad you asked. Let me tell you the story of the event loop, and what things actually happen behind the scenes when you call `setTimeout`.

### Single-threaded asynchronicity
So, JavaScript is single threaded. Which means that it cannot run multiple threads (duh!). So how does it handle asynchronous requests?

The answer is that it doesn't.

To understand, consider the following (hypothetical but real-world) scenario.

### The store story
Ashe is a store associate. In fact, she's the only one in that store. She helps customers find items they are looking for, helps them bag them, check them out, etc.

Now, suppose she finds out that the store needs something for its inventory. Maybe it's out of frozen hamburgers, and that's kind of a big deal, because customers love to buy them. She has two options -

1. Temporarily close the store and go to the wholesaler.
2. Ask somebody else to `fetch` them for her.

The first option is not acceptable, because her boss isn't cool with losing all the business during the store being "unresponsive". So, she calls upon her friend/ally/compatriot, Bob, for help. Here's how their conversation goes:

>👩‍🦳 **Ashe**: Hey Bob, I need you to do something for me. Could you please get me 200lbs of hamburgers?

>🧑 **Bob**: Sure. My only concern is that it would take a while. What do you want me to do when I'm done?

>👩‍🦳 **Ashe**: No biggie, just put them in that box over there. I'll restock them when I have some free time.

If you understand the story, you now understand how the whole event loop thing works, with regards to the JavaScript runtime.

(Bonus, unrelated points for guessing what video game consumes my life)

You see, just like in the story above, JavaScript isn't the lone warrior on the field. JavaScript has help when it comes to async tasks. And the Bob in our web browser is something called "Web API".

JavaScript knows it cannot handle async operations, so whenever it comes across a request like that, it delegates it to the Web API. Which then takes its own sweet time to do whatever was asked of it. JavaScript execution continues on the main thread, pushing and popping functions off the "call stack" (functions, in the form of something called "stack frames", are pushed just at the beginning of execution and popped when the function returns). When the async call is completed, it doesn't hop on to the call stack by itself, but goes into a sort of a waiting room, called the callback queue/task queue. When the stack is empty, there is a mechanism called the event loop that kicks in, dequeing this queue and processing the messages in it, one by one.

I know I rushed through the explanation, but I was only supposed to talk about `setTimeout`, and look how big this post has already become. Instead, I will let you watch and learn from this YouTube video, like I did - https://www.youtube.com/watch?v=8aGhZQkoFbQ&vl=en
Trust me, it's worth 26 minutes of your life, easily.

Now that you have an idea of how the event loop works, let me go back to the implications of this mechanism on `setTimeout`. Remember, `setTimeout` is carried out asynchronously by the Web API. And therefore, no matter how fast it completes, _it won't be executed until the stack is empty_.

Let's take this example from the MDN website (which is a fantastic place to learn JavaScript, btw) at https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop

```javascript
const s = new Date().getSeconds();

setTimeout(function() {
  // prints out "2", meaning that the callback is not called immediately after 500 milliseconds.
  console.log("Ran after " + (new Date().getSeconds() - s) + " seconds");
}, 500)

while (true) {
  if (new Date().getSeconds() - s >= 2) {
    console.log("Good, looped for 2 seconds")
    break;
  }
}
```

As you can see above, the async code isn't executed after just half a second, as you might be inclined to believe. Because the synchronous code doesn't free the stack until after 2 seconds. Similarly, a `setTimeout(cb, 0)` call won't necessarily mean immediate execution, but will wait for the pending tasks to be done first, like in the example below.

```javascript
(function() {

  console.log('this is the start');

  setTimeout(function cb() {
    console.log('Callback 1: this is a msg from call back');
  }); // has a default time value of 0

  console.log('this is just a message');

  setTimeout(function cb1() {
    console.log('Callback 2: this is a msg from call back');
  }, 0);

  console.log('this is the end');

})();

// "this is the start"
// "this is just a message"
// "this is the end"
// "Callback 1: this is a msg from call back"
// "Callback 2: this is a msg from call back"
```

> I just talked about the browser end of things, but similar mechanisms also exist for browserless JavaScript environments like Node JS.

That's it for now. For those of you who didn't know, hope you learned something. And if you're already an expert, I would appreciate a critique - let me know if my understanding is correct.

Take care, and stay safe. Happy coding.
