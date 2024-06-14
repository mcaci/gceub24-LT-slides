---
theme: seriph
background: /images/Gems1.jpeg
title: Step up your Go game with these few simple gems
info: |
  ## Step up your Go game with these few simple gems
  Slides for GopherConEUb 2024
class: text-center
layout: cover
highlighter: shiki
drawings:
  persist: false
transition: slide-left
# enable MDC Syntax: https://sli.dev/guide/syntax#mdc-syntax
mdc: true
---

# Step up your Go game with these few simple gems

<div class="absolute bottom-10 text-left">
    <div>Michele Caci</div>
    <div>Senior Software Engineer at Amadeus</div>
    <div class="flex m-0 gap-1">
      <a href="https://github.com/mcaci" target="_blank" alt="Michele's GitHub" title="Michele's GitHub"
        class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
        <carbon-logo-github />
      </a>
      <a href="https://x.com/goMicheleCaci" target="_blank" alt="Michele's X" title="Michele's X"
        class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
        <carbon-logo-x />
      </a>
      <a href="https://www.linkedin.com/in/michele-caci-47770132/" target="_blank" alt="Michele's Linkedin" title="Michele's Linkedin"
        class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
        <carbon-logo-linkedin />
      </a>
    </div>
</div>

<!-- 
Bio: I'm Michele, Italian from Sicily, I am a passionate Gopher since 2018 and before then I used to work in Java, Scala and C++. I always like make side projects and develop new things when time let's me :D. Besides programming, I enjoy swimming, cooking and learning languages; currently, I'm learning Japanese: Yoroshiku onegai shimasu! 
-->

---
transition: fade-out
layout: fact
class: 'text-white bg-#00ADD8 font-size-10'
---

Go is a simple language

---
transition: fade-out
layout: image-right
image: /images/Gophers1.jpeg
backgroundSize: 80%
---

# About Go's simplicity

One of Go's strengths

<v-clicks>

- Go is simple to learn
- Go is simple to write
- Go is simple to read
</v-clicks>

<v-clicks>

Leveraging the simplicity of Go is the key to improving our Go skills.

There have already been many discussions about this topic:
</v-clicks>

<v-clicks>

- Rob Pike's [talk](https://www.youtube.com/watch?v=rFejpH_tAHM&t=1s): Simplicity is Complicated;
- Go Time's episode [#296](https://changelog.com/gotime/296): Principles of simplicity;
</v-clicks>

<v-click>But today I'm going to take Go's simplicity from a new angle.</v-click>

---
transition: fade-out
layout: lblue-fact
---

The evolution of Go code over time

<!-- 
Both related to our codebase, especially from my personal experience, and to the standard library.

The **standard library** has tools from old and new that can level up our Go game.

Let's make our Go game even stronger with these treasures from the standard library.

Additional information -> With this proposal I want to showcase at most three usecases from the standard that can help our daily life while writing Go code:

1) How to manage data transfer with the io package: from io.Copy vs io.ReadAll up to the io.Pipe and io.MultiWriter;
2) How to manage error creation from the fmt package to the errors package, from the verbs "%s", "%v" and "%w" up to the errors.Join;
3) How to manage efficiently slices and maps operations witht the use of the generic functions in the slices and maps packages;

I know it's a lot of content for the lightning talk and I will be very careful at the end in selecting which of these items I will present but I'm confident that it will give to other gophers some inspiration in going out of the box yet still in the standard library to discover all the powerful tools it provides. I plan to use just a slideshow since the time is short.
-->

---
transition: fade-out
---

# Example #1

Tranferring data

<v-click>
Let's see the transfer a message to one destination chat application
</v-click>

<v-click>

````md magic-move {lines: true}
```go {all|2}
// Read all input then write it
b, err := io.ReadAll(in)
if err != nil {
  return err
}
w.Write(b)
```


```go {2|all}
// copy the input from the src to the dst
_, err := io.Copy(dst, src)
if err != nil {
  return err
}
```
````
</v-click>

<v-click>
How about transferring the message to multiple destinations?
</v-click>

<v-click>
````md magic-move {lines: true}
```go {all|2|all}
// Read all input then write it
b, err := io.ReadAll(in)
if err != nil {
  return err
}
w1.Write(b)
w2.Write(b)
```


```go {all|2|all}
// list all writers you need to write to with io.MultiWriter
dsts := io.MultiWriter(dst1, dst2, dst3)
// then copy the input from the src to the dst
_, err := io.Copy(dsts, src)
if err != nil {
  return err
}
```
````
</v-click>

<!-- Imagine I'm writing a chat application and I want to transfer a message to a person, a common way to do it is to read it and transfer it again -->

---
transition: fade-out
layout: lblue-fact
---

Gem #1: use io.Copy when transferring data

---
transition: fade-out
---

# Example #2

Error creation and wrapping

<v-click>Let's see how errors can be created in Go code</v-click>

<v-click>
````md magic-move {lines: true}
```go
// A new error
err := errors.New("error in transferring the message")
```

```go
// A new error with fmt.Errorf
err := fmt.Errorf("error in transferring the message: %s", msg)
```

