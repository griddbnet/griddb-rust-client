GridDB Driver for Rust

This is a fork of the GridDB client library. Please see the original repository [here](https://github.com/griddb/rust_client) for details.
 

## Supported functionalities:
- STRING, BOOL, BYTE, SHORT, INTEGER, LONG, FLOAT, DOUBLE, TIMESTAMP, BLOB type for GridDB
- Put single row, get row with key
- Normal query, aggregation with TQL

## Not yet supported:
- GEOMETRY, Array type for GridDB
- Multi-Put/Get/Query (batch processing)
- Timeseries-specific function, affinity

Please refer to the following files for more detailed information.  
- [Rust Client API Reference](https://griddb.github.io/rust_client/RustAPIReference.htm)

## Get Started

## Overview

GridDB Rust Client is GridDB client library for [Rust Programing Language](https://www.rust-lang.org/).

It is developed using GridDB C Client and [Rust bindgen](https://github.com/rust-lang/rust-bindgen).

# Operating environment

Building of the library and execution of the sample programs have been checked in the following environment.

```text
OS: Ubuntu 20.04/CentOS 7.9
Rust: 1.62
Clang: >=7
GridDB Server/C Client: 5.0 CE
```

## QuickStart
### Preparations

Install rust.
```console
$ curl https://sh.rustup.rs -sSf | sh
```

Install [GridDB Server](https://github.com/griddb/griddb) and [C Client](https://github.com/griddb/c_client).

Install clang.

* Ubuntu 20.04
    ```console
    $ sudo apt-get install clang-10 libclang-10-dev 
    ```

* CentOS 7
    ```console
    $ sudo yum install llvm-toolset-7.0
    $ scl enable llvm-toolset-7.0 bash
    ```

### Build and Run

1. Execute the command on project directory.
```console
$ cargo build
```

2. Use "extern crate griddb_rust;" in Rust source code.

### How to run sample

GridDB Server need to be started in advance.

1. If you build GridDB C Client from source code, set LD_LIBRARY_PATH.
```console
  $ export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:<C client library file directory path>
```

2. The command to run sample

```console
  $ cargo run --example sample1 <GridDB notification address> <GridDB notification port>
       <GridDB cluster name> <GridDB user> <GridDB password>
  --> Person: name=name01 status=false count=100 lob=[ABCDEFGHIJ]
```

