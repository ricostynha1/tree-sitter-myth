# tree-sitter-myth

## Base changes to python Grammar
This is the Myth language Grammar for tree-sitter
The grammar is forked from the python grammar as it is mostly simmilar with the python grammar, 
main differences are:
- when atributinh = is copy := is pass by reference
a = 5
b = a
b += 1
print(a) // gives 5
print(b) // gives 6

a = 5
b := a
b += 1
print(a) // gives 6
print(b) // gives 6

In this way it is more clear what is copied and not.

Myth will for now not support classes and instead will gave structs that can have appended fucntions

struct something:
  x : Int
  y : Uint
  c : String
  def valid_somthing():
    etc

List compreension has the regular contructs syntaxes:
[x for x in range(10)] is intead written as
[ for x in range(10): x] // This is clearer as it has the same syntax as regular code
and more generagl
[ 0, for x in range(1:10): x , 24]
and for double
[for x in range(10): for y in range(20): (x,y)] 
[for x in range(10): 
  for y in range(20): 
    (x,y)]

Heavy support for types as myth is static types and anotation language for refined specificaions also supporting refined types 
## Completly new things to python grammar

## Types
Basic types:

Types are always Uppercase (only thing that start with upper case)
Int -> not specify resolution integer (stated on compiler modifidable)
Uint -> not specify resolution uint (stated or modified in compler)
Iinf -> infite integers (non bouded)
Uinf ->  infinite uintgers (non bounded)
String 
Ix -> x bytes inters  (I32/I64)
Ux -> x bytes uinteger 
Float -> not specify resolution float 
FX -> float with x number of bytes (F32 regular float/F64 double/F16 other types can exist also)
Finf -> infite resolution floats (they are reals only used in specification)
Bool

Basic groups of types
mut [Int] list elements same type can change size  List
[Int*5] Fixed sized arrays (cant change size)      Array
(Int,String, String) tuples different type elemnts  compact form cant change size  Tuple
(Int,String*2)
mut (String,Int*3) -> Tuples can change size (+ means expandable)

mut {Type->Type2} -> dictionary where each elemnt have the same types
mut {type_hashable->*} -> dictionary were last elemnt can change types 

Type grammar:
Name,
{Name:refinememt}
parsable pois -> nada pode comecar por maiuscula exceto tipos

Refined types
     new_type_name     base_type   refinement
type Nat             =  Int      {self>0};
type Natbigger5      =  Nat      {self>5} ;

This predicate has to be SMT convertable, passible to be defined in decidable logics
talvez possa limitar a forma como estas funcionam

Type polymorphism and subtypeing: 
generics A_ (single letter underscore, followed or not by other things), or A_{name} (single upper)
type List = Null | Cons(A_, List)

Typeclasses
typeclass _Printable:
  def show(): String -> ()
                    //definition of typecasses
def print_to_screen {A_: _Printable, _Other; B_ .. } (x:A_, y:A_)
model {a}

(This is for arrays though)
Like zig for multiple structs can be usefull to dislay them in memory differenty.
struct cenas;
cenas b[] 
regular like in C
cenas b[]. 
structure here have the disposition in memory fields of all continuous and then next field

## Specification
// Functions with only specifications can only be used inside type refienemtns or other specifications
def is_sorted(l): Bool
    ensures (result == true) <==> (forall i<j : f[i] <= f[j])

// fucntion with specification and code to execute them can be used everywhere (specificaiton used for)
// type refinements etc, the other part for execution

def is_sorted(l): Bool
  ensures (result = true) <==> (forall i,j :: 0<i<j<len(l) ==> f[i] <= f[j]) //dafny
  ensures (result = true) <==> (forall i<j ==> f[i] <= f[j])          // i want to contract to this
  for(i, l.len):
    for(j in range(i, l.len)):
      l[i]> l[j]
      return false
  return true                          spec 
type sortedList      = List        {is_sorted(self)};

type GreaterX(x: Nat) = {Nat: self>x}
typeGreaterGenericx(A: Type, b) = {A:self>b}
type Greater10 = GreaterX(10)
  type(type_arguements) 
  {type_name(type_arguments) : refinement}  

Specification language

def nome(i):
  ensures output > 20
  requires i>10 

  forall i: f[i] > 100  (bound indices always infered)
  exist i: f[i] == 30

                        
invariants and asserts also.
          
Type_name 
Type_name : refinement
Type_name(Type_arguments) 
Type_name(Type_arguments) : refinemetn

## Algeberaic Effects
// This is an effect that receives an Int and demands a number to output
// from the handler
effect Divison.by_zero : Int -> Int

method divison(a, b) -> String effect Divison.by_zero
    if(b == 0):
        res = perform Divison.by_zero(a)
        return res
    else:
        return a/b

// Is safe_div pure?
// Yes it only gives same assertions indepednt on a or b.
safe_div (a,b):
    try divison(a,b):
      
    handle (effect):
      Divison.by_zero(a):
         resume with a
         // Can do other things like exit etc etc

     (has it andles all effects it is pure i belive !)
      
Imagine a python try catch would be
method divison(a,b):
    if(b == 0):
        throw Divison.by_zero

safe_div(a,b):
    try divison(a,b):

    except:
        return 0

Need to think in a lot more examples and use cases will follow again blog not sure
also and the type? what is it?      

# Implementation order first types
- Adapted grammar to handle refined types and other (created test examples for it)
- Need to make new type declarations
- Need to make typeclasses
# than alebraic effects
# Than base changes
# Than effect types

# Credits of the original python grammar too:

[![CI][ci]](https://github.com/tree-sitter/tree-sitter-python/actions/workflows/ci.yml)
[![discord][discord]](https://discord.gg/w7nTvsVJhm)
[![matrix][matrix]](https://matrix.to/#/#tree-sitter-chat:matrix.org)
[![crates][crates]](https://crates.io/crates/tree-sitter-python)
[![npm][npm]](https://www.npmjs.com/package/tree-sitter-python)
[![pypi][pypi]](https://pypi.org/project/tree-sitter-python/)

Python grammar for [tree-sitter][].

[tree-sitter]: https://github.com/tree-sitter/tree-sitter

## References

- [Python 2 Grammar](https://docs.python.org/2/reference/grammar.html)
- [Python 3 Grammar](https://docs.python.org/3/reference/grammar.html)

[ci]: https://img.shields.io/github/actions/workflow/status/tree-sitter/tree-sitter-python/ci.yml?logo=github&label=CI
[discord]: https://img.shields.io/discord/1063097320771698699?logo=discord&label=discord
[matrix]: https://img.shields.io/matrix/tree-sitter-chat%3Amatrix.org?logo=matrix&label=matrix
[npm]: https://img.shields.io/npm/v/tree-sitter-python?logo=npm
[crates]: https://img.shields.io/crates/v/tree-sitter-python?logo=rust
[pypi]: https://img.shields.io/pypi/v/tree-sitter-python?logo=pypi&logoColor=ffd242
