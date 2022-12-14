# setup-dlang

Automatically downloads and installs the D programming language compiler [DMD](https://dlang.org) or [LDC](https://github.com/ldc-developers/ldc) and the package manager [dub](https://code.dlang.org) on Windows, Linux and OSX in a GitHub Action.

## Usage

Basic usage:
```yml
steps:
  - uses: actions/checkout@v2
  - uses: dlang-community/setup-dlang@v1
    with:
      compiler: dmd-latest
  - name: Run tests
    run: dub test
```

Matrix testing:
```yml
name: Run all D Tests
on: [push, pull_request]

jobs:
  test:
    name: Dub Tests
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macOS-latest]
        dc: [dmd-latest, ldc-latest, dmd-2.085.0, ldc-1.17.0]
        exclude:
          - { os: macOS-latest, dc: dmd-2.085.0 }

    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v2

      - name: Install D compiler
        uses: dlang-community/setup-dlang@v1
        with:
          compiler: ${{ matrix.dc }}

      - name: Run tests
        run: dub -q test
```
The above example test 11 possible combinations: the latest `dmd` and latest `ldc` on all three platforms,
`ldc-1.17.0` on all three platforms, and `dmd-2.085.0` on `ubuntu-latest` and `windows-latest`.

Simply add the setup-dlang action to your GitHub Actions workflow to automatically download and install a D compiler and package manager bundled with the compiler or separately downloaded. The action will automatically add the D binaries to the `PATH` environment variable and set the `DC` environment variable to the selected compiler executable name.

All DMD versions of releases and pre-releases on https://downloads.dlang.org/releases/2.x/ and https://downloads.dlang.org/pre-releases/2.x/ are supported. For LDC all releases on https://github.com/ldc-developers/ldc/releases are available.

Additionally instead of a version for both DMD and LDC you can specify `-latest` to get the latest stable release of the compiler, use `-beta` to get the latest pre-release of the compiler and also use `-master` to get the newest nightly builds.

Valid version examples are:
- `dmd-latest`
- `ldc-latest`
- `dmd-beta`
- `ldc-beta`
- `dmd-2.088.0`
- `dmd-2.088.0-beta.2`
- `ldc-1.17.0`
- `ldc-1.18.0-beta1`
- `dmd-master`
- `ldc-master`

## DUB installation

[dub](https://github.com/dlang/dub) is installed alongside the selected compiler for any versions >= v2.072.0 (2016-10-31).

If the `dub` parameter is provided to the action, this version will be the one installed instead.
Accepted parameters are 'latest', or a DUB version string (e.g. '1.29.0').

Note that DUB versions prior to v1.13.0 (DMD version v2.084.0, released 2019-01-02) do not support HTTP2,
meaning they will not work for fetching packages.
