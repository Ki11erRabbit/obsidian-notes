Look into ordinal algebra

Answer for order of terms making proof harder: 




```rocq
Goal forall (n m : nat), S (add_accum n m) = add_accum n (S m).

intros n.

induction n as [| n' IHn'].

- intros. simpl. reflexivity.

- intros o. simpl. rewrite (IHn' (S o)). reflexivity.

Qed.
```

for `IHn' (S o))` it is like applying a function with a specific argument


Good advice: don't dive into code if you don't know what you are doing.

Question to ask for next time:
sometimes when using `simpl` it will expand a function to its body. While doing a proof for another class, I expanded the definition of a function in order to operate on the terms of the function. Do you know if any of the book's proofs require any of this?