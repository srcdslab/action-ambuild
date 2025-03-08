# action-ambuild

Build AlliedModders extensions with ambuild

[![Actions Status](https://github.com/srcdslab/action-ambuild/workflows/Release/badge.svg)](https://github.com/srcdslab/action-ambuild/actions)

## Usage

```yaml
name: Build AlliedModders extension

on:
  push:
    branches:
      - master
      - main
    tags:
      - "*"
  pull_request:
    branches:
      - master
      - main

jobs:
  build:
    strategy:
      matrix:
        os:
          - ubuntu-20.04
          - windows-2022
        include:
          - os: ubuntu-20.04
            cc: clang-10
            cxx: clang++-10
          - os: windows-2022
            cc: msvc
            cxx: msvc
      fail-fast: false
    name: ${{ matrix.os }} - ${{ matrix.cc }}
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v4

      - name: Build extension
        uses: srcdslab/action-ambuild@v1
        id: ambuild
        with:
          os: ${{ matrix.os }}
          cc: ${{ matrix.cc }}
          cxx: ${{ matrix.cxx }}
          ambuild-ref: 'master'
          mm-ref: '1.12-dev'
          sm-ref: '1.12-dev'
          hl2sdk-manifests-ref: 'master'
          sdks: 'css hl2dm dods tf2'

      - name: Upload package
        uses: actions/upload-artifact@v4
        with:
          name: ${{ runner.os }}
          path: ${{ steps.ambuild.outputs.package-path }}
          if-no-files-found: error
          retention-days: 14
```
