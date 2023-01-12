###Structural Typing
Type script is structurally typed, and therefore has some strange rules when dealing with interfaces and objects
```typescript
interface Vector2D {
  x: number;
  y: number;
}

interface Vector3D {
    x: number;
    y: number;
    z: number;
  }

  function calculateLength(v: Vector2D): number {
    return Math.sqrt(v.x * v.x + v.y * v.y)
  }

  function normalize(v: Vector3D): Vector3D {
    let length = calculateLength;
    return {
        x: v.x / length,
        y: v.y / length,
        z: v.z / length,
    }
  }
```
In this example no errors are thrown, even though in normalize() a 3D vector is getting passed to a 2D vector. 
This works because its *structure* was compatible, that is, both interfaces had an x and y and typescript 
checks the stucture of the types, rather than their names (as in the case with nominal type systems i.e. C++)

Typescript also makes assumptions about objects. Take this for example.

```typescript
  function normalize(v: Vector3D): Vector3D {
    let length = calculateLength(v);
    for (const axis of Object.keys(v)){
      const coord = v[axis] // error
      length += coord
    }
    return length;
  }
```
This will return an error that says coord has any type for 2 reasons. 
1. because there could be any properties on the object, not necessarily just numbers
2. [axis] (the key) hasn't been given a type of string. 

```typescript
interface Vector3D {
  [key: string]: number;
  x: number;
  y: number;
  z: number;
}
```
This locks the key with the type of string, which allows you to use it to index the object,
and it locks the value as a number.

```typescript
  class C {
    foo: string;
    constructor(foo: string) {
      this.foo = foo;
    }
  }

  const c = new C('instance of C');
  const d: C = {foo: 'object literal'};
```
Because typescript is structurally typed, const *d* is permitted to be of type C, due to the fact 
that the foo property is present with a string as the value. 

###The any type
There are language services for interfaces and types, autocomplete and tsserver are a nice combo.

Avoid any whenever you can, it minimizes the value that typescript provides.
