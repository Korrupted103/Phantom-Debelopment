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
console.log(out)
```

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
    print(f"Error {res.status_code}: {res.text}")
```

## C++
```cpp
#include <iostream>
#include <string>
#include <curl/curl.h>

size_t write_callback(void* ptr, size_t size, size_t nmemb, void* userdata){
    std::string* s = (std::string*)userdata;
    s->append((char*)ptr, size * nmemb);
    return size * nmemb;
}

int main(){
    std::string script_key = "key_here";
    std::string lua = "print('hello world')";
    std::string response;

    CURL* curl = curl_easy_init();
    if(curl){
        struct curl_slist* headers = nullptr;
        headers = curl_slist_append(headers, ("x-api-key: " + script_key).c_str());
        headers = curl_slist_append(headers, "Content-Type: text/plain");

        curl_easy_setopt(curl, CURLOPT_URL, "https://api.phantomdevelopment.org/obf");
        curl_easy_setopt(curl, CURLOPT_HTTPHEADER, headers);
        curl_easy_setopt(curl, CURLOPT_POSTFIELDS, lua.c_str());
        curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, write_callback);
        curl_easy_setopt(curl, CURLOPT_WRITEDATA, &response);

        CURLcode res = curl_easy_perform(curl);
        if(res != CURLE_OK) std::cerr << "Error: " << curl_easy_strerror(res) << "\n";
        else std::cout << response << "\n";

        curl_slist_free_all(headers);
        curl_easy_cleanup(curl);
    }
    return 0;
}
```
## C#
```C#
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

class Program {
    static async Task Main() {
        string script_key = "key_here";
        string lua = "print('hello world')";
        using var client = new HttpClient();
        var request = new HttpRequestMessage(HttpMethod.Post, "https://api.phantomdevelopment.org/obf") {
            Content = new StringContent(lua, Encoding.UTF8, "text/plain")
        };
        request.Headers.Add("x-api-key", script_key);

        var response = await client.SendAsync(request);
        string outLua = await response.Content.ReadAsStringAsync();
        Console.WriteLine(outLua);
    }
}
```
