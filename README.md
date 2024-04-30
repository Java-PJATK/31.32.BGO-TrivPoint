# BGO-TrivPoint  
  
31 BGO-TrivPoint/TrivPoint.java 61  
32 BGO-TrivPoint/Main.java 62  

# Section 8. Classes  

## 8.1 Basic concepts  

In object oriented programming we deal with **objects** that we can think of as aggregates of some pieces of information together with actions which can be performed on them. 

Similar objects (sharing the same type of information and the same actions) are described by the definition of a **class** which encapsulates properties of all object of this class that will be created in our program.  

Each object of a class can contain some information in the form of **fields** — fields have fixed types and names and will be present in any object of the class; of course their values are usually different in different objects. 

All these values, which can be subject to modifications during the execution of a program, define current state of an object. Values of fields in an object which are accessible to the user, at least for reading, are sometimes called its **attributes**.  

Actions are represented by functions (called **methods**) which operate on objects — methods can return information about the state of an object, modify it, etc.  

They are always invoked on a specific object and have direct access to all fields of this particular object they were invoked on, and also to all fields of _any_ object of the same class.  

Invoking a method on an object is sometimes described as sending a message to this object.  

Fields and methods of a class are collectively called its **members**, or, strictly speaking, its non-static members; there are also static members: static fields and static functions (methods).

## 8.2 Classes and objects

Classes define new types. A class can contain

* fields (non-static); 
* static fields;  
* constructors;  
* methods (non-static);  
* static functions (methods);  
* static and non-static initializer blocks;  
* definitions of other classes (the so called inner classes) or enumerations.  

Objects (instances) of classes are always created on the heap (in free memory). They are always anonymous — there is no way to give a name to an object.  

It is also not possible to create an object locally on the stack — only references can be local have names. Operator `new` creates an object and returns a reference (which in C/C++ corresponds to a pointer, i.e., address) to the object created.  

We can store this reference (address) in a named reference variable. If there are no references to an object left, the object may be removed by the garbage collector sub-process of the JVM (although we don’t know if and when it will happen).  

In the example below, we define a new type (class) `TrivPoint`, which is supposed to describe points on a plane:  

* `x` and `y` are (non-static) fields. Each object of the class will contains two such `int`s — of course their values may be different for each object (point).
  
* `translate`, `scale`, `getX`, `getY`, `setX`, `setY` and `info` are (non-static) methods;  

* `infoStatic` is a static function (static method).

There are no static fields here. Also, there is no constructor defined, but in fact there is one, called default constructor, created by the compiler (more about constructors in a moment).

## Listing 31 BGO-TrivPoint/TrivPoint.java

```java
// BGO-TrivPoint/TrivPoint.java
 
public class TrivPoint {
    public int x, y;

    public void translate(int dx,int dy) {
        x += dx;
        y += dy;
    }

    public void scale(int sx,int sy) {
        x *= sx;
        y *= sy;
    }

      // setters
    public void setX(int x) {
        this.x = x;     // this required
    }
    public void setY(int yy) {
        y = yy;         // this.y assumed
    }

      // getters
    public int getX() {
        return this.x;
    }
    public int getY() {
        return y;       // this assumed
    }

      // static
    public static void infoStatic(TrivPoint p) {
        System.out.println("[" + p.x + "," + p.y + "]");
    }

      // non-static
    public void info() {
        System.out.println("[" + this.x + "," + y + "]");
    }
}
```

The class `Main` (below) by itself doesn’t serve any purpose — it is just a wrapper for the main function which must be defined (with a signature exactly as shown) somewhere and is the entry point to any Java application.  

In main we create an object of type `TrivPoint`; it will contain fields x and y — we could have created several objects of this type: each would contain ‘its own’ fields x and y, independent of x and y of any other object of the same class. Note the syntax used to create an object: we have to write round parentheses, as when calling a function. 

In parentheses, we can pass arguments to a constructor. In our case, there is only the default constructor (by definition, it doesn’t take any arguments) created by the compiler, as we haven’t defined any custom constructor ourselves. 

Note that p is not the name of any object; it is the name of a separate local reference variable whose value is the address of the object proper (which itself is anonymous).  

In C++ we would call such a variable a pointer; in Java it is called a reference (although is has nothing in common with references in C++.)  

## Listing 32 BGO-TrivPoint/Main.java

```java
public class Main {
    public static void main(String[] args) {
        TrivPoint p = new TrivPoint();
        p.x = 1;
        p.y = 2;
        p.info();
        p.setX(3);
        p.info();
        System.out.println("x=" + p.getX() + "; " +
                           "y=" + p.getY());
        TrivPoint.infoStatic(p);
        p.infoStatic(p);          // not recommended!

        p.scale(2,3);
        p.info();
        p.translate(1,-3);
        p.info();
    }
}
```


Let us briefly explain the difference between static functions and methods (non-static).  

The method `scale` seems to have two parameters. However, it’s a `method` (there is no `static` keyword in its declaration). 

This means that it has one additional parameter, not shown in the list of parameters (as it would be, e.g., in Python). 

This ‘hidden’ parameter is of type `TrivPoint`, i.e., it is a reference to an object of this type. 

Therefore, `scale` has in fact three parameters and hence, invoking it, we have to specify three arguments. Let’s look at its invocation in line 10

`p.scale(2, 3);`

Two arguments are given explicitly and they correspond to parameters sx and sy of the method. What about the third? It will be the reference to the object on which the method is invoked, in our case the value of the variable p which holds the reference (address) to the object created in line 3. So, conceptually, the invocation is equivalent to something like  

```
TrivPoint::scale(p, 2, 3);
```

Therefore, methods are always called on a specific object (which, of course, must exist). 

The reference to this object is passed (pushed on stack) to the method as one of the arguments. 

Inside the body of a method we can refer to it — but what is its name there? 

As it was not mentioned in the list of parameters, we were not able to give it any name. 

Therefore, the name is fixed once for all and is `this`.  

Now look at the definition of the method `scale`. We use the name `x` there. There are variables `sx` and `sy` declared there, but no `x`, so compilation should crash. 

In such situation, however, the compiler will automatically add `this` in front of undeclared variable (x in this case) to obtain `this.x`. 

But this means ‘field x of the object referenced to by `this`’ — this is exactly the field x in the object the method was invoked on (i.e., the one referenced to by `p` in the `main` function). The same, of course, applies to `y`.  

The situation is different for static functions. Here, we don’t have any hidden parameters (therefore `this` doesn’t exist inside static functions). If a static function is to have access to an object of type `TrivPoint`, the reference to this object must be passed explicitly — as in function `infoStatic` in our example.

More details and more examples will be presented in the following sections.
