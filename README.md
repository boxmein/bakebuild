# bake

Probably the smallest build tool that still involves a dep-graph

## Usage

- [Install it](#installing)

## Examples

TypeScript set-up:

```yaml
tasks:
  - inputs:
      - !File package.json
    outputs:
      - !File yarn.lock
      - !File .yarn/install-state.gz
      - !Glob node_modules/**
    actions:
      - !Shell yarn install
  - inputs:
      - !Glob node_modules/**
      - !Glob src/**/*.ts
    outputs:
      - !Glob dist/**/*.js
    actions:
      - !Shell yarn run tsc -p .
```

Go monorepo set-up:

```yaml
tasks:
  - inputs:
      - !Glob pkg/**/*.go
      - !Glob service/X/**/*.go
      - !File cmd/X/main.go
    outputs:
      - !File bin/X
    actions:
      - !Shell go build -o bin/X ./cmd/X
  - inputs:
      - !Glob pkg/**/*.go
      - !Glob service/Y/**/*.go
      - !File cmd/Y/main.go
    outputs:
      - !File bin/Y
    actions:
      - !Shell go build -o bin/Y ./cmd/Y
```

## Syntax

YAML syntax is currently supported

```yaml
# ./bakefile.yml
tasks:
  - inputs:
      - !File x.yml
      - !File y.yml
    outputs:
      - !File z.yml
    actions:
      - !Shell "cat x.yml y.yml > z.yml"
```

### Inputs

If any input changes, the actions are run again.

- `!File filename` -- when the file changes, re-run the actions
- `!Glob pattern` -- when any file indicated by the glob changes, re-run the actions

### Actions

Actions are just commands / things to do to go from input to output.

Action types:

- `!Shell command` -- invoke `bash -c "command"`

### Outputs

Used to build a dependency graph (if output files of task X affect input files 
of task Y)

- `!File filename` -- this task modifies this file
- `!Glob pattern` -- this task modifies these files

## Compiling it yourself

Get rust:

```
open https://rustup.rs
<some manual steps>
```

Get the repo:

```
git clone git@github.com:boxmein/bakebuild
cd bakebuild
```

Compile the code:

```
cargo build --release
```

Take the files to /usr/local/bin:

```
sudo install \
    -o root \
    -g root \
    -m 755 \
    target/release/bake \
    /usr/local/bin/bake
```

## Goals

- Start small
- Maybe solve an issue for someone
- Have fun with coding
