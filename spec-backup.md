Due to recent events I've been worried about
the permanence of the Internet Archive so I'm reposting
the specs for Funge-97 and Befunge-96 here.

# Funge-97
```
Funge-97 Spec
Last Updated Oct 31 Chris Pressey, Cat's-Eye Technologies.

What is Funge-97?
Funges are programming languages whose programs are typically expressed in a given topological pattern and number of dimensions.
Funge-97 is the generalization of Befunge-97, which is an update of the Befunge-93 language definition. However, this document is intended for an audience not already familiar with Befunge-93.
Because Funge-97 is a class of programming languages, you can't just go and start programming in Funge - you have to choose a specific Funge first. Most popular by far is Befunge, which is two-dimensional and based on a Cartesian topology. Other Cartesian Funges include Unefunge (one-dimensional) and Trefunge (three-dimensional.) Since not all Funge commands are appropriate in all Funges, comparison to Befunge is often used to clarify points in this document.

Code and Data
All code and data is stored in two places: a last-in, first-out (LIFO) stack; and a matrix appropriate to the dimensionality, topology and tiling pattern of the Funge, usually called Funge-space, the playfield or the grid. Each value on the stack or node in Funge-space is usually called a cell.
Befunge-93 originally had 32-bit stack cells and 8-bit playfield cells, but in Funge-97 stack and playfield cells alike should be treated as signed machine words. That is, on a 32-bit machine, each cell would be a signed 32-bit integer. This data type is sometimes called the memsize of the Funge.
In Befunge-93, the playfield is restricted to 80 cells across and 25 cells down. No such limits are imposed on Funge-97 programs; the only assumptions any Funge-97 program is expected to make about size are that a) it can rely on all code and data that it puts in Funge-space not disappearing and b) it can rely on a behaviour consistant with being surrounded by cells which contain space instructions (32).
But of course this is the real world, and there are things like implementation limits. If the interpreter cannot provide that reliability (too many commands; command coordinates out-of-range) it should exit with an error. Thus the user can reasonably expect to have access to 2^31-1 playfield cells on all axes, but very few non-contrived cases will actually approach that barrier.
The playfield in Befunge-93 is also static, where every cell exists at all times in an array of 2000 bytes. The playfield in Funge-97 is considered dynamic, where cells don't necessarily exist unless they are referenced.
The co-ordinate mapping used for all Funges reflects the "Computer Storage" co-ordinate system used in screen graphics and spreadsheets; a larger y coordinate means further down the "page". Compared to a standard mathematical representation of the usual Cartesian co-ordinate system, it is "upside-down."
     Befunge-93                    Befunge-97
      ==========                    ==========
   0      x     79                      |-2,147,483,648
  0+-------------+                      |
   |                                    |    x
   |                               -----+-----
  y|                  -2,147,483,648    |    2,147,483,647
   |                                    |
   |                                   y|2,147,483,647
 24+


Stack
The Funge stack is a typical LIFO stack. In Befunge-93, two operations are possible on the stack: to push a cell onto the top of the stack, and to pop a cell off the top of the stack. Funge-97 allows the program to specify whether pushes and pops work on the top or bottom of the stack, as well as adding clearing and rolling functions.
If a program attempt to pop a cell off the stack when it is empty, no error occurs; the program acts as if it popped a 0.
Stacks are notated from bottom to top. This means the leftmost values listed in the documentation are the bottommost and the first to be pushed onto the stack.

Funge Source File Format
A Befunge-93 source (program) file name, by common convention, ends in the extension .bf. There is no common convention for the file name ending for all Funge-97 files. However, Befunge-97 files usually have the extension .bf, Unefunge-97 files usually have the extension .uf, and Trefunge-97 files usually have the extension .tf.
Befunge-93 source files are plain ASCII files which may not have "low ASCII" (less than 32, except where noted in the following paragraph) or "high ASCII" characters (greater than 126) embedded within them.
Funge-97 source files and made up of Funge characters. The Funge-97 character set overlays the ASCII subset used by Befunge-93 and may have characters greater than 127 present in it (and greater than 255 on systems where characters are stored in multiple bytes; but no greater than 2^31-1.) For a full table describing the Funge-97 character set, see Appendix C.
In both Befunge-93 and Funge-97, each line ends in one of these sequences of characters:
Line Feed (10)
Carriage Return (13)
Carriage Return, Line Feed (13, 10)
End-of-line markers do not appear in the playfield once the program is loaded.
In Befunge-93, each line can contain up to 80 significant characters before the "End of Line" marker. There can be up to 24 such lines in the source file. There are no such restrictions on Befunge-97 and the user can reasonably expect to be able to have up to 2^31-1 lines of 2^31-1 characters each.
Before load, every cell in Funge-space contains a space (32) character. These default contents are written over by characters in the program source when it is loaded. However, spaces in the program source do not overwrite anything in Funge-space; in essence the space character is transparent in source files. This becomes important when you use directives to include overlapping source files.
Unless changed by load directives, the source file begins at the origin of Funge-space. Subsequent columns of characters increment the x coordinate, and subsequent lines increment the y coordinate, if one is present. Subsequent lines in Unefunge are simply appended to the first, and the end of the source file indicates the end of the (single) line.

Funge-97 Directives
Any line in a Funge-97 source file may begin with the special directive character, which is =(equals sign) by default. When this directive character is used, the remaining characters of that line (up to the next end-of-line sequence) are considered an interpreter directive.
Thus, a directive takes the form
=d[irective] a1..an [d a1..an]...
where = is the directive character, d[irective] is a character string token, the first character of which is assigned a specific meaning (the remainder of the characters up to the following space are discarded), and all a's (arguments) are defined in the context of d.
See Appendix A for a table of option and load directives standard to Funge-97 files.
An unlimited number of interpreter directives can be entwined with code. The interpreter will first process all option directives as it would command-line options, then proceed loading the code, ignoring the option directives but paying attention to the load directives, which it uses to ensure the Funge program is loaded correctly.
All option directives are position-insensitive; they may be placed at the top, bottom, or middle of the program, as desired. Load directives must be placed before the source code they alter.
Interpreter directives do not appear in Funge-space once the program is loaded.

Script Directives
Before and only before all interpreter directives in a Funge source file, there can appear as many script directives as necessary. These are lines that begin with the # character, and they are entirely ignored by the Befunge interpreter. They are allowed so that Funge languages can be used for scripting much like Perl is. They do not affect Funge-space in any way.
If there are no interpreter directives in a file, there can be no script directives, either: # is then treated as an instruction.

The Instruction Pointer
The instruction pointer (IP) can be thought of as a vector which is the "current coordinate" of a running Funge program. It holds the same function as the instruction pointer (or program counter (PC)) in any other language or processor.
In most other languages the PC is restricted to unidirectional travel in a single dimension, with random jumps. However, in Funge, the IP describes another vector called the delta. Every tick, the IP executes it's current instruction (that is, the instruction at the location of the IP), then travels to a new location based on it's delta.
In two dimensions, we have the following terminology.
If the IP's delta is either (0,-1) (south), (1,0) (east), (0,1) (north), or (-1,0) (west), it is said to be traveling cardinally. This is the same as how a rook moves in chess and this is in fact the only way the IP can move in Befunge-93.
Any IP with a nonzero delta is considered moving. Any non-moving IP is said to be stopped. Any moving IP that is not traveling cardinally is said to be flying.
If the IP's delta is either (1,-1) (southeast), (1,1) (northeast), (-1,1) (northwest), or (-1,-1) (southwest), it is said to be flying diagonally. This is the same as how a bishop moves in chess.

Starting position
In Befunge-93, the IP always begins at (0, 0) and starts with a delta of (1, 0). In Funge-97, the IP begins where-ever the first valid instruction or space is parsed - essentially, the upper-left hand corner of the source file. It calls this the storage offset, but that's later (for p and g.) Unless the =o directive is used, the default starting position is the origin of Funge-space (in Befunge-97 that's (0, 0).)

Wrapping
Befunge-93 handles the case of the IP travelling 'off' the playfield by treating Befunge-space as a torus. If the IP leaves the west edge, it reappears on the east edge; if it leaves the south edge, it reappears at the north edge.
For various reasons, toroidal wrapping is problematic in Funge-97. Instead, we use a special wrapping technique that has more consistant results in this new, more flexible environment where the playfield can have an arbitrary size and the IP can fly. It is called same-line wrapping.
Same-line wrapping can be described in several ways, but the crucial idea it encompasses is this: unless the delta or position of the IP is changed by a command, the IP will always wrap such that it returns to the instruction it was on before it wrapped.
The mathematical description of same-line wrapping is known as Lahey-space wrapping, which defines a special topological space. It is generally of more interest to topologists and mathematicians than programmers. We won't cover it here, but it is included in Appendix D for completeness.
The algorithmic description of same-line wrapping can be described as backtrack wrapping. It is more of interest to Befunge Interpreter implementers than Befunge programmers. However, it does describe exactly how the wrapping acts in terms that a programmer can understand, so we will include it here.
When the IP attempts to travel into the whitespace between the code and the end of addressable space, it backtracks. This means that it's delta is reflected 180 degrees and it ignores all instructions. Travelling thusly, it finds the other 'edge' of code when there is again nothing but whitespace in front of it. It is then reflected 180 degrees once more (to restore it's original delta), stops ignoring, and executes the first available instruction.
  C
    \
     \|
     -
       0
        1
  A--> 11X  -->D

           \
            \|
            -B
It is easy to see at this point that the IP remains on the same line: thus the name. (Also note that this never takes any ticks in regards to multithreading, as would be expected from any wrapping process.)
Same-line wrapping has the advantage of being backward-compatible with Befunge-93's toroidal wrapping. It also works safely both when the IP delta is non-cardinal, and when the size of the program changes.

Space
As noted, by default every cell in Funge-space contains a space (32) character.
In Befunge-93, when interpreted as an instruction, a space is treated as a "no operation" or NOP. The interpreter does nothing and continues on it's merry way.
Without multithreading, Funge-97 acts much the same way, except technically, Funge-97 processes any number of spaces in no time whatsoever, and this becomes important when you have more than one IP on the playfield, which you'll learn about later. For an explicit NOP instruction in Funge-97, use o. Space also takes on special properties (in Funge-97) with a special mode of the interpreter called stringmode, which you'll also learn about later.

Instructions
All instructions are one character long. There are no multicharacter identifiers in Funge except the interpreter directives.

Direction Changing
A few instructions are essential for changing the delta of the IP. The > instruction causes the IP to travel east; the < instruction causes the IP to travel west. These instructions are valid in all Funges.
The ^ instruction causes the IP to travel north; the v instruction causes the IP to travel south. These instructions are not available in Unefunge.
The U instruction causes the IP to travel up (0,0,1); the D instruction causes the IP to travel down (0,0,-1). These instructions are not available in Unefunge or Befunge.
The ? instruction causes the IP to travel in a random cardinal direction appropriate to the number of dimensions in use: east or west in Unefunge; north, south, east or west in Befunge, etc.
The following instructions are Funge-97 only.
The ] "Turn Right" and [ "Turn Left" instructions rotate by 90 degrees the delta of the IP which encounters them. To remember which is which, visualize bicycle handlebars pointing towards you. These are not available in Unefunge.
The A "About Face" instruction reflects the delta of the IP 180 degrees.
The X "Absolute Vector" instruction pops a vector off the stack, and sets the IP delta to that vector.
A vector on the stack is stored bottom-to-top, so that in Befunge, X pops a value it calls dy, then pops a value it calls dx, then sets the delta to (dx, dy).

Integers
Instructions 0 through 9 push the values 0 through 9 onto the stack, respectively. (In Funge-97, a through f also push 10 through 15 respectively.) The + instruction pops two cells from the stack, adds them using integer addition, and pushes the result back onto the stack. The *instruction pops two cells from the stack, multiplies them using integer multiplication, and pushes the result back onto the stack. In this way numbers larger than 9 (or 15) can be pushed onto the stack.
For example, to push the value 123 onto the stack, one might write
 99*76*+
or you might write
 555**2-
Funge also offers the following arithmetic instructions:
the - instruction, which pops two values, subtracts the first from the second using integer subtraction, and pushes the result;
the / instruction, which pops two values, divides the second by the first using integer division, and pushes the result; and
the % instruction, which pops two values, divides the second by the first using integer dividion, and pushes the remainder.

Stack manipulation
The $ instruction pops a cell off the stack and discards it.
The : instruction pops a cell off the stack, then pushes it back onto the stack twice, duplicating it.
The \ instruction pops two cells off the stack, then pushes the first cell back on, then the second cell, in effect swapping the top two cells on the stack.
The following instructions are Funge-97 only.
The n "Clear Stack" instruction completely wipes the stack (popping and discarding elements until it is empty.)
The q "Toggle Queuemode" instruction toggles an internal flag called queuemode. When queuemode is active, cells are popped off the stack from the bottom instead of the top.
The i "Toggle Invertmode" instruction toggles an internal flag called invertmode. When invertmode is active, cells are pushed on the stack onto the bottom instead of the top.
When both queue- and invert-modes are on, it is as if the entire stack has been inverted; when one mode is on and the other is off, the stack acts like a queue. It can be tricky to determine if invertmode and/or queuemode is in effect. See "information retrieval," below.
The r "Roll" instruction pops a value n off the stack and "rolls" the stack n places: if n is positive, the bottom n elements become the top n elements. If n is negative, the top |n| elements become the bottom |n| elements. 0r does nothing (it acts like o.)
e.g
Before 2r       After 2r
5 4 3 2 1 0     1 0 5 4 3 2

Before 02-r     After 02-r
5 4 3 2 1 0     3 2 1 0 5 4

Strings
The instruction " toggles a special mode of the Funge interpreter called stringmode. In stringmode, every cell encountered by the IP (except " and in Funge-97, space) is not interpreted as an instruction, but rather as a Funge character, and is pushed onto the stack. A subsequent " toggles stringmode once more, turning it off. In Funge-97 stringmode, spaces are treated "SGML-style"; that is, when any contiguous series of spaces is processed, it only takes one tick and pushes one space onto the stack. This introduces a small backward-incompatibility with Befunge-93; programs that have multiple spaces and/or wrap while in stringmode will have to be changed to work the same under Funge-97.
Befunge-93		Befunge-97

"hello world"           "hello world"
"hello   world"         "hello "::"world"
There is also a ' "One-Shot Charmode" instruction in Funge-97. This pushes the Funge character value of the next encountered cell onto the stack. For example, the following two snippets perform the same function, printing a Q: 
"Q",
'Q,
Some instructions expect a Funge string on the stack as one of their arguments. The standard format for these strings is called 0"gnirts" - that is, a null-terminated string with the start of the string at the top of the stack.

Simple Terminal Input/Output
The . and , instructions provide numeric and Funge character/control output, respectively; they pop a cell off the stack and send it in numeric or Funge character format to the standard output, which is usually displayed by the interpreter in an interactive user terminal.
The & and ~ instructions provide numeric and Funge character/control input, respectively. They each suspend the program and wait for the user to enter a value in numeric or Funge character format to the standard input, which is usually displayed with the standard output. They then push this value on the stack.
Numeric output is formatted as a decimal integer followed by a space. More details on I/O are available in the "advanced" section.

Flow Control
The # instruction moves the IP one cell beyond the next instruction in it's path, bypassing or trampolining it.
The following instructions are in Funge-97 only.
The ; "Jump Over" command jumps over all subsequent commands until the next ; command. Like space, this takes zero ticks to execute, so that subroutines, comments, and satellite code can be insulated by surround it with ; instructions, with no effect on multithreading.
The J "Relative Jump" instruction pops a vector off the stack, and sets the IP location to (that vector + current location vector).
J acts as if it jumps past the given coordinates. The logic is that on every tick, the IP executes the current instruction, then moves to the next instruction. If the current instruction is "move to 7,-18", it moves to (7,-18)... then moves to the next instruction.
Z is just like J except that it jumps to the given coordinates, instead of past them, for convenience.
The j "Jump Forward" command works as a relative bridge which executes like # except that it pops a value off the stack, and jumps over that many spaces. e.g. 2j789. would print 9 and leave an empty stack. Negative values are legal arguments for j, such that 04-j@ is an infinite loop.
R "Reposition" pops two vectors from the stack; and set's the IP's storage offset to the first and location to the second. It then pushes it's original location and storage offset onto the stack, usually for a subsequent R instruction. The storage offset can make subsequent g and pcommands relative to subroutines.  R functions as both a GOSUB-like instruction, and a RETURN-like one.
The direction of the IP is independent of the jump to and from the procedure. The direction in which the IP travels is determined purely by other means. At a "returning" R, the IP jumps back to the position of the call, with the very same delta it had before it encountered the "returning" R.

Decision Making
The ! instruction pops a value off the stack and pushed the logical negation of it. If the value is zero, it pushes one; if it is non-zero, it pushes zero.
The ` instruction pops two cells off the stack, then pushes a one if second cell is greater than the first. Otherwise pushes a zero.
Funge has instructions that act like directional 'if' statements. The _ instruction pops a value off the stack; if it is zero it acts like >, and if non-zero it acts like <.
The | instruction pops a value off the stack; if it is zero it acts like v, and if non-zero it acts like ^.  | is not available in Unefunge.
The H instruction pops a value off the stack; if it is zero it acts like D, and if non-zero it acts like U.  H is not available in Unefunge or Befunge.
The w instruction pops two values off the stack and compares the second to the first. If the second is smaller, w acts like [, and turns left. If the second is greater, w acts like ], and turns right. If the second is equal, w does not affect the IP's delta. This command is not available in Befunge-93, nor Unefunge.

