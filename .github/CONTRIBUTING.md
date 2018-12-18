# Contributing

## Requirements

Set up your SSH Key GitHub Enterprise account and install node.js 4 or higher.

- [Generating SSH Keys - GitHub](https://help.github.com/articles/generating-ssh-keys/)
- [`nvm` (Node Version Manager)](https://github.com/creationix/nvm) to use the `Node 6`.

Contributing to carbon-components requires that you can run this repo locally on your computer.

## Coding Style

### Class names

Prefix all class names with `#{$prefix}--` in SCSS, which is replaced with `bx--` by default,
and design systems inheriting Carbon can override.
This prefix prevents potential conflicts with class names from the user.

**HTML**

```html
<div class="bx--inline-notification bx--inline-notification--error" role="alert">
  <div class="bx--inline-notification__details">...</div>
</div>
```

**SCSS**

```scss
.#{$prefix}--inline-notification {
  ...
}

.#{$prefix}--inline-notification__details {
  ...
}
```

Follow BEM naming convention for classes. Again, the only thing we do differently is prefix all classes with `#{$prefix}--`.

```scss
.#{$prefix}--block
.#{$prefix}--block__element
.#{$prefix}--block--modifier
```

Avoid nesting selectors, this will make it easier to maintain in the future.

```scss
<-- Don't do this -->
.#{$prefix}--inline-notification {
  .#{$prefix}--btn {
    &:hover {
      svg {
        ...
      }
    }
  }
}

<!-- Do this instead -->
.#{$prefix}--inline-notification .#{$prefix}--btn {
    &:hover svg {
      ...
    }
  }
}
```

### Start a new `block` or `element`?

A nested element can use a new block name as long as the styles are independent of the parent.

```html
<div class="bx--component"><button class="bx--component-button">Button</button></div>
```

:point_up: The `#{$prefix}--component-button` class implies that this button has independent styles from its parent.
Generally, it's preferred to start a new block.

### Red Flags

Avoid names with multiple `__element` names:

- :x: `.#{$prefix}--card__list__item`
- :white_check_mark: `.#{$prefix}--card-item`
- :white_check_mark: `.#{$prefix}--card__item`

## Files and folders

All components belong in `src/components` in their own folder.

Name files and folders using **singular** form; not plural.

```
button
  - button.hbs
  - _button.scss
  - button.js
  - button.config.js
```

Also note that all variants of a component can live in a single HBS, SCSS and JS file respectively.
For example, while there are many button variants (primary, secondary, etc.), they're all contained in those single source files in the button folder.

## Defining markups for components and their variants

There are two ways to define markups for components and their variants:

1. Defining markup with no conditional or data interpolations
2. Defining markup with conditionals or data interpolations

### Defining markup with no conditional or data interpolations

Defining markup with no conditional or data interpolation is as easy as adding a `.hbs` file to the component directory. No `.config.js` file in the component directory is required in this case. One thing to note is that If there is a `.hbs` file whose basename is exactly the same as the component name, other `.hbs` files has to be in `componentname--variantname.hbs` format.

### Defining markup with conditionals or data interpolations

Defining markup with conditionals or data interpolations requires creating `.config.js` file, which is a [JavaScript module format of Fractal configuration](https://fractal.build/guide/core-concepts/configuration-files.html#configuration-file-formats), in component directory. `.hbs` files are rendered with the data given via `context` property in `variants[n]` (below).

Supported [properties in `.config.js`](https://fractal.build/guide/components/configuration-reference.html#component-properties) are the following:

- [`default`](https://fractal.build/guide/components/configuration#default): The default variant name
- [`variants`](https://fractal.build/guide/components/configuration#variant-properties) - An array of objects, supporitng the following properties:
  - `name`: The variant name
  - `label`: The variant name shown in dev env UI
  - `notes`: A short explainer the variant shown in dev env UI
  - `context`: The data used for rendering `.hbs`
  - `view`: The basename of the `.hbs` file for variant markup (Unlike [default Fractal environment](https://fractal.build/guide/components/configuration#view), this property should point to the basename of a `.hbs` file under `demo` directory or `src` directory, _without_ its path)
  - `preview`: The basename of the `.hbs` file for the markup that lays out the variant markup, in "full render" mode (Unlike [default Fractal environment](https://fractal.build/guide/components/configuration#preview), this property should point to the basename of a `.hbs` file under `demo` directory or `src` directory, _without_ `@` symbol)
  - `meta`: Some metadata. Carbon vanilla development environment reads the following ones specifically:
    - `linkOnly`: Only full-page demo is allowed
    - `useIframe`: Use of `<iframe>` for non full-page demo
    - `xVersionOnly`: Supports "experimental" theme only
    - `xVersionNotSupported`: "Experimental" theme is not supported

What `.hbs` file is used for rendering a variant is determined by searching for `.hbs` files in `demo` or `src` directory and find one whose basename matches one of the following (the priority is the following order):

1. `view` property in `variants[n]`
2. Variant handle, which takes a format of `componentname--variantname` format
3. Component handle, which is `componentname`

## Start Contributing

### 1. Fork The Repo:

Go to [carbon-components](https://github.com/IBM/carbon-components) and click the "Fork" button in the top-right corner.

### 2. Clone Your Fork:

1.  Go to your [GitHub Repositories](https://github.com/settings/repositories).
1.  Click on `[your_github_username]/carbon-components`.
1.  Click on the `Clone or Download` button and copy the URL from the `Clone with SSH` option. It should start with `git@github.com...`

In your terminal:

```sh
git clone git@github.com:[your_github_username]/carbon-components.git
cd carbon-components
```

See [GitHub docs](https://help.github.com/articles/fork-a-repo/) for more details.

### 3. Add Upstream Remotes

When you clone your forked repo, doing a `git remote -v` will show that the `origin` remote is set up for you already by default. This should be pointing to your forked repo.

Add the `IBM/carbon-components` repo to your remote (this can be useful to update your fork of new changes down the road):

```sh
# Add the upstream remote to your repo
git remote add upstream git@github.com:IBM/carbon-components.git

# Verify the remote was added
git remote -v
```

When you do `git remote -v`, you'll see these remotes:

- `origin`: connection to your fork
- `upstream`: connection to the original repo.

### 4. Work in a Branch

- Always work in a branch.
- Submit pull requests from a branch.
- All commits must follow the convention outlined [here](https://github.com/conventional-changelog/conventional-changelog/blob/v0.5.3/conventions/angular.md).

### 5. Start the server

```sh
npm run dev

# or

yarn dev
```

Once it's done building, you can start editing source code or creating new components. The system is set up to automatically bundle your changes/additions. Visit http://localhost:3000 to see the changes happen on the fly.

Options:

- `-b`: Enable breaking changes for the next release
- `-e`: Enable experimental features

### 6. Test your JavaScript code

If you're contributing to our JavaScript code, test your changes by running our test commands:

```sh
gulp test:unit
```

If you add any features to our JavaScript code, make sure to add tests so that your code is covered.
Tests are written in [Mocha](https://mochajs.org)/[Chai](http://chaijs.com).
You can see if your code is covered by looking at carbon-components/tests/coverage/\*/index.html after running test.

If your change may hit some browser quirks, use `-b` option, like:

```sh
gulp test:unit -b IE -b Firefox
```

(Other browsers tests can run with are: `Safari`, `Chrome` and `ChromeHeadless`)

If you are very sure that your change affects a specific set of components, you can use `-f` option, like:

```sh
gulp test:unit -f tests/spec/fab_spec.js
```

Other options for testing are:

- `-d`/`--debug`: Stop generating code coverage report. Useful to debug your code when running test.
- `-k`/`--keepalive`: Keep running test runner even after test ends. Test will restart running when you make changes to any test files or any files under test.
- `-v`/`--verbose`: Let Karma emit detailed log.

### 7. Test your HTML/CSS code for a11y

If you're contributing to our HTML/CSS code, a11y compliance of your code should be tested.

To do so, you can test your changes by running our test commands:

```sh
gulp test:a11y
```

If you are very sure that your change affects a specific set of components, you can use `--name` option, like:

```sh
gulp test:a11y --name dropdown
```

The a11y test may report potential issues that should be handled in application-level, not in carbon-components code. In such case, you can ignore those issues by adding an item to `shouldIssueBeIgnoredForRule` table in [tests/a11y/global-ignore-aat-issues.js](https://github.com/IBM/carbon-components/blob/master/tests/a11y/global-ignore-aat-issues.js). The table is keyed by something like `wcag20.tech.h59.linkValid` which helps indentifying what RPT rule to ignore. You can specify `true` to the value which ignores all violations of the rule, or a function which takes the DOM element violating the rule and returns `true` if such violation should be ignored.

### 8. Make a Pull Request

**Note:** Before you make a pull request, [search](https://github.com/IBM/carbon-components/issues) the issues to see if a similar issue has already been submitted. If a similar issue has been submitted, assign yourself or ask to be assigned to the issue by posting a comment. If the issue does not exist, create a new issue.

When you're at a good stopping place and you're ready for feedback from other contributors and maintainers, **push your commits to your fork**:

#### Commit Tip

> **Writing commit messages**
>
> - `<type>` indicates the type of commit that's being made. This can be: `feat`, `fix`, `perf`, `docs`, `chore`, `style`, `refactor`
> - `<scope>` The scope could be anything specifying place of the commit change or the thing(s) that changed.
>
> **Commit message format:**

```
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

_Do not submit pull requests from the `master` branch of your fork._

```
git checkout -b { YOUR_BRANCH_NAME }
git add .
git commit -m "fix(table): IE11 positioning error" -m "Fixes #34"
```

- [Close a commit via commit message](https://help.github.com/articles/closing-issues-via-commit-messages/)

```
git push origin { YOUR_BRANCH_NAME }
```

In your browser, navigate to [IBM/carbon-components](https://github.com/IBM/carbon-components) and click the button that reads "Compare & pull request"

> **Is it a Breaking Change?**

> We want to respect semver.
> It's important to discern whether your pull request contains breaking changes or not.
> Sometimes, renaming or removing things in the code can result in breaking changes.

> Here are some examples of breaking changes...changing, renaming or removing any of the following:
>
> - HTML attributes
> - Folders or Files
> - Any SCSS `@mixin`, `$variable` or `function`
> - Any JS `function` or `class`

> We also practice **graceful deprecation** when something is slated to be removed -- we mark it as deprecated in the current version and remove it in the next major version.

Before you create a pull request, change the base branch depending on what kind of change you're submitting.

- Pull requests with **non-breaking changes** like patches and minor updates use the `master` as the base branch.
- Pull requests with **breaking changes** use the latest `major version number` branch as the base branch (i.e. `7.0.0` or whatever the next major version is).

Write a title and description then click "Create pull request"

- [How to write the perfect pull request](https://github.com/blog/1943-how-to-write-the-perfect-pull-request)

### 9. Updating a Pull Request

Stay up to date with the activity in your pull request. Maintainers from the Design System team will be reviewing your work and making comments, asking questions and suggesting changes to be made before they merge your code.

:tada: You no longer need to squash commits :tada:

When you need to make a change, add, commit and push to your branch normally.

Once all revisions to your pull request are complete, someone from Design Systems will squash and merge your commits for you.
