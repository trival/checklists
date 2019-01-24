# launch mocha test with debug ability

```json
{
	// Use IntelliSense to learn about possible attributes.
	// Hover to view descriptions of existing attributes.
	// For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
	"version": "0.2.0",
	"configurations": [
		{
			"name": "Run unit tests",
			"type": "node",
			"request": "launch",
			"program": "${workspaceRoot}/node_modules/mocha/bin/_mocha",
			"stopOnEntry": false,
			"env": {
				"NODE_ENV": "test"
			},
			"args": [
				"--no-timeouts",
				"--require",
				"ts-node/register",
				"--require",
				"jsdom-global/register",
				"--require",
				"${workspaceRoot}/test/setupBrowserTests.ts",
				"${workspaceRoot}/test/setupTestsHooks.ts",
				"${workspaceRoot}/app/**/*.spec.ts?(x)"
			],
			"cwd": "${workspaceRoot}",
			"protocol": "inspector"
		}
	]
}
```

here the same from the command line:

```
NODE_ENV=test mocha --require ts-node/register --require jsdom-global/register --require ./test/setupBrowserTests.ts --watch --watch-extensions ts,tsx './test/setupTestsHooks.ts' './app/**/*.spec.ts?(x)'
```