Storage
The g "Get" and p "Put" instructions are be used to store and retrieve data and code in Funge-space.
In Befunge-93, g pops a vector (that is, it pops a y value, then an x value,) and pushes the character in the playfield cell at (x, y). p pops a vector, then a value, then places that value in the playfield cell at (x, y).
In Funge-97, each IP has an additional vector called the storage offset. Initially this vector is the same as the starting location of the IP. As such, it works to emulate Befunge-93. The arguments to g and p are the same, but instead of pointing to absolute locations in Funge-space, they reference a cell relative to the storage offset. The storage offset can be set with the R instruction.
The G "Relative Get" and P "Relative Put" operators work as g and p do, but use vectors which are relative to the current IP location. For example, 00G, would print a G.
The ( "Get Left Hand", ) "Get Right Hand", { "Put Left Hand", } "Put Right Hand" instructions work as the G and P instructions do, but they use a vector perpendicular to the delta of the IP. If the IP is travelling left-to-right and the stack contains a "*" when it encounters a }, it will pop the * off the stack and put it one cell below the }. When a flying IP encounters a (, ), {, or }, the location of the value is flying too. If in the above example the IP was travelling (2, 0), the * would be placed two cells below the }.)

Information Retrieval
The V instruction tests another instruction for validity in the current Funge. It pops a cell off the stack, and if the instruction specified by that value is implemented in the current interpreter, acts like v. Otherwise acts like o.
The Q instruction tests the queuemode and invertmode settings. If neither mode is on, acts like o. If both modes are on, acts like A. If only queuemode is on, acts like [, and if only invertmode is on, acts like ].
The Y instruction pushes the current delta, as a vector, on the stack.

