### 1. What is monad in Haskell?
Monad is a _typeclass_:
- Design goal of this typeclass: reducing code verboseness, increasing code composability
- How to achieve the goal: automatically deriving implicit standard implementation for common computation
- Basic rules for members of this typeclass: function `return` and `bind`

### 2. What is class in Haskell?
A _class_ or a _typeclass_ is a category of types those share a same set of rules/functions.

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
  
### 3. What is the killer app of monad?
- Short circuiting (`Maybe` monad, demonstrated in the next question)
- Keeping states with immutable variables in Haskell (`State` monad)
- Avoiding ugly syntax when implementing exception handling (`Except` monad)

### 4. How does monad reduce verboseness?
It turns _a chain of verbose functions_ into _a chain of succinct monad functions_. Here's an example of chaining functions. The first two implementations are done without monad, the last three exploit the convenience provided by the monad syntax.

**Goal**: chain the three functions below and implement `func :: a -> Maybe c`:
``` Haskell
-- Maybe is widely used in Haskell to replace null,
-- so it is safer to return (Maybe a) instead of a for any computation/function
computationX :: a -> Maybe a
computationY :: a -> Maybe b
computationZ :: b -> Maybe c
```

1. An implementation without monad (the modular way):
``` Haskell
funcB :: Maybe a -> Maybe b
funcB Nothing = Nothing
funcB (Just x) = computationY x

funcC :: Maybe b -> Maybe c
funcC Nothing = Nothing
funcC (Just x) = computationZ x

func = funcC . funcB . computationX
```

2. Another implementation without monad (the monolithic way):
``` Haskell
func x = mc
  where
    ma = computationX x
    mb = case ma of
        Nothing -> Nothing
        Just a -> computationY a
    mc = case mb of
        Nothing -> Nothing
        Just b -> computationZ b
```

3. An implementation with monad (`Maybe` is a monad):
``` Haskell
func x = do
  a <- computationX x
  b <- computationY a
  computationZ b
```

4. Another monad version using the bind operator:
``` Haskell
func x = computationX x >>= computationY >>= computationZ
```

5. Another monad version with Kleisli composition:
``` Haskell
func = computationX >=> computationY >=> computationZ
```

### 5. Where to find more about monad?
- https://www.youtube.com/watch?v=ZhuHCtR3xq8&index=2&list=FLjosXY3GhJ1cIHAnqZM-QBQ
- http://learnyouahaskell.com/a-fistful-of-monads#the-monad-type-class
