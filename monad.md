### What is moand in Haskell?
Monad is a _typeclass_:
- Design goal: reduce code verboseness by providing standard implementation for common code
- Core rules for its members: function `return` and `bind`

### What is class in Haskell?
A class or a _typeclass_ is a category of types those share a same set of rules/functions.
It is similar to an interface in Java or a virtual functions in C++.

Class examples:
- `Eq`
  - member rules (any type in `Eq` needs to implement): `==` and `/=`
  - member type instances: `Int`, `String`, and `Char`
- `Enum`
  - member rules (any type in `Enum` needs to implement): `succ` and `pred`
  - member type instances: `Bool`, `Char`, and `Float`
- `Monad`
  - member rules (any type in `Monad` needs to implement): `return`, `>>=` (bind), `>>`, and `fail`
  - member type instances: `[]` (list), `Maybe`, and `State`
  
### What is the killer app of monad?
It turns _a chain of verbose functions_ into _a chain of succinct monad functions_.
