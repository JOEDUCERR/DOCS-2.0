Java for IPA

Basics:
* Used to build Applications for web, desktop, mobile and also code games.
* Open source and free
* Close to c or c++

--------------------------------

Basic Code example:
public class Main {
    public static void main(String[] args) {
        String name = "John";
        System.out.println(name + " is my name");
    }
}

--------------------------------

* Java class name must always start with capital letter
* every java file must have main() method as it is where the program starts running.
* System.out.println():
	System = built in java class
	out = member of System (output)
	println() = method for print line
	(you can replace it with print() but it will not add a newline at the end.)
* Every statement must be ended with semicolon ;
* // is usefd for single line comments and /**/ for multi.

-----------------------------------

Variables:
Data types = String, int, float, char, boolean
* declaration: TYPE NAME = value;
* add final before type to make it immutable
* Types cannot change later in the program for a variable.

var keyword: this helps Java assign data type according to value. (You cant declare it without assigning value). Actual value is just to make code easier to read.

Primitive Data Types:
byte 	Stores whole numbers from -128 to 127 to (save memory)
short 	Stores whole numbers from -32,768 to 32,767
int 	Stores whole numbers from -2,147,483,648 to 2,147,483,647
long 	Stores whole numbers from -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 (remember to end the value with L)
float 	Stores fractional numbers. Sufficient for storing 6 to 7 decimal digits
double 	Stores fractional numbers. Sufficient for storing 15 to 16 decimal digits (Much safer to use in important calculations)
boolean 	Stores true or false values
char 	Stores a single character/letter or ASCII values

Non Primitive Data Types:
Strings, Arrays, Classes

--------------------------------------------

Type Casting:
Widening Casting (automatic) - converting a smaller type to a larger type size
byte -> short -> char -> int -> long -> float -> double

Narrowing Casting (manual) - converting a larger type to a smaller type size
double -> float -> long -> int -> char -> short -> byte 
E.g.:
double a = 9.7d;
int b = (int) a;

Operators:
= 	x = 5 	x = 5 	
+= 	x += 3 	x = x + 3 	
-= 	x -= 3 	x = x - 3 	
*= 	x *= 3 	x = x * 3 	
/= 	x /= 3 	x = x / 3 	
%= 	x %= 3 	x = x % 3 	
&= 	x &= 3 	x = x & 3 	
|= 	x |= 3 	x = x | 3 	
^= 	x ^= 3 	x = x ^ 3 	
>>= 	x >>= 3 	x = x >> 3 	
<<= 	x <<= 3 	x = x << 3

Comparison:
== 	Equal to 	x == y 	
!= 	Not equal 	x != y 	
> 	Greater than 	x > y 	
< 	Less than 	x < y 	
>= 	Greater than or equal to 	x >= y 	
<= 	Less than or equal to 	x <= y 	

Logical:
&&  	Logical and 	Returns true if both statements are true 	x < 5 &&  x < 10 	
||  	Logical or 	Returns true if one of the statements is true 	x < 5 || x < 4 	
! 	Logical not 	Reverse the result, returns false if the result is true 	!(x < 5 && x < 10) 	

----------------------------------------------------------

Strings:
a.length()
toUpperCase()
indexOf(): find position of word or letter
equals(): comparison
trim(): remove whitespace
concat()

Math:
abs()
pow(): power
round(): round off

---------------------------------------------------------

## Switch:

int expression = 2;
switch (expression) {
	case 1:
		print(correct);
		break;
	case 2:
		print(wrong);
		break;
}
* break keyword breaks out of switch block
* 'default' instead of cases runs if there is no matching case

## for:

for (statement 1; statement 2; statement 3) {
  // code block to be executed
}

Multiplication example:
public class Main {
    public static void main(String[] args) {
        for (int i=1;i<=3;i++){
            for(int j=1;j<=3;j++) {
                System.out.println(i*j+" ");
            }
            System.out.println();
        }
    }
}

for-each loop: exclusively used for traversing through elements in an array
for (type variableName : arrayName) {
  // code block to be executed
}

----------------------------------------------------------------

Break: Jump out of a switch statement or a loop
Continue: Breaks on the condition

Arrays:
String[] a = {"Hello", "Hi", "Yo"};
int[] num = {1,2,3,4};

System.out.println(num[2]); #3

* To create a array and specify its size
String[] a = new String[4]; #useful if you do not know values
* Multidimention: int a[][]

Check sum of all elements:
public class Main {
    public static void main(String[] args) {
        int a[] = {1,2,3};
        int sum = 0;

        for(int i=0;i<a.length;i++) {
            sum = a[i] + sum;
        }
        System.out.println(sum);
    }
}

--------------------------------------------------------

Methods:

static void myMethod(parameter1, parameter2) {
	System.out.println(parameter1 + parameter2);
}

public static void main(String[] args){
	myMethod("Hello", "Hi");
}

Method Overloading: Have multiple methods with same name but different parameters or parameter types.

-------------------------------------------------------------

Recursion: Making a function call itself to solve complexities in problems in a faster manner.

Halting condition: Condition on which the recursive loop stops calling the method (itself). Usually an if statement.

E.g.
public class Main {
    public static int sum(int k){
        if(k > 0) {
            return k + sum(k-1);
        } else {
            return 0;
        }
    }

    public static void main(String[] args){
        int x = 10;
        System.out.println(sum(x));
    }
}

Countdown E.g.
public class Main{
    static void countdown(int k){
        if(k > 0) {
            System.out.println(k + " ");
            countdown(k-1);
        }

    }
    public static void main(String[] args) {
        countdown(5);
    }
}

Try: Factorial, 

Object: car is an object which can have attributes and methods. And the class is like an object constructor or blueprint for object.

public class Main {
	int x = 5;
	
	public static void main(String[] args){
		Main obj = new Main();
		System.out.println(obj.x);
	}
}

E.g. 1
public class Main{
    int x = 5;

    public static void main(String[] args) {
        Main myObj1 = new Main();
        Main myObj2 = new Main();
        System.out.println(myObj1.x);
        System.out.println(myObj2.x);
    }
}
