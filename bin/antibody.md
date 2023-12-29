# Antibody

[antibody][antibody] is the legacy plugin manager antidote is based upon. antidote ships with a full Zsh implementation of antibody as a standalone script as of version 1.9.5.

## clitest

This README serves as testable documentation using [clitest][clitest]. It can be tested with `clitest --list-run --progress dot --color always antibody.md`.

We need a convenience function, `subenv`, so that test output doesn't have to have named directories embedded and we can instead use variables like `$PWD` and `$HOME`.

```sh
$ subenv() { sed -e "s|$PWD|\$PWD|g" -e "s|$HOME|\$HOME|g"; }
$
```

Also, adding the current directory to path allows us to avoid having to do `./antibody` everytime we call it, and can simply call `antibody` without the leading `./`.

```sh
$ PATH=$PWD:$PATH
$
```

## Commands

### Version

Show version with `-v, --version`.

```sh
$ antibody --version
antibody version 1.9.4
$
```

The `-v, --version` short and long flags are equivalent.

```sh
$ test "$(antibody -v)" = "$(antibody --version)"  #=> --exit 0
$
```

### Help

Show help with the help command.

```sh
$ antibody help
usage: antibody [<flags>] <command> [<args> ...]

A pure Zsh implementation of the legacy antibody plugin manager
Packaged with the antidote plugin manager

Flags:
  -h, --help           Show context-sensitive help (also try --help-long and --help-man).
  -v, --version        Show application version.

Commands:
  help [<command>...]
    Show help.

  bundle [<bundles>...]
    downloads a bundle and prints its source line

  update
    updates all previously bundled bundles

  home
    prints where antibody is cloning the bundles

  purge <bundle>
    purges a bundle from your computer

  list
    lists all currently installed bundles

  path <bundle>
    prints the path of a currently cloned bundle

  init
    initializes the shell so Antibody can work as expected

$
```

The`-h, --help` flags show help too.

```sh
$ test "$(antibody help)" = "$(antibody -h)"  #=> --exit 0
$ test "$(antibody -h)" = "$(antibody --help)"  #=> --exit 0
$
```

### init

Use `source <(antibody init)` to initialize antibody. The `init` command produces a script that wraps antibody for dynamic plugin loading.

See what the output produces:

```sh
$ antibody init | subenv
#!/usr/bin/env zsh
antibody() {
  case "$1" in
  bundle)
    source <( $PWD/antibody $@ ) || $PWD/antibody $@
    ;;
  *)
    $PWD/antibody $@
    ;;
  esac
}

_antibody() {
  IFS=' ' read -A reply <<< "help bundle update home purge list init"
}
compctl -K _antibody antibody

$
```

### home

`antibody home` prints where antibody is cloning bundles. This differs based on which system you are on.

```sh
$ ANTIBODY_TEST_OSTYPE=linux antibody home | subenv
$HOME/.cache
$ ANTIBODY_TEST_OSTYPE=darwin antibody home | subenv
$HOME/Library/Caches
$
```

You can also change Antibody’s home folder by manually setting `ANTIBODY_HOME` to a path of your choosing:

```sh
$ export ANTIBODY_HOME=$HOME/path/to/antibody/home
$ antibody home | subenv
$HOME/path/to/antibody/home
$
```

### bundle

TODO:

### update

TODO:

### list

TODO:

### purge

TODO:

### Misc

Some misc testing

```sh
$ antibody foo  #=> --exit 1
antibody: error: expected command but got "foo", try --help
$
```

[antibody]: https://github.com/getantibody/antibody
[clitest]: https://github.com/aureliojargas/clitest