```go
// A new error with fmt.Errorf referencing another error by error message (%s, %v or %q)
err := fmt.Errorf("error in transferring the message: %s", oErr.Error())
```
````
</v-click>

<v-clicks>

An application gets more complex if you need to bind the way errors are handled with its text message.

- Unit tests broken because the error message was not the one expected

_A human cares about the error message, an application cares more about what type of error it is_

To our help, error wrapping was released with Go [v1.13](https://tip.golang.org/doc/go1.13#error_wrapping) (Sep 2019).

And more recently, multi-error wrapping with Go [v1.20](https://tip.golang.org/doc/go1.20#errors) (Feb 2023).
</v-clicks>

<v-click>
````md magic-move {lines: true}
```go
// A new error with fmt.Errorf wrapping another error 
err := fmt.Errorf("error in transferring the message: %w", oErr)
```

```go
// A new error with fmt.Errorf wrapping multiple errors
err := fmt.Errorf("error in transferring the message: %w, %w, %w", oErr1, oErr2, oErr3)
```

```go
// A new error with errors.Join wrapping multiple errors
err := errors.Join(oErr1, oErr2, oErr3)
```
````
</v-click>

<v-click>

With wrapping you can use _errors.As_ and _errors.Is_ to handle errors in a simpler and safer way.
</v-click>

<!-- Errors: we all love them, we started creating them with errors.New and fmt.Errorf. -->

---
transition: fade-out
layout: lblue-fact
---

Gem #2: wrap errors with %w or errors.Join

<v-click>
<div class="font-size-5">*changing from %[s|q|v] to %w is transparent</div>
</v-click>
<v-click>
<div class="font-size-5">most of the times</div>
</v-click>


<!-- and if you see fmt.Errorf still using %s to refer to error messages translate them to wrapped errors with no harm -->

---
transition: fade-out
---

# Example #3

Slice and maps operations and generics

<v-clicks>

Let's see the example of a proxy server that has to manipulate an input header from an HTTP request by adding some custom headers and removing others.

````md magic-move {lines: true}
```go
// http.Header is an alias for map[string][]string
// update updates the header h with some headers to add and some keys to delete
func update(h http.Header, toAdd http.Header, toDelete []string) {
  for key, values := range toAdd {
    for _, value := range values {
      h.Add(key, value)
    }
  }
  for _, key := range toDelete {
    delete(h[key])
  }
}
```

```go
// http.Header is an alias for map[string][]string
// update updates the header h with some headers to add and some keys to delete
func update(h http.Header, toAdd http.Header, toDelete []string) {
  // Copy all key/value pairs from a source map (toAdd) to the destination one (h)
  maps.Copy(h, toAdd)
  // Deletes all k,v pairs where the function yields true
  maps.DeleteFunc(h, func(k, v string)) bool { return slices.Contains(toDelete, k)}
}
```
````

Since Go [v1.21](https://tip.golang.org/doc/go1.21#library) (Aug 2023) and the introduction of generics, we can use the _maps_ and _slices_ packages to do operations on any kind of map or slice.

This results in less loops to read and more straightforward code.
</v-clicks>


<!-- 
I have been working in an application that works as a proxy server between client sending http requests, manipulates them and forwards them to the intended target. And one of the main job was to manipate an `http.Header` which in Go is simply a `map[string][]string`.
-->

---
transition: fade-out
layout: lblue-fact
---

Gem #3: use maps and slices packages
<v-click>
<div class="font-size-5">and reduce the number of loops you'll have to do on them</div>
</v-click>

---
transition: fade-out
layout: image-right
image: /images/Gophers10.jpeg
backgroundSize: 80%
---

# Conclusions

Strive for the same simplicity that Go strives for 

<v-clicks>

The standard library evolves.

Our code should follow as well.

From time to time have a look at:

- the [standard library](https://pkg.go.dev/std) packages
- the [release notes](https://tip.golang.org/doc/devel/release)

These are the places where we'll find the most valuable gems to make our Go code simpler
</v-clicks>

---
layout: fact
transition: fade-out
class: "font-size-7.8"
---

And making our code simpler is how we step up our Go game!

---
layout: lblue-end
transition: fade-out
---

<div class="text-white font-size-10">
Thank you very much!
</div>

<div class="absolute bottom-10">
  <div  class="text-white">Michele Caci</div>
  <div class="flex m-0 gap-1">
    <a href="https://github.com/mcaci" target="_blank" alt="Michele's GitHub" title="Michele's GitHub"
      class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
      <carbon-logo-github />
    </a>
    <a href="https://x.com/goMicheleCaci" target="_blank" alt="Michele's X" title="Michele's X"
      class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
      <carbon-logo-x />
    </a>
    <a href="https://www.linkedin.com/in/michele-caci-47770132/" target="_blank" alt="Michele's Linkedin" title="Michele's Linkedin"
      class="text-xl slidev-icon-btn opacity-50 !border-none !hover:text-white">
      <carbon-logo-linkedin />
    </a>
  </div>
</div>

---
transition: fade-out
---
