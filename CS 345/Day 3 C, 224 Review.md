bug in printf implementation

```c
printf("%i: %s\n", 3, "hello");
```
This will not print properly

The test for ping pong sometimes breaks because output overlaps because they share a output stream


```C

//  
// Created by Alec Davis on 9/8/25.  
//  
#include "kernel/types.h"  
#include "kernel/stat.h"  
#include "user/user.h"  
#include "kernel/fs.h"  
#include "kernel/fcntl.h"  
  
  
void lines(char *input, int input_length, char **output, int *output_length);  
  
  
void lines(char *input, int input_length, char **output, int *output_length) {  
    printf("lines start\n");  
    int offset = 0;  
    int offset_length = 0;  
    char *buffer[512];  
    int buffer_length = 0;  
    printf("input_length = %d\n", input_length);  
    do {  
        printf("offset: %d\n", offset);  
        printf("offset_length: %d\n", offset_length);  
        if (input[offset + offset_length] == '\n' || offset + offset_length == input_length) {  
            printf("splitting string\n");  
            input[offset + offset_length] = '\0';  
            buffer[buffer_length] = input + offset;  
            buffer_length++;  
            offset = offset + offset_length + 1;  
            offset_length = 0;  
        }        offset_length++;  
    } while (offset + offset_length < input_length);  
  
    *output_length = buffer_length;  
    printf("printing buffer\n");  
    for (int i = 0; i < buffer_length; i++) {  
        if (buffer[i] != 0) {  
            printf("%p\n", buffer[i]);  
            printf("%s\n", buffer[i]);  
        }    }    for (int i = 0; i < buffer_length; i++) {  
        if (buffer[i] != 0) {  
            output[i] = buffer[i];  
        }    }    return;  
}  
  
  
  
int main(int argc, char *argv[]){  
    if (argc < 2) {  
        fprintf(2, "Usage: xargs <..args>\n");  
        exit(1);  
    }  
    char input[1024] = {0};  
    read(0, input, 1024);  
    char *args[512];  
    int args_length = 0;  
  
    printf("input_length = %d\n", strlen(input));  
    lines(input, strlen(input), args, &args_length);  
    for (int i = 0; i < args_length; i++) {  
        if (args[i] != 0)  
            printf("%s ", args[i]);  
    }    int all_args_length = args_length + (argc);  
    char **all_args = malloc(sizeof(char*) * all_args_length);  
    all_args[all_args_length] = (char *)0;  
  
    memcpy(all_args, argv + 1, (argc - 1) * sizeof(char*));  
    memcpy(all_args + (argc - 1), args, args_length * sizeof(char*));  
  
    for (int i = 0; i < all_args_length; i++) {  
        printf("%s\n", all_args[i]);  
    }  
    exec(argv[1], all_args);  
    exit(0);  
}
```