# ./build-objects

This is a script for simplifying C/C++ compilation. It's meant to be copied into
a project and used from there. It's written in Perl and should work on most
Unix-based operating systems (not Windows.) It should work with most Make
implementations, and supported compilers include GCC, Clang, and TinyCC.
(Compilers must support the `-MD` and `-MF` options. This may exclude some old
versions, I don't know.)

## Example

Say you wanted to make the optimized build of a project whose source files were
in the `source` directory. You could put this in your Makefile:

```
optimized-executable: source/*
	@./build-objects -s source -b build/O3 -j auto $(CC) -O3 $(CFLAGS)
	$(CC) -o $@ $(LDFLAGS) build/O3/*.o $(LDLIBS)
```

(The `@` before the script invocation is optional.)

`optimized-executable` will be rebuilt whenever a file in `source` changes. The
unlinked objects and other intermediate files will be placed in `build/O3`,
which will be created if it does not exist. `-j auto` will tell the script to
use all available processors for compilation. If there were a file `f.c` to
compile, it would be compiled like so:

    $(CC) -O3 $(CFLAGS) -MD -MF build/O3/f.c.d -c -o build/O3/f.c.o source/f.c

The build directory can be safely removed when cleaning up.

For more information, run `./build-objects --help` or read the source code.

## Advantages

* The compilation and dependency handling can be done purely in the Makefile.
  However, using this script is probably more portable and requires less
  boilerplate.
* Supporting multiple optimization levels/configurations is also with this
  script; just use separate build directories. Using pure Make to do this would
  again be more complex, as far as I know.

## Disadvantages

* The script only supports a simple project structure: all the compilation units
  must be in a single source directory. This setup is common enough for the
  script to be useful, but larger, more complex projects may not be supported.
* While I think the code is portable, I haven't tested except on the few
  computers I own.
* Using the format of the example above, removing a file will not trigger a
  rebuild. This is annoying, though I doubt it will be a problem very often.

## History

This is based on my other project [jdibs](https://github.com/TurkeyMcMac/jdibs),
except that it doesn't do its own slow dependency detection and is generally
simpler and easier to use.
