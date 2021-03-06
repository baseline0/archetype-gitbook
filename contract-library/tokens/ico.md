---
description: A real life ico process
---

# ICO

This is the archetype version of the ICO process set up by [BCDiploma](https://www.bcdiploma.com/) and described at the following address:

{% embed url="https://github.com/VinceBCD/BCDiploma/blob/master/sources/BCDT/contracts/BCDToken/" %}

```ocaml
archetype[%erc20%] ico

constant symbol : string = "BCDT"

constant decimals : int  = 18

(* contribution thresholds *)
variable[%mutable (owner, (state = Init))%] min_contribution : tez = 1tz
variable[%mutable (owner, (state = Init))%] max_contribution_silver : tez = 10tz

(* bcd token data *)
variable[%mutable (owner, (state = Init))%] max_bcd_to_sell : tez = 100000000tz
variable[%mutable (owner, (state = Init))%] exchange_rate_bcd_tez : int = 13000

(* round caps *)
variable[%mutable (owner, (state = Init))%] soft_cap : tez = 1800tz
variable[%mutable (owner, (state = Init))%] presales_cap : tez = 1800tz
variable[%mutable (owner, (state = Init))%] round1_cap : tez = 3600tz
(* presales_cap + 1600 *)

(* Number tokens sent, eth raised, ... *)
variable nb_bcd_sold : tez = 0tz
variable nb_tez_raised : tez = 0tz

(* Roles *)

variable[%transferable%] owner : role = @tz1_owner

variable whitelister : role = @tz1Lc2qBKEWCBeDU8npG6zCeCqpmaegRi6Jg

variable reserve : role = @tz1bfVgcJC4ukaQSHUe1EbrUd5SekXeP9CWk

variable community : role = @tz1iawHeddgggn6P5r5jtq2wDRqcJVksGVSa

(* contributor *)

enum whitelist =
 | Silver
 | Gold

asset[@add @remove @update owner (state = Init)]
     contributor identified by id {
   id           : address;
   wlist        : whitelist;
   contrib      : tez = 0tz;
}

enum gstate =
| Init initial     (* ICO isn't started yet, initial state *)
| PresaleRunning   (* Presale has started *)
| PresaleFinished  (* Presale has ended   *)
| Round1Running    (* Round 1 has started *)
| Round1Finished   (* Round 1 has ended   *)
| Round2Running    (* Round 2 has started *)
| Round2Finished   (* Round 2 has ended   *)

variable vstate : gstate = Init

function is_running () : bool {
  return
    match vstate with
    | PresaleRunning | Round1Running | Round2Running -> true
    | _ -> false
    end
}

function get_rate () : rational {
  let coeff : rational =
    match vstate with
    | PresaleRunning  -> 1.2
    | Round1Running   -> 1.1
    | _               -> 1
    end
  in
  return coeff * exchange_rate_bcd_tez
}

function get_remaining_tez_to_raise () : tez {
  return
    match vstate with
    | PresaleRunning | PresaleFinished -> presales_cap - nb_tez_raised
    | Round1Running   | Round1Finished   -> round1_cap - nb_tez_raised
    | _ -> (
      let remaining_bcd : tez = max_bcd_to_sell - nb_bcd_sold in
      remaining_bcd / exchange_rate_bcd_tez)
    end
}

function transition_to_finished () : gstate {
  return
    match vstate with
    | PresaleRunning  -> PresaleFinished
    | Round1Running   -> Round1Finished
    | Round1Finished  -> Round2Running
    | _               -> Round2Finished
    end
}

action contribute () {

  (* specification {
    postcondition p1 {
        let some c = contributor.get(caller) in
        let some c_old = before.contributor.get(caller) in
        transferred = transferred_to(caller) + c.contrib - c_old.contrib
        otherwise true
        otherwise true
    }
  } *)

  require {
     c1 : contributor.contains(caller);
     c2 : is_running ();
     c3 : transferred >= min_contribution;
     c4 : (let c = contributor.get(caller) in
           not (c.wlist = Silver and transferred >= max_contribution_silver));
  }

  effect {
    let c = contributor.get(caller) in
    (* cap contribution to max_contrib if necessary *)
    let lcontrib = transferred in
    if    c.wlist = Silver
      and c.contrib + lcontrib >= max_contribution_silver
    then lcontrib := max_contribution_silver - c.contrib;
    (* cap contribution to round cap if necessary *)
    let remaining_tez : tez = get_remaining_tez_to_raise () in
    if remaining_tez <= lcontrib
    then (
      lcontrib := remaining_tez;
      vstate := transition_to_finished ()
    );
    (* convert contribution to nb of bcd tokens *)
    let rate = get_rate () in
    let nb_tokens : tez = lcontrib * rate in
    (* update ico stats *)
    nb_bcd_sold   += nb_tokens;
    nb_tez_raised += lcontrib;
    (* update caller's contribution *)
    c.contrib     += lcontrib;
    (* syntaxic sugar for
       contributor.update caller { contrib += contrib } *)
    if lcontrib <= transferred
    then transfer (transferred - lcontrib) to caller
  }
}

(* the onlyonce extension specifies that withdraw action can be
  executed only once, that is a contributor can withdraw only once. *)

action[%onlyonce%] withdraw () {
  require {
    c5 : vstate = Round2Finished;
    c6 : nb_tez_raised <= soft_cap;
    c7 : contributor.get(caller).contrib > 0tz;
  }

  effect {
    let c = contributor.get(caller) in
    transfer c.contrib to caller;
    (* do not forget this *)
    c.contrib := 0tz
  }
}

```

