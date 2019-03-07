Add following launch configuration to vscode to run ts-node scripts in debug
mode:

```
{
	"name": "Debug",
	"type": "node",
	"request": "launch",
	"args": ["${file}"],
	"runtimeArgs": ["--nolazy", "-r", "ts-node/register", "-r", "dotenv/config"],
	"sourceMaps": true,
	"cwd": "${workspaceRoot}",
	"protocol": "inspector"
}
```

or simple attach to a running process, if it was started with the `--inspect`
option of nodejs

```
{
  "type": "node",
  "request": "attach",
  "name": "Attach",
  "port": 9222,
  "protocol": "inspector"
}
```

or even simpler: In settings, enable option `Debug>Node:Auto Attach`
