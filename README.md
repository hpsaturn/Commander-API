# Commander API

**Version 1.0.0**

 Commander API is a C API to interpret character based commands and map them to their functions easily. It is designed mainly to work with low memory capacity devices for example small ARM devices or some embedded stuff.



## Installation

Installation is easy. Jus add __interpreter.h__, and __interpreter.c__ files to your project.

## Usage

First you have to add the two files to your project. Than you have to create the instruction data in __interpreter.c__ file under the following comment:

'''c
//  +----  Create instruction data for the API  ----+
//  |                                               |
//  |       This is where you have to add           |
//  |               your commands!                  |
//  |                                               |
//  +-----------------------------------------------+

create_instruction_data(stop, "basic stop command");
'''

Use the __create_instruction_data()__ macro to add your commands to the interpreter like above. You have to do this for every instruction that you want to add. The first argument is the exact command name( this will be the exact command name! ), and the second one is a short description. Note that the first argument __must not have any quotes before and after!__

The next step is to match the functions to your commands. You have to do it in interpreter.c file, in the init_interpreter() function, under the following comment:

'''c
//  +----  Match instruction to it's function   ----+
//  |                                               |
//  |       This is where you have to match         |
//  |       every instruction name to it's          |
//  |                   function.                   |
//  |                                               |
//  +-----------------------------------------------+
    add_instruction(stop, stop_func);
'''

Use the __add_instruction()__ macro to match the name and the asociated function like above. You have to do this for every instruction that you have added previously. The first argument is the instruction name __exactly like the previous step__, the second argument is the function that you want to use when this command gets executed. This function has to be a __void__ return type with two arguments. The first argument is a __char*__ type and the second one is a __int(*resp_fn)(const char*, ...)__ type. I know that it looks scary but it can be very handy and you have to just copy the example function to create a new one.

The next thing you have to do is to specify the number of commands you have been added. It can be done in the __interpreter.h__ file under the following comment:

'''c
//  +----  Set the correct values  ----+
//  |                                  |
//  |     NUM_OF_API_FUNCS value has   |
//  |           to be exact!           |
//  |                                  |
//  +----------------------------------+

#define NUM_OF_API_FUNCS  4
'''

Modify the __NUM_OF_API_FUNCS__ value to the right number unless __the program will crash!__

The last thing you have to do is to actually create your functions. A simple example function can be like this:

'''c
void stop_func(char *args, int(*resp_fn)(const char*, ...))
{
    printf("STOP!\r\n");
    printf("Args: %s\r\n", args);

    //  Always check the response function channel!
    //  If it's a NULL pointer you can't use it,
    //  because itt will crash your porgram!
    if( resp_fn != NULL ){

        resp_fn( "Wow! Magic!!!!\r\n" );

    }
}
'''

The example function above shows the function of the response function. The interpreter can send messages back to the sender channel. If not used just pass a __null__ pointer to it.

## How it works, why to use?
 The library does not use any malloc or dynamic memory. The module creates a balanced binary tree by alphabetical order in initialization time. The memory requirement of this tree can be calculated at compile time if we know how many instructions we have. This is why you must configure __NUM_OF_API_FUNCS__ symbol correctly! To create the balanced tree it is a bit more compute hungry than the old strcmp method, but the tree method will boost significantly the speed in search time, so I think it's worth it. In the worst case it finds a command in O*log2(N). That means it can find a command(if it exists) in a __1024__ element long command list under __10__ search steps.

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.


## License & copyright
© Daniel Hajnal
Licensed under the [MIT License](https://choosealicense.com/licenses/mit/)