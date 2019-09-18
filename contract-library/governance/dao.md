# Mini DAO

```ocaml
archetype mini_dao

asset shares identified by addr = {
    addr : address;
    value : mtez;
}

action withdraw = {
    specification {
        postcondition p1 = {
            balance = before.balance - shares.get(caller).value
        }
    }
    require {
        r1 : shares.get(caller).value > 0;
        r2 : balance >= shares.get(caller).value;
    }
    effect {
        let v = shares.get(caller).value in
        transfer v to caller;
        shares.update (caller, { value = 0mtz })
    }
}
```

