
hoare logic is a correctness logic

incorrectness logic is the dual: prove that certain programs are incorrect

Soundness: Hoare Logic is sound. If you are told it is correct, it is correct
Completeness: You can do anything but it may not be sound. 

Hoare triple doesn't tell you termination

Hoare logic is compositional

Hoare logic is either the postcondition or it doesn't terminate

```
{ ⊤ } # Top (all states) precondition
N := n { N = n /\ 1 = 1 }
A := 1 { N = n /\ A = 1 }
while N > 0 
	A := A * N
	N := N - 1

{ N = 0 /\ A = factoral(n)} # postcondition
```


Can devise Hoare logic for any code that has side effects


Most Approximate program: diverges on all input states
Least Approximate program: does not exist. `skip X := 1`