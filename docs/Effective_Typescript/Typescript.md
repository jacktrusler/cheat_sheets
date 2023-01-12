# Typescript
Time to learn how to become a more based JavaScript Developer, thanks Michaelsoft.

###tsconfig.json
found in the root of typescript apps, many settings auto applied when doing 
``
npx create-react-app <name> --template typescript
``
consider adding: 
``
"compilerOptions": {
  "noImplicitAny": true,
  "strictNullChecks": true (this also checks undefined)
}
``
Interfaces, types and type annotations are removed during compilation to javascript.



