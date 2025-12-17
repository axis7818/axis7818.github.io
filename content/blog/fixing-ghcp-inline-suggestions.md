+++
title = "Fixing GitHub Copilot Inline Suggestions"
date = "2025-09-17"
updated = "2025-12-16"
description = "and other improvements to GitHub Copilot usability..."

[taxonomies]
tags = ["Software", "Config", "VSCode", "GitHub Copilot"]
+++

and other improvements to GitHub Copilot usability...

- [Better Inline Suggestions](#better-inline-suggestions)
- [Fix Tab Completion with Terminal Suggestions](#fix-tab-completion-with-terminal-suggestions)

---

## Better Inline Suggestions

One of the most frustrated things about using GitHub Copilot has been the default behavior of inline suggestions. If I were piloting a plane, it would be very annoying if the copilot decided to reach over and grab my controls any time it pleased.

However, there are times when I do wish I had access to these suggestions, so I do not want to disable them completely.

The following shows how to disable inline suggestions by default and invoke it with an explicit keyboard shortcut.

### Disable Inline Suggestions

Add the following to your VSCode user settings. This disables inline suggestions.

```json
{
    // other settings . . .

    "editor.inlineSuggest.enabled": false
}
```

### Trigger Inline Suggestions

When you wish to invoke inline suggestions, use the `editor.action.inlineSuggest.trigger` vscode command.

By default, this is bound to `alt+\` on windows and `opt+\` on mac.

### Accept Inline Suggestions

One the suggestion appears, it can be accepted as normal with `tab`. Or, using the `editor.action.inlineSuggest.acceptNextWord` and `editor.action.inlineSuggest.acceptNextLine` vscode commands, the first parts of the suggestions can be accepted. These are helpful when only the first portion of the inline suggestion is desired.

By default, `editor.action.inlineSuggest.acceptNextWord` is bound to `ctrl+right` on windows and `cmd+right` on mac. But, `editor.action.inlineSuggest.acceptNextLine` has no keybinding, so you will need to make a custom shortcut.

![screen recording of using inline suggestions](/img/fixing-ghcp-inline-suggestions/incremental-inline-suggest.gif)

## Fix Tab Completion with Terminal Suggestions

GitHub Copilot includes suggestions for completing commands when using the integrated terminal in VSCode. By default, pressing `tab` or `enter` will execute the first command, which is annoying because tab completion is deeply ingrained in the muscle memory of anyone who uses a terminal regularly.

![screenshot of inline suggestions](/img/fixing-ghcp-inline-suggestions/inline-suggest.png)

> _No... I don't want to clone a whole repo in my current project..._

Setting the selection mode in user settings to `"never"` will require first navigating to a suggestion using arrow keys.

```json
{
    // other settings . . .

  "terminal.integrated.suggest.selectionMode": "never",
}
```
