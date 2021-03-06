# Miles

This archetype is a very basic miles management system with add and consume transactions. It may be considered as a simplified version of ERC20.

{% code title="miles.arl" %}
```ocaml
archetype miles

variable[%transferable%] validator : role = @tz1Lc2qBKEWCBeDU8npG6zCeCqpmaegRi6Jg

asset account identified by owner {
  owner  : role;
  amount : int;
}

action add (ow : role, value : int) {
  called by validator
  effect {
    if account.contains(ow) then
      account.update (ow, { amount += value })
    else
      account.add({ owner = ow; amount = 0 })
  }
}

action consume (ow : role, value : int) {
  specification {
    postcondition s1 {
      let some ba = before.account.get(ow) in
      let some a = account.get(ow) in
      ba.amount = a.amount + value
      otherwise true
      otherwise true
    }
  }

  called by validator

  require {
    c1 : account.get(ow).amount >= value;
  }

  effect {
    account.update (ow, { amount -= value })
  }
}

```
{% endcode %}

