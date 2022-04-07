---
slug: "/blog/scraping-master-russian"
date: "2022-04-06"
title: "Scraping phrases of most common words from Master Russian site"
---

## Why?

For a while now I've been studying Russian.
There's a site called Master Russian which has a list of the 1000 words most commonly used in Russian.
For each of these words it has example phrases explaining how a word is used.
A lot of times the same word can be used in a variety of ways.

Take for example the most common word `и`.
In its most basic way it just means "and":

> Мальчик и девочка играют.
>
> A boy and a girl are playing

The same word can also mean "too":

> Она и нам рассказала.
>
> She told us too.

As you can see these example phrases are very concise so very useful for beginners to practice.
That's why I decided to write a little script to scrape all of the example phrases for the most common words.

## Steps

The list of most common words consists of multiple pages.
On each page there are some common words with a link to a page giving all the example phrases.
So a script should do roughly the following:
- open page with common words
- find links to example phrases
- loop over these links
- scrape the example phrases

I've learnt my lesson from past endeavours and tried to start small first.
This means starting with scraping a single page of example phrases and ignoring the looping.
Adding the looping later on when a basic example already works is child's play.

## The script

The main body of the script looks like this:

```javascript
for (const initialLink of createInitialLinks()) {
        await page.goto(initialLink)

        const links = await getWordLinks()

        for (let link of links) {
            try {
                console.log(link)
                link = checkLink(link)

                await page.goto(link)

                const phrases = await getPhrases()

                phrases.forEach(phrase => {
                    writePhaseToStream(phrase, phrasesStream)
                })
            }
            catch (error) {
                writeErrorToStream(link, error, errorStream)
            }
        }
    }
```

I hope the function names are pretty self-explanatory based on the explanation of the steps above.

As you can see the code has been wrapped in a `try...catch` statement for error handling.
When scraping there are many things which can go wrong and it's hard to say in advance what exactly might be the issue.
It might be a malformed URL, some encoding issue, ...
The only thing I saw when running the script is there aren't too many issues.
That's why I decided to just write to a file with `writeErrorToStream` when something goes wrong.
This file contains the error and the link causing that error.
If later on I still want to improve this script I'll have a nice basis to start from.


## Obstacles and interesting titbits

I kept a list of issues I struggled with and interesting discoveries during the development of the script.

### No Puppeteer knowledge

I never really used [Puppeteer](https://github.com/puppeteer/puppeteer) before.
Puppeteer is a really fitting name since that's what the library does.
Anything you can do manually in a browser you can automate.
It uses the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) so it's tied to using Chrome(or more accurately Chromium).

Using Puppeteer turned out to be a breeze.
It boils down to the following:
- you create an instance of a browser
- you open a page
- you can interact with that page using the Puppeteer API
- close the browser

The Puppeteer API is high-level so it matches how you think about using a browser.
For example the command to open a link is `page.goto(link)`.
"Go to" is also how you'd describe opening a link in plain English.

### Extracting values

I assumed there would be some Puppeteer-specifc commands to do basic tasks like extracting a value from an element.
However, Puppeteer uses a different approach and leverages existing Web APIs.

For example after opening the initial links I wanted to extract the `href` value of the `a` tags.

```javascript
async function getWordLinks() {
        return await page.evaluate(() => {
            return [...document.querySelectorAll('.word > a')].map(element => element.getAttribute('href'))
        })
    }
```

