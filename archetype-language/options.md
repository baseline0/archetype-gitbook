---
description: >-
  An option is convenient when a variable may or may not have a value of a given
  type.
---

# Options

## Declare option

`some` and`none` are the 2 operators to build an option value.

```javascript
variable receiver : address option = none // to be set later ...

variable message : string option = some("there is a message")
```

## Test option

`issome` and `isnone` are the 2 operators to test whether an option value is none or is some.

```javascript
entry testopt (a : int option) {
  requires {
     r1 : issome(a);
  }
  failif {
     f1 : isnone(a);
  }
  effect {
    ...
  }
}
```

## Get option

`getopt` is the operator to extract the value from a `some` option value. It _fails_ if the value is `none`.

```javascript
effect {
  var a := some("a string");
  if issome(a) then (
    if a.getopt() <> "a string" then fail "getting weird ...";
  );
}
```











