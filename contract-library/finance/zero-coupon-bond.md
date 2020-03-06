# Zero coupon bond

This contract is the archetype version of the contract taken from the [Findel paper](http://orbilu.uni.lu/handle/10993/30975) \(Financial Derivatives Language\).

Suppose Alice sells to Bob a zero-coupon \(i.e., paying no interest\) bond that pays $11 in one year for $10.

The findel expression below describes this contract:

```text
And ( Give( Scale( 10; One( USD ))); At( now+1 years; Scale( 11 ; One( USD ))))
```

{% code title="zero\_coupon\_bond.arl" %}
```ocaml
archetype zero_coupon_bond

variable issuer : role  = @tz1bfVgcJC4ukaQSHUe1EbrUd5SekXeP9CWk (* seller 'Alice' *)

variable owner : role = @tz1Lc2qBKEWCBeDU8npG6zCeCqpmaegRi6Jg
(* buyer 'Bob'; receives 11 tez in one-year *)


variable price : tez = 10tz

variable payment : tez = 1.1 * price

variable maturity : date = 2020-12-31

states =
  | Created initial
  | Confirmed (* owner has purchased bond *)
  | Repaid    (* issuer has transferred payment to contract *)
  | Collected (* owner has collected payment *)

transition confirm () {
  specification {
    postcondition s1 {
      balance = 0tz
    }
  }

  from Created
  to Confirmed
  when { transferred = price }
  with effect {
    maturity := now + 365d;
    transfer price to issuer
  }
}

transition repay () {
  called by issuer

  from Confirmed
  to Repaid when { transferred = payment }
}

transition collect () {
  called by owner

  from Repaid
  to Collected
  when { now >= maturity }
  with effect {
    if balance >= payment
    then transfer balance to owner
  }
}

```
{% endcode %}

