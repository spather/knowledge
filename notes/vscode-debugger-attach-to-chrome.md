# Attaching the VS Code Debugger to Chrome

1. Quit all instances of Chrome.
2. Run Chrome with remote debugging enabled:

`/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222`

3. Create a VSCode launch configuration (in `.vscode/launch.json`) like:
```
    {
      "name": "Attach to Chrome",
      "port": 9222,
      "request": "attach",
      "type": "pwa-chrome",
      "webRoot": "${workspaceFolder}"
    },
```
