# Zenn CLI
* [ð explain](https://zenn.dev/zenn/articles/install-zenn-cli)
* [ð How to use](https://zenn.dev/zenn/articles/zenn-cli-guide)

# è¨äºã®ä½ãæ¹
1.ä»¥ä¸ã®ã³ãã³ããstart 
> npx zenn new:article
- ããã§ãOK
> npx zenn new:article --title "ç¶æç®¡çstateã«ã¤ãã¦ð" --type idea --emoji â¨
1. ãã¬ãã¥ã¼ãç¢ºèªãã
> npx zenn preview


# sample
ãã®ãã¼ã¸ã§ã¯ Zenn ã®markdownè¨æ³ãä¸è¦§ã§ç´¹ä»ãã¾ãã

# è¦åºã

```
# è¦åºã1
## è¦åºã2
### è¦åºã3
#### è¦åºã4
```

# ãªã¹ã

```
- Hello!
- Hola!
  - Bonjour!
  * Hi!
```

- Hello!
- Hola!
  - Bonjour!
  * Hi!

ãªã¹ãã®ã¢ã¤ãã ã«ã¯`*`ãããã¯`-`ãä½¿ãã¾ãã

## çªå·ä»ããªã¹ã

```
1. First
2. Second
```

1. First
2. Second

# ãã­ã¹ããªã³ã¯

```
[ã¢ã³ã«ã¼ãã­ã¹ã](ãªã³ã¯ã®URL)
```

[ã¢ã³ã«ã¼ãã­ã¹ã](https://zenn.dev)
`Ctrl + K`ã®ã·ã§ã¼ãã«ããã§ãæ¿å¥ã§ãã¾ãã

# ç»å

```
![altãã­ã¹ã](https://ç»åã®URL)
```

![altãã­ã¹ã](https://storage.googleapis.com/zenn-user-upload/gxnwu3br83nsbqs873uibiy6fd43)

## ç»åã®æ¨ªå¹ãæå®ãã

ç»åã®è¡¨ç¤ºãå¤§ããããå ´åã¯ãURL ã®å¾ã«åè§ã¹ãã¼ã¹ãç©ºãã¦`=ââx`ã¨è¨è¿°ããã¨ãç»åã®å¹ã px åä½ã§æå®ã§ãã¾ãã

```
![altãã­ã¹ã](https://ç»åã®URL =250x)
```

![altãã­ã¹ã](https://storage.googleapis.com/zenn-user-upload/gxnwu3br83nsbqs873uibiy6fd43 =250x)

## ã­ã£ãã·ã§ã³ãã¤ãã

ç»åã®ããä¸ã®è¡ã«`*`ã§æãã ãã­ã¹ããéç½®ããã¨ãã­ã£ãã·ã§ã³ã®ãããªè¦ãç®ã§è¡¨ç¤ºããã¾ãã

```
![](https://ç»åã®URL)
*ã­ã£ãã·ã§ã³*
```

![](https://storage.googleapis.com/zenn-user-upload/gxnwu3br83nsbqs873uibiy6fd43 =250x)
_captions_

## ç»åã«ãªã³ã¯ãè²¼ã

ä»¥ä¸ã®ããã«ãããã¨ã§ç»åã«å¯¾ãã¦ãªã³ã¯ãè²¼ããã¨ãã§ãã¾ãã

```
[![altãã­ã¹ã](ç»åã®URL)](ãªã³ã¯ã®URL)
```

# ãã¼ãã«

```
| Head | Head | Head |
| ---- | ---- | ---- |
| Text | Text | Text |
| Text | Text | Text |
```

| Head | Head | Head |
| ---- | ---- | ---- |
| Text | Text | Text |
| Text | Text | Text |

# ã³ã¼ããã­ãã¯

ã³ã¼ãã¯ã```ãã§æããã¨ã§ãã­ãã¯ã¨ãã¦æ¿å¥ã§ãã¾ããä»¥ä¸ã®ããã«è¨èªãæå®ããã¨ã³ã¼ãã¸è£é£¾ï¼ã·ã³ã¿ãã¯ã¹ãã¤ã©ã¤ãï¼ãé©ç¨ããã¾ãã

> \```js
>
> \```

```js
const great = () => {
  console.log("Awesome");
};
```

ã·ã³ã¿ãã¯ã¹ãã¤ã©ã¤ãã«ã¯ Prism.js ãä½¿ç¨ãã¦ãã¾ãã
[ð å¯¾å¿è¨èªã®ä¸è¦§ â](https://prismjs.com/#supported-languages)

## ãã¡ã¤ã«åãè¡¨ç¤ºãã

`è¨èª:ãã¡ã¤ã«å`ã¨`:`åºåãã§è¨è¼ãããã¨ã§ããã¡ã¤ã«åãã³ã¼ããã­ãã¯ã®ä¸é¨ã«è¡¨ç¤ºãããããã«ãªãã¾ãã

> \```js:ãã¡ã¤ã«å
>
> \```

```js:fooBar.js
const great = () => {
  console.log("Awesome")
}
```

## diff ã®ã·ã³ã¿ãã¯ã¹ãã¤ã©ã¤ã

2021/01/25ãã`diff`ã¨è¨èªã®ãã¤ã©ã¤ããåæã«é©ç¨ã§ããããã«ãªãã¾ãããä»¥ä¸ã®ããã«`diff`ã¨`è¨èªå`ãåè§ã¹ãã¼ã¹åºåãã§æå®ãã¾ãã

> \```diff js
>
> \```

```diff js
@@ -4,6 +4,5 @@
+    const foo = bar.baz([1, 2, 3]) + 1;
-    let foo = bar.baz([1, 2, 3]);
```

ãªãã`diff`ã®ä½¿ç¨æã«ã¯ãåé ­ã«`+`ã`-`ã`åè§ã¹ãã¼ã¹`ã®ããããå¥ã£ã¦ããªãè¡ã¯ãã¤ã©ã¤ãããã¾ããã

åæã«ãã¡ã¤ã«åãæå®ãããã¨ãå¯è½ã§ãã

> \```diff js:ãã¡ã¤ã«å
>
> \```

```diff js:fooBar.js
@@ -4,6 +4,5 @@
+    const foo = bar.baz([1, 2, 3]) + 1;
-    let foo = bar.baz([1, 2, 3]);
```

# æ°å¼

Zenn ã§ã¯**KaTeX**ã«ããæ°å¼è¡¨ç¤ºã«å¯¾å¿ãã¦ãã¾ãã
KaTeXã®ãã¼ã¸ã§ã³ã¯å¸¸ã«ææ°ãã¼ã¸ã§ã³ãä½¿ç¨ãã¾ãã

[ð KaTeXããµãã¼ãããè¨æ³ã®ä¸è¦§ â](https://katex.org/docs/support_table.html)

## æ°å¼ã®ãã­ãã¯ãæ¿å¥ãã

`$$`ã§è¨è¿°ãæããã¨ã§ãæ°å¼ã®ãã­ãã¯ãæ¿å¥ããã¾ãããã¨ãã°

```
$$
e^{i\theta} = \cos\theta + i\sin\theta
$$
```

ã¯ä»¥ä¸ã®ããã«è¡¨ç¤ºããã¾ãã

$$
e^{i\theta} = \cos\theta + i\sin\theta
$$

:::message
`$$`ã®åå¾ã¯ç©ºã®è¡ã§ãªãã¨æ­£ããåãè¾¼ã¾ããªããã¨ãããã¾ãã
:::

## ã¤ã³ã©ã¤ã³ã§æ°å¼ãæ¿å¥ãã

`$a\ne0$`ã¨ããããã«`$`ã²ã¨ã¤ã§æããã¨ã§ãã¤ã³ã©ã¤ã³ã§æ°å¼ãå«ãããã¨ãã§ãã¾ãããã¨ãã°$a\ne0$ã®ãããªã¤ã¡ã¼ã¸ã§ãã

# å¼ç¨

```
> å¼ç¨æ
> å¼ç¨æ
```

> å¼ç¨æ
> å¼ç¨æ



# æ³¨é

æ³¨éãæå®ããã¨ãã¼ã¸ä¸é¨ã«ãã®åå®¹ãè¡¨ç¤ºããã¾ãã

```
èæ³¨ã®ä¾[^1]ã§ããã¤ã³ã©ã¤ã³^[èæ³¨ã®åå®¹ãã®2]ã§æ¸ããã¨ãã§ãã¾ãã

[^1]: èæ³¨ã®åå®¹ãã®1
```

èæ³¨ã®ä¾[^1]ã§ããã¤ã³ã©ã¤ã³^[èæ³¨ã®åå®¹ãã® 2]ã§æ¸ããã¨ãã§ãã¾ãã

[^1]: èæ³¨ã®åå®¹ãã® 1

# åºåãç·

```
-----
```

---

# ã¤ã³ã©ã¤ã³ã¹ã¿ã¤ã«

```
*ã¤ã¿ãªãã¯*
**å¤ªå­**
~~æã¡æ¶ãç·~~
ã¤ã³ã©ã¤ã³ã§`code`ãæ¿å¥ãã
```

_ã¤ã¿ãªãã¯_
**å¤ªå­**
~~æã¡æ¶ãç·~~
ã¤ã³ã©ã¤ã³ã§`code`ãæ¿å¥ãã

## ã¤ã³ã©ã¤ã³ã®ã³ã¡ã³ã

èªåç¨ã®ã¡ã¢ããããã¨ãã¯ HTML ã®ã³ã¡ã³ãè¨æ³ãä½¿ç¨ã§ãã¾ãã

```html
<!-- TODO: â¯â¯ã«ã¤ãã¦è¿½è¨ãã -->
```

<!-- ã³ã¡ã³ããã¹ã -->

ãã®å½¢å¼ã§æ¸ããã³ã¡ã³ãã¯å¬éããããã¼ã¸ä¸ã§ã¯è¡¨ç¤ºããã¾ããããã ããè¤æ°è¡ã®ã³ã¡ã³ãã«ã¯å¯¾å¿ãã¦ããªãã®ã§ãæ³¨æãã ããã

# Zenn ç¬èªã®è¨æ³

## ã¡ãã»ã¼ã¸

```
:::message
ã¡ãã»ã¼ã¸ãããã«
:::
```

:::message
ã¡ãã»ã¼ã¸ãããã«
:::

```
:::message alert
è­¦åã¡ãã»ã¼ã¸ãããã«
:::
```

:::message alert
è­¦åã¡ãã»ã¼ã¸ãããã«
:::

## ã¢ã³ã¼ãã£ãªã³ï¼ãã°ã«ï¼

```
:::details ã¿ã¤ãã«
è¡¨ç¤ºãããåå®¹
:::
```

:::details ã¿ã¤ãã«
è¡¨ç¤ºãããåå®¹
:::


:::message
ãdetailãã§ã¯ãªããdetailsãã§ãã
:::

### è¦ç´ ããã¹ããããã«ã¯

å¤å´ã®è¦ç´ ã®éå§/çµäºã« `:` ãè¿½å ãã¾ãã

```
::::details ã¿ã¤ãã«
:::message
ãã¹ããããè¦ç´ 
:::
::::
```

::::details ã¿ã¤ãã«
:::message
ãã¹ããããè¦ç´ 
:::
::::

# ã³ã³ãã³ãã®åãè¾¼ã¿

## ãªã³ã¯ã«ã¼ã

```bash
# URLã ãã®è¡
https://zenn.dev/zenn/articles/markdown-guide
```

URL ã ããè²¼ãä»ããããè¡ãããã¨ããã®é¨åãã«ã¼ãã¨ãã¦è¡¨ç¤ºããã¾ãã

https://zenn.dev/zenn/articles/markdown-guide

ã¾ã`@[card](URL)`ã¨ããæ¸ãæ¹ã§ã«ã¼ãåã®ãªã³ã¯ãè²¼ããã¨ãã§ãã¾ãã

:::details ã¢ã³ãã¼ã¹ã³ã¢ _ ãå«ãURLãæ­£ããèªè­ãããªãå ´å
[markdownãã¼ãµã®ä»æ§](https://zenn.dev/catnose99/scraps/e94c8e789f846a)ã«ãããã¢ã³ãã¼ã¹ã³ã¢ï¼`_`ï¼ãå«ãURLã§ãæ­£ããURLãèªè­ãããªããã¨ãããã¾ãã

```
https://zenn.dev/__example__
```

> https://zenn.dev/__example__

å¯¾å¦æ³

1. ã«ã¼ãåã®ãªã³ã¯ã¨ãã¦è¡¨ç¤ºãããå ´åã¯
   `@[card](ããã«URL)`ã¨ããæ¸ãæ¹ããã¦ãã ãã
2. åç´ã«ãªã³ã¯åããã URL ãè²¼ãä»ãããå ´åã¯`<https://zenn.dev/__example__>`ã®ãããªå½¢ã§`<`ã¨`>`ã§ URL ãå²ãããã«ãã¦ãã ãã
:::

## ãã¤ã¼ã

```bash
# ãã¤ã¼ãã®URLã ãã®è¡ï¼åå¾ã«æ¹è¡ãå¿è¦ã§ãï¼
https://twitter.com/jack/status/20
```

ä»¥åã¯`@[tweet](ãã¤ã¼ãã®URL)`ã®è¨æ³ãæ¡ç¨ãã¦ãã¾ãããã2020/12/27ãURL ãè²¼ãä»ããã ãã§ãã¤ã¼ããåãè¾¼ããã¨ãå¯è½ã«ãªãã¾ããã

:::details ã¢ã³ãã¼ã¹ã³ã¢ _ ãå«ã URL ãæ­£ããèªè­ãããªãå ´å
[markdown ãã¼ãµã®ä»æ§](https://zenn.dev/catnose99/scraps/e94c8e789f846a)ã«ãããURL ã®`/`ã®åºåãã®ä¸­ã« 2 ã¤ä»¥ä¸ã¢ã³ãã¼ã¹ã³ã¢ï¼`_`ï¼ãå«ãã¨ãèªåãªã³ã¯ãéä¸­ã§éåãã¦ãã¾ãã¾ãã

```
https://twitter.com/__example__/status/12345678910
```

> https://twitter.com/__example__/status/12345678910

å¯¾å¦æ³

ãã®ãããª URL ã§ã¯`@[tweet](ãã¤ã¼ãã®URL)`ã¨ããæ¸ãæ¹ããã¦ããã ããããé¡ããã¾ãã
:::

### ãªãã©ã¤åã®ãã¤ã¼ããéè¡¨ç¤ºã«ãã

ãªãã©ã¤ãåãè¾¼ãã å ´åãããã©ã«ãã§ãªãã©ã¤åã®ãã¤ã¼ããå«ã¾ãã¦è¡¨ç¤ºããã¾ãã`ãã¤ã¼ãã®URL?conversation=none`ã®ããã«ã¯ã¨ãªãã©ã¡ã¼ã¿ã«`conversation=none`ãæå®ããã¨ããªãã©ã¤åã®ãã¤ã¼ããå«ã¾ããªããªãã¾ãã

## YouTube

```bash
# YouTubeã®URLã ãã®è¡ï¼åå¾ã«æ¹è¡ãå¿è¦ã§ãï¼
https://www.youtube.com/watch?v=WRVsOCh907o
```

ä»¥åã¯`@[youtube](YouTubeã®åç»ID)`ã¨ããè¨æ³ãæ¡ç¨ãã¦ãã¾ãããã2021/03/03ãURL ãè²¼ãä»ããã ãã§åç»ãåãè¾¼ããã¨ãå¯è½ã«ãªãã¾ããã

## GitHub

2022/04ããããGitHubä¸ã®ã½ã¼ã¹ã³ã¼ããã¡ã¤ã«ãåãè¾¼ããããã«ãªãã¾ããã
GitHubä¸ã®ãã¡ã¤ã«ã¸ã®URLã¾ãã¯ãã¼ããªã³ã¯ã ãã®è¡ãä½æããã¨ããã®é¨åã«GitHubã®åãè¾¼ã¿ãè¡¨ç¤ºããã¾ãã

```bash
# GitHubã®ãã¡ã¤ã«URLã¾ãã¯ãã¼ããªã³ã¯ã ãã®è¡ï¼åå¾ã«æ¹è¡ãå¿è¦ã§ãï¼
https://github.com/octocat/Hello-World/blob/master/README
```

ä¸è¨ã®ãªã³ã¯ã¯ãä»¥ä¸ã®ããã«è¡¨ç¤ºããã¾ãã

https://github.com/octocat/Hello-World/blob/master/README


### è¡ã®æå®

GitHubã¨åãããã«ããªã³ã¯ã®æ«å°¾ã«`#L00-L00`ã®ãããªå½¢ã§è¡¨ç¤ºãããã¡ã¤ã«ã®éå§è¡ã¨çµäºè¡ãæå®ãããã¨ãã§ãã¾ãã

```bash
# ã³ã¼ãã®éå§è¡ã¨çµäºè¡ãæå®
https://github.com/octocat/Spoon-Knife/blob/main/README.md#L1-L3
```

ä¸è¨ã®ãªã³ã¯ã¯ä»¥ä¸ã®ããã«è¡¨ç¤ºããã¾ãã

https://github.com/octocat/Spoon-Knife/blob/main/README.md#L1-L3

ã¾ããéå§è¡ã®ã¿æå®ãããã¨ãã§ãã¾ãã

```bash
# ã³ã¼ãã®éå§è¡ã®ã¿æå®
https://github.com/octocat/Spoon-Knife/blob/main/README.md#L3
```

ä¸è¨ã®ãªã³ã¯ã¯ä»¥ä¸ã®ããã«è¡¨ç¤ºããã¾ãã

https://github.com/octocat/Spoon-Knife/blob/main/README.md#L3

### ãã­ã¹ããã¡ã¤ã«ä»¥å¤ã¯åãè¾¼ãã¾ãã

åãè¾¼ãããã¡ã¤ã«ã¯ãã½ã¼ã¹ã³ã¼ããªã©ã®ãã­ã¹ããã¡ã¤ã«ã®ã¿ã¨ãªã£ã¦ãã¾ãã
ããç»åãªã©ã®ãã¡ã¤ã«ãæå®ããå ´åã¯ãä»¥ä¸ã®ãããªè¡¨ç¤ºã«ãªãã¾ãã

https://github.com/zenn-dev/zenn-editor/blob/canary/packages/zenn-cli/images/example-images/zenn-editor.png

## GitHub Gist

```bash
@[gist](Gistã®ãã¼ã¸URL)
```

2020/12/28ãå¯¾å¿ãã¾ãããç¹å®ã®ãã¡ã¤ã«ã ãåãè¾¼ã¿ããå ´åã¯`@[gist](https://gist.github.com/foo/bar?file=example.json)`ã®ããã«ã¯ã¨ãªæå­åã§`?file=ãã¡ã¤ã«å`ã¨ããå½¢ã§æå®ãã¾ãã

## CodePen

```
@[codepen](ãã¼ã¸ã®URL)
```

ããã©ã«ãã®è¡¨ç¤ºã¿ãã¯`ãã¼ã¸ã®URL?default-tab=html,css`ã®ããã«ã¯ã¨ãªãæå®ãããã¨ã§å¤æ´ã§ãã¾ãã

## SlideShare

```
@[slideshare](ã¹ã©ã¤ãã®key)
```

SlideShare ã®åãè¾¼ã¿ iframe ã«å«ã¾ãã`...embed_code/key/ââ...`ã®`â¯â¯`ã®é¨åãå¥åãã¾ãã

## SpeakerDeck

```
@[speakerdeck](ã¹ã©ã¤ãã®ID)
```

SpeakerDeck ã§åå¾ããåãè¾¼ã¿ã³ã¼ãã«å«ã¾ãã`data-id`ã®å¤ãå¥åãã¾ãã

## JSFiddle

```
@[jsfiddle](ãã¼ã¸ã®URL)
```

## CodeSandbox

```
@[codesandbox](embedç¨ã®URL)
```

CodeSandbox ã§ã¯ãåãã¼ã¸ããåãè¾¼ã¿ç¨ã®`<iframe>`ãåå¾ã§ãã¾ãããã®`<iframe>`ã«å«ã¾ãã`src`ã® URL ãæ¬å¼§ã®ä¸­ã«å¥åãã¾ãã

## StackBlitz

```
@[stackblitz](embedç¨ã®URL)
```

StackBlitz ã§ã¯ãåãã¼ã¸ãããEmbed URLããåå¾ã§ãã¾ããåå¾ãã URL ããã®ã¾ã¾æ¬å¼§ã®ä¸­ã«å¥åãã¾ãã

## ãªã³ã©ã¤ã³ã¨ãã£ã¿ã¼ã§ã¯ã¢ã¼ãã«ããæ¿å¥å¯è½

ãªã³ã©ã¤ã³ã®ã¨ãã£ã¿ã¼ã§ã¯ã+ããã¿ã³ãæ¼ããã¨ã§ãå¤é¨ã³ã³ãã³ãåãè¾¼ã¿ç¨ã®ã¢ã¼ãã«ãè¡¨ç¤ºã§ãã¾ãã

![](https://storage.googleapis.com/zenn-user-upload/t87wf3d7xgfv7cabv4a9lfr1t79q)



# ãã¤ã¢ã°ã©ã 

2021/06/08ãã[mermaid.js](https://mermaid-js.github.io/mermaid/#/) ã«ãããã¤ã¢ã°ã©ã è¡¨ç¤ºã«å¯¾å¿ãã¾ãããã³ã¼ããã­ãã¯ã®è¨èªåã`mermaid`ã¨ãããã¨ã§èªåçã«ã¬ã³ããªã³ã°ããã¾ãã

~~~
```mermaid
graph TB
    A[Hard edge] -->|Link text| B(Round edge)
    B --> C{Decision}
    C -->|One| D[Result one]
    C -->|Two| E[Result two]
```
~~~
ã¯ä»¥ä¸ã®ããã«è¡¨ç¤ºããã¾ãã



```mermaid
graph TB
    A[Hard edge] -->|Link text| B(Round edge)
    B --> C{Decision}
    C -->|One| D[Result one]
    C -->|Two| E[Result two]
```

ä»ã«ãã·ã¼ã±ã³ã¹å³ãã¯ã©ã¹å³ãè¡¨ç¤ºã§ãã¾ããææ³ã¯ mermaid.js ã«å¾ã£ã¦ãã¾ãã®ã§ãã©ã®ããã«æ¸ãã°ãããã¯[å¬å¼ãµã¤ãã®ææ³](https://mermaid-js.github.io/mermaid/#/flowchart)ãåç§ãã¦ãã ããã

:::message
mermaid.jså´ã§ç ´å£çå¤æ´ãè¡ãããå ´åãè¡¨ç¤ºãå¤æ´ãããããé©åã«è¡¨ç¤ºãããªããªãå¯è½æ§ãããã¾ãã
:::

## å¶éäºé 

Zenn ã§ mermaid.js å¯¾å¿ãè¡ãã«ããããããã¤ãå¶éäºé ãè¨­å®ããã¦ããã ãã¦ãã¾ããå¶éäºé ã¯ä»å¾ãæ§å­ãè¦ã¦è¿½å ã»å»æ­¢ã»å¤ã®å¤æ´ãªã©è¡ãå¯è½æ§ãããã¾ãã

### ã¯ãªãã¯ã¤ãã³ãã®ç¡å¹å

[Interactionæ©è½](https://mermaid-js.github.io/mermaid/#/classDiagram?id=interaction)ã¨ãã¦å³ã®è¦ç´ ã«ã¯ãªãã¯ã¤ãã³ããªã©ãè¨­å®ã§ãã¾ãããã»ã­ã¥ãªãã£ã®è¦³ç¹ã§Zennã§ã¯ç¡å¹ã«ããã¦ããã ãã¾ãã

### ãã­ãã¯ãããã®æå­æ°å¶é - 2000æå­ä»¥å

ãã­ãã¯ãããã®æå­æ°ã**2000**æå­ã«å¶éããã¦ããã ãã¦ãã¾ãããããè¶ããå ´åããã¤ã¢ã°ã©ã ãè¡¨ç¤ºãããä»£ããã«ã¨ã©ã¼ã¡ãã»ã¼ã¸ãè¡¨ç¤ºããã¾ãã

### ãã­ãã¯ãããã®Chainæ°å¶é - 10ä»¥ä¸

ãã­ã¼ãã£ã¼ãã«ããã¦ããã¼ããã²ã¨ã¾ã¨ã¾ãã§è¡¨ç¾ããè¨è¿°ã¨ãã¦`&`ãå©ç¨ã§ãã¾ããä»¥ä¸ã®ãããªã¤ã¡ã¼ã¸ã§ãã

~~~
```mermaid
graph LR
   a --> b & c--> d
```
~~~
ã¯ä»¥ä¸ã®ããã«è¡¨ç¤ºããã¾ãã


```mermaid
graph LR
   a --> b & c--> d
```

ä¾¿å©ã§ãããæ°ãå¤ããªãã¨ãã¼ãã®æ¥ç¶ãå¤ããªãããã©ã¦ã¶å´ã§ã®æç»ã«è² è·ãçããå¯è½æ§ãããããã`&`ã®æ°ã**10**ã«å¶éããã¦ããã ãã¾ãããã¡ããè¶ããå ´åã¯ãã¤ã¢ã°ã©ã ã®ä»£ããã«ã¨ã©ã¼ã¡ãã»ã¼ã¸ãè¡¨ç¤ºããã¾ãã