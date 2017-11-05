### What is moand in Haskell?
Monad is a _typeclass_:
- Design goal: reduce code verboseness by providing standard implementation for common code
- Core rules for its members: function `return` and `bind`

### What is class in Haskell?
A class or a _typeclass_ is a category of types those share a same set of rules/functions.

Similar thoughts in other languages:
- Java: interface
- C++: abstract class

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
- An example: to implement `func` with three given functions:
``` Haskell
toMaybeA :: a -> Maybe a
toMaybeB :: a -> Maybe b
toMaybeC :: b -> Maybe c

func :: a -> Maybe c
```
- An implementation without monad:
``` Haskell
funcA :: Maybe a -> Maybe a
funcA Nothing = Nothing
funcA (Just x) = toMaybeA x

funcB :: Maybe a -> Maybe b
funcB Nothing = Nothing
funcB (Just x) = toMaybeB x

funcC :: Maybe b -> Maybe c
funcC Nothing = Nothing
funcC (Just x) = toMaybeC x

func x = funcC . funcB . funcA $ Just x
```
- A single function implementation without monad:
``` Haskell
func x = mc
  where
    mc = case mb of
        Nothing -> Nothing
        Just b -> toMaybeC b
    mb = case ma of
        Nothing -> Nothing
        Just a -> toMaybeB a
    ma = case x of
        Nothing -> Nothing
        Just z  -> toMaybeA z
```
- The simple monad version:
``` Haskell
func x = return x >>= toMaybeA >>= toMaybeB >>= toMaybeC
```
- The monad version with Kleisli composition:
``` Haskell
func = toMaybeA >=> toMaybeB >=> toMaybeC
```
- The monad version with `do` notation:
``` Haskell
func :: a -> Maybe c
func x = do
  a <- toMaybeA x
  b <- toMaybeB a
  toMaybeC b
```
