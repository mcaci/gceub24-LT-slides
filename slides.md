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
layout: image-right
image: /images/Gophers1.jpeg
backgroundSize: 80%
---

# Leveling up our Go game

Go is a simple language

<v-clicks>

We know how to write simple and effective Go.

And leveraging the simplicity of Go is the key to our Go skills.

</v-clicks>

---
transition: fade-out
layout: fact
---

<v-clicks>

But over the time Go has evolved.

The **standard library** has tools from old and new that can level up our Go game.

Let's make our Go game even stronger with these treasures from the standard library.
</v-clicks>

<!-- 
Additional information -> With this proposal I want to showcase at most three usecases from the standard that can help our daily life while writing Go code:

1) How to manage data transfer with the io package: from io.Copy vs io.ReadAll up to the io.Pipe and io.MultiWriter;
2) How to manage error creation from the fmt package to the errors package, from the verbs "%s", "%v" and "%w" up to the errors.Join;
3) How to manage efficiently slices and maps operations witht the use of the generic functions in the slices and maps packages;

I know it's a lot of content for the lightning talk and I will be very careful at the end in selecting which of these items I will present but I'm confident that it will give to other gophers some inspiration in going out of the box yet still in the standard library to discover all the powerful tools it provides. I plan to use just a slideshow since the time is short.
-->

---
transition: fade-out
---

# Gem 1

How to manage data transfer with the io package: from io.Copy vs io.ReadAll up to the io.Pipe and io.MultiWriter;

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
---

# Gem 2

How to manage error creation from the fmt package to the errors package, from the verbs "%s", "%v" and "%w" up to the errors.Join;

Errors: we all love them, we started creating them with errors.New and fmt.Errorf.

<v-click>
````md magic-move {lines: true}
```go
// A new error
err := errors.New("error in transferring the message")
```

```go
// A new error with fmt.Errorf
err := fmt.Errorf("error in transferring the message %q", msg)
```

```go
// A new error with fmt.Errorf referencing another error (?)
err := fmt.Errorf("error in transferring the message %q", oErr)
```

```go
// A new error with fmt.Errorf wrapping another error 
err := fmt.Errorf("error in transferring the message %w", oErr)
```

```go
// A new error with fmt.Errorf wrapping multiple errors
err := fmt.Errorf("error in transferring the message %w, %w, %w", oErr1, oErr2, oErr3)
```

```go
// A new error with errors.Join wrapping multiple errors
err := errors.Join(oErr1, oErr2, oErr3)
```

````
</v-click>

<v-clicks>
But most commonly with fmt.Errorf you can still see the usage of %s and %v to relate to the error. Not good.

But then again %w was introduced in 2019 (with [v1.13](https://tip.golang.org/doc/go1.13#error_wrapping))... so 4 years ago, it indicates the wrapping of an error, but since early 2023 (with [v1.20](https://tip.golang.org/doc/go1.20#errors)) the possibility to wrap multiple errors was introduced.

Which is great! But how do we do this? `fmt.Errorf("%w: %w: %w", err1, err2, err3)`, `fmt.Errorf("err1 %w; err2 %w; err3 %w", err1, err2, err3)`...

Let's have this solved by `errors.Join(err1, err2, err3)`

It is important to be able the error, but also from the application it is important to have it clear that one error can be one or several others. (application does not care of the formatting)
</v-clicks>

---
transition: fade-out
---

# Gem 3

How to manage efficiently slices and maps operations with the use of the generic functions in the slices and maps packages;

I have been working in an application that works as a proxy server between client sending http requests, manipulates them and forwards them to the intended target. And one of the main job was to manipate an `http.Header` which in Go is simply a `map[string][]string`.

````md magic-move {lines: true}
```go
// http.Header is an alias for map[string][]string
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
func update(h http.Header, toAdd http.Header, toDelete []string) {
  // Copy all key/value pairs from a source map (toAdd) to the destination one (h)
  maps.Copy(h, toAdd)
  // Deletes all k,v pairs where the function yields true
  maps.DeleteFunc(h, func(k, v string)) bool { return slices.Contains(toDelete, k)}
}
```
````

Since [v1.21](https://tip.golang.org/doc/go1.21#library) we can use the maps, slices packages to do operations in maps and slices thanks to generics.

---
transition: fade-out
layout: fact
---

## Leveraging the simplicity of Go makes our applications simpler

<v-click>
And this is how we can improve our Go game.
</v-click>

---
transition: fade-out
layout: image-right
image: /images/Gophers10.jpeg
backgroundSize: 80%
---

# Conclusions

Strive for the same simplicity that the Go strives for 

The standard library has evolved as our code does.

Take the time to check the [standard library](https://pkg.go.dev/std) to find the content of its packages.

Take from it what you need to make your code even simpler.

---
layout: lblue-end
transition: fade-out
---

# Thank you!

<div class="absolute bottom-10">
  <div>Michele Caci</div>
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
