---
title: Hacking Hexo
date: 2017-05-20 18:19:05
categories:
- Study Note
- Hexo
tags:
  - Hexo

---

This is a post I used to test the functionality of Hexo.

### Basics

I added a tag and a category to this post to learn how Hexo works. In addition, I added a music player at the bottom of this page, which is much simpler than I expected. The code for the music player is as followed:

```html
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=28481646&auto=1&height=66"></iframe>
```

Here I play music from *music.163.com*, where one can generate external link easily. To auto play the music, simply change the `auto=1` to `auto=0` in the code above.

### Math Formulas

I also tried Mathematic formula here with my editor. For example, **Eulers Formula** is:

$$
	e^{\pi i} + 1 = 0
$$

To support $\LaTeX{}$ in Hexo, we need to install additional plugin. Thanks to [1], the process is quite simple. All we need to do is to install *MathJax* by:

```
npm install hexo-math --save
hexo math install
```

However, since Hexo already uses *Marked.js* to render our page, we need to ensure no conflicts between these two plugins.

The only conflicts are escape characters and underscore for italic. We can ignore the latter since for markdown, `*` can also be used to show italic. 

For escape characters, we need to change marked.js file in `./node_modules/marked/lib/`. 

First change

```
escape: /^\\([\\`*{}\[\]()# +\-.!_>])/,
```

to 

```
escape: /^\\([`*\[\]()# +\-.!_>])/,
```
Then change 

```
em: /^\b_((?:[^_]|__)+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

to 

```
em:/^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
```

Done! 

---
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 auto=0
src="//music.163.com/outchain/player?type=2&id=28481646&auto=0&height=66"></iframe>

---

### Reference 

[1] http://blog.csdn.net/emptyset110/article/details/50123231
