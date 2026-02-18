# Phantom Obfuscator API Usage Examples

This repository demonstrates how to send Lua scripts to the [Phantom Obfuscator API](https://api.phantomdevelopment.org/obf) in **Node.js**, **Python**, **C++**, and **C#**.  

All examples send a Lua script with your `x-api-key` in headers and return the obfuscated code.

---

## Setup

1. Replace `key_here` with your whitelisted key.  
2. Replace the Lua script with your own code.  

---

## Node.js (Node 24+)

```js
const script_key = "key_here"
const lua = `print("hello world")`

const res = await fetch("https://api.phantomdevelopment.org/obf", {
  method: "POST",
  headers: {
    "Content-Type": "text/plain",
    "x-api-key": script_key
  },
  body: lua
})

const out = await res.text()
console.log(out)```

## Python

```Python
import requests

script_key = "key_here"
lua = "print('hello world')"

res = requests.post(
    "https://api.phantomdevelopment.org/obf",
    headers={
        "Content-Type": "text/plain",
        "x-api-key": script_key
    },
    data=lua
)

if res.ok:
    print(res.text)
else:
    print(f"Error {res.status_code}: {res.text}")```
