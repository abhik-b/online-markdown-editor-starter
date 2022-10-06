# Explanation of markdown editor :

This is a reference guide for what we will be doing throughout this challenge & why are doing those. Here I divided the coding into 3 parts. First we take the user input. Then we detect the various text patterns in user input & finally in 3rd part I parse html for those patterns detected. This is a very basic md editor & lacks a lot of original functionalities. This should be treated as a project to learn **regex** & **DOM manipulation**.

# 1. Take input from user in markdown format

The `textarea` element represents a multiline plain text edit control for the element's raw value. I removed border & outline for it & also set background & color to inherit. This way it looks like a native text editor interface. I also increased it's height to fit the screen.

# 2. Detect the markdown format

## Markdown Syntax for the following formats are:

- headings 1 & 2 : `# H1`,`## H2`,
- bold : `**bold text**` ,
- italics : `*italicized text*` ,
- highlight : `==highlighted text==`,
- lists (unordered & ordered)

  - unordered :

  ```
  - First item
  - Second item
  ```

  - ordered :

  ```
  1. First item
  2. Second item
  ```

- hyperlinks : `[title](https://www.example.com)`

For detecting those syntaxes we will use regex (short for regular expression). A regex is a string of text that lets you create patterns that help match, locate, and manage text.

In Javascript, we normally test a regex pattern like this : `/abhik/g.test('my name is abhik')`. Here `/abhik/` is the regex pattern. `.test()` method tests for a match in a string. It returns true or false based on pattern matched or not. We will also use `match()` method which returns an array containing all of the matches, including capturing groups, or null if no match is found.

Learn more about regex [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions).

## Below are some basics of regex.

- `/`
  : this is basic syntax for regex. It means beginning & ending of regex.Example : `/ /`
- `g`
  : this means global . It retains the index of the last match, allowing subsequent searches to start from the end of the previous match.
- `m`
  : When the multiline flag is enabled, beginning and end anchors (^ and \$) will match the start and end of a line, instead of the start and end of the whole string.
- `$`
  : Matches the end of the string, or the end of a line if the multiline flag (m) is enabled. This matches a position, not a character.
- `.`
  : Matches any character except linebreaks
- `*`
  :Matches 0 or more of the preceding token.
- `+`
  : means 1 or multiple occurences

## Parsing Headings

`/^#[^#].*$/gm` is the regex for Heading 1. Let me break it down for you . So when a user writes

```md
# H1

some lines

## h2

Some more lines
```

The above regex will select the 1st line `# H1` & ignore others. Now let me explain the pattern in details.

- `/^`
  : It matches the begining of the string if multiline (m) flag is enabled.
- `#`
  : this is the character we want to match. It is standardly used for markdown headings.
- `[^#]`
  : `[^]` stands for negated set. It means if the characters in this set is found in the string then the string is **not matched**. Here it means if the # is succeded by another # then it is not heading 1 . Example :`## hi` is not heading 1 but `# hi` is heading 1.
- `.*$/gm`
  : means any character (except linebreaks) is allowed till the string or line ends. `g` & `m` means global & multiline.That means all the matches for the pattern will be captured (globally) & those can be multiple lines.

Similarly for heading 2 I will use `/^##[^#].*$/gm`

## Text Formatting

