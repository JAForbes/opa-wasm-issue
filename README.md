# opa-wasm-issue
A small reproducible test case for issues I'm having with wasm builds of rego


## Problem

I get different output depending on if I'm running a wasm bundle or running the server against raw policies in the CWD.

## Repro

```bash
$ opa version

Version: 0.51.0
Build Commit: 1f52ea1908181bf2ffa4a742f87f4838f1403728
Build Timestamp: 2023-03-31T13:44:46Z
Build Hostname: c9f079ef8af2
Go Version: go1.20.2
Platform: linux/amd64
WebAssembly: unavailable
```

Start the server:

```
opa run -s .
```

Run the http test from `test.http` (I use VSCode Rest Client extension, but you can do a curl if you like)

```http
POST http://localhost:8181/v1/data

{ "input": {} }
```

I get back this result:

```json
{
  "result": {
    "wasm": {
      "common": {
        "utils": {}
      },
      "test": {
        "main": {
          "a": 18,
          "d": 18
        }
      }
    }
  }
}
```

Now if I create a wasm bundle:

```bash
opa build -b . -t wasm -e 'wasm/test/main'
```

Then run the server with the wasm bundle:

```bash
opa run -s bundle.tar.gz 
```

Then I get back this result:

```json
{
  "result": {
    "wasm": {
      "common": {
        "utils": {}
      },
      "test": {
        "main": {
          "d": 18
        }
      }
    }
  }
}
```

Notice the `a` property has disappeared.