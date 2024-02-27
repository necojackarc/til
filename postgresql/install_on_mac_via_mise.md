You can use [mise](https://mise.jdx.dev/) to install PostgreSQL to Mac.

```
mise use --global postgres@16.1
```

But by default, it'll try to enable ICU and you may get the following error:
 
```
configure: error: ICU library not found
If you have ICU already installed, see config.log for details on the
failure.  It is possible the compiler isn't looking in the proper directory.
Use --without-icu to disable ICU support.
```

You have to have `icu4c` installed and tell the locations of them to the PostgreSQL configure command.


```
brew install icu4c
```

Set the following env vars (only the last two may be required but I added those four):

```
# icu4c
export CPPFLAGS="-I/opt/homebrew/opt/icu4c/include"
export LDFLAGS="-L/opt/homebrew/opt/icu4c/lib"

# icu4c for PostgreSQL
export ICU_CFLAGS="-I/opt/homebrew/opt/icu4c/include"
export ICU_LIBS="-L/opt/homebrew/opt/icu4c/lib -licui18n -licuuc -licudata"
```

Ref: https://www.postgresql.org/docs/15/install-procedure.html