Multithreading
Befunge-93 does not allow for multithreaded execution. However, Funge-97 defines a list of any number of concurrently running instruction pointers called the IP list. In Funge-97, IP's are sometimes called threads and each has it's own location, delta, and stack.
You can also think of the Funge-97 interpreter as having an internal and imaginary clock which produces ticks sequentially. Each tick, the IP list is processed: instructions encountered by each IP are dealt with in the sequence the IPs appear on the list, and each IP then moves as specified by it's delta.
The list is always processed repetitively, sequentially, and in the same direction, and when IP's are deleted they fall out and the next one which would normally execute anyway actually executes.
Creating additional IP's is done with the T "Split" instruction, available in Funge-97 only. It causes the current IP to be duplicated, and this duplicate is added to the end of the IP list. This means it is executed for the first time before the parent IP is next executed.
When a child IP is borne unto Befunge-space thusly, it's location, storage offset, and stack are all copied verbatim from the parent IP's. The child IP's delta is reflected 180 degrees from it's parent's, though.
The @ instruction kills the current IP. If it is the only extant IP (as it would be if you were not using multithreading,) the program ends. If it was not the last in the IP list, there would then be a gap in the list: in this case the top part of the list "shifts down" and the next thread in the list is executed.
The E "End Program" instruction, only in Funge-97, ends the entire program, regardless of the number of IP's. It also pops a cell off the stack and uses that value as the return value of the Funge interpreter to the operating system.

