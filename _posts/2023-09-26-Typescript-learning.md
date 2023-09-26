---
layout: post
title: "Typescript learning and practice"
date:   2023-09-26 15:56:39 +1000
categories: fullstack
---



[LIVE DEMO](https://dianaleo.github.io/FinanceLogger/)

what typescript can do and javascript cannot do?
- type declarations
- interfaces
- generics
- enums and tuples


## 1. Compile
In terminal
```
tsc ***.ts -w
```
will compile it to ***.js, while __-w__ stands for watching.

## 2. Type Basics
### 2.1 Implicit
```
let character = 'mario';
character = 20;//error
```
Will be an compile error, because compiler infer the type.

But sometimes the inference is not reliable:
```
const circ = (diameter) =>{
    return diameter * Math.PI;
}
console.log(circ('hello'));
```
There will be no compile error, and browser will log an "NaN"
We want the problem solved during compile time, then we explicitly give a type.
```
const circ = (diameter:number) =>{
    return diameter * Math.PI;
}
console.log(circ('hello'));//error
```

### 2.2 Explicit

<span style="color: #fcbdb8">It is easy to mix ":" and "=".</span>
let ninja<span style="color: #fcbdb8">=</span>{ name: any, age: any } is not correct
Should be
let ninja<span style="color: #fcbdb8">:</span>{ name: any, age: any }

##### Arrays/Objects: 

If we don't initialize it, we cannot use array/objects functions
```
let ninjas: string[];
ninjas.push('sss')//error
```
> Uncaught TypeError: Cannot read properties of undefined (reading 'push')
When we initialize it, we canuse their functions
```
let ninjas: string[] = [];
ninjas.push('sss')
```

##### Union types
- For arrays, we need ()
```
let ninjas: (string|number|boolean)[]=[]
ninjas.push('sss')
ninjas.push(5)
ninjas.push(true)
```
- For normal variables, we don't need ()
```
let uid: string|number;
```

##### Objects
```
let ninjaOne: object;
ninjaOne = { name: 'yoshi', age: 30 };
```
```
let ninjaTwo: {
  name: string,
  age: number,
  beltColour: string
};
ninjaTwo = { name: 'ken', age: 20, beltColour: 'black' };
```
Either way, there will be a compile error if we use ``ninjaOne.values()``

### 2.3 Dynamic(any) types
```
let ninja: { name: any, age: any }
```


## 3. Better Workflow & tsconfig

To compile multiple ts files at the same time, and specify folders, we create a **tsconfig.json** by ```tsc init```
Then ```tsc -w``` to watch everything.

Inside **tsconfig.json**, there are something important:
- ```"rootDir": "./src"``` specify compile from, but not only from
- ```"outDir": "./public"``` specify compile to
- ```"include":["src"]``` only compile ts files inside src, not ts files outside src.

"include" is in parallel with "compilerOptions"


## 4. Function Basics

### 4.1 More generic
**Function** has to be **capitalized**, while other types like **string** doesn't.
```
let greet: Function;
greet = () => { console.log('object') }
```

### 4.2 Parameter type declaration
```
const add = (a: number, b: number, c: (number | string) ) => {
    console.log(a + b);
    console.log(c);
}
add(5, 10);//error
```
- Optional parameter method 1
```
const add = (a: number, b: number, c?: (number | string) ) => {
    console.log(a + b);
    console.log(c);//undefined
}
add(5, 10);
```
- Optional parameter method 2
```
const add = (a: number, b: number, c: (number | string) = 10 ) => {
    console.log(a + b);
    console.log(c);
}
add(5, 10);
```

### 4.3 Return type declaration
Return type is inferred by typescript compiler. Doesn't need to explicitly declare return type.
If you add readability, you can.
```
const add = (a: number, b: number, c?: (number | string) ): number => { ... }
```

### 4.4 Function signature
Same as function declaration in C++: ```int max(int, int)```
In typescript, the syntax looks weird to me: 
```
let max: (a: number, b:number) => number;
```
Declaration is not compulsary in typescript.


## 5. Type Aliases

For reusability

```
type StringOrNum = string | number;
type ObjWithName = {
    name: string,
    uid: StringOrNum
}

const logDetails = (uid: StringOrNum, item: string) => { ... }

const greet = (user: ObjWithName) => { ... }
```

## 6. DOM & Type Casting
As, HTML*Element
```
const form = document.querySelector('.new-item-form') as HTMLFormElement;

//input
const type = document.querySelector('#type') as HTMLSelectElement;
const toFrom = document.querySelector('#tofrom') as HTMLInputElement;
const details = document.querySelector('#details') as HTMLInputElement;
const amount = document.querySelector('#amount') as HTMLInputElement;

form.addEventListener('submit', (e: Event) => {
    e.preventDefault();
    console.log(
        type.value,
        toFrom.value,
        details.value,
        amount.valueAsNumber,
    );
})
```


## 7. Classes
```
class Invoice {
    readonly client: string;
    private details: string;
    private amount: number;

    constructor(c:string,d:string,a:number){
        this.client = c;
        this.details = d;
        this.amount = a;
    }

    // Same as
    // constructor(
    //     readonly client: string,
    //     private details: string,
    //     public amount: number
    // ) {}

    format() {
        return `${this.client} owes \$${this.amount} for ${this.details}.`
    }
}

const invOne = new Invoice('Mario', 'work', 1000);
const invTwo = new Invoice('Luigi', 'work', 300);

let invoices: Invoice[] = [];
invoices.push(invOne);
invoices.push(invTwo);

invoices.forEach(inv => {
    inv.client;
    inv.client = 'somthing else';//error
    inv.details;//error
    inv.amount;
    inv.amount = 100;
    inv.format();
})
```

## 8. Modules

We are using typescript for front end. 
So instead of **commonJS**, we should set ```"module": "es2015"```

And we add a ```type="module"``` into the **<script src='app.js'></script>** tag in html

Then we can separate files, and use **export** and **import from**.
Just be careful, when importing, file extension **.js** has to be given explicitly, otherwise, the compiler will look for ts files, which will cause a **404 module not found** error.


## 9. Interfaces
### Since interfaces are only for declaration, and we still have to implement them, why do we need them?
Interfaces definition is good for writing standardized code. In the scenario of big project, team architect is responsible for interfaces definition, or cleaning some uneccessary interfaces. The aim is to demonstrate a clear and simplified instruction on which business logics need to be implemented. Meanwhile, it can prevent naming problem and code cluttering.

### Interface definition
Interfaces just define how an object should look
```
interface HasFormatter {
    format():string;
}
```
### Interface implementation
```
class Invoice implements HasFormatter {
    format() {
        return `${this.client} owes \$${this.amount} for ${this.details}.`
    }
}
class Payments implements HasFormatter {
    format() {
        return `${this.recipient} is owed \$${this.amount} for ${this.details}.`
    }
}
```
### Using Interfaces as Types
```
let docOne:HasFormatter;
let docTwo:HasFormatter;

docOne = new Invoice('Mario', 'work', 1000);
docTwo = new Payments('Luigi', 'work', 300);

let docs:HasFormatter[]=[];
docs.push(docOne);
docs.push(docTwo);

console.log(docs);
```


## 10. Generics

### Usage scenario 1
If we didn't specify the fields of the input object, compiler won't know.
```
const addUID = (obj:object)=>{
    let uid = Math.floor(Math.random()*100);
    return {...obj, uid};
}
let docOne = addUID({name:'diana',age:30});
docOne.name;//error: name does not exist
```
If we use generics, compiler will know the input object fields when it is passed in.
```
const addUID = <T>(obj:T)=>{
    let uid = Math.floor(Math.random()*100);
    return {...obj, uid};
}
let docOne = addUID({name:'diana',age:30});
docOne.name;//No errors
```

### Usage scenario 2
When we have a type like below: the type of two fields are known, but data type is unknown, we may take any type of data.
```
interface Resource{
    uid: number,
    resourseName:string,
    data:'???'
}
```
We can use generics.
```
interface Resource<T>{
    uid: number,
    resourseName:string,
    data:T
}

const docThree: Resource<object>={
    uid: 1,
    resourseName:'person',
    data:{name:'diana'}
}
const docFour: Resource<string[]>={
    uid: 1,
    resourseName:'shopping list',
    data:['milk','egg','tissue']
}
```



## 11. Enums

```
enum ResourceType { BOOK, AUTHOR, FILM, DIRECTOR, PERSON};

interface Resource<T>{
    uid: number,
    resourseType:ResourceType,
    data:T
}

const docThree: Resource<object>={
    uid: 1,
    resourseType:ResourceType.BOOK,
    data:{title:'gone with the wind'}
}
const docFour: Resource<object>={
    uid: 1,
    resourseType:ResourceType.PERSON,
    data:{name:'diana',age:30}
}

```
In console.log, ```ResourceType.BOOK``` is a number.



## 12. Tuples

The type of the data in each position in a tuple is fixed once it is initialized.
```
let arr = ['string', 30,true];
arr[0]=false;//allowed
arr[1]='a string';//allowed
arr = [20, 'yes',20];//allowed

let tup:[string, number, boolean]=['hello',40,true];
tup[0] = false;//error
```

Now we can modify the form submit handeler to this:
```
let inputs = [toFrom.value, details.value, amount.valueAsNumber];

if(type.value==='invoice'){
    doc = new Invoice(...inputs);//error
}else{
    doc = new Payments(toFrom.value, details.value, amount.valueAsNumber)
}
```
However, compiler doesn't know the type of **inputs**. It only knows ```let inputs: (string|number)[]```
It doesn't know the type on each position.
If we want to reuse the code, we have to use **tuple**.
```
let inputs:[string,string,number];
inputs = [toFrom.value, details.value, amount.valueAsNumber];

if(type.value==='invoice'){
    doc = new Invoice(...inputs)
}else{
    doc = new Payments(...inputs)
}
```