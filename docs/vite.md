# Vite 
![Vite](./svgs/vite.svg "Vite")
*Vite means Blazingly Fast*

How I set up this Vite project  
[prettier pre-commit](https://prettier.io/docs/en/precommit.html)  
[husky-prettier-eslint](https://www.coffeeclass.io/articles/commit-better-code-with-husky-prettier-eslint-lint-staged)

### Using pnpm
`pnpm` is a faster version of `npm` that links packages instead of copying.

`pnpm create vite`  
follow the prompts then make sure eslint is installed (should be by default)  
`pnpm install eslint --save-dev`  
quick note on `--save-dev`: Use save dev so you can use the packages while developing the project, but if 
you don't want them bundled and built when you deploy the project.  
`pnpm install --save-dev --save-exact prettier eslint-config-prettier`  
Then create an eslint config by following the prompt from:  
`pnpm create @eslint/config`  
Install husky and pretty-quick for pre-commit hooks and formatted output.  
`pnpm install husky && pnpm install pretty-quick`  

Then change a bunch of settings in  

.eslintrc.json  
tsconfig.json  
.husky/pre-commit  
