+++
title = "Supercharging your Go presentation with Slidev"
date = 2024-09-09

[taxonomies]
categories = ["tech"]
tags = ["slides", "golang", "slidev"]
+++

# Supercharging your Go presentation with Slidev

I do technical presentations pretty often, and I always try to find new ways to make them more interactive and engaging.
There were still some things I didn't try yet, like:
1. Coding my slides. This has always been appealing, but I never managed to take the time to switch from the usual tools I was
   using (Google Slides mostly).
2. Adding some interactive code samples. I saw some presentation with this kind of feature, I was always impressed.

I had to do a new presentation about Go in my current company and I decided that this was the opportunity I was
waiting for to tackle those challenges !

## Coding my slides

I knew it was possible using tools like reveal.js but when I tried to approach it, I found it too close to frontend code
for my taste. I am not a frontend developer, CSS is a mystery to me and I don't want to spend time on it.
But recently, I saw a presentation about [sli.dev](https://sli.dev/) at a tech conference and It clicked !
[sli.dev](https://sli.dev/) is a tool to create presentations with Markdown and optionally Vue components.
From their website:

> Slidev aims to provide flexibility and interactivity for developers to make their presentations even more interesting,
> expressive, and attractive by using the tools and technologies they are already familiar with.

Starting a new presentation is pretty straightforward, you just have to run the following command:

```bash
npx create-slidev
```

Then you can start the development server with:

```bash
npm run dev
```

Boom, you have a working presentation with a default theme and some example slides. Edit the markdown and you are good to go !
If you want to make things a bit more fancy, you can apply a theme or create your own or you can also add some Vue components to your slides.

I won't go into the details of creating a presentation with Slidev, you can find all the information you need on their website.

## Interactive code samples

While creating the presentation I wanted to add some runnable code to the slides
as advertised in the [documentation](https://sli.dev/guide/demos.html#runnable-code).

Unfortunately, this only works by default with JavaScript and TypeScript (runnable in the browser).

But fear not ! We can still hook our custom code runner to make it work with Go.

### Serving a sandboxed Go environment

It's not directly possible to run Go code in the browser (at least not without some heavy lifting like WebAssembly).
But we can still run Go code in a sandboxed environment and expose this through an API that the browser can call.

I found a project called [yaegi](https://github.com/traefik/yaegi) maintained by the Traefik team that allows you to run Go code in a sandboxed environment.
Creating a simple API that runs Go code is pretty straightforward (if you omit the error handling) :

```go
package main

import (
	"bytes"
	"encoding/json"
	"io"
	"log"
	"net/http"

	"github.com/traefik/yaegi/interp"
	"github.com/traefik/yaegi/stdlib"
)

func main() {
	// create a http handler and start the server
	http.HandleFunc("/", handleGoCompileRequest)
	err := http.ListenAndServe(":8080", nil)
	if err != nil {
		log.Panic("couldn't start the server", err)
	}
}

func handleGoCompileRequest(w http.ResponseWriter, r *http.Request) {
	// parse the request
	code, err := io.ReadAll(r.Body)
	if err != nil {
		http.Error(w, "couldn't read the request body", http.StatusBadRequest)
		return
	}
	// compile the go code
	var myStdout, myStderr bytes.Buffer
	i := interp.New(interp.Options{
		Stdout: &myStdout,
		Stderr: &myStderr,
	})
	_ = i.Use(stdlib.Symbols)
	_, err = i.Eval(string(code))
	// send the response
	w.Header().Set("Content-Type", "application/json")
	w.Header().Set("Access-Control-Allow-Origin", "*")
	w.WriteHeader(http.StatusOK)
	responseObject := map[string]string{
		"stdout": myStdout.String(),
		"stderr": myStderr.String(),
	}
	if err != nil {
		responseObject["compilation_error"] = err.Error()
	}
	err = json.NewEncoder(w).Encode(responseObject)
	if err != nil {
		http.Error(w, err.Error(), http.StatusBadRequest)
	}
}
```

Pretty simple right ? This code will start a server that listens on port 8080 and compiles Go code sent in the request body.
It will return the stdout and stderr of the program and an error message if the compilation failed.

Of course there are some security concerns with this approach, **DO NOT PUT THIS IN PRODUCTION**.
You've been warned, but for a simple presentation it should be good enough.

### Mapping the Go code runner in Slidev

Now that we have our Go code runner, we need to instruct Slidev how to use it.
This is done by creating a custom code runner in the `setup/code-runner.js` file.

```typescript
import {defineCodeRunnersSetup} from '@slidev/types'

export default defineCodeRunnersSetup(() => {
    return {
        async go(code, ctx) {
            const result = await executeGoCodeRemotely(code)
            if (result.compilation_error) {
                return {
                    error: "Compilation error: " + result.compilation_error
                }
            }
            if (result.stderr) {
                return {
                    error: "Stderr: " + result.stderr
                }
            }
            return {
                html: result.stdout.replaceAll("\n", "<br>"),
            }
        }
    }
})

interface GoResponse {
    stdout: string
    stderr: string
    compilation_error: string
}

async function executeGoCodeRemotely(code: string): Promise<GoResponse> {
    let request = new Request("http://localhost:8080/", {
        method: "POST",
        body: code,
        cache: "no-cache",
    });
    return fetch(request)
        .then(response => {
            return response.json()
        });
}
```

This code will send the Go code to the server we created earlier and return the stdout, stderr and compilation error if any.
It will then display the stdout in the slide.

Now in your Markdown file you can use a code block with the `{monaco-run}` directive.

For instance:
```go {monaco-run}
import "fmt"

func add(a int, b int) (int, int, int) {
  return a, b, a + b
}

func main() {
  _, _, sum := add(1, 2)
  fmt.Println(sum)
}
```

When starting you presentation you should now see the output of the Go code in the slide.
And you can even change the code live and see the output change !

## Wrapping up

I am pretty happy with the result, I managed to code my slides and add some interactive code samples.
The effect was great, the audience was more engaged and I had a lot of fun creating the presentation.

You can find the code of the `Go 101` presentation, with the code runner and the Go server in my [Github repository](https://github.com/ImFlog/go101). 

What I would like to do next would be to be able to run things like shell commands in the slides.
I think it would be a great way to show some real world examples.

But that's for another time !

Flo.