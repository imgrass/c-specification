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

        Eg: (alpha/include/asm/futex.h) - Linux kernel C
        >static inline int futex_atomic_op_inuser (int encoded_op, 
        >                                          u32 __user *uaddr)
        >{
        >    ......
        >    return ret;
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
		>if((init_atu & (1 << i)) == (1 << i))
		>	plat_pci->nr_controllers++;

        Eg: (powerpc/boot/stdio.c) - Linux kernel C
		>if (base == 16)
		>	size -= 2;
		>else if (base == 8)
		>	size--;
        
        !This does not apply if one branch of a conditional statement is a single statement. Use braces in both branches.
        
        Eg: if(condition) {
            do_one();
            do_two();
        } else {
            do_one_();
        }

    2.4 Placing Spaces

3. Reference
https://www.oschina.net/translate/linux-kernel-coding-style?cmp&p=4
