# Constructors, destructors, and inheritance
It is possible for the base class, the derived class, or both to have constructor and/or destructor functions. When a base class and a derived class both have constructor and destructor functions, the constructor functions are executed in order of derivation. **The destructor functions are executed in reverse order**. 

That is, the base constructor is executed before the constructor in the derived class. 

The reverse is true for destructor functions: **the destructor in the derived class is executed before the base class destructor**.

So far, you have passed arguments to either the derived class or base class constructor. When only the derived class takes an initialization, arguments are passed to the derived class constructor in the normal fashion. 

However, if you need to pass an argument to the constructor of the base class, a little more effort is needed: 

All necessary arguments to both the base class and derived class are passed to the derived class constructor.
Using an expanded form of the derived class' constructor declaration, you then pass the appropriate arguments along to the base class
The syntax for passing an argument from the derived class to the base class is as 
```cpp
derived-constructor(arg-list) : base(arg-list) {
 	// body of the derived class constructor
}
```
Here base is the name of the base class. It is permissible for both the derived class and the base class to use the same argument. 
It is also possible for the derived class to ignore all arguments and just pass them along to the base. 
```cpp
// Illustrates when base class and derived class
// constructor and destructor functions are executed
#include <iostream>
using namespace std;
class base {
  public:
   base() { cout << "Constructing base\n"; }
   ~base() { cout << "Destructing base\n"; }
};
class derived : public base {
  public:
   derived() { cout << "Constructing derived\n"; }
   ~derived() { cout << "Destructing derived\n"; }
};
int main() {
   derived obj;
   return 0;
}
```
This program displays
```cpp
Constructing base
Constructing derived
Destructing derived
Destructing base
```
In the following example both the derived class and the base class take arguments: 
```cpp
#include <iostream>
using namespace std;
class base {
   int i;

  public:
   base(int n) {
      cout << "Constructing base\n";
      i = n;
   }
   ~base() { cout << "Destructing base\n"; }
   void showi() { cout << i << "\n"; }
};
class derived : public base {
   int j;

  public:
   derived(int n) : base(n) {
      // pass argument to the base class
      cout << "Constructing derived\n";
      j = n;
   }
   ~derived() { cout << "Destructing derived\n"; }
   void showj() { cout << j << "\n"; }
};
int main() {
   derived o(10);
   o.showi();
   o.showj();
   return 0;
}
```
Pay special attention to the declaration of the derived class constructor. Notice how the parameter n (which receives the initialization argument) is both used by derived() and base().

# Multiple Inheritance
There are two ways that a derived class can inherit more than one base class. 

First, a derived class can be used as a base class for another derived class, creating a multilevel class hierarchy. In this case, the original base class is said to be an indirect base class of the second derived class. 

Second, a derived class can directly inherit more than one base class. In this situation, two or more base class are combined to help create the derived class. 

There several issues that arise when multiple base classes are involved. Those will be discussed in this section. If a class B1 is inherited by a class D1, and D1 is inherited by a class D2, the constructor functions of all the three classes are called in order of derivation. Also the destructor functions are called in reverse order. 

When a derived class inherits multiple base classes, it uses the expanded declaration:
```cpp
class derived-class-name : access base1, access base2, ... , access baseN
{
 	// ... body of class
};
```
Here **base1** through **baseN** are the base class names and access the access specifier, which can be different for each base class. 

When multiple base classes are inherited, constructors are executed in the order, left to right that the base classes are specified. Destructors are executed in reverse order. 

When a class inherits multiple base classes that have constructors that requires arguments, the derived class pass the necessary arguments to them by using this expanded form class constructor function:
```cpp
derived-constructor(arg-list) : base1(arg-list) ,..., baseN(arg-list)
{
 	// body of derived class constructor
}
```
Here base1 through baseN are the names of the classes. 

 

**Virtual base classes**

A potential problem exists when multiple base classes are directly inherited by a derived class. 

To understand what this problem is, consider the following class hierarchy: 

Base class Base is inherited by both Derived1 and Derived2. Derived3 directly inherits both Derived1 and Derived2. 

However, this implies that Base is actually **inherited twice** by Derived3. First it is inherited through Derived1, and then again through Derived2. This causes ambiguity when a member of Base is used by Derived3. Since two copies of Base are included in Derived3, is a reference to a member of Base referring to the Base inherited indirectly through Derived1 or to the Base inherited indirectly through Derived2? 

To resolve this ambiguity, C++ includes a mechanism by which only one copy of Base will be included in Derived3. This feature is called a **virtual base class**

