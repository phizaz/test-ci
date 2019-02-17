---
layout: post
title: Vim conflicting <Ctrl-d> with VSCode
categories: dev
tags: vscode
---

VSCode **Vim** extension binds `<Ctrl-d>` for its own use. This keymap is used by VSCode for "selecting next word occurrence" in a multi-cursor fashion. If you like VSCode to handle this keymap (and others of your choice) you could do so via `vim.handleKeys` option.

The following table is copied and pasted from the [project](https://github.com/VSCodeVim/Vim#vscodevim-settings):

| Setting        | Description                                                  | Type   | Default Value   |
| -------------- | ------------------------------------------------------------ | ------ | --------------- |
| vim.handleKeys | Delegate configured keys to be handled by VSCode instead of by the VSCodeVim extension. Any key in `keybindings` section of the [package.json](https://github.com/VSCodeVim/Vim/blob/master/package.json) that has a `vim.use<C-...>` in the when argument can be delegated back to VS Code by setting `"<C-...>": false`. Example: to use `ctrl+f` for find (native VS Code behaviour): `"vim.handleKeys": { "<C-f>": false }`. | String | `"<C-d>": true` |

Using the `vim.handleKeys` option you could delegate the handles to VSCode by setting each to `false` like so:

```json
"vim.handleKeys": {
    "<C-d>": false
}
```