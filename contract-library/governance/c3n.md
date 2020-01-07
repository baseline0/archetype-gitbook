# C3N

Last month saw the first smart contract deployed on Tezos by a government authority \(French cybercrime unit\)

{% embed url="https://beincrypto.com/tezos-smart-contracts-used-by-french-army-since-september/" %}

Below is the Michelson of the contract \(see it on [better call dev](https://better-call.dev/main/KT1Gbu1Gm2U47Pmq9VP7ZMy3ZLKecodquAh4/script)\): 

```text
{
{ { DUP ; CAR ; DIP { CDR } } ; { DUP ; CAR @newadmin ; DIP { CDR } } ; DIP { { DUP ; CAR @oldhash ; DIP { CDR @newhash } } } ; DIP { DIP { DIP { { DUP ; CAR @storedadmin ; DIP { CDR @storedhash } } } } } } ;
SWAP ;
{ DIP { DIP { DIP { SWAP } } } } ;
{ DIP { DIP { SWAP } } } ;
DIP { SWAP } ;
{ DIP { DIP { DIP { SWAP } } } } ;
{ DIP { DIP { SWAP ; DUP ; DIP { SWAP } } } } ;
{
{ COMPARE ; EQ } ;
IF { }
$ELSE { { UNIT ; FAILWITH } }
} ;
SENDER ;
SWAP ;
{ DIP { DIP { PUSH @admin bool false } } } ;
ITER { DIP { DUP } ; { COMPARE ; EQ } ; SWAP ; DIP { OR @admin } } ;
DROP ;
{
IF { }
$ELSE { { UNIT ; FAILWITH } }
} ;
IF_NONE { }
$ELSE { DIP { DROP } } ;
NIL operation ;
{ DIP { PAIR %admin %hash } ; PAIR %op }
}
```

For those who are not fluent in stack machine, here is the transcription to Archetype:

```text
archetype c3n

asset admins {
  addr : address;
}
variable hash : string = "..." (* to set to initial value *)
action register (newadmins : admins collection option,
                 oldhash   : string,
                 newhash   : string) {
  require {
    r1 : oldhash = hash;
    r2 : admins.contains(caller);
  }
  effect {
    hash := newhash;
    let some newa = newadmins in
      admins := newa
    otherwise
      ()
  }
}

```

Quite straightforward, isn’t it?

