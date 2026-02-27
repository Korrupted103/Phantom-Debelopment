Ok lets update this so it reads input.lua writes output.lua 

So modify examples

# Phantom Obfuscator API Usage Examples

This repository demonstrates how to send Lua scripts to the [Phantom Obfuscator API](https://api.phantomdevelopments.org/obf) in **Node.js**, **Python**, **C++**, and **C#**.  

All examples send a Lua script with your `x-api-key` in headers and return the obfuscated code.

---

## Setup

1. Replace `key_here` with your whitelisted key.  
2. Replace the Lua script with your own code.  

---

## Node.js (Node 24+)

```js
const fs = require('fs');

const script_key = "key_here";
const lua = fs.readFileSync('input.lua', 'utf8');

fetch("https://api.phantomdevelopments.org/obf", {
  method: "POST",
  headers: { "Content-Type": "text/plain", "x-api-key": script_key },
  body: lua
})
.then(r => r.text())
.then(data => fs.writeFileSync('output.lua', data))
.catch(console.error);
```

## Python

```Python
import requests

script_key = "key_here"

with open("input.lua", "r") as f:
    lua = f.read()

res = requests.post(
    "https://api.phantomdevelopments.org/obf",
    headers={
        "Content-Type": "text/plain",
        "x-api-key": script_key
    },
    data=lua
)

if res.ok:
    with open("output.lua", "w") as f:
        f.write(res.text)
else:
    print(f"Error {res.status_code}: {res.text}")
```

## Cpp
```cpp
#include <iostream>
#include <fstream>
#include <string>
#include <sstream>
#include <curl/curl.h>

size_t write_callback(void* ptr, size_t size, size_t nmemb, void* userdata){
    std::string* s = (std::string*)userdata;
    s->append((char*)ptr, size * nmemb);
    return size * nmemb;
}

int main(){
    std::string script_key = "key_here";
    
    std::ifstream t("input.lua");
    std::stringstream buffer;
    buffer << t.rdbuf();
    std::string lua = buffer.str();
    
    std::string response;

    CURL* curl = curl_easy_init();
    if(curl){
        struct curl_slist* headers = nullptr;
        headers = curl_slist_append(headers, ("x-api-key: " + script_key).c_str());
        headers = curl_slist_append(headers, "Content-Type: text/plain");

        curl_easy_setopt(curl, CURLOPT_URL, "https://api.phantomdevelopments.org/obf");
        curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
        curl_easy_setopt(curl, CURLOPT_POSTFIELDS, lua.c_str());
        curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, write_callback);
        curl_easy_setopt(curl, CURLOPT_WRITEDATA, &response);

        CURLcode res = curl_easy_perform(curl);
        if(res == CURLE_OK){
            std::ofstream out("output.lua");
            out << response;
            out.close();
        }

        curl_slist_free_all(headers);
        curl_easy_cleanup(curl);
    }
    return 0;
}

```
## Csharp
```C#
using System;
using System.IO;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

class Program {
    static async Task Main() {
        string script_key = "key_here";
        string lua = File.ReadAllText("input.lua");

        using var client = new HttpClient();
        var request = new HttpRequestMessage(HttpMethod.Post, "https://api.phantomdevelopments.org/obf") {
            Content = new StringContent(lua, Encoding.UTF8, "text/plain")
        };
        request.Headers.Add("x-api-key", script_key);

        var response = await client.SendAsync(request);
        if (response.IsSuccessStatusCode) {
            string outLua = await response.Content.ReadAsStringAsync();
            File.WriteAllText("output.lua", outLua);
        }
    }
}
```