Befunge System
The y "Befunge System Call" instruction is a partable interface to the underlying computer and operating system. y first pops a cell off the stack, calls it the sysid and uses it to specify what system information to be gathered, or what system resources to be called, in a non-system-dependant-way.
sysid  result
0      pushes the id of this IP, which uniquely identifies it
1      pushes the number of cells currently on the stack
2      pushes a vector representing the current IP's location
3      pushes a vector representing the current IP's delta
4      pushes a vector representing the current IP's storage offset
5      toggles current output filed's mode (binary or Funge) and returns it
6      pushes current sec, min, hr, day, mo, yr onto stack



File I/O
File I/O is done with the F instruction, which is only available in Funge-97.
Each IP in Funge-97 has two file descriptors or fileds: filed 0, the input filed, and filed 1, the output filed. By default the output filed points to the standard (terminal) output, and the input filed points to the standard (terminal) input.
The F instruction is used to open and close files, and to attach those files to the IP's fileds. Each IP can only attach each of it's fileds to one file at a time, so to have simultaneous access to two files when both are output or both are input, multithreading must be used.
F pops a cell it telling it which filed (0 or 1) to operate on, and a null-terminated 0"gnirts" string for the filename. If the file can be opened, F attaches it to the specified filed, which determines the file mode (0=read; 1=write).
A null filename, @ or E closes all open fileds for the executing IP and resets the filed to standard input and output.
The following program prints "hello" to the file "hello.txt" then "done" to the standard output:
0"txt.olleh"1F"olleh",,,,,01F"enod",,,,@
An F call with a single argument of -1 (01-F) explicitly flushes all fileds.
As noted in the previous section, 5y toggles the binary mode for the current output filed. When binary mode is on, bytes are passed as bytes, no translation is done. When it's off, the Funge 10->eol, 32-126->ASCII, 127+->extended conversion takes place. EOF conversion is also done if required by the underlying OS. Binary mode is off by default.

