# rust-scaffold

A template rust project with a nix flake setup.

## Features

- building the rust project in nix with [naersk](https://github.com/nix-community/naersk)
- nightly toolchain management with [fenix](https://github.com/nix-community/fenix)
- development shell for rust with `nix develop .`
- checking formatting in nix with `cargo-fmt`
- `rust-analyzer` in the dev shell, compiled with the nightly version
- generating the `Cargo.lock` in nix (so you don't have to bootstrap it yourself)
- helper functions in the dev-shell
- nix formatting with `https://github.com/kamadorueda/alejandra`

## Requirements

An up to date version of nix, with flakes enabled. 

NixOS users may want to install the `nix` executable from an overlay of `nixpkgs/unstable`, as the latest stable NixOS release has a much older version.

```nix
{ pkgs, ... }: {
  nix = {
    # With an overlay of nixpkgs-unstable:
    package = pkgs.nixpkgs-unstable.nix;
    
    # Or without 
    package = pkgs.nixFlakes;
    
    extraOptions = ''
      experimental-features = nix-command flakes
    '';
   };
}
```

## Modifying this template

[flake.nix](./flake.nix) provides a derivation which builds the cargo package defined in this repo. Modifying the Cargo.toml will update the nix derivations appropriately. 

However, you may wish to modify [flake.nix](./flake.nix) to account for changes to the build process such as environment-variables & non-rust dependencies. The majority of Rust projects will depend on some frequent C dependencies (glibc aside). 
The file includes commented sections with additions for some common cases.


## Building

The project can be built with nix with:

```nix
nix build .
```

By default, this builds the `bin` target for your system in `result`, also running the test-suite.


## Development Shell

When developing, it is preferable to use the devShell & build with Cargo directly.

The devShell provides cargo & rustc, as well as extra utils for development.

```nix
nix develop .
```

I suggest using [direnv](https://direnv.net/) with [nix-direnv](https://github.com/nix-community/nix-direnv) instead, which will enter the shell automatically when you `cd` to the project directory, cache the result of the shell & allows you to use your preferred shell program. 

## Editor environment with rust-analyzer

nix-direnv can also be used to sync your editor's environment with the packages provided by the devShell - Look for your editor's direnv plugin.

## Changing the nightly snapshot

The nightly snapshot can be selected by editing [nightly-version](./nightly-version), or by running `update-nightly-version` in the devShell to update to today's snapshot.

## Useful Commands

- `nix build .` - build the Cargo package
- `nix develop .` - enter the devShell
- `nix repl` - loads the nix repl `:lf .` will load the flake & allow you to inspect the nix-expressions.
- `nix flake check` - run the package checks (suitable for CI), checks: building, testing, formatting.
- `nix fmt` - format the nix files
- `nix flake lock --update-input <input>` - update the flake.lock with the latest commit to an input

## Future Additions

- clippy
- windows cross-compiling
- Better nix code stucture
- NixOS/home-manager modules for editor configs
- extra devShell commands
- CI/CD
