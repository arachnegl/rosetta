

dynamically-typed, garbage-collected, object-oriented programming language with functional programming capabilities


## control flow

### conditional

conditional execution of blocks of code.

```supercollider
if (5 > 3) { "True".postln };  // Output: "True"
if (x > 5) { "greater".postln } { "lesser".postln };
```



### loops

```supercollider

5.do { arg i; i.postln };  // prints 0 to 4


(
var i = 0;
while { i < 3 } {
    i.postln;  // Output: 0, 1, 2
    i = i + 1;
};
)

// pattern based
Pseq([1, 2, 3], inf).do { arg val; val.postln };  // Output: 1, 2, 3, 1, 2, 3, ...
```

### exception

```supercollider
// exception handling
try { /* code */ } { "An error occurred".postln };
```

## functions

```supercollider
// named functions
f = { arg x; x * x };
f.value(5);  // Outputs 25

f = { arg x, y; x + y };
f.value(5, 10);

// method calls
5.sin  // Output will be the sine of 5
```

## object-oriented


### classes

```supercollider
MyClass {
    var myVariable;
    
    *new { arg value; ^super.newCopyArgs(value) }
    
    play { myVariable.postln }
}

```

### inheritance

```supercollider
MyDerivedClass : MyClass { /* ... */ }

```


## event system

```supercollider
Pbind(\freq, Pseq([400, 500, 600], inf)).play;
```
