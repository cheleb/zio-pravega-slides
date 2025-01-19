---
# You can also start simply with 'default'
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://cover.sli.dev
# some information about your slides (markdown enabled)
title: Pravega
info: |
  ## Pravega
  Presentation Pravega

  Learn more at [pravega.io](https://pravega.io)
# apply unocss classes to the current slide
class: text-center
# https://sli.dev/features/drawing
drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: slide-left
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
---

# Pravega

Stream reimagined

<div @click="$slidev.nav.next" class="mt-12 py-1" hover:bg="white op-10">
  Press Space for next page <carbon:arrow-right />
</div>

<div class="abs-br m-6 text-xl">
  <button @click="$slidev.nav.openInEditor" title="Open in Editor" class="slidev-icon-btn">
    <carbon:edit />
  </button>
  <a href="https://github.com/slidevjs/slidev" target="_blank" class="slidev-icon-btn">
    <carbon:logo-github />
  </a>
</div>

<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---
src: ./pages/01_intro.md
---

---
src: ./pages/02_pravega.md
---

---
src: ./pages/03_ono-at-ledger.md
---

---
src: ./pages/04_zio-pravega.md
---

---
src: ./pages/05_zio.md
---

---
src: ./pages/06_demo.md
---

---
src: ./pages/07_demo-zio-pravega.md
---

---
transition: fade-out
---

# Spaciba!

## Questions?

---

# Pravega

## References

<table>
 <tbody>
  <tr>
   <td>
     Pravega
   </td>
   <td>
    <a href="https://pravega.io">pravega.io</a>
    </td>
  </tr>
  <tr>
   <td>
    ZIO
   </td>
   <td>
    <a href="https://zio.dev">zio.dev</a>
   </td>
  </tr>
  <tr>
   <td>
    ZIO Pravega
   </td>
   <td>
    <a href="https://github.com/cheleb/zio-pravega">https://github.com/cheleb/zio-pravega</a>
   </td>
  </tr>
  <tr>
   <td>
    Scala
   </td>
   <td>
    <a href="https://www.scala-lang.org">scala-lang.org</a>
   </td>
  </tr>
  </tbody>
</table>
