A short-hand for writing redex tests, similar to the
[rackunit-chk](https://github.com/jeapostrophe/rackunit-chk) pkg.

The main feature besides providing short-hand for rackunit test
primitives is that all expressions ```e``` are automatically
wrapped by redex's 

example:


```racket
(module+ test
  (define-language Nats
    [Nat ::= Z (S Nat)])
  
  (define-metafunction Nats
    add2 : Nat -> Nat
    [(add2 Nat) (S (S Nat))])
  
  (define-judgment-form Nats
    #:mode (even I)
    #:contract (even Nat)
    [---------- "E-Zero"
     (even Z)]
    
    [(even Nat)
     ---------- "E-Step"
     (even (S (S Nat)))])

  (define-judgment-form Nats
    #:mode (equal-nats I I)
    #:contract (equal-nats Nat Nat)
    [---------- "Eq-Zero"
     (equal-nats Z Z)]
    
    [(equal-nats Nat_1 Nat_2)
     ---------- "Eq-Step"
     (equal-nats (S Nat_1) (S Nat_2))])
  
  (redex-chk
   Z Z
   #:f Z (S Z)
   #:t (even Z)
   #:f (even (S Z))
   #:f #:= (add2 Z) (S (S (S Z)))
   
   #:= (add2 (add2 (add2 Z)))
   (S (S (S (S (S (S Z))))))
   
   #:= (even (add2 (add2 (add2 Z))))
   (even (S (S (S (S (S (S Z)))))))
   
   #:f (even (S Z)))

  (redex-relation-chk
   even
   [#:t Z]
   [#:f (S Z)]
   [(S (S Z))])

  (redex-relation-chk
   equal-nats
   [#:t Z Z]
   [#:f (S Z) Z]
   [(S (S Z)) (add2 Z)]))
```
