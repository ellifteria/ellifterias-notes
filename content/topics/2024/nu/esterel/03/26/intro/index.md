---
title: intro to esterel
summary: introduction to esterel and esterel in racket
template: page
---

## What is Esterel?

Esterel<label for="sidenote--esterelLink"
       class="margin-toggle sidenote-number">
</label>
<input type="checkbox"
       id="sidenote--esterelLink"
       class="margin-toggle"/>
<span class="sidenote">
    <a href="https://en.wikipedia.org/wiki/Esterel">Esterel on Wikipedia</a>
</span>
is a synchronous reactive programming language developed in France.
In Esterel, operations are executed in parallel threads with specific synchronous timing.
Since Esterrel operates similarly to the way circuits do, a compiler was developed to compile Esterel directly to circuits.

Dr. Robby Findler developed a way to run Esterel code in Racket, using [[https://docs.racket-lang.org/esterel/index.html|Esterel in Racket]].

Writing Esterel in Racket is be done using:

```scheme
#lang racket

(require esterel/full)

(esterel
    ...)
```

In Esterel, `signal`s are "variables"; they are `emit`ed by Esterel programs<label for="sidenote--definingSignals"
       class="margin-toggle sidenote-number">
</label>
<input type="checkbox"
       id="sidenote--definingSignals"
       class="margin-toggle"/>
<span class="sidenote">
    `define-signal` defines `signal`s globally while `with-signal` allows you to locally define `signal`s.
</span>
.

```scheme
#lang racket

(require esterel/full)

(define-signal red)

(define forever-red
    (esterel
        (sustain red)))
```

Running this code doesn't visibly do anything.
You need to `react!` to "ask" Esterel if there are any signals `emit`ted.

```scheme
> (react! forever-red)
'#hash((#<signal: red> . #t))
```

To control the timing of when Esterel signals are `emit`ted, `pause` is used.

```scheme
#lang racket

(require esterel/full)

(define-signal red)

(define forever-red
    (esterel
        (pause)
        (sustain red)))
```

```scheme
> (react! forever-red)
'#hash()
> (react! forever-red)
'#hash((#<signal: red> . #t))
```

Since `forever-red` is `pause`d on the first step, there is no signal `emit`ted.
`react!`ing again walks to the next step and causes `red` to be `sustain`ed.

To run multiple threads in parallel, use `par`:

```scheme
#lang racket

(require esterel/full)

(define-signal red)

(define forever-red
    (esterel
        (par
            (sustain red)
            (sustain green))))
```

```scheme
> (react! forever-red)
'#hash((#<signal: green> . #t) (#<signal: red> . #t))
```
