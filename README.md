# Age of Reason

In previous training we defined code easy to reason about as :

* Does not affect or mutate external state (ie : no side effects)
* Does not rely on external state
* Always return same output for a given input
* Have up to date documentation

## Goals of this training

* How a statically typed language with functional programming pattern could help ?
* How Type Driven Design and Test Driven Development could secure developments ?
* Discovery of Reason and Reason-React

## Meet ReasonML

[Reason](https://reasonml.github.io/) is not a new language; it's a new syntax and toolchain powered by the battle-tested language, OCaml. Reason gives OCaml a familiar syntax geared toward JavaScript programmers.

Reason compiles to JavaScript thanks to our partner project, [BuckleScript](https://bucklescript.github.io), which compiles OCaml/Reason into readable JavaScript with smooth interop. Reason also compiles to fast, barebone assembly, thanks to OCaml itself.

### First tour

Open the [try site](https://reasonml.github.io/en/try.html?reason=Q) and try each code bellow, step by step

#### Binding

Variable declaration and assignement use `let` binding :

```OCaml
let greeting : string = "hello";
let score : int = 10;
```

As you can see, syntax looks like Javascript with Flow (~‾▿‾)~

Bidings are immutables :

```OCaml
let greeting : string = "hello";
greeting = "bye"; /* Error : The value greeting is not an instance variable */
```

But you can wrap it with a ref, which is like a box whose content can change.

```OCaml
let score : ref(int) = ref(10);
score := 5;
let five : int = score^;
print_endline(string_of_int(five));
```

However, you may create a new binding of the same name which shadows the previous binding :

```OCaml
let greeting : string = "hello";
print_endline(greeting); /* print hello */
let greeting : string = "bye";
print_endline(greeting); /* print bye */
```

#### Types

The type system is completely "sound". This means that, as long as your code compiles fine, every type guarantees that it's not lying about itself. In a conventional, best-effort type system, just because the type says it's e.g. "an integer that's never null", doesn't mean it's actually never null. In contrast, a pure Reason program has no null bugs.

ReasonML types can be inferred. The type system deduces the types for you even if you don't manually write them down.

```OCaml
let greeting = "hello";
let score = 10;
```

Alias types can refer to a type by a different name. They'll be equivalent:

```OCaml
type scoreType = int;
let score : scoreType = 10;
```

Reason provides two list primitives : List and Array

```OCaml
/* Lists are homogeneous, immutable, fast at prepending items */
let heroes : list(string) = ["Carl", "Rick", "Michonne"];
let moreHeroes = ["Negan", ...heroes]; /* spread operator like javascript */

/* Arrays are mutable, fast at random access & updates, fix-sized on native (flexibly sized on JavaScript) */
let heroesArray : array(string) = [|"Carl", "Rick", "Michonne"|];
let carl : string = heroesArray[0];
heroesArray[0] = "Negan";
```

Product types

```OCaml
/* Tuples are immutable, ordered, fix-sized at creation time heterogeneous */
let nameAndHeart: (string, int) = ("Negan", 10);
type coord3d = (int, int, int);
let warehouseCoord : coord3d = (1, 4, 18);

/* Records are immutable by default and fixed in field names and types */
type hero = {
  heart: int,
  name: string
};
let negan = {
    heart : 10,
    name : "Negan"
}
let name = "Carl";
let carl = {
    name, /* punning like javascript */
    heart : 8
}
```

Variant types

```OCaml
type lifeVariant =
  | Alive
  | Dead
  | Zombi;

let howAreYou = Alive;
```

Alive, Dead and Zombi are called "constructors" (or "tag"). The | bar separates each constructor. A variant's constructors need to be capitalized.

Variant comes with one of the most important features of Reason : **pattern matching**

```ReasonML
let message =
  switch (howAreYou) {
  | Zombi => "Aaaaaarg !"
  | Alive => "Great !"
  | Dead => "!!!"
  };
/* message is "Great!" */
```

#### Functions

Functions are declared with an arrow and return the expression

```OCaml
let add : (int, int) => int = (x, y) => x + y;
let multiply = (x, y) => x * y; /* inference also works on function type */
add(1, 3); /* 4 */
```

ReasonML function are auto-curried. The above multiply is nothing but syntactic sugar for this

```OCaml
let multiply = (x) => (y) => x * y;
```

So Reason functions can automatically be partially called.

```OCaml
let multiply5 = multiply(5);
let ten = multiply5(2);
```

Another great feature is the pipe opertator which can compose function by piping them

```OCaml
let twenty = multiply2(5) |> multiply2;
twenty |> string_of_int |> Js.log;
```

After this overview we will start our first projet [Tennis Kata](./TENNIS.md)
