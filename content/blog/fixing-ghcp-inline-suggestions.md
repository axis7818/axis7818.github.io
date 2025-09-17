+++
title = "Fixing GitHub Copilot Inline Suggestions"
date = "2025-09-17"
description = "A short post on how to control GitHub Copilot's inline suggestions in VSCode."

[taxonomies]
tags = ["Software", "Config", "VSCode", "GitHub Copilot"]
+++

One of the most frustrated things about using GitHub Copilot has been the default behavior of inline suggestions. If I were piloting a plane, it would be very annoying if the copilot decided to reach over and grab my controls any time it pleased.

However, there are times when I do wish I had access to these suggestions, so I do not want to disable them completely.

The following shows how to disable inline suggestions by default and invoke it with an explicit keyboard shortcut.

## Disable Inline Suggestions

Add the following to your VSCode user settings. This disables inline suggestions.

```json
{
    // other settings . . .

    "editor.inlineSuggest.enabled": false
}
```

## Trigger Inline Suggestions

When you wish to invoke inline suggestions, use the `editor.action.inlineSuggest.trigger` vscode command.

By default, this is bound to `alt+\` on windows and `opt+\` on mac.

## Accept Inline Suggestions

One the suggestion appears, it can be accepted as normal with `tab`. Or, using the `editor.action.inlineSuggest.acceptNextWord` and `editor.action.inlineSuggest.acceptNextLine` vscode commands, the first parts of the suggestions can be accepted. These are helpful when only the first portion of the inline suggestion is desired.

By default, `editor.action.inlineSuggest.acceptNextWord` is bound to `ctrl+right` on windows and `cmd+right` on mac. But, `editor.action.inlineSuggest.acceptNextLine` has no keybinding, so you will need to make a custom shortcut.

![screen recording of using inline suggestions](/img/fixing-ghcp-inline-suggestions/incremental-inline-suggest.gif)
