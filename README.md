# sys2cyg

Download MSYS2 MinGW packages for Cygwin.

## Dependencies

Most of the programs that sys2cyg uses are preinstalled in Cygwin. However there are two programs that must be installed with the Cygwin setup utility:

-   curl
-   zstd

curl is used to download the package index and the packages themselves.

zstd is required to decompress some of the newer MSYS2 packages.

## Installation

sys2cyg can be installed with curl like this:

```
curl -o /usr/local/bin/sys2cyg -L https://raw.githubusercontent.com/mnxn/sys2cyg/master/sys2cyg && chmod +x /usr/local/bin/sys2cyg
```

## Usage

Run `sys2cyg <command>`, where the command is one of the following:

| Command     | Parameter | Description                        |
| ----------- | --------- | ---------------------------------- |
| `help`      |           | show the help message              |
| `info`      | package   | show information about the package |
| `install`   | package   | download and install a package     |
| `list`      |           | list installed packages            |
| `search`    | package   | search for a package               |
| `uninstall` | package   | uninstall a package                |
| `update`    |           | update the package index           |
| `url`       | package   | navigate to a package's url        |

## Details

The package index is downloaded from the MSYS2 repository and parsed by sys2cyg. At the moment, the package index only provides the latest version of the packages. The package index must be updated before any packages can be installed or searched for.

Packages are installed to a sys-root for use with the Cygwin installation of the mingw gcc compiler.

The recommended usage is to install C libraries from the MSYS2 repository with sys2cyg and compile with the Cygwin MinGW compiler.

## MINGW_TYPE Differences

Set the `MINGW_TYPE` environmental variable to `32` to use the 32-bit MinGW packages. Otherwise, sys2cyg will default to 64-bit packages.

| MINGW_TYPE           | 32                                                      | 64                                                    |
| -------------------- | ------------------------------------------------------- | ----------------------------------------------------- |
| Package index source | http://repo.msys2.org/mingw/x86_64/mingw64.files.tar.gz | http://repo.msys2.org/mingw/i686/mingw32.files.tar.gz |
| Install location     | /usr/x86_64-w64-mingw32/sys-root/mingw/                 | /usr/i686-w64-mingw32/sys-root/mingw/                 |

## Why?

Although both Cygwin and MSYS2 provide a suitable environment for POSIX-like development, they diverge in the amount of up-to-date MinGW libraries. Cygwin has [about 400 MinGW-64 packages](https://cygwin.com/packages/package_list.html) while MSYS2 has [about 1500](http://repo.msys2.org/mingw/x86_64/).

I realized this difference when writing a LLVM-based compiler with OCaml on Windows. The [most supported versions](https://ocaml.org/docs/install.html#Windows) of Windows OCaml use Cygwin as POSIX emulation layer instead of MSYS2. The Cygwin build of LLVM has been stuck on [version 5.0.1](https://cygwin.com/packages/summary/mingw64-x86_64-llvm.html) for a couple years now, while the MSYS2 build is already on the latest [version 10.0.0](https://packages.msys2.org/package/mingw-w64-x86_64-llvm). sys2cyg allows one to easily use the MSYS2-built libraries in situations where it is not feasible to switch the entire toolchain to MSYS2.

## Example

To build a popular OCaml package, [ctypes](https://opam.ocaml.org/packages/ctypes/), libffi is required to be installed on the system. If you use the Cygwin package manager, it will install [version 3.2](https://cygwin.com/packages/summary/mingw64-x86_64-libffi.html), sys2cyg allows the newer [version 3.3](https://packages.msys2.org/package/mingw-w64-x86_64-libffi) to be used. Here's how:

First update the package index. This should be done the first time you use sys2cyg and every time you want to update to newer versions of packages.

```
$ sys2cyg update

Updating MSYS2 MinGW-64 package index ... done.
```

Now it is possible to install libffi:

```
$ sys2cyg install libffi

Collecting dependencies, please wait ... done.

libffi (version 3.3-1) will be installed.
Continue? (y/n) y

Installing libffi (version 3.3-1) ... done.
```

That's it! libffi is now ready to use from OCaml and the MinGW GCC compiler.

If you are an OCaml developer, you can expect to install ctypes with no problems.

```
$ opam install ctypes ctypes-foreign
```
