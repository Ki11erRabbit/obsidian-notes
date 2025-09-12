## Programs
### Xargs for CS 345
```c
#include "kernel/types.h"  
#include "kernel/stat.h"  
#include "user/user.h"  
#include "kernel/fs.h"  
#include "kernel/fcntl.h"  
  
  
void mark_newlines(char *input, int input_length, char **output) {  
    *output = malloc(input_length + 2); // add null terminator and extra null to know when to stop  
    (*output)[input_length + 2] = '\0';  
    (*output)[input_length + 1] = '\0';  
    memcpy(*output, input, input_length);  
    for (int i = 0; i < input_length; i++) {  
       if ((*output)[i] == '\n') {  
          (*output)[i] = '\0';  
       } else if ((*output)[i] == '\0') {  
          break;  
       }    
    }
}  
  
void exec_for_each(char **argv, int argc, char *lines) {  
    char **args = malloc((argc + 1) * sizeof(char *));  
    args[argc + 1] = (char *)0;  
  
    memcpy(args, argv + 1, (argc - 1) * sizeof(char *));  
  
    while (1) {  
       args[argc - 1] = lines;  
       if (fork() == 0) {  
          exec(args[0], args);  
       } else {  
          wait((void*)0);  
       }       
       lines += strlen(lines) + 1;  
       if (lines[0] == '\0') {  
          break;  
       }    
    }
}  
  
int main(int argc, char *argv[]){  
    if (argc < 2) {  
        fprintf(2, "Usage: xargs <..args>\n");  
        exit(1);  
    }  
    char input[1024] = {0};  
    int total_read = 0;  
    while (total_read < 1024) {  
        int result = read(0, input + total_read, 1024 - total_read);  
        if (result <= 0) {  
            break;  
        }        
        total_read += result;  
    }    
    char *lines = (char*)0;  
    mark_newlines(input, total_read, &lines);  
  
    exec_for_each(argv, argc, lines);  
  
    exit(0);  
}
```

#### Questions
- do I write to a well formed pointer?
- do I write within the bounds of a pointer?
- will I get stuck on read?
- will I write past array input's bounds?
- is the call to memcpy proper?

#### How an abstract debugger can help answer the questions
- for 1 and 2: it could track the sizes and locations of pointers to show if there is an index that goes past the size of pointers
- This one might be impossible to do since it requires operating system knowledge if we can't set the file descriptor to be non-blocking
- same as the first answer
- It could tell as if we write beyond the boundaries of a pointer but not if it is under the boundary. An abstract debugger can't tell us if our behavior is correct only our use.

