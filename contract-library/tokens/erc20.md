---
description: The one and only
---

# ERC20

The standard may be found at this address:

{% embed url="https://theethereum.wiki/w/index.php/ERC20\_Token\_Standard" %}

{% tabs %}
{% tab title="erc20.arl" %}
```ocaml
archetype erc20

constant total : int = 1000000000000000
variable onetoken : int = 1000000
with {
  i0: total > 0
}

asset allowance identified by allower spender {
  allower     : address;
  spender     : address;
  amount      : int = 0;
} with {
  i2 : amount > 0;
}

asset tokenHolder identified by holder {
  holder     : address;
  tokens     : int = 0;
} with {
  i1: tokens >= 0;
} initialized by {
  { holder = caller; tokens = total }
}

entry dotransfer (dest : pkey<tokenHolder>, value : int) {

  failif {
    f0 : value < 0;
    f1 : tokenHolder[caller].tokens < value
  }

  effect {
    tokenHolder.addupdate( dest, { tokens += value });
    tokenHolder.update( caller, { tokens -= value })
  }
}

entry approve(ispender : address, value : int) {
  require {
    d1 : tokenHolder[caller].tokens >= value;
  }
  failif {
    f2 : value <= 0;
  }
  effect {
    allowance.addupdate((caller, ispender), { amount += value });
  }
}

entry transferFrom(from_ : address, to_ : address, value : int) {
  require {
    (* d1: allowance.contains(from_); *)
    d2: allowance[(from_,to_)].spender = caller;
    d3: allowance[(from_,to_)].amount >= value;
  }

  failif {
    f3 : value < 0;
    f4 : tokenHolder[from_].tokens < value
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

