---
description: The one and only
---

# ERC20

The standard may be found at this address:

{% embed url="https://theethereum.wiki/w/index.php/ERC20\_Token\_Standard" %}

Note that the implementation of the allowance data is an asset with a double key: the allower address and the spender address. 

{% tabs %}
{% tab title="erc20.arl" %}
```ocaml
archetype erc20

constant total : nat = 1000000000000000
variable onetoken : nat = 1000000

asset allowance identified by allower spender {
  allower     : address;
  spender     : address;
  amount      : nat = 0;
}

asset tokenHolder identified by holder {
  holder     : address;
  tokens     : nat = 0;
} initialized by {
  { holder = caller; tokens = total }
}

entry dotransfer (dest : pkey<tokenHolder>, value : nat) {

  failif {
    f1 : tokenHolder[caller].tokens < value
  }

  effect {
    tokenHolder.addupdate( dest, { tokens += value });
    tokenHolder.update( caller, { tokens -= value })
  }
}

entry approve(ispender : address, value : nat) {
  require {
    d1 : tokenHolder[caller].tokens >= value;
  }
  effect {
    allowance.addupdate((caller, ispender), { amount += value });
  }
}

entry transferFrom(from_ : address, to_ : address, value : nat) {
  require {
    (* d1: allowance.contains(from_); *)
    d2: allowance[(from_,to_)].spender = caller;
    d3: allowance[(from_,to_)].amount >= value;
    d4: tokenHolder[from_].tokens >= value
  }
  effect {
    (* update allowance *)
    allowance.update((from_,to_), { amount -=  value });

    (* update token *)
    tokenHolder.addupdate(to_,   { tokens += value });
    tokenHolder.update(from_, { tokens -= value });
  }
}
```
{% endtab %}
{% endtabs %}

