# Transfers, contract calls

## Transfer

The `transfer` instruction is used to transfer Tezis to an account.

```javascript
effect {
  transfer 1tz to coder;
}
```

## Calling a contract

It is possible to call a smart contract with the `transfer` instruction.

### Entry points

Say for example you want to call the `add_value` entry of the following contract: 

```javascript
archetype contract_called

variable v : int = 0

entry add_value(a : int, b : int) {
  effect {
    v := a + b
  }
}
```

In the calling contract, you need to:

* declare the external entry points with the `entries` keyword
* declare a variable typed with this signature and give it the address value of the called contract 

```javascript
entries {
   <int * int> add_value
}

variable c : address = @KT1RNB9PXsnp7KMkiMrWNMRzPjuefSWojBAm
```

The contract may then be called with the `transfer` instruction:

```javascript
effect {
    transfer 0tz to c call add_value(3,2);
}
```

Note that the contract interface may _not_ list the entire target contract entry points. It may even declare non existing entries as long as they are not called, even if it is not recommended for parsimony reason.

### Inlined entry point

It is possible to declare a single entry point to a contract. The type for an entry point is `entrysig` followed by its signature. Operator `entrypoint` builds an entry point with the contract address and the name of the entry point.

Say for example you want to call the `createtoken` entrypoint of the following contract:

```javascript
archetype token

constant admin : address = @tz1Lc2qBKEWCBeDU8npG6zCeCqpmaegRi6Jg

asset holder {
  id : address;
  nb : nat = 0;
}

entry createtoken(h : pkey<holder>, n : nat) {
   called by admin
   effect {
      holder.addupdate(h, { nb += n });
   }
}
```

Then you may call the `createtoken` entry with the following:

```javascript
effect {
   var ctopt : option<entrysig<address * nat>> = entrypoint(token,"createtoken");
   if issome(ct) then (
      var ct = getopt(ctopt);
      transfer 0tz to entry ct(anaddress,10)
   )
   else fail("no contract found or no entrypoint 'createtoken' found")
}
```

Note that the type of `ctopt` \(line 2 above\) is _mandatory_, since the typer cannot infer its type from `entrypoint` operator. 

### Callbacks to _self_

It is possible to pass a callback to the current contract as an argument of a call to an external contract. 

This is necessary when you want for example to implement the _two-way-inter-contract_ _invocation_ mechanism presented in the article below:

{% embed url="https://medium.com/protofire-blog/enabling-smart-contract-interaction-in-tezos-with-ligo-functions-and-cps-e3ea2aa49336" %}

In archetype, the `self` keyword is used to refer to entry points of the current contract.

The archetype version of the _sender_ contract \(see article above\):

```javascript
archetype sender

variable bar : int = 0

entry getBar (cb : entrysig<int>) { transfer 0tz to entry cb(bar) }

entry setBar (b : int) { bar := b }
```

The archetype version of the _inspector_ contract \(see article above\):

```javascript
archetype inspector

variable foo : int = 0

entries {
  <entrysig<int>> getBar
}

entry getFoo(asender : address) { 
  transfert 0tz to asender call getBar(self.setFoo) 
}

entry setFoo(v : int) { foo := v }
```



