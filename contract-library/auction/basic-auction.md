# Basic

{% tabs %}
{% tab title="auction\_basic.arl" %}
```ocaml
archetype auction

variable max_bid mtez = 0mtz

asset bid identified by incumbent = {
  incumbent : address;
  value : mtez;
}

variable deadline date = 2019-01-01T00:00:00


action place_bid = {

  require {
    c1 : now < deadline;
  }

  effect {
    bid.add({ incumbent = caller; value = transferred });
    max_bid := max (transferred, max_bid)
  }
}


(* Users need to exhibit proof they are not the winner to reclaim their bid *)
action reclaim = {

 require {
   c1 : now < deadline;
   c2 : (let bc = bid.get(caller) in
         bc < max_bid);
 }

 effect {
   transfer bid.get(caller) to caller
 }
}

specification {
  postcondition s1 = {
    forall a in address,
      bid.contains (a) -> balance > bid.get(a).value
  }
}
```
{% endtab %}
{% endtabs %}

