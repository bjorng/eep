These sections were removed from EEP 79. They could be the
basis for a new EEP for extending basic records.

### Checking whether a record is current

FIXME: Remove. New EEP for OTP 30.

Do we need a way to check that the native-record is the current version?

Yes, we should add a BIF that essentially does the following:

```erlang
record:get_fields(Record) =:= record:get_fields(#Module:Record{})
```

but guaranteed to always work and be more efficient.

NOT_REVIEWED_BY_OTB. Questions by Raimo in a comment:
> Such a BIF needs to consult the defining module. What should it do if that module is not loaded?
Return false because obviously there is no record definition, currently?
Is it possible that a later module load will load the current definition, or is that impossible?
I guess we do not want the BIF to return false now and true later. The other way around must be ok...

TODO: What should the name of the BIF be? (Raimo suggests `is_record_current/1`.)

We should also have a BIF that checks whether an instance of a native
record referes to the current definition of the native record.

TODO: What should the name of that BIF be?


### Native records in specs and in the language of types

This syntax is currently not implemented.

Native records can be used as types using the following syntax:

```erlang
%% local or imported native-record
#RecordName(TField :: TType, ... )
%% remote native-record
#Module:RecordName(TVar1, ..., TVarN)
```

If you export a native record, its type will be available for other
modules to use.  Dialyzer will complain if you attempt to use an
un-exported native record.

Example:

```erlang
-module(misc).
-export_record([user/0, pair/2]).
-record #user() {
    id = -1 :: integer(),
    name :: binary(),
    city :: binary()
}.
-record #pair(A, B) {
    first :: A,
    second :: B
}.
-type int_pair() :: #pair(integer(), integer()).
-spec mk_user() -> #user().
mk_user() ->
    #user{id = 1, name = ~"Alice", city = ~"London"}.

-spec mk_user_limited() -> #user().
mk_user() ->
    #user{id = 1, name = ~"Alice", city = ~"London"}.

-spec mk_pair(A, B) -> #pair(A, B).
mk_pair(A, B) ->
    #pair{first = A, second = B}.
```

A new builtin type `record()` is introduced. It denotes the set of all
possible native record values at runtime.
