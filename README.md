# c-specification
create a c language specification for ADC

This is a short document describing the preferred coding style for the design
of ADC - LWLB. This coding style is refer to the Linux kernel.

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

1. Foundational requirement

    > The procedure is correct first, followed by elegance.

    > The program structure should be clear, simple and easy to understand.

    > The design should be simple, straightforward, concise, and avoid garbage 
      programs.

    > Use standard library functions and public functions as far as possible.

    > Do not arbitrarily define global variables and use local variables as 
      much as possible.

    > It is impossible to prove that your program is not wrong, 
      so after writing a program, you should check back first.

    > New errors may occur when correcting an error, 
      so the impact on other programs should be considered before modifying.

    > When the program is related to the environment or state, it must take 
      the initiative to deal with unexpected events, such as whether the file 
      can be locked logically, whether the printer is online, etc.  For clear 
      errors, there must be a clear fault-tolerant code to prompt users.

    > Unit testing is also part of programming, and the program to submit the 
      debugging test must pass the unit test.

    > ...

2. C programming specification
    This standard is applicable to the use of C.

    2.1 Indentation
        + Set the indentations as 4 characters.
        If you think it is hard to read on a 80-character terminal screen, that
        means you use more than 3 level of indentation, the structure of 
        program need to be simplified.


        + The preferred way to ease multiple indentation levels in a switch 
        statement is to align the "switch" and its subordinate "case" labels in
        the same column instead of "double_indenting" the "case" label

        Eg: (alpha/include/asm/futex.h) - Linux kernel C
        >switch (op) {
        >case FUTEX_OP_SET:
        >    __futex_atomic_op("mov %3,%1\n", ret, oldval, uaddr, oparg);
        >    break;
        >......
        >default:
        >    ret = -ENOSYS;
        >}


        + Don't put multiple statements on a single line like:
        if(condition) do_something


        + Don't put multiple assignments on a single line either.


        + Avoid tricky expressions.


        + Use 4 SPACE to replace a TAB.
        To uniform the display format(like gerrit). 

    2.2 Breaking long lines and strings
        Coding style is all about readability and maintainability using commonly
        available tools.

        + Limit the length of lines to 80 columns. 
        Statements longer than 80 columns will be broken into sensible chunks.

        Eg: 
        >printk(KERN_WARNING "Warning this is a long printk with " 
        >                    "3 parameters a: %u b: %u " 
        >                    "c: %u \n", a, b, c); 

    2.3 Placing Braces
        + put the opening brace last on the line, and put the closing brace 
        first.

        Eg:
        >if(x is true) {
        >    we do y
        >}
        This applies to all non-function statement blocks (if, switch, for, 
        while, do).


        + However, there is one special case, namely functions: they have the
        opening brace at the beginning of the next line.

        Eg: (arch/powerpc/boot/stdio.c) - Linux kernel C
        >size_t strnlen(const char * s, size_t count)
        >{
        >    const char *sc;

        >    for (sc = s; count-- && *sc != '\0'; ++sc)
        >        /* nothing */;
        >    return sc - s;
        >}


        + The closing brace is empty on a line of its own, except in the cases 
        where it is followed by a continuation of the same statement, ie a 
        "while" in a do-statement or an "else" in an if-statement.

        Eg: (alpha/include/asm/xor.h) - Linux kernel C
        >#define XOR_TRY_TEMPLATES				\
        >do {						\
        >    xor_speed(&xor_block_8regs);		\
        >    xor_speed(&xor_block_32regs);		\
        >    xor_speed(&xor_block_alpha);		\
        >    xor_speed(&xor_block_alpha_prefetch);	\
        >} while (0)

        Eg: (alpha/include/asm/core_apecs.h) - Linux kernel C
        >if (addr >= APECS_DENSE_MEM) {
        >    addr -= APECS_DENSE_MEM;
        >    APECS_SET_HAE;
        >    base_and_type = APECS_SPARSE_MEM + 0x08;
        >} else {
        >    addr -= APECS_IO;
        >    base_and_type = APECS_IO + 0x08;
        >}

        +? Do not unnecessarily use braces where a single statement will do.
        
        Eg: (arm/mach-iop13xx/pci.c) - Linux kernel C
		>if ((init_atu & (1 << i)) == (1 << i))
		>	plat_pci->nr_controllers++;

        Eg: (powerpc/boot/stdio.c) - Linux kernel C
		>if (base == 16)
		>	size -= 2;
		>else if (base == 8)
		>	size--;
        
        ! This does not apply if one branch of a conditional statement is a 
        single statement. Use braces in both branches.
        
        Eg: 
        >if (condition) {
        >    do_one();
        >    do_two();
        >} else {
        >    do_one_();
        >}

    2.4 Placing Spaces
        + Use a space after these keywords:
            if, switch, case, for, do, while (?...)

        Eg: (arch/powerpc/boot/stdio.c) - Linux kernel C
        >for (sc = s; count-- && *sc != '\0'; ++sc)
        >    /* nothing */;

        ! But not with: 
            sizeof, __typeof__, __attribute__ (?...)
        
        Eg: (arch/powerpc/boot/stdio.c) - Linux kernel C
        >field_width = 2*sizeof(void *);


        + When declaring pointer data or a function that returns a pointer type,
        the preferred use of '*' is adjacent to the data name or function name 
        and not adjacent to the type name.

        Eg: (arch/powerpc/boot/stdio.c) - Linux kernel C
        >size_t strnlen(const char * s, size_t count)
        >{
        >    const char *sc;
        >    ......
        >}


        + Use one space around (on each side of) most binary and ternary 
        operators:
            `=`   `...=`(+=, -=, ...)
            `? : `
            `+`   `-`   `*`   `/`   `%`  `>>`  `<<`
            `==`  `>=`  `<=`  `!=`  `>`  `<`
            `|`   `&`   `^`
            `&&`  `||`  
            (?...)

        ! But no space after unary operators:
            `&`   `*`
            `+`   `-`   `~`   `!`
            (?...)
        ! And no space before or after the postfix increment & decrement unary 
        operators:
            `++`   `--`
        ! And no space around structure member operators:
            `.`   `->`


        +? Do not leave trailing whitespace at the ends of lines.

    2.5 Naming
        + GLOBAL VARIABLES (to be used only if you really need them) need to 
        have descriptive names, as do GLOBAL FUNCTIONS. If you have a function
        that counts the number of active users, it is better to call that 
        "count_active_users()" or similar, and not to call it "cntusr()"

        Eg: (arch/powerpc/boot/stdio.c) - Linux kernel C
        >static char sprint_buf[1024];


        + LOCAL VARIABLES names should be short, and to the point. If you have 
        some random integer loop counter, it should probably be called "i". 
        Calling it "loop_counter" is non-productive, if there is no chance of it        being mis-understood. Similarly, "tmp" can be just about any type of 
        variable that is used to hold a temporary value.

        Eg: (arch/powerpc/boot/stdio.c) - Linux kernel C
        >static int skip_atoi(const char **s)
        >{
        >    int i, c;

        >    for (i = 0; '0' <= (c = **s) && c <= '9'; ++*s)
        >        i = i*10 + c - '0';
        >    return i;
        >}

        ! If you are afraid to mix up your local variable names, please refer
        to chapter 2.x (Functions).

    2.6 Typedefs
        + Do not use `typedef` casually.
        In general, a pointer, or a struct that has elements that can 
        reasonably be directly accessed should never be a typedef.

        ! But if there is a clear reason for why it under certain circumstances
        might be an "unsigned long" and under other configurations might be 
        "unsigned int", then by all means go ahead and use a typedef.
        Note! there needs to be a reason for this.

    2.7 Functions
        + Functions should be short and sweet, and do just one thing. They 
        should fit on one or two screenfuls of text(the ISO/ANSI screen size 
        is 80x24).


        + The maximum length of a function is inversely proportional to the
        complexity and indentation level of that function. 
        So if you have a conceptually simple function that is just one long
        (but simple) case-satement, where you have to do lots of small things
        for a lot of different cases, it's OK to have a longer function.

        ! However, if you have a complex function, use helper functions with 
        descriptive names(you can ask the compiler to in-line them if you 
        think it's performance-critical).


        + The number of LOCAL VARIABLES should not exceed 5-10. If this 
        function need more local variables, re-think it and split it into 
        smaller pieces.


        + In source files, separate functions with one blank line. If the
        function is exported, the EXPORT* macro for it should follw 
        immediately after the closing function brace line.
        
        Eg:
        >int system_is_up(void) 
        >{ 
        >    return system_state == SYSTEM_RUNNING; 
        >} 
        >EXPORT_SYMBOL(system_is_up); 

    2.8 Function return values and names
        + If the name of a function is an action or an imperative command, the
        function should return an error-code integer. If its name is like an
        assertion, then a boolean value should be returned.
        For example, "add work" is a command, and the `add_work()` function 
        returns 0 for success or -ERROR for failure. In the same way, "PCI
        device present" is a predicate, and the `pci_dev_present()` function
        returns 1 for success or 0 for failure.


        + Functions whose return value is the actual result of a computation,
        rather than an indication of whether the computation succeeded, usually
        indicate failure by returning some out-of-range result. Typical 
        examples would be functions that return pointers; they use NULL or the
        ERR_PTR mechanism to report failure.


    2.9 Centralized exiting of functions
        +? To goto statement comes in handy When a function exits from multiple
        locations and some common work such as cleanup has to be done.
        The rationale is:
            - unconditional statements are easier to understand and follow
            - nesting is reduced 
            - errors by not updating individual exit points when making 
            modifications are prevented
            - saves the compiler work to optimize redundant code away

        Eg: (powerpc/boot/stdlib.c) - Linux kernel C
        >unsigned long long int strtoull(const char *ptr, char **end, int base)
        >{
        >   ...
        >	if (base > 36)
        >		goto out;
        >   ...
        >out:
        >	if (end)
        >		*end = (char *)ptr;
        >   ...
        >}

    2.10 commenting
        + Comments are good, but there is also a danger of over-commenting.
        NEVER try to explain HOW your code works in a comment, it is much 
        better to write the code so that the working is obvious. Generally,
        comments are used to tell WHAT it code dose, not HOW.


        + Put the comments at the head of the function and try to avoid putting
        comments inside a function body.
        
        Eg: (powerpc/boot/stdlib.c) - Linux kernel C
        >/* Not currently supported: ...... */
        >unsigned long long int strtoull(...)


        + If function is so complex that it need to be separately commented
        parts of it, then it is time to simplify this function(refer to 2.7 
        Functions).


        + Linux style for comments is the C89 `/*...*/` style rather than
        C99-style `//...` comments.


        + The preferred style for long (multi-line) comments looks as followd.
        
        Eg: (powerpc/boot/stdlib.c) - Linux kernel C
        >/*
        > * stdlib functions
        > * ......
        > */

        It is also important to comment data, use just one data declaration 
        per line(no commas for multiple data declarations). This leaves room
        for a small comment on each item, explaining its use.

    2.11 Macros


3. Reference
https://www.oschina.net/translate/linux-kernel-coding-style?cmp&p=4

