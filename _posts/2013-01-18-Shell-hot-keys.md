---
layout: post
title: Shell hot keys
tags: [Linux Shell]
---
shell 下的常用热键，在bash与zsh测试均可用，分类总结如下：

**Move**:  

+   Ctrl+f forward one character
+   Ctrl+b back one character
+   Escape+f forward one word
+   Escape+b back one word
+   Ctrl+a start of line
+   Ctrl+e end of line

**Search**:

+   ctrl-r does a search in the previously given commands so that you don't have to repeat long command. 
+   Ctrl+p previous line in history
+   Ctrl+n next line in history
+   Ctrl+g cancel (cancels search, goes back to original command)

**Delete**:

+   Ctrl+d delete char to right
+   Ctrl+u delete to start of line from current position (works in bash, in zsh, empty the entire line)
+   Ctrl+k delete to end of line from current position
+   Ctrl+w delete the word before the cursor
+   Escape+d delete the word after the cursor

**Modify**:

+   Ctrl+t Transpose (exchange) the character at the cursor location with the one preceding it.
+   Escape+t Transpose the word at the cursor location with the one preceding it.
+   Escape+l Convert the characters from the cursor location to the end of theword to lowercase.
+   Escape+u Convert the characters from the cursor location to the end of the word to upcase.


**Others**:

+   ctrl-l -- clear screen 
+   ctrl-c -- kills the current command or process. 
+   ctrl-d -- kills the shell. (没有输入时)
+   ctrl-z -- puts the currently running process in background, the process can be brought back to run state by using fg command. 
