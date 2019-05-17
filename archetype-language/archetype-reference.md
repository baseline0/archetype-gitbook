# Reference

{% hint style="warning" %}
coming soon
{% endhint %}

## Grammar

`
 <loc(X)> ::= X

<snl(separator, X)> ::= X
                      | X separator
                      | X separator <snl(separator, X)>

<snl2(separator, X)> ::= X separator X
                       | X separator <snl2(separator, X)>

<paren(X)> ::= LPAREN X RPAREN

<braced(X)> ::= LBRACE X RBRACE

<bracket(X)> ::= LBRACKET X RBRACKET

<start_expr> ::= <expr> EOF
               | <loc(error)>

<main> ::= <loc(<archetype_r>)>
         | <loc(error)>

<archetype_r> ::= <implementation_archetype> EOF
                | <archetype_extension> EOF

<archetype_extension> ::= ARCHETYPE EXTENSION <ident> <paren(<declarations>)>
                          EQUAL <braced(<declarations>)>

<implementation_archetype> ::= <declarations>

<declarations> ::= <declaration>+

<declaration> ::= <loc(<declaration_r>)>

<declaration_r> ::= <archetype>
                  | <constant>
                  | <variable>
                  | <enum>
                  | <states>
                  | <asset>
                  | <action>
                  | <transition>
                  | <dextension>
                  | <namespace>
                  | <contract>
                  | <function_decl>
                  | <verification_decl>

<archetype> ::= ARCHETYPE [<extensions>] <ident>

<vc_decl(X)> ::= X [<extensions>] <ident> <type_t> [<value_options>]
                 [<default_value>]

<constant> ::= <vc_decl(CONSTANT)>

<variable> ::= <vc_decl(VARIABLE)>

<value_options> ::= <value_option>+

<value_option> ::= <from_value>
                 | <to_value>

<default_value> ::= EQUAL <expr>

<from_value> ::= FROM <qualid>

<to_value> ::= TO <qualid>

<dextension> ::= PERCENT <ident> [nonempty_list(<simple_expr>)]

<extensions> ::= <extension>+

<extension> ::= <loc(<extension_r>)>

<extension_r> ::= LBRACKETPERCENT <ident> [<simple_expr>+] RBRACKET

<namespace> ::= NAMESPACE <ident> <braced(<declarations>)>

<contract> ::= CONTRACT [<extensions>] <ident> EQUAL <braced(<signatures>)>
               [<default_value>]

<signatures> ::= <signature>+

<signature> ::= ACTION <ident>
              | ACTION <ident> COLON <types>

<fun_effect> ::= EFFECT <braced(<expr>)>

<fun_body> ::= <expr>
             | <verification_fun> <fun_effect>

<function_gen> ::= FUNCTION <ident> <function_args> [<function_return>] EQUAL
                   LBRACE <fun_body> RBRACE

<function_item> ::= <loc(<function_gen>)>

<function_decl> ::= <function_gen>

<verif_predicate> ::= PREDICATE <ident> <function_args> EQUAL
                      <braced(<expr>)>

<verif_definition> ::= DEFINITION <ident> EQUAL LBRACE <ident> COLON <type_t>
                       PIPE <expr> RBRACE

<verif_axiom> ::= AXIOM <ident> EQUAL <braced(<expr>)>

<verif_theorem> ::= THEOREM <ident> EQUAL <braced(<expr>)>

<verif_variable> ::= VARIABLE <ident> <type_t> [<default_value>]

<verif_invariant> ::= INVARIANT <ident> EQUAL <braced(<expr>)>

<verif_effect> ::= EFFECT <braced(<expr>)>

<verif_specification> ::= SPECIFICATION <braced(<expr>)>

<verif_item> ::= <verif_predicate>
               | <verif_definition>
               | <verif_axiom>
               | <verif_theorem>
               | <verif_variable>
               | <verif_invariant>
               | <verif_effect>

<verification(spec)> ::= <loc(spec)>
                       | VERIFICATION [<extensions>] LBRACE
                         <loc(<verif_item>)>* <loc(<verif_specification>)>
                         RBRACE

<verification_fun> ::= <loc(<verification(<verif_specification>)>)>

<verification_decl> ::= <loc(<verification(<verif_specification>)>)>

<enum> ::= ENUM [<extensions>] <ident> EQUAL <pipe_idents>

<states> ::= STATES [<extensions>] [<ident>] [<states_values>]

<states_values> ::= EQUAL <pipe_ident_options>

<types> ::= <type_t> (COMMA <type_t>)*

<type_t> ::= <loc(<type_r>)>

<type_r> ::= <type_s> <type_tuples>
           | <type_s_unloc>
           | <type_t> IMPLY <type_t>

<type_s> ::= <loc(<type_s_unloc>)>

<type_s_unloc> ::= <ident>
                 | <type_s> <container>
                 | <ident> <type_s>
                 | <paren(<type_r>)>

<type_tuples> ::= <type_tuple>+

<type_tuple> ::= MULT <type_s>

<container> ::= COLLECTION
              | QUEUE
              | STACK
              | SET
              | PARTITION

<pipe_idents> ::= [PIPE] <ident> (PIPE <ident>)*

<pipe_ident_options> ::= [PIPE] <pipe_ident_option> (PIPE
                         <pipe_ident_option>)*

<pipe_ident_option> ::= <ident> [<state_options>]

<state_options> ::= <state_option>+

<state_option> ::= INITIAL
                 | WITH <braced(<expr>)>

<asset> ::= ASSET [<extensions>] [<bracket(<asset_operation>)>] <ident>
            [<asset_options>] [<asset_fields>] <asset_post_options>

<asset_post_option> ::= WITH STATES <ident>
                      | WITH <braced(<expr>)>
                      | INITIALIZED BY <simple_expr>

<asset_post_options> ::= <asset_post_option>*

<asset_fields> ::= EQUAL <braced(<fields>)>

<asset_options> ::= <asset_option>+

<asset_option> ::= AS <ident>
                 | IDENTIFIED BY <ident>
                 | SORTED BY <ident>

<fields> ::= <snl(SEMI_COLON, <field>)>

<field_r> ::= <ident> [<extensions>] COLON <type_t> [REF] [<default_value>]

<field> ::= <loc(<field_r>)>

<ident> ::= <loc(IDENT)>

<action> ::= ACTION [<extensions>] <ident> <function_args> <transitems_eq>

<transition_to_item> ::= TO <ident> [<require_value>] [<with_effect>]

<transitions> ::= <transition_to_item>+

<on_value> ::= ON <ident> COLON <ident>

<transition> ::= TRANSITION [<extensions>] <ident> <function_args>
                 [<on_value>] FROM <expr> EQUAL LBRACE <action_properties>
                 <transitions> RBRACE

<transitems_eq> ::= [EQUAL LBRACE <action_properties> [<effect>] RBRACE]

<action_properties> ::= [<calledby>] [ACCEPT_TRANSFER] [<require>]
                        [<verification_fun>] <function_item>*

<calledby> ::= CALLED BY [<extensions>] <expr>

<require> ::= REQUIRE [<extensions>] <braced(<expr>)>

<require_value> ::= WHEN [<extensions>] <braced(<expr>)>

<with_effect> ::= WITH EFFECT [<extensions>] <braced(<expr>)>

<effect> ::= EFFECT [<extensions>] <braced(<expr>)>

<function_return> ::= COLON <type_t>

<function_args> ::= [<function_arg>+]

<function_arg> ::= <ident> [<extensions>]
                 | LPAREN <ident> [<extensions>] COLON <type_t> RPAREN

<assignment_operator_record> ::= EQUAL
                               | <assignment_operator_extra>

<assignment_operator_expr> ::= COLONEQUAL
                             | <assignment_operator_extra>

<assignment_operator_extra> ::= PLUSEQUAL
                              | MINUSEQUAL
                              | MULTEQUAL
                              | DIVEQUAL
                              | ANDEQUAL
                              | OREQUAL

<qualid> ::= <ident> (DOT <ident>)*

<branchs> ::= <branch>+

<branch> ::= <patterns> IMPLY <expr>

<patterns> ::= <loc(<pattern>)>+

<pattern> ::= PIPE UNDERSCORE
            | PIPE <ident>

<expr> ::= <loc(<expr_r>)>

<ident_typ_q_item> ::= LPAREN <ident>+ COLON <type_t> RPAREN

<ident_typ_q> ::= <ident_typ_q_item>+

<expr_r> ::= <quantifier> <ident_typ1> COMMA <expr>
           | <quantifier> <ident_typ_q> COMMA <expr>
           | LET <ident_typ1> EQUAL <expr> IN <expr> OTHERWISE <expr>
           | LET <ident_typ1> EQUAL <expr> IN <expr>
           | <expr> SEMI_COLON <expr>
           | <ident> COLON <expr>
           | FUN <ident_typs> EQUALGREATER <expr>
           | ASSERT <paren(<expr>)>
           | BREAK
           | FOR LPAREN <ident> IN <expr> RPAREN <expr>
           | IF <expr> THEN <expr>
           | IF <expr> THEN <expr> ELSE <expr>
           | MATCH <expr> WITH <branchs> END
           | <snl2(COMMA, <simple_expr>)>
           | <expr> <assignment_operator_expr> <expr>
           | TRANSFER [BACK] <simple_expr> [<to_value>]
           | REQUIRE <simple_expr>
           | FAILIF <simple_expr>
           | <order_operations>
           | <expr> <loc(<bin_operator>)> <expr>
           | <loc(<un_operator>)> <expr>
           | <ident> <app_args>
           | <simple_expr> DOT <ident> <app_args>
           | <simple_expr_r>

<order_operation> ::= <expr> <loc(<ord_operator>)> <expr>

<order_operations> ::= <order_operation>
                     | <loc(<order_operations>)> <loc(<ord_operator>)> <expr>

<app_args> ::= LPAREN RPAREN
             | <simple_expr>+

<simple_expr> ::= <loc(<simple_expr_r>)>

<simple_expr_r> ::= <simple_expr> DOT <ident>
                  | LBRACKET RBRACKET
                  | LBRACKET <expr> RBRACKET
                  | LBRACE <record_item> (SEMI_COLON <record_item>)* RBRACE
                  | <literal>
                  | <ident> COLONCOLON <ident>
                  | <ident>
                  | LPAREN <ident> COLONCOLON <ident> AT <ident> RPAREN
                  | LPAREN <ident> AT <ident> RPAREN
                  | <paren(<expr_r>)>

<ident_typs> ::= <ident>+ COLON <type_t>
               | <ident_typ>+

<ident_typ> ::= <ident>
              | LPAREN <ident>+ COLON <type_t> RPAREN

<ident_typ1> ::= <ident> [COLON <type_t>]

<literal> ::= NUMBER
            | RATIONAL
            | TZ
            | STRING
            | ADDRESS
            | <bool_value>
            | DURATION
            | DATE

<bool_value> ::= TRUE
               | FALSE

<record_item> ::= <simple_expr>
                | <ident> <assignment_operator_record> <simple_expr>

<quantifier> ::= FORALL
               | EXISTS

<spec_operator> ::= OP_SPEC1
                  | OP_SPEC2
                  | OP_SPEC3
                  | OP_SPEC4

<logical_operator> ::= AND
                     | OR
                     | IMPLY
                     | EQUIV

<comparison_operator> ::= EQUAL
                        | NEQUAL

<ordering_operator> ::= LESS
                      | LESSEQUAL
                      | GREATER
                      | GREATEREQUAL

<arithmetic_operator> ::= PLUS
                        | MINUS
                        | MULT
                        | DIV
                        | PERCENT

<unary_operator> ::= PLUS
                   | MINUS
                   | NOT

<bin_operator> ::= <spec_operator>
                 | <logical_operator>
                 | <comparison_operator>
                 | <arithmetic_operator>

<un_operator> ::= <unary_operator>

<ord_operator> ::= <ordering_operator>

<asset_operation_enum> ::= AT_ADD
                         | AT_REMOVE
                         | AT_UPDATE

<asset_operation> ::= <asset_operation_enum>+ [<simple_expr>+]

`

