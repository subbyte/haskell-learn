### 1. What is monad in Haskell?
Monad is a _typeclass_, i.e., one class of types.

### 2. What is class in Haskell?
A _class_ or a _typeclass_ is a class of types that share the same set of rules specified for this class.

Similar thoughts in other languages:
- Java: interface
- C++: abstract class

Class examples in Haskell:
- `Eq`
  - member rules (any type in `Eq` needs to implement): `==` and `/=`
  - member type instances: `Int`, `String`, and `Char`
- `Enum`
  - member rules (any type in `Enum` needs to implement): `succ` and `pred`
  - member type instances: `Bool`, `Char`, and `Float`
- `Monad`
  - member rules (any type in `Monad` needs to implement): `return`, `>>=` (bind), `>>`, and `fail`
  - member type instances: `[]` (list), `Maybe`, and `State`
  
### 3. What does class buy us?
Do not repeat yourself.

Abstractions in programming languages such as macros and modules in C/Java/Python are invented to avoid repeating yourself. Class is another abstraction to avoid repeating yourself. This abstraction is more powerful than a macro in C:
- A macro is only doing straightforward code replacement.
- A class deducts and generates code from the rules.

### 3. What does the monad class buy us?
Do not repeat yourself when composing functions around specific types.

If a type is in the monad class (satisfying rules of monad), then the developer can compose functions of this type without programming details of how to compose them. The details are handled by automatically generated code, which is deducted from the rules of the monad class.

This becomes extremely important in cases where the detailed code is a mess, e.g., the _callback hell_ many developers experience in Javascript. The `cont` monad in Haskell completely cleans the code in this case and makes it possible to develop large applications with lots of `cont` monad composed.

Additionally, in some cases, the developer cannot specify the details of a function composition. Monad will mask out those details from the developer's view and enable programming without specifying such details. For example, one can view the Haskell IO as a special state monad where the state is the entire universe. IO operations are functions around the state monad, and they can be composed in spite of knowing how to issue a system call and change the universe outside the running program. The monad keeps the programming view in a pure functional style.

### 4. What are killer apps of monad (to save code)?
- Short circuit (`Maybe` monad, demonstrated in the next question)
- Enhanced short circuit (`Either` and `Except` monad)
- Realize states with variable immutability (`State` monad)
- Operate on streamed items (`List` monad)
- Perform IO in a pure functional language (`IO` monad)
- Turn the _callback hell_ into composable functions (`Cont` monad)

### 5. What does it look like to save code using `Maybe` monad?
Check the following example and compare
- the first 2 implementations with no knowledge about monad
- the last 3 implementations exploiting the fact that `Maybe` is a monad

**Example**: chain the 3 functions below and implement `func :: a -> Maybe d`:
``` Haskell
-- first do computationX, pass the result into computationY,
--   then pass the second result to computationZ, finally output
-- in any step, if an error occur and the step returns Nothing,
--   skip the following steps and return Nothing
computationX :: a -> Maybe b
computationY :: b -> Maybe c
computationZ :: c -> Maybe d
```

1. An implementation without knowing `Maybe` is a monad (the modular way):
``` Haskell
funcB :: Maybe b -> Maybe c
funcB Nothing = Nothing
funcB (Just x) = computationY x

funcC :: Maybe c -> Maybe d
funcC Nothing = Nothing
funcC (Just x) = computationZ x

func = funcC . funcB . computationX
```

2. Another implementation without knowing `Maybe` is a monad (the monolithic way):
``` Haskell
func x = w
  where
    my = computationX x
    y = case my of
        Nothing -> Nothing
        Just y -> computationY y
    w = case y of
        Nothing -> Nothing
        Just z -> computationZ z
```

3. An implementation knowing `Maybe` is a monad:
``` Haskell
func x = do
  y <- computationX x
  z <- computationY y
  computationZ z
```

4. Another monad version using the bind operator:
``` Haskell
func x = computationX x >>= computationY >>= computationZ
```

5. Another monad version with Kleisli composition:
``` Haskell
func = computationX >=> computationY >=> computationZ
```

**What is the difference?**
The `Nothing` situation is automatically handled and is transparent to the developer, which leads to much cleaner code.
- `Maybe a` defined only as `Nothing | Just a` does not do the magic
- putting `Maybe` into the monad class does the magic
  - Haskell gives rules to `Maybe` on how to handle `Nothing` when chained together
  - the set of rules satisflies the monad class, i.e., `Maybe` a monad
  - the set of rules is already specified in core Haskell
  - it is straightforward to generate code from the rules
  - when using `>>=` and `>=>`, the compiler automatically generates code to handle the `Nothing` situation

### 6. Where to find more about monad?
- https://www.youtube.com/watch?v=ZhuHCtR3xq8&index=2&list=FLjosXY3GhJ1cIHAnqZM-QBQ
- http://learnyouahaskell.com/a-fistful-of-monads#the-monad-type-class