Advanced Input/Output
When using ~, receieving an input of 10 on a terminal means "User pressed ENTER (or equivalent)", and receieving 32-126 means "User entered this ASCII character." In Funge-97, receieving 128-(2^31-1) means "User entered this valid extended character somehow with his or her input device." Anything else is from some system-specific IO sequence completely unknown to and unfiltered by Funge -- use it at your own risk and do not expect portability.
~ is 'like ASCII'--that is, it reads 'cooked' keystrokes, such as 'shift-m' ('M') and 'f1'. It doesn't know anything about keys like alt, shift, capslock, and control. It doesn't know when a key is pressed vs when it is released; it only sees keystrokes in a buffer. It suspends the entire program, and it takes one multithreading tick.
In a more ideal universe, ~ may suspend only it's own the thread. Suspending the entire program is the usual and entirely forgivable real behaviour.
When using ,, outputting 10 to the terminal means "Start a new line," and outputting 32-126 means "Print this ASCII character." In Funge-97, outputting 128-(2^31-1) means "Print this extended character which is assumed to be the same [visual] size as any regular character and is indeed printable." Outputting anything else is unpredictable -- use at your own risk and do not expect portability.
',' is the logical inverse of '~'. it accepts Funge characters (like 10) and cooks them into meaningful output (like eol).
Programmers should not rely on Funges to truncate data such as control map (character) values or exit codes. On a computer where these are a full machine word, a full machine word will be used.

