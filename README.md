# Apple-silicon-scripts
Scripts for building third-party libraries for Apple silicon.


## OpenSSL

#### download:

[openssl-1.1.1-mac-silicon-universal.zip](https://github.com/CaptainTeemo/Apple-silicon-scripts/raw/main/openssl-1.1.1-mac-silicon-universal.zip)

#### build your own:

`git clone git@github.com:openssl/openssl.git`

`cd openssl`

`vim Configurations/10-main.conf`

add following section:
```
"darwin64-arm64-cc" => {
    inherit_from     => [ "darwin-common", asm("aarch64_asm") ],
    CFLAGS           => add("-Wall"),
    cflags           => add("-arch arm64"),
    lib_cppflags     => add("-DL_ENDIAN"),
    bn_ops           => "SIXTY_FOUR_BIT_LONG",
    perlasm_scheme   => "macosx",
},
```

build for arm

```
./Configure darwin64-arm64-cc --prefix="/tmp/openssl-arm" no-asm
make
make install
```

build for x86_64

```
./Configure darwin64-x86_64-cc --prefix="/tmp/openssl-x86"
make
make install
```

merge to universal

```
lipo /tmp/openssl-arm/lib/libssl.a /tmp/openssl-x86/lib/libssl.a -create -output libopenssl/lib/libssl.a
lipo /tmp/openssl-arm/lib/libcrypto.a /tmp/openssl-x86/lib/libcrypto.a -create -output libopenssl/lib/libcrypto.a
```

copy headers

```
cp /tmp/openssl-arm/include/openssl/* include/openssl/
```