#### What an abstract debugging session would look like
##### Checking if `mark_newlines` will write to a well formed pointer
- debugger asks for a term to evaluate
- > we select `(*output)[input_length + 2] = '\0';`
- debugger lists actionable items to look for
- > we select what we want to know (no null pointers, write within a valid pointer
- debugger then proceeds in a bottom up fashion, learning facts about the program up until the point we selected. It also knows how `malloc` behaves. It knows that `malloc` may return null. It also knows that the pointer will be the size of the input to `malloc`.
  It learns the following facts:
  1. `char *input` is at most 1024 `char` sizes
  2. `char *input` may not be a valid C string
  3. `int input_length` is no larger than 1024
  4. `char *lines` is null and that we pass the pointer to its value as `char **output`. This means that the pointer behind `char **output` is valid.
  5. The value pointed to by `output` may still be null from `malloc`, but also may be of size `input_length + 2`
- debugger comes back with 3 facts:
  1. There is a possibility of a null pointer dereference that isn't checked for from the result of `malloc`
  2. If the pointer is null, there will be an invalid write.
  3. If the pointer isn't null, there won't be an invalid write because the size of the pointer is within the bounds of the input to `malloc`
##### Checking if uses of `memcpy` are valid
The C function `memcpy` is dangerous due to its type unsafe nature. Another unsafe aspect is that it doesn't work with overlapping memory addresses.
- debugger asks for a term to evaluate
- > we select `memcpy(*output, input, input_length);` 
- debugger lists actionable items to look for
- > we select what we want to know (no null pointers, writing within the bounds of a valid pointer, pointers don't alias to each other (non-overlapping))
- debugger proceeds in a bottom up fashion, learning facts about the program up until that point. It knows the behavior of `malloc`, it knows that `malloc` creates two possibilities of pointer output.
  It comes up with the following facts:
  1. `char *input` is at most 1024 `char` sizes
  2. `char *input` may not be a valid C string
  3. `int input_length` is no larger than 1024
  4. `char *lines` is null and that we pass the pointer to its value as `char **output`. This means that the pointer behind `char **output` is valid.
  5. The value pointed to by `output` may still be null from `malloc`, but also may be of size `input_length + 2`
  6. If `*output` is null, then the `memcpy` will be invalid
  7. If `*output` isn't null, then `memcpy` will be valid due to how `*output` is larger than the size of `input` which it knows from how `int input_length` is no larger than 1024 from the call to `malloc` where it was `input_length + 2`
  8. The results from the `mark_newlines` example will also appear because it is along the same code path

### Recursive Fib in Java
```java
public class Fib {
    public static int fib(int n) {
        if (n <= 1) {
            return n;
        }

        return fib(n - 1) + fib(n - 2);
    }

    public static void main(String[] args) {
        System.out.println(fib(40));
    }
}
```
#### Questions
1. Is recursion bounded (no infinite recursion/function is total)?
2. Will there be overflow of Java ints?
#### How an abstract debugger help answer questions
1. It could detect that n only gets smaller and therefore must hit the if statement that bounds the recursion. 
   This is only a heuristic that would work on some functions. It most definitely would fail on something like merge sort
2. I doubt it would be able to figure this one out since it requires knowledge of the current size of ints

#### What an abstract debugging session look like
##### Will `fib` be a total function
We will assume with recursion that input must be decreasing to be total (the same as Rocq). If we can't detect this behavior, then we could try other heuristics. If all of those fail then we will report that `fib` may not be a total function.

The debugger may also ask what the stack size the user will be using for the JVM or provide the default if not specified
- debugger asks us for a term to explore
- > we select the definition of `fib` so `public static int fib(int n)`
- debugger asks us for what we want to learn about the method
- > we select what we want to know (selecting total will select a few items)
- debugger proceeds to look at the structure of `fib`.
  It learns the following facts:
  1. Java doesn't throw exceptions for integer overflow.
  2. The return value may be `<= 1` due to the if statement
  3. There is recursion in the method
  4. There is a bound from the if statement
  5. Since we are bounded, we know that the stack depth is no larger than the largest value of `n - 1`
- debugger comes back with the following facts about `fib`:
  1. If the value of `n` is less than the stack size, then `fib` is total
  2. If the value of `n` is not less than the stack size, then `fib` isn't total as it will throw a stack overflow exception


### Merge Sort in Python
```python

def merge(left, right):
	result = []
	left.reverse()
	right.reverse()
	while left and right: # lists in Python are true if full
		if left[-1] <= right[-1]:
			result.append(left.pop())
		else:
			result.append(right.pop())
	
	while left:
		result.append(left.pop())
	while right:
		result.append(right.pop())
	return result

def merge_sort(lst):
	if len(lst) <= 1:
		return lst
	
	left = [:len(lst) // 2]
	right = [len(lst) // 2:]
	
	left = merge_sort(left)
	right = merge_sort(right)
	
	return merge(left, right)

merge_sort([3, 7, 4, 5, 9, 0, 1, 23,])

```

#### Questions
1. is `merge_sort` implemented properly?
2. does the function guard against improper use?
3. is `merge_sort` a total function?
4. is `merge_sort` a total function if called correctly?

#### How an abstract debugger would help answer questions
1. Question is too vague. But It can look at the body to prove other facts related to merge sort
2. Given an abstract Python value, it could see if a member access could go wrong
3. It would likely fail on the `Rocq` decreasing heuristic so, likely not. Plus, the fact it can accept any Python value invalidates this fact
4. It would likely fail on the `Rocq` decreasing heuristic so, likely not

#### What an abstract debugging session look like
##### Does the length of the input to merge sort equal the output?

- the debugger asks for some term from the user
- > we select the definition of `merge_sort` (`def merge_sort(lst)`)
- debugger asks us for things we want to know about the function
- > we select what we want to know. We do this by doing a custom constraint.
  Since this is Python, we write some Python-esk code. The return keyword is used as a variable that represents the return value. With this, we write `len(lst) == len(return)`
- Since we are also in a dynamically typed language, the input bounds could be uncountably infinite (this is from arbitrary lists/tuples nested in lists/tuples) or most possible values are just invalid anyway.
  The debugger asks what kind of type we would like apply to any variables.
  This time we can do one of the following
  1. write out `lst = list` 
  2. write out `return = ` and then click on the definition of our type
- > we say that `lst = list` and `return = list`
- the debugger goes and looks up facts about the functions `merge_sort` and `merge`
  It tries its best with the knowledge the programmer provides. It could fail if the programmer doesn't use the right features to constrain the state space
  With how we wrote the program, it finds the following facts:
  1. if `len(lst) <= 1` then `return = lst` which satisfies the constraint.
  2. the variable `left` is approximately half of `lst`
  3. the variable `right` is approximately half of `lst`
  4. in `merge` due to the behavior of the loops and the operations that shrink `left` and `right`, the debugger learns that `len(result) == len(left) + len(right)`
  5. `merge` will have the same size as `lst`
- the debugger comes back with the following facts:
  1. our constraint `len(lst) == len(return)` is true

##### Notes
Dynamically Typed languages may be more fiddly than statically typed languages.
There are ways we could have rewritten the program that might have prevented the abstract debugger from being able to figure out the above facts. One idea is if instead of doing list slicing, we instead did a for loop over the length of `lst` and splitting indices based off of `len(lst) // 2`. This may trick the debugger to not realize that we have split `lst` at an arbitrary point.

We should probably stay away from dynamically typed languages if we do make an abstract debugger.