Instead of using some Puppeteer-specifc command, you use `page.evaluate()`.
It evaluates a function in the context of the page.
In this case we use the [document Web API](https://developer.mozilla.org/en-US/docs/Web/API/Document).
`document` gives us access to the page's content.

There are alternatives to `page.evaluate()` but it seems like overcomplicating.
For example you could [use XPath instead of CSS selectors](https://github.com/puppeteer/puppeteer/issues/3786#issuecomment-454929267) and point to the attribute with `@`.

Another advantage of using the standard Web APIs is you can already check if your selection is working correctly in the browser itself by using the browser developer tools.

![Dev tools used to check if selection will work](/get-href-masterrussian.png)

### You can't iterate over a NodeList

A `NodeList` is a [collection of nodes](https://developer.mozilla.org/en-US/docs/Web/API/NodeList) but it's not an `Array` although in some ways it does behave like one.
For instance just like an `Array` a `NodeList` has the `forEach` method.
However, it doesn't provide all the iterator methods the `Array` does provide.
In my case I wanted to use the `map` method to get the text for each element:

```javascript
const russian = [...document.querySelectorAll('.phrase_plain .first')].map(element => element.innerText)
```

The crucial part in the code above is the three dots.
This [spread operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) turns the `NodeList` into an `Array`.
That way we can use the `map` method.

![NodeList has no map method](/nodelist-has-no-map-method.png)

### My knowledge about CSS selectors is very incomplete

I know the [basic CSS selectors](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors) but there's a lot more to learn about them. For example you can use the `nth-child` selector in multiple ways. Before I always used it as `element:nth-child({integer})`. But if you add the letter n next to the integer like `element:nth-child({integer}n)` it will select every nth-child instead of just one.

### map is a very versatile function

In the script I used `map` in 3 very different ways:

1. to extract the translations from all the phrase nodes
2. to zip two arrays together
3. to create a sequence of increasing numbers to make the initial links

Extracting the translations was the most straightforward usage:

```javascript
const russian = [...document.querySelectorAll('.phrase_plain .first')].map(element => element.innerText)
```

For each phrase I extract the `innerText` and save the result in the variable `russian`.

The other 2 ways to use `map` are a bit more creative and I didn't come up with them myself.

```javascript
return russian.map((a, i) => {
    return { "russian": a, "english": english[i] }
})
```

In my script I first got all the Russian translations and then all the English ones leaving me with two separate arrays.
To turn this into a single array of translations I take advantage of the fact the `map` method gives you [access to the index of the current iteration](https://stackoverflow.com/a/22015771).

The last way I used `map` is to create the suffixes in the initial links:

```javascript
const suffixes = ['', Array(11).fill().map((element, index) => `_${index + 2}`)].flat()
```

You first create an array of a certain length with each element being empty.
Since you have access to the index and that index increases by 1 over each iteration,
you can create a sequence of consecutive numbers.

### `forEach` doesn't work in a asynchronous way

Once I had my array of initial links, I thought I could just loop over it with `forEach` opening a page on each iteration.
However, that's [not how forEach works](https://stackoverflow.com/a/49787226).
Here's a place for the `for...of` loop to use.

### saving JavaScript data to csv is very easy

There's [no need for any external libraries](https://nodejs.org/en/knowledge/advanced/streams/how-to-use-fs-create-write-stream/). 
You just create a stream and start writing to it.
The only part where an external library would come in handy is putting the data in the correct csv format.
Without a library you have to write newlines, quotes, ... yourself based on the csv spec.

### error handling is a necessity when scraping

When scraping there's a lot of things which can go wrong.
Sometimes URLs are malformed, sometimes there are time outs, ...
It's hard to know in advance what exactly might go wrong.
Even worse is it might go wrong at the very end of you scraping,
obliging you to run the whole script again.
That's why it's better to add some error handling with `try...catch`.

For this script I added some code to the `catch` part:

```javascript
catch (error) {
    writeErrorToStream(link, error, errorStream)
}
```

When something goes wrong it saves the failing link and the exact error to file.
This way I can improve on the script later on if I return to it.

## Further improvements

- For now I just copy code from my script in this post. It would be nice to have code snippets you can execute in the post itself.
- My debugging process is still very basic. It's time to move beyond `console.log`.
- I get the basic gist of working with asynchronicity in JavaScript but would benefit from looking into it more in-depth

## Conclusion

This was a nice little project to get back into programming in JavaScript.
Keeping a log while doing the programming and reflecting on issues faced afterwards is very enjoyable and a way of working I'll try to repeat.