---
title: "{{ replace .File.ContentBaseName "-" " " | title }}"
linkTitle: ""  # text on the Folklore index, if different from the title
topic: qec     # one of the keys under params.folkloreTopics in hugo.toml
date: {{ .Date }}
description: ""
draft: true
---

<!--
Cheat sheet (this comment is not rendered):

  Inline math:   $x$  or  \(x\)          A literal dollar sign: \$
  Display math:  $$ on its own line, the TeX, then $$ on its own line

  A citation[^knill97] is numbered automatically and listed at the end:
  [^knill97]: E. Knill and R. Laflamme. "Title". *Journal* (1997). [arXiv](https://arxiv.org/abs/...)

  A figure: put the file in this folder, then
  ![alt text](figure.svg "Figure 1: Caption, which may contain $math$.")
  {width="30em"}

  A sage-ruled note: write the paragraph, then {.note} on the next line.

  Preview with `hugo server -D`; set draft: false to publish.
-->