Strange Instructions
s toggles switchmode on and off. In switchmode, the pairs of instructions [ and ], { and }, and ( and ) are treated as switches. When one is executed, the cell it is located in is immediately overwritten with the other instruction of the pair, providing a switching mechanism and a way to seperate coincident IP's.
MWlmtxBK are reserved for Befunge-98 use.

Appendix A. Directives
Option Directives
Token           Args                Description
---             ---                 ---
d[isable]       cmds                disable commands (binds each cmd in cmds to NOP)
r[equire]       cmds                require commands (aborts if any cmd in cmds is not supported)
l[anguage]      langid              OK to use language(s) as specified by langid
                                       b93     Befunge-93 (Default)
                                       b97     Befunge-97 (Default if = is present)
                                       u97     Unefunge-97
                                       t97     Trefunge-97
                                       bg97    BeGlad-97
                                       bb97    BefBots-97
#[comment]      comment             comment, use if you feel you must
*[any]          forward             forward compatibility, do not use
Load Directives
Token           Args                Description
---             ---                 ---
=		char                change directive character (default =)
o[rientation]	[pad pdd] coord     specify the page-across and page-down dimensions
                                    and coordinates of the upper-left corner of source
i[nclude]	[coord] filename    insert additional source code at coord (or here)


Appendix B. Funge Instructions

