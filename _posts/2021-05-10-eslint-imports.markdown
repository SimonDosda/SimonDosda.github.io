---
layout: post
title: Automatically Remove Unused Imports From Your JS Projects
date: 2021-05-10
cover_image: 2021-05-10-feature.jpg
author: Simon Dosda
categories: javascript tool
excerpt_separator: <!--more-->
---

> Photo by [The Creative Exchange](https://unsplash.com/@thecreative_exchange).

Recently I went across a substantial Angular project with a lot of unused imports. Not that it is a big deal but it looked quite messy, which seems too bad to me as they can easily be automatically removed.

<!--more-->

In this article, I will show you how to do so with any node-based project using _ESLint_. It might sound like a very cosmetic thing and it kinds of is, but I believe having too many unused imports can really hurt code readability.

And as a bonus, we will also sort our imports by alphabetical order.

## Add _ESLint_ to your project

[ESLint](https://eslint.org/) is a static code analyzer and will prevent you from making a lot of dummy mistakes, like using undeclared variables or expecting an output from a function that doesn't have any.

It can also enforce code style rules, like the type of quotes you want to use or defining if code lines should always end with semicolons; even though you will most likely use a code formatter like [Prettier](https://prettier.io/) to take care of this.

If you don't use it yet, you will need to add _ESLint_ to your project. You can easily install it and generate its configuration file with _npm_.

```bash
npm install eslint --save-dev
npx eslint --init
```

You can then check the errors and warnings from _ESLint_ by running it in your project.

```bash
npx eslint <source-directory>
```

## Automatically remove unused imports

To be able to remove unused imports automatically we will need to add the [eslint-plugin-unused-imports](https://www.npmjs.com/package/eslint-plugin-unused-imports) plugin.

To install it:

```bash
npm install eslint-plugin-unused-imports --save-dev
```

Then add it to your configuration file, here with the recommended rules from the author:

```json
{
  "plugins": ["unused-imports"],
  "rules": {
    "no-unused-vars": "off",
    "unused-imports/no-unused-imports": "error",
    "unused-imports/no-unused-vars": [
      "warn",
      {
        "vars": "all",
        "varsIgnorePattern": "^_",
        "args": "after-used",
        "argsIgnorePattern": "^_"
      }
    ]
  }
}
```

Now when you run _ESLint_, you should see error lines saying `error '<imported-var>' is defined but never used unused-imports/no-unused-imports` for the files where you have unused imports, and the print should end with the following line `X errors and Y warnings potentially fixable with the --fix option.`.

The number of errors should be superior to 0, unless you don't have any unused imports in your project. If that's the case, go add some for the sake of this exercise ;).

Then run `npx eslint <project-directory> --fix` and...voilà!

There should not be any unused import in your code anymore.

## Bonus: sort your imports by alphabetical order

Sorting import by alphabetical order is the last thing I want to take care of. I don't think it really matters even though it can be part of a company or a team rules to do so.

In any case, _ESLint_ allows us to do this automatically, so why deprive ourselves from it?

To benefit from this feature you just need to add the [sort-import](https://eslint.org/docs/rules/sort-imports) rule to your _ESLint_ configuration file.

```json
{
	"rules": {
		...
		"sort-imports": [
			"error",
			{
				"ignoreDeclarationSort": true
			}
		]
  }
```

Unfortunately the `--fix` option will not automatically fix multiple lines errors. For this reason I prefer to set `ignoreDeclarationSort` to `true`.

It is for the best anyway because this rule provides very few customization to order your imports. And I don't think alphabetical order at line level makes sense without taking into account the kind of import; you don't want your local imports mixed with third party libraries for instance. If you are using `TSLint` though, check [ordered-imports](https://palantir.github.io/tslint/rules/ordered-imports/) that allows you to define your import order and fixes multiple lines imports.

Now, running _ESLint_ with the `--fix` option will reorder your multiple members imports. For instance, `import { d, a, c, b } from e;` will be changed to `import { a, b, c, d } from e;`.

That can't hurt anyone!