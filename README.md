# An introduction to building Steam Bots, and JS as a whole.

_Or, 'I'll throw some code at your feet and hope you will learn something from it.'_

I say this, because I want to make it clear here that I am not trying to teach you to code properly, look at this as giving you a running start in the never ending journey of learning programming languages. I will (have to) leave out somewehat important bits and pieces, that I hope you will learn as you go.

I want to encourage you to use external resources while reading this document, namely the [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript) (Mozilla Developer Network) - their JS references are excellent. Since their site is structured somewhat akwardly, it is probably the fastest to just search for 'MDN string/array/operators/whatever' in your search engine of choice.

### The basics

Even I am not cruel enough to expect you to be able to understand some random code, so here goes my take at demistifying this whole thing.

####Browser console

If you are in a browser, just press __F12__ and switch to the __Console__ tab. This should work in all recent versions of Chrome (Chromium), Firefox, and virtually any browsers that sprang out of those two.

Normally, as a developer, you would be using this tab to view the output of your JS scripts, but we can also use it to type a one-liner, and immediately get a result back, like so:

![GIF](https://i.imgur.com/gfZZD2X.gif)

While this is pretty fun to play around with, and also great to learn new things thanks to the autocompletion suggestions, we want to write bigger, more complex scripts.

Now, we could write a quick website and run our JavaScript via that (which is how it is usually used), but that's a bigger detour than I would like - we are going to use __NodeJS__ ([download](https://nodejs.org/dist/v8.8.1/node-v8.8.1-x64.msi)) to run our code. This will also be convenient later, when we will begin development of the steam bot.

####NodeJS

> Node.js® is a JavaScript runtime built on [Chrome's V8 JavaScript engine](https://developers.google.com/v8/). Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient. Node.js' package ecosystem, [npm](https://www.npmjs.com/), is the largest ecosystem of open source libraries in the world.

Yes. Got that? No? Well, that's ok. What you need to know is: NodeJS can run JavaScript on your PC, on your mother's laptop, on your server, and probably also on your toaster. It's open source, runs on any modern OS, is backed by the linux foundation, and it's growing very quickly. It is mainly used on servers and as a console application, but through some pretty amazing frameworks, you can also develop sexy desktop and mobile apps with it (*cough* Discord, Atom, ...).

Once you've installed Node, go ahead and fire up a console (fastest way: Win + R, type in 'cmd'; you can just look for 'cmd' in the search tab though), and type `node`.

![](https://i.imgur.com/OiuvB8d.gif)

As you can see, in it's base form, NodeJS is just like the console tab you had open just a minute ago. (Which is not surprising, since it's pretty much the same thing, only built to run standalone.) OK, this  is pretty cool and all, but we want to get into making scripts, that run using NodeJS, don't we? Well, here goes.

#### First scripts

To get started, just create a file in any directory you want and name it `index.js`. The name isn't really important, and this is just a suggestion.

Let's start out with something simple, paste the following snippet into the file:

```javascript
console.log('elliot')
```

Now, run `node index.js` in your console (obviously, replace index.js with whatever you named your file). Predictably, you will see a single line saying 'elliot', after which the script terminates.

Well, that was too easy now, wasn't it? How about this:

```javascript
const name = 'elliot'

function printName (name) {
    console.log(name)
}

printName(name)
```

This produces the exact same result, but it introcudes two new concepts:

- Variables ([var](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var), [let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let), [const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)), and
- Functions ([function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function))

I've added links to the relevant MDN article(s), respectively - I recommend you at least skim them, you will be using variables and functions all the time. I will not go into detail about these things here, but it is imperative that you understand the use of the above two before continuing.

Things that MDN does not cover (or, at least, only covers insufficiently) are best-practises, in this case it's a best practise to use `const` as often as possible, and to use `let` where it is not. This is to prevent unintended changes of variables that should remain constant, and to improve readability. When you define a `const` it is instantly apparent that it's value *will not change.*

```javascript
const name = 'elliot' // Is the unchangeable version of...
let name = 'elliot' // which is the block scoped version of...
var name = 'elliot'

function printName (name) { ... } // Is the same as...
const printName = function (name) { ... } // which is the same as...
const printName = (name) => { ... }
```

New thingies!

- Comments (nice and basic [guide on comments](https://www.digitalocean.com/community/tutorials/how-to-write-comments-in-javascript))
- [Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

__A note on different ways to declare functions:__ There is no consistent standard on this, and in the end the best option is to just pick a ruleset and stick with it. Consistent code style should be a priority. I personally almost always use `const functionName = (arguments) => { /** code */ }`, but that's really not much more than personal preference (although it's consistent with the best practice of always preferring `const`, functions are not constants by default!).

Still here? OK, let's try something a bit more complex, then.

### The Bot

We will be using __modules__ to create our bot, as it will allow for a very quick and convenient setup. Consider the following code, and note the use of `require`.

```javascript
// npm install -g steam-user
const SteamUser = require('steam-user')
const Client = new SteamUser()

// No need to install this.
const readline = require('readline').createInterface({
	input: process.stdin,
  output: process.stdout
})

client.setOption('promptSteamGuardCode', false)

// Replace the below values with your data,
// or use the testing account provided by me.
const accountData = {
	accountName: 'fsoc10',
	password: '2GFiWQDF4V'
}

client.logOn(accountData)

// Event which is emitted once steam needs a code.
// Domain is only defined when email is needed.
client.on('steamGuard', (domain, callback) => {
	readline.question((domain ? 'EMail' : 'Mobile') + ' code: ', code => {
		callback(code)
		readline.close()
	})
})

client.on('error', console.error)

client.on('loggedOn', () => {
   console.log('logged on')
   // Client/Bot is now ready.
})
```