Appendix C. Funge Character/Control Set Map
-2bil..-1: "keystroke and term controls" (some input only, some output only)
0..31 : "ASCII controls" (only 10 is currently defined to mean EOL)
32..126 : "ASCII printable characters" (all are input/output and fixed-width)
127 : "delete control" (not yet defined)
128..2bil: "extended printable characters" (machine and font specific)
 a-z
  0-9
  `-=[]\;',./*+
  enter
  tab
  backspace
  esc
  f1-f12
  arrows
  insert
  delete
  home
  end
  page up
  page down
  shift
  ctrl
  alt
  caps lock
  num lock
  scroll lock
  key lock
  pause
  sysrq
  break
  clear screen
  print screen
  reset
  turbo
  power
  any

Appendix D. Lahey-Space
Imagine a sphere of radius one centered one unit above the origin. (The origin becomes the previously mysterious south pole.) Imagine a plane placed two units above the origin, i.e. resting on top of the sphere. Now take the origin as well as a special point and remove it from the sphere.
One thing you might notice is that when you draw a line from the south pole/origin to a point on the plane, you intersect the sphere exactly once (and once at the south pole.) 
So, no matter what direction you head, you reach the south pole. If you go the other way you reach the south pole from the other direction. So we might call this a line in our space. I haven't figured out exactly what the line looks like, but we might surmise that if the IP headed out on a line, and that empty spaces took no time to execute even if there was an infinite number of them, the program would return along the same line, going in the opposite direction. This would imply that it would hit the same points and viola - consistant wrapping.
Glossary
 backtrack wrapping
  Befunge
  Befunge-space
  cardinal IP
  coordinate system
  directive character
  flying IP
  Funge
  instruction pointer (IP)
  interpreter directive
  Lahey-space
  load directive
  lost in space
  non-executing
  option directive
  origin
  program counter (PC)
  same-line wrapping
  storage offset
  tick
  Trefunge
  Unefunge
  yield
  zero-tick
Thanks
Thanks to everyone who contributed.
```