- `**bold**` = `/\*\*[^\*\n]+\*\*/gm`
  : `\*` means look for star(`*`) however since \* has a different meaning in regex we use `\` escape pattern to say it is the star(\*) character & not regex \*.Here it is written twice `\*\*` which means look for 2 consecutive stars. Then a negated set of `*` so that only 2 stars not more than that. However the set is also required here with `+` because after 2 stars there should be atleast 1 or more characters, every character which is not a star will be selected. Then the ending should also have 2 stars. Also this is multiline & global.
- `_italics_` = `/[^\*]\*[^\*\n]+\*/gm`
  : `[^\*]` means the italic segment should not start with 2 consecutive stars. So the 1st character should not be a star , it can be anything but not a star. The 2nd character should be a star. Then any character except star. Then the ending should also have 1 star. This is also multiline & global.
- `==highlight==` = `/==[^==\n]+==/gm`
  : It begins with double = sign & in between any char except double = then ends with double =.

## Hyperlinks

The regex for links is pretty complex `/\[[\w|\(|\)|\s|\*|\?|\-|\.|\,]*(\]\(){1}[^\)]*\)/gm`.

- `\[`
  : the alternate text for hyperlink starts with [
- `[\w|\(|\)|\s|\*|\?|\-|\.|\,]*`
  : after the [ , there can be any word or symbols like `() * ? - . white space ,` . The occurence can be 0 or more. That means text can be empty too.
- `(\]\()`
  : then the ] will close the text section & open the url section with (.
- `{1}[^\)]*\)`
  : there can be only 1 `(` & inside the link section , there can be any character except `)`. \* here means that the link section can be empty or filled.Then \) closes the link section.

## Lists

The regex for entire group of lists is `/^(\s*(\-|\d\.) [^\n]+)+$/gm`. Lets break it down :

- `^(\s*` means either 0 or more space at the start of the line`
- `(\-|\d\.)` means either `-` or `{digit}.`. Example of `{digit}.` is `1. 2.` like that.
- `[^\n]+)+$` means whitespace , then a negated set of any character except newline (\n),`+)+` means the negated set has 1 or more occurences , the entire `((\-|\d\.) [^\n]+)` has 1 or more occurences. `$` means end of the line (because m flag is enabled).

We need the regex for entire list group so that we can have ul tags. We also have seperate regex for each list item for assigning li tags.

The regex for unordered list is `/^\-\s.*$/` it means the line begins with `-` (note the whitespace) , then any character except newline (\n).

The regex for ordered list is `/^\d\.\s.*$/` it means the line begins with `{digit}.` (note the whitespace) , then any character except newline (\n)

# 3. Show preview to user (convert markdown to html)

So when we type the input is stored in content. Then this content is tested with `.test()` method for various regex patterns & then the text is wrapped with html tags based on that pattern. We get an array of potential matches of a particular pattern using `match()` method of string & then we extract text from each of the match & wrap it with correct html tags & replace the original match.

For example : When we match a heading 1 & capture `# hi` we then extract `hi` from the capture & this is the extracted text. Now we replace `# hi` with `<h1> hi</h1>`. Similarly

> `.test()` method tests for a match in a string. It returns true or false based on pattern matched or not. We will also use `match()` method which returns an array containing all of the matches, including capturing groups, or null if no match is found.

First things first , we will be doing all our markdown to html conversions inside the input event listener. Read the comments inside code to understand it better.

```js
textinput.addEventListener('input', (e) => {
  let content = e.target.value
  // here we will do the conversion
  markdown_preview.innerHTML = content
})
```

> Note : The `slice()` method is used to extract a section of a string and return it as a new string, without modifying the original string.

## The conversion for h1 is something like this below :

```javascript
if (h1.test(content)) {
  const matches = content.match(h1) // returns array [] of all heading 1

  matches.forEach((element) => {
    const extractedText = element.slice(1)
    // each element is sliced from index 1
    // Example string : # Hi , then string will be ' Hi' becaue at index 1 is whitespace.
    content = content.replace(element, `<h1>${extractedText}</h1>`)
    // then replace the matched string with formatted HTML whose text content is extracted text.
    // finally reassign this replaced string.
  })
}
```

## The conversion for h2 is something like this below :

```javascript
if (h2.test(content)) {
  const matches = content.match(h2) // returns array [] of all heading 2
  matches.forEach((element) => {
    const extractedText = element.slice(2) // each element is sliced from index 2
    // Example string : ## Hi , then string will be ' Hi' becaue at index 2 is whitespace.
    content = content.replace(element, `<h2>${extractedText}</h2>`)
    // then replace the matched string with formatted HTML whose text content is extracted text.
    // finally reassign this replaced string.
  })
}
```

## The conversion for bold is something like this below :

```javascript
if (bold.test(content)) {
  const matches = content.match(bold)
  matches.forEach((element) => {
    const extractedText = element.slice(2, -2) //sliced from index 2 till the (total length - 2)
    // Example : **abhik** , index 2 is a, so the new string is started from a
    // total length is 9  therefore 9 - 2 is 7. So the new string is from index 2 to 7 which is abhik
    content = content.replace(element, `<strong>${extractedText}</strong>`)
  })
}
```

## The conversion for highlight is same as bold :

```javascript
if (highlight.test(content)) {
  const matches = content.match(highlight)
  matches.forEach((element) => {
    const extractedText = element.slice(2, -2) //sliced from index 2 till the (total length - 2)
    // Example : ==abhik== , index 2 is a, so the new string is started from a
    // total length is 9  therefore 9 - 2 is 7. So the new string is from index 2 to 7 which is abhik
    content = content.replace(
      element,
      `<span class="highlight">${extractedText}</span>`,
      // we will add custom styles for highlight class in style.css
    )
  })
}
```

## The conversion for italics is something like this below :

```javascript
if (italics.test(content)) {
  const matches = content.match(italics)
  matches.forEach((element) => {
    const extractedText = element.slice(2, -1)
    //sliced from index 2 till the (total length - 1)
    // Example : *abhik* , index 2 is a because the regex for italics says there should be 1 more character before star, so the new string is started from a
    // total length is 8  therefore 8 - 1  is 7. So the new string is from index 2 to 7 which is abhik
    content = content.replace(element, `<em>${extractedText}</em>`)
  })
}
```

## The conversion of hyperlinks :

```js
if (link.test(content)) {
  const links = content.match(link)
  // ['[abhikb](https://www.youtube.com/c/abhikb)']
  links.forEach((element) => {
    const text = element.match(/^\[.*\]/)[0].slice(1, -1) // abhikb will be extracted
    const url = element.match(/\]\(.*\)/)[0].slice(2, -1)
    // https://www.youtube.com/c/abhikb will be extracted
    content = content.replace(element, `<a href="${url}">${text}</a>`)
  })
}
```

## The conversion of lists is :

Example string

```md
- hi
- bye

1. hdhd
2. jdjdj
```

Code for converting that md to html :

```javascript
if (lists.test(content)) {
  const matches = content.match(lists)

  matches.forEach((list) => {
    const listArray = list.split('\n')
    // ['- hi', '- bye', '', '1. hdhd', '2. jdjdj']
    const formattedList = listArray
      .map((currentValue, index, array) => {
        if (unorderedList.test(currentValue)) {
          currentValue = `<li>${currentValue.slice(2)}</li>`

          if (!unorderedList.test(array[index - 1])) {
            //array[index-1] will be false if it is null,undefined or < 0
            // unorderedList.test(array[index - 1]) will return true only if the the array element at index - 1 is ul element
            // !unorderedList.test(array[index - 1]) will return true if the unorderedList.test(array[index - 1]) returns false
            currentValue = '<ul>' + currentValue
            // this means if the previous element of the list element in the array  is not a list element or this list element is the 1st element of the array  then add a starting ul tag
          }
          if (!unorderedList.test(array[index + 1])) {
            //array[index+1] will be false if it is null,undefined or > length of the array
            // unorderedList.test(array[index + 1]) will return true only if the the array element at index+1 is ul element
            // !unorderedList.test(array[index + 1]) will return true if the unorderedList.test(array[index + 1]) returns false
            currentValue = currentValue + '</ul>'
            // this means if the next element of the list element in the array  is not a list element or this list element is the last element of the array  then append a closing ul tag
          }
        }
        //Similarly create ol
        if (orderedList.test(currentValue)) {
          currentValue = `<li>${currentValue.slice(2)}</li>`

          if (!orderedList.test(array[index - 1])) {
            currentValue = '<ol>' + currentValue
          }

          if (!orderedList.test(array[index + 1])) {
            currentValue = currentValue + '</ol>'
          }
        }

        return currentValue
      })
      .join('')

    content = content.replace(list, formattedList)
  })
}
```

## Other lines

Finally we add all the normal text lines (not headings or lists) inside p tags

```javascript
content = content
  .split('\n')
  .map((line) => {
    if (!line.startsWith('<') && line !== '') {
      // if line is not empty & does not start with html tag
      return line.replace(line, `<p>${line}</p>`)
    } else {
      return line
    }
  })
  .join('\n')
```

By Now Preview side should have HTML preview of whatever markdown you write on text editor.
