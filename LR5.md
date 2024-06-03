# Lab Report 5
In this lab report I will be discussing the last few concepts and a reflection of my experience in the class overall! 

## Part 1: Debugging Scenario 
In this section I will be simulating a debugging scenario to showcase what I have learned in the last few weeks of this class. Specifically, I will have a focus on using JDB te debug and reach a solution! 

### Student Piazza Post 
<table>
  <tr>
    <td style="background-color: #e6e6fa; color: black;">

**Strange I/O bug for factorial function-- help!**   
Hi y'all! 
I'm working on a Java project that calculates the factorial of a number using a recursive method however when I run the program it returns the wrong results for certain inputs. 

For example, when I input 13, it returns 1932053504 instead of 6227020800 (expected). I attached a screenshot below of my terminal output and the Java code-- I suspect the issue might be related to how the recursion is handled but I'm not too sure... Any help is much appreciated!
<br><br>
</td>
  </tr>
</table>

```java
public class Factorial{
    public static void main(String[] args){
        int num = Integer.parseInt(args[0]);
        System.out.println(factorial(num)); //value check print
    }

    public static int factorial(int num){
        if (num == 0){
            return 1; //if the number is 0 automatically return 1
        }else{
            //call recursion of factorial method 
            return num * factorial(num - 1);  
        }
    }
}
```
<table>
  <tr>
    <td style="background-color: #e6e6fa; color: black;">
Terminal output: 
</td>
  </tr>
</table>

```bash
javac Factorial.java
java Factorial $1
./run_factorial.sh 13
1932053504
```


<table>
  <tr>
    <td style="background-color: #d4f1c5; color: black; padding: 10px;">
    Hi! Just by skimming it seems like the issue is related to the input itself but lets use JDB to figure this out! Set up a breakpoint in the factorial method and let me know what you see? 
</td>
  </tr>
</table>


<table>
  <tr>
    <td style="background-color: #e6e6fa; color: black;">
Okay sounds good! Using JDB I set the following breakpoints: 

</td>
  </tr>
</table>


```bash
javac -g Factorial.java
jdb Factorial 13
> stop in Factorial.factorial
> run 13
``` 

<table>
  <tr>
    <td style="background-color: #e6e6fa; color: black;">
at every before and after every recursive call back to factorial:
</td>
  </tr>
</table>

```bash
> step
Step completed: "thread=main", Factorial.factorial(), line=11 bci=4
11  if (n==0) {
> print n
n = 13
> step
Step completed: "thread=main", Factorial.factorial(), line=14 bci=12
14  return n * factorial(n - 1);
> step
Step completed: "thread=main", Factorial.factorial(), line=10 bci=0
10  public static int factorial(int n) {
> print n
n = 12

```
<table>
  <tr>
    <td style="background-color: #e6e6fa; color: black;">
all the way down to the end of the method. This being said, I did not glean any insight as to why my code was wrong, so I added the following line and recompiled: 
</td>
  </tr>
</table>

```java
System.out.println("factorial(" + n + ") = " + result);
```
<table>
  <tr>
    <td style="background-color: #e6e6fa; color: black;">
This gave me the following result: 
</td>
  </tr>
</table>

```java
factorial(1) = 1
factorial(2) = 2
factorial(3) = 6
factorial(4) = 24
factorial(5) = 120
factorial(6) = 720
factorial(7) = 5040
factorial(8) = 40320
factorial(9) = 362880
factorial(10) = 3628800
factorial(11) = 39916800
factorial(12) = 479001600
factorial(13) = 1932053504
```

<table>
  <tr>
    <td style="background-color: #e6e6fa; color: black;">
I see that at the values are consistent with a factorial up until 

```factorial(12)``` however I am not sure why... 

</td>
  </tr>
</table>

<table>
  <tr>
    <td style="background-color: #d4f1c5; color: black; padding: 10px;">
    Great first steps so far! To guide you in the right direction, what datatype is your input? Be mindful of the accepted values of this datatype
</td>
  </tr>
</table>

<table>
  <tr>
    <td style="background-color: #e6e6fa; color: black;">
    Huh, good point! I had to google it but the integer ceiling is 2,147,483,647 ... 13! exceeds that... would the solution be float? 
</td>
  </tr>
</table>


<table>
  <tr>
<td style="background-color: #d4f1c5; color: black; padding: 10px;">
Hm.. thats close! But if we want to keep outputs consistent with no decimals, there is another datatype would be a better option. </td>
</tr>
</table>

<table>
  <tr>
    <td style="background-color: #e6e6fa; color: black;"> oh wait you're right! I used long and it prevented the overflow, thank you! 
</td>
  </tr>
</table>

### Recap: The problem and the fix

As seen above, the problem the student was experiencing as a overflow error. 

Their original code was as follows: 

**The file and directory structure**
```bash
project/
└── Factorial.java
```

**The contents of each file before fixing the bug**

Factorial.java:
```java
public class Factorial{
    public static void main(String[] args){
        int num = Integer.parseInt(args[0]);
        System.out.println(factorial(num)); //value check print
    }

    public static int factorial(int num){
        if (num==0){
            return 1; //if the number is 0 automatically return 1
        }else{
            //call recursion of factorial method 
            return num * factorial(num - 1);  
        }
    }
}
```

**Lines to trigger the bug**
```bash
javac Factorial.java
java Factorial $1
./run_factorial.sh 13
1932053504
```

**A description of what to edit to fix the bug**
To fix the overflow error the student had to change the input parameter from accepting the ``int`` datatype to the ``long`` datatype to accomodate the exponentially increasing number load. ``13!`` specifically exceeds the integer ceiling making the datatype change necessary. 

## Part 2: Reflection
All in all I am extremely glad I have taken this class and moreover very satisfied with the "get your hands dirty" experience that the labs have given me. Specifically, through these labs I was able to engage in concepts like user testing the code of others as well as understanding the concepts of localhosting and access across domains-- something I would not be able to do alone. 

Despite being an aspiring data scientist I was shocked at how little I knew about domains, which, to the credit of this class, has forced me to expand my breadth of knowledge. Not only did I learn to view them akin to filepaths, but also how queiries work, the concept of localhosting on the same and different server, accessing someone elses localhost, and running. This served to be extremely useful in some of my MLE projects when implementing tensorflow.js and setting up my website portfolio! 

Ultimately, through these labs I was able to learn and practice so many topics in real time such as testing and evaluating the code of others through accessing their forks, something I would not be able to do on my own. I am extremely grateful for this experience and would recommend this class to anyone looking to crack down on the fundamental skills and techniques of being a stronger programmer.