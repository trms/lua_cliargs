# cliargs

[![travis-ci status](https://secure.travis-ci.org/amireh/lua_cliargs.png)](http://travis-ci.org/#!/amireh/lua_cliargs/builds)

cliargs is a command-line argument parser for Lua. It supports 4 types of arguments:

1. required arguments
1. optional arguments with different notations: `-short-key VALUE` and/or `--expanded-key=VALUE`
1. optional "flag" arguments (on/off options) with notations: `-short-key` and/or `--expanded-key`
1. a single optional "splat" argument which can be repeated (must be the last argument)

Optional arguments can have default values (strings), flags always default to 'true'.

## Usage Example
See `example.lua` for an example on how to use the parser.
Try it with the following sample command lines;

```bash
example.lua --help
example.lua -o myfile -d --compress=gzip inputfile
example.lua --__DUMP__ -o myfile -d --compress=gzip inputfile
```

**Accessing arguments**

All types of arguments must specify a *key*. In the case of required arguments, the keys are only used in the help listings. However, for optional arguments, they are mandatory (either *--key* or *--extended-key* must be specified, the other is optional).

The `parse()`  method will parse the command line and return a table with results. Accessing argument or option values in this table can be done using the key with the leading dashes omitted (`-` or `--`). When defining an option (or a flag) , you can access it using either key or expanded-key; they'll both be defined.

## Help listings `--help`

A help listing will be automatically generated and accessed using the `--help` argument. You can also force its display in the code using `cli:print_help()`.

This is the result for our example:

```bash
Usage: cli_example.lua [OPTIONS]  INPUT  [OUTPUT-1 [OUTPUT-2 [...]]]

ARGUMENTS:
  INPUT                 path to the input file (required)
  OUTPUT                multiple output paths (optional, default:
                        /dev/stdout)

OPTIONS:
  -c, --compress=FILTER the filter to use for compressing output: gzip,
                        lzma, bzip2, or none (default: gzip)
  -o FILE               path to output file (default: /dev/stdout)
  -d                    script will run in DEBUG mode
  -v, --version         prints the program's version and exits
  --verbose             the script output will be very verbose
```

## Validations

### Runtime argument validation

From a parsing point of view, there are 3 cases that need to be handled which are outlined below. If I missed something, please open a ticket!

**Missing a required argument**

```bash
>> lua example.lua
cli_example.lua: error: bad number of arguments; 1-4 argument(s) must be specified, not 0; re-run with --help for usage.
```

**Missing value for an optional argument**

```bash
>> example.lua --compress inputfile
cli_example.lua: error: option --compress requires a value to be set; re-run with --help for usage.
```

**Unknown arguments**

```bash
>> example.lua -f inputfile
cli_example.lua: error: unknown/bad flag; -f; re-run with --help for usage.
```

### Some sanity guards

In the following cases, `cliargs` will report an error to you and terminate the running script:

1. flag options can not accept a value. For example: `cli:add_flag('-v VERSION')` will return an error
2. duplicate keys are not allowed: defining two options with the key `--input` will be rejected

## Tests

Running test specs is done using [busted](http://olivinelabs.com/busted/). You can install it using [LuaRocks](http://www.luarocks.org/), and then just call it with the `spec` folder:

```bash
luarocks install busted
cd /path/to/lua_cliargs/
busted spec
```

## Contributions

If you come across a bug and you'd like to patch it, please fork the repository, commit your patch, and request a pull.

**For collaborators**

To deploy a new version, you must bump the rockspec and do a few things:

1. rename the rockspec to reflect the new version (by incrementing the minor version, for example)
1. edit the rockspec to point to the tarball that contains the new version (which also must follow the same naming convention)
1. bump the version stored in the variable `cli._VERSION` in the bottom of the script `src/cliargs.lua`
1. create the tarball using the helper bash script `tarballs/create_tarball.sh`: invoke it with two parameters: the MAJOR version and the MINOR one, ie: `./create_tarball.sh 1 4` to create a 1.4 versioned tarball of the repository
1. add the new tarball to the Downloads of the repository so Luarocks can find it

## Thanks to

Many thanks to everyone who reported bugs, provided fixes, and added entirely new features:

1. [Thijs Schreijer](https://github.com/Tieske)
1. [Jack Lawson](https://github.com/ajacksified)
1. [Robert Andrew Ditthardt](https://github.com/DorianGray)
1. [Oscar Lim](https://github.com/o-lim)

*If I missed you, don't hesitate to update this file or just email me.*

## Reference

A function reference was generated using [LunaDoc](http://jgm.github.com/lunamark/lunadoc.1.html) which can be found [here](http://lua-cliargs.docs.mxvt.net).

## Changelog

Changes in 2.2-0 from 2.1-2

1. the `=` that separates keys from values in the `--expanded-key` notation is no longer mandatory; using either a space or a `=` will map the value to the key (e.g., `--compress lzma` is equal to `--compress=lzma`)

Changes in 2.0.0 from 1.x.x

1. added the 'splat' argument, an optional repetitive argument for which a maximum number of occurrences can be set
1. removed the reference, arguments are now solely returned by their key/expanded-key (BREAKING!)
1. removed object overhead and the `new()` method as the library will only be used once on program start-up (BREAKING!)
1. after parsing completed successfully, the library will effectively delete itself to free resources (BREAKING!)
1. option/flag is now allowed with only an expanded-key defined
1. Debug aid implemented; adding a first option `--__DUMP__`, will dump the results of parsing the command line. Especially for testing how to use the commandline with arguments containing spaces either quoted or not.
1. the `print_usage()` and `print_help()` now have a 'noprint' parameter that will not print the message, but return it as an error string (`nil + errmsg`)

## License

The code is released under the MIT terms. Feel free to use it in both open and closed software as you please.

Copyright (c) 2011-2012 Ahmad Amireh

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
