# aeson-gadt-th

Provides Template Haskell expressions for deriving `ToJSON` and `FromJSON` instances for GADTs.

## Example Usage:

```haskell
{-# LANGUAGE GADTs #-}
{-# LANGUAGE KindSignatures #-}
{-# LANGUAGE TemplateHaskell #-}
{-# LANGUAGE FlexibleContexts #-}
{-# LANGUAGE FlexibleInstances #-}
{-# LANGUAGE MultiParamTypeClasses #-}

import Data.Aeson
import Data.Aeson.GADT.TH

data A :: * -> * where
  A_a :: A a
  A_b :: Int -> A ()

data B c :: * -> * where
  B_a :: c -> A a -> B c a
  B_x :: B c x

deriveJSONGADT ''A
deriveJSONGADT ''B

main :: IO ()
main = return ()
```

## Encoding:
```
encode A_a
> "[\"A_a\",[]]"
```

## Decoding:

When decoding a JSON-encoded GADT, the result will be wrapped using [Data.Some.This](http://hackage.haskell.org/package/dependent-sum-0.4/docs/Data-Some.html).
```
case (decode $ encode A_a) :: Maybe (Some A) of
  Nothing -> error "Couldn't decode
  Just (This A_a) -> putStrLn "it worked"
  Just (This A_b) -> putStrLn "wat"
> it worked
```
