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