# Befunge-96 spec
```
Befunge-96
Edit #4 - Jan 2 Chris Pressey, Cat's-Eye Technologies.Table of Contents
I. Background
II. Breaking New Ground
IIa. Much, Much Larger Playfield
IIb. Befunge Source Headers
IIc. The PC List: Multitasking
IId. New Ways to do Old Tricks
III. Discussion

Befunge-96
I. Background
See the Befunge-93 documentation on the World-Wide Web: http://www.cats-eye.com/cet/soft/lang/befunge/doc.html
II. Breaking New Ground
Befunge-96 will have some interesting new commands and somewhat updated syntax; however it will remain backwards-compatible with existing Befunge-93 sources.
Some of the new features are an unlimited playfield, source code headers, multitasking using a list of program counters instead of a single one, and some new commands.
IIa. Much, Much Larger Playfield
The grammar of Befunge-96 no longer restricts the Befunge source file to 80 columns or 25 rows; the new limits are at least 65536 columns and at least 65536 rows. (Some independant versions of the interpreter may provide even larger playfields.) Practically speaking, implementation is most effective with a well-designed hash table.
In Befunge-93 the source was stored in a 80x25 array of bytes, or 2000 bytes, regardless of the actual size of the source. Befunge-96 requires the interpreter allocate at least 1 byte for each command in the input source file plus implementation overhead of addressing a dynamic sparse matrix.
IIb. Befunge Source Headers
To include addition information in Befunge source code without interfering with the program code itself, Befunge-96 introduces the ; "Interpreter Directive" command. It may only appear in the 0 column - the character position immediately to the right of the left margin - and indicates that the text on this line is either a comment or an interpreter directive. In either case, it indicates the line is not a valid row of Befunge code and should not be paid attention to in the context of running the program.
; followed by a space is a comment
;$I rand6.bf 42 18
; that was a compiler directive to include another .bf source
The interpreter directives are:
$I filename x y
Inserts another includable Befunge-96 source. The filename is inserted at an offset (x, y) from the origin What would normally be located at (0,0) in the included source is copied to (x,y), (1,0) is copied to (x+1,y), etc.
$R sourcechar befungechar
Treats every occurance of the command sourcechar as the command befungechar. Allows remapping of the source file.
$C command constant
Defines a constant value to be pushed onto the stack by a particular extentible null command. For example, to include hexidecimal syntax in a befunge program, preced it with:
;$C a 10
;$C b 11
;$C c 12
;$C d 13
;$C e 14
;$C f 15
$A command "nullextendor"
Defines the action taken by a particular extentible null command, such as a Befunge subprogram, an API call, or a DOS shell.  There is currently no agreed-upon format for the nullextendor discriptor. Leave it alone. However, it might look something like:
"sub:sourcefilename"
"api:apiname:functionname:argumentmap"
"run:command"
IIc. The PC List: Multitasking
The grammar of Befunge-93 specified one program counter moving in any of four directions. The grammar of Befunge-96 defines a list of any number of them with seperate locations and directions but sharing the same stack. (This keeps it interesting.)
In each 'tick' in the Befunge interpreter, the PC list is processed: code encountered by each program counter in dealt with in the sequence the counters appear on the list, and each counter then moves in it's direction.
The list is always processed "sanely"; that is, repetitively, always processed in the same direction, sequentially (one PC after another,) and when PC's are deleted they fall out and the next one which would normally execute anyway executes for real.
Multiplying PC's requires the addition of a new command T , which causes the current program counter - the one which encountered the T - to be duplicated, and this duplicate is added to the end of the PC list with it's direction reflected backwards. The meaning of the @ command has changed: instead of ending the entire program, as it did in Befunge-93, it now ends only the program counter which encountered it, removing it from the PC list and discarding it. If it was not the last in the list, there would then be a gap in the list: in this case the top part of the list "shifts down" and the next task in the list is executed.
IId. New Ways to do Old Tricks
The g "Get" and p "Put" commands have not really been deprecated, but their use is discouraged (like goto has been in C and Pascal.) It has been decided that they are not totally in the "spirit" of Befunge - whatever that is. Befunge seems to like relative operations better than absolute ones.
The G "Relative Get" and P "Relative Put" operators work as g and p did, but use relative offsets instead of absolute ones. For example, 00G, would print a G.
But these commands don't create a compact feel. The ( "Get Left Hand", ) "Get Right Hand", { "Put Left Hand", } "Put Right Hand" commands work as the G and P commands do, but relative one space perpendicular to the direction of the program counter. If the program counter is travelling left-to-right and the stack contains an ASCII "*" when it encounters a }, it will pop the * off the stack and put it one cell below the }.
There is also now a ' "One-Shot Stringmode" command. This pushes the ASCII value of the next encountered cell onto the stack. For example, the following two snippets perform the same function, printing a Q: 
"Q",
'Q,
The h "Holistic Delta" command pops a value off the stack and makes that value the "Holistic Delta" of the current program counter. This value is added to the value of all code and data that this program counter encounters further in the Befunge code.
The i "Invert" command inverts the entire stack. (A really "friendly" trick to pull on other PC's while multitasking.)
The j "Jump" command works as a relative bridge which executes like # except that it Pops a value off the stack, and jumps over that many spaces. e.g. 2j789. would print 9 and leave an empty stack.
Using up the last of the non-alphabetic characters, [ "Rotate Left Hand" and ] "Rotate Left Hand" rotate the direction of the program counter which encounters them 90 degrees, left or right. (There is no reflect 180 degree operator; but one can be simulated with T@.)
Commands now defined, all that's really left in low-order ASCII are the lowercase letters a-z except for h, i, j, v, g, and p, and all the capital letters except T, G, and P; this set of symbols is called the set of extendable nulls. They are treated as null commands unless they are previously assigned to:
Hex Option (a, b, c, d, e, and f are treated as hex 10-15)
a Befunge subprogram
an API call (such as Graphics, Turtle, or Sound calls)
a DOS shell call (using portable syntax??)
The Directive syntax for assigning extendable nulls is incomplete as of yet. Such a design addendum will be made in a later version of this document.
III. Discussion
If anyone has anything at all to say, please feel free. I'd like suggestions, questions and constructive criticism to be posted on the Befunge mailing list.
If you are not a subscriber, e-mail befunge-request@cats-eye.com with the text "subscribe your-email@your.dom.ain" (w/o quotes) in the Subject: field.
If you are a subscriber, e-mail your suggestions and comments to befunge@cats-eye.com
Befunge Archive: http://www.cats-eye.com/cet/soft/lang/befunge/ 
```