In situations like this, in which a derived class indirectly inherits the same base class more than once, it is possible to prevent multiple copies of the base from being present in the derived class by having that base class inherited as virtual by any derived classes. Doing this prevents two or more copies of the base from being present in any subsequent derived class that inherits the base class indirectly. The virtual keyword precedes the base class access specifier when it is inherited by a derived class. 

 

***Diamond Problem - Programmer and Software Interview Questions and Answers***
```cpp
// This program uses a virtual base class.
#include <iostream>

using namespace std;
class Base {
  public:
   int i;
};

// Inherit Base as virtual
class Derived1 : virtual public Base {
  public:
   int j;
};

// Inherit Base as virtual here, too
class Derived2 : virtual public Base {
  public:
   int k;
};

// Here Derived3 inherits both Derived1 and Derived2.
// However, only one copy of base is inherited.
class Derived3 : public Derived1, public Derived2 {
  public:
   int product() { return i * j * k; }
};

int main() {
   Derived3 ob;
   ob.i = 10;  // unambiguous because virtual Base
   ob.j = 3;
   ob.k = 5;
   cout << "Product is: " << ob.product() << "\n";
   return 0;
}
 ```
Output:
```cpp
Product is: 150
 ```
If Derived1 and Derived2 had not inherited Base as virtual, the statement **ob.i=10** would have been ambiguous and a compile-time error would have resulted. 

# Polymorphism
The word **polymorphism** means having many forms. Typically, polymorphism occurs when there is a hierarchy of classes and they are related by inheritance.

C++ polymorphism means that a call to a member function will cause a different function to be executed depending on the type of object that invokes the function.

Consider the following example where a base class has been derived by other two classes.
```cpp
#include <bits/stdc++.h>
using namespace std;

class Base {
  protected:
   int a, b;

  public:
   Base(int _a, int _b) {
      a = _a;
      b = _b;
   }
   void func() { cout << "Inside Parent Class" << endl; }
};

class Derived1 : public Base {
  public:
   Derived1(int a, int b) : Base(a, b) {}

   void func() { cout << "Inside Derived1 Class" << endl; }
};

class Derived2 : public Base {
  public:
   Derived2(int a, int b) : Base(a, b) {}

   void func() { cout << "Inside Derived2 Class" << endl; }
};

// Main function for the program
int main() {
   Base *b;
   Derived1 d1(10, 7);
   Derived2 d2(10, 5);

   // Store the address of Derived1
   b = &d1;

   // Call Derived1 Func.
   b->func();

   // Store the address of Derived2
   b = &d2;

   // Call Derived2 Func.
   b->func();

   return 0;
}
```
When the above code is compiled and executed, it produces the following result −
```cpp
Inside Parent Class
Inside Parent Class
```
The reason for the incorrect output is that the call of the function func() is being set once by the compiler as the version defined in the base class. 

This is called **static resolution** of the function call, or **static linkage** - the function call is fixed before the program is executed. This is also sometimes called **early binding** because the **func()** function is set during the compilation of the program.

But now, let's make a slight modification in our program and precede the declaration of func() in the Base class with the keyword **virtual** so that it looks like below
```cpp
class Base {
  protected:
   int a, b;

  public:
   Base(int _a, int _b) {
      a = _a;
      b = _b;
   }
   virtual void func() { cout << "Inside Parent Class" << endl; }
};
```
After this slight modification, when the previous example code is compiled and executed, it produces the following result −
```cpp
Inside Derived1 Class
Inside Derived2 Class
```
This time, the compiler looks at the contents of the pointer instead of it's type. Hence, since addresses of objects of d1 and d2 classes are stored in *b the respective func() function is called.

As you can see, each of the child classes has a separate implementation for the function func(). This is how **polymorphism** is generally used. 

 

*You have different classes with a function of the same name, and even the same parameters, but with different implementations.*

# Virtual Functions
A **virtual** function is a function in a base class that is declared using the keyword **virtual**. Defining in a base class a virtual function, with another version in a derived class, signals to the compiler that we don't want static linkage for this function.

What we do want is the selection of the function to be called at any given point in the program to be **based on the kind of object for which it is called**. This sort of operation is referred to as **dynamic linkage**, or **late binding**.

# Pure Virtual Functions
It is possible that you want to include a virtual function in a base class so that it may be redefined in a derived class to suit the objects of that class, but that there is no meaningful definition you could give for the function in the base class.

We can change the virtual function **func()** in the base class to the following
```cpp
class Base {
  protected:
   int a, b;

  public:
   Base(int _a, int _b) {
      a = _a;
      b = _b;
   }
   virtual void func() = 0;
};
```
The = 0 tells the compiler that the function has no body and above virtual function will be called **pure virtual function.**
