# Project instructions

- Put Python checks in a script file in the scratchpad (written with the Write tool or a quoted heredoc). Never run regex code through `py -c`.
- Test pages through a local HTTP server with a fixed free port and `autoPort: false`; the browser pane cannot load `file://` pages with relative images.
- On CRLF files, do not use `sed -i` in Git Bash; it writes LF. Use the Edit tool or a Python script.
