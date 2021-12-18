---
title: "TIL: LSP hover support in Neovim with vim-lsp"
date: 2021-12-17
tags:
  - til
  - vim

layout: layouts/post.njk
---
I recently installed Neovim and the plugin [vim-lsp](https://github.com/prabirshrestha/vim-lsp) and set up LSP support for Go. By default vim-lsp provides hover hints that show, for example, function type signatures as you type. They also show any available docstrings.

Not only will the hover functionality pull docstrings from standard library modules or imported modules, but it will dynamically load docstrings on your own types!

For example, I wrote this type:

```go
// chatChannel wraps a tview TextView and InputField as well as two channels for incoming and outgoing messages
type chatChannel struct {
	view    tview.TextView
	input   tview.InputField
	inMsgs  chan string
	outMsgs chan string
}
```
 Now, when I start typing `chatCh` and tab-select the type from the drop down, I can see my docstring without even reloading the file!
