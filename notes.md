# set state
## named var
`a = 1`  
->
```
[_
for a in [1]
]
```

### block of vars
```
a = 1
b = a + 1
```
->
```
[_
for a in [1]
for b in [a + 1]
]
```

## functions
```
def x(a,b):
  return a+b
x(1,2)
```
->
```
[x(1,2)
for x in [lambda a,b : a+b]
]
```

### recursion
```
def fib(n):
  if n in {1,2}:
    return 1
  return fib(n-1) + fib(n-2)
fib(4)
```
->
```
[fib(4)
for _ in [[]]
for fib in
 [
  lambda n : 1 if n in {1,2}
    else fib(n-1) + fib(n-2)
 ]
]
```

## object
```
class A:
  y = 1
  def x(self, arg):
    return arg + self.y
a = A()
a.x(2)
```
->
```
A = type('obj', (object,), {'y':1, 'x': lambda self, arg : arg+self.y})
a = A()
a.x(2)
```

### object mutate self
```
class A:
  y = 1
  def x(self):
    self.y += 1
    return self.y
a = A()
a.x()
```
->
```
A = type('obj', (object,), {'y':1, 'x': lambda self : (setattr(self, 'y', self.y+1),self.y)[1]})
a = A()
a.x()
```

# control flow
## error supression
->
```
class Supress:
  def __enter__(self): pass
  def __exit__(self, *args): return True
supress = Supression()
with supress: x = 1
with supress: x = 1/0
# x is 1, and no error is thrown
```

## throw error
`raise Exception('hi')`  
->
`(_ for _ in ()).throw(Exception('hi'))`  

# other
## import
```
import math
math.inf
```
->
```
[math.inf
for math in [__import__('math')]
]
```
