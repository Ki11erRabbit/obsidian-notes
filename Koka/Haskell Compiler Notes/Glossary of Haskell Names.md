
# KUserType
| Type     | Meaning                      | Example     | Clearer Meaning |
| -------- | ---------------------------- | ----------- | --------------- |
| TpQuan   | Quantified Type              | forall\<a\> |                 |
| TpFun    | Lambda Type                  | \(a\) -> b  |                 |
| TpApp    | Applied Type                 |             |                 |
| TpCon    | Type Constructor             | list        |                 |
| TpParens | Parenthesized Type           |             |                 |
| TpAnn    | Annotated Type (with a kind) | t :: V      |                 |

# Ctail
A monad that is a combo of reader and a state monad
The state monad is unneeded as we can just use the unique effect