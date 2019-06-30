# Health care

```text
archetype health_care

constant insurer role = @tz1KksC8RvjUWAbXYJuNrUbontHGor25Cztk
constant patient role = @tz1uNrUbontHGor25CztkKksC8RvjUWAbXYJ

constant fee tez = 100tz
constant deductible tez = 500tz
constant period duration = 30D

variable last_fee date

variable consultation_debt tez = 0tz

asset doctor identified by id = {
    id   : role;
    debt : tez = 0tz;
}

states =
| Created   initial
| Running
| Canceled

transition[%signedbyall [patient; insurer]%] confirm from Created = {
    (*signed by [insrurer; patient]*)
    to Running
    with effect {
        last_fee := now
    }
}

transition cancel from Created = {
    called by insurer or patient
    to Canceled
}

action[%signedbyall [patient; insurer]%] register_doctor (docid : address) = {
    (*signed by [insurer; patient]*)
    require { r1 : state = Running }
    effect {
        doctor.add { id = docid }
    }
}

action declare_consultation (v : tez) = {
    called by doctor  (* <-> require { doctor.contains caller } *)

    require { r2 : state = Running }
    effect {
        doctor.update caller { debt += v };
        consultation_debt += min v deductible
    }
}

action pay_doctor (docid : address) = {
    verification {
      specification idem_balance_pay_doctor = {
        balance = before balance
      }
    }
    called by insurer
    accept transfer
    require { r3 : state = Running }
    effect {
        let debt = (doctor.get docid).debt in
        let decrease = min transferred debt in
        transfer decrease to docid;
        transfer (transferred - decrease) to insurer;
        doctor.update docid { debt -= decrease }
    }
}

action pay_fee = {
    verification {
      specification idem_balance_pay_fee = {
         balance = before balance
      }
    }
    called by patient
    accept transfer
    require { r4 : state = Running }
    effect {
        let nb_periods = (now - last_fee) / period in  (* div is euclidean *)
        let due = nb_periods * fee in
        let decrease = min transferred due in
        transfer decrease to insrurer;
        transfer (transferred - decrease) to patient;
        last_fee := last_fee + period * nb_periods     
    }
}

action pay_consulation = {
    verification {
        specification idem_balance_pay_consultation = {
            balance = before balance
        }
    }
    called by patient
    accept transfer
    require { r5 : state = Running }
    effect {
        let decrease = min (transferred, consultation_debt) in
        transfer decrease to insurer;
        transfer (transferred - decrease) to patient;
        consultation_debt -= decrease
    }
}




```

