## The problem
**It can often take several minutes just to figure out how to *start* working on
something.**

Every project is different, but damn near every project comes with a set of
development commands or scripts to run common actions. And if it doesn’t, then
it probably should.
Different languages, people, and tools accomplish this in different ways. Some
projects use the good ol’ `Makefile`, while others use `package.json` “scripts”,
bash scripts, `rake`, `fabric`, and so on and so on…


## A solution

**Running `p` will show you which commands are available for the project you are on.**

For example:
```sh
$ cd project
$ p
Usage: p [OPTIONS] COMMAND [ARGS]...

Options:
  --version
  --help     Show this message and exit.

Commands:
  mypy        Using: ./scripts/mypy
  pre-commit  Using: ./scripts/pre-commit
  setup       Using: pipenv sync --dev
  test        Using: ./scripts/test
```

It does this by discovering scripts and commands that already exist within
the project, and by adding automatically preset commands for known package
managers or tools.

**P is not a project requirement or dependency -- it is a personal
tool.** Nothing in your project should depend on p, but rather conform
to p-friendly standards which are usable with or without p itself.

Ideally, p will “just work”. But if not, it is often in your project’s best
interest to design a developer experience that *would* work if someone
were using p. That is – script out some of the most commonly used actions for
your project (`install`, `test`, `deploy`,
etc.), and put them in a uniform place where contributors can easily figure
out how to use them. P is simply a small abstraction layer to make it even
easier yet for people that use it.

## Install it for yourself

Don't add it to a project. Add it to your machine, system-wide or user-wide.

```sh
$ pip3 install -U git+https://github.com/dropseed/p.git#egg=p
```

## Project commands

### Local scripts

P will automatically find executable scripts (with no file extension) in `./scripts` or `./bin`.
The filename will be added as a command so that they can simply be run by doing `p {script-name}`.

```sh
$ tree scripts/
scripts/
├── compile-assets
├── load-fixtures
├── pre-commit
├── setup
├── start-postgres
├── test
└── work
```

Will result in:

```sh
$ p
  Usage: p [OPTIONS] COMMAND [ARGS]...

  Options:
    --version
    --help     Show this message and exit.

  Commands:
    compile-assets  Using: ./scripts/compile-assets
    load-fixtures   Using: ./scripts/load-fixtures
    pre-commit      Using: ./scripts/pre-commit
    setup           Using: ./scripts/setup
    start-postgres  Using: ./scripts/start-postgres
    test            Using: ./scripts/test
    work            Using: ./scripts/work
```

### Makefile

If there is a `Makefile` in your project,
p will automatically parse `.PHONY` and make those commands available via p.
So if you have `make test`,
it will also be available to p users via `p test`.

### package.json scripts

Entries in your `package.json` "scripts" will automatically be mapped to p commands.

For example:

```js
{
  "scripts": {
    "start": "react-scripts start"
  }
}
```

Would result in:

```sh
Usage: p [OPTIONS] COMMAND [ARGS]...

Options:
  --version
  --help     Show this message and exit.

Commands:
  install  Using: yarn install
  start    Using: npm run start
```

## Preset commands

P will automatically add commands for known package managers. These
can be overridden by providing your own script of the same name in one of the
recognized locations. So, for example, if you want contributors to use
something more specific than a regular `yarn install`, just add an
“install” script to `package.json`.

We only add things as people need them. If you don't see what you need, tell us!

### package.json
- `npm install` as `p install`

### yarn.lock
- `yarn install` as `p install`

### .terraform or terraform.tfstate
- `terraform init` as `p install`
- `terraform apply` as `p deploy`

### Pipfile.lock
- `pipenv sync --dev` as `p install`

### combine.yml
- `combine work` as `p work`

### Cartfile or Cartfile.resolved
- `carthage update` as `p install`

### Gopkg.toml or Gopkg.lock
- `dep ensure` as `p install`

## Git hooks

P also provides automatically installation of git hooks, if you use
commands named `pre-commit` or `post-merge`. On `p
install` or `p {git-hook-name}` it will prompt you to install
it into your local `.githooks`.

## Grouping (advanced)

To make the p help more user friendly you can group and hide commands from the top-level.
This works automatically by using a `:` in your command name.

For example, if you have commands like `db:load` and `db:reset`, you'll get a `db` group.
You can run `p db` to see the subcommands in db, and run `p db load` to run a subcommand.

```sh
$ p
Usage: p [OPTIONS] COMMAND [ARGS]...

Options:
  --version
  --help     Show this message and exit.

Commands:
  db

$ p db load
```

(You can also invoke the grouped commands directly as `p db:load`.)

## Inspired by
- [Dropseed’s](https://github.com/dropseed) project-cli (private)
- [Flint Hills Design’s](https://github.com/flinthillsdesign) fhd-cli (private)
- [https://github.com/github/scripts-to-rule-them-all](https://github.com/github/scripts-to-rule-them-all)
- [https://github.com/bkeepers/strappydoo](https://github.com/bkeepers/strappydoo)
- having too many projects
