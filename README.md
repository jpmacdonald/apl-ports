# apl-ports

Build-from-source definitions for APL packages.

Ports are packages that can't be distributed as prebuilt binaries (e.g., Python, Ruby, OpenSSL). The `apl-builder` compiles these in CI and uploads artifacts to R2.

## Structure

```
<package>/
  <package>.toml           # build definition
  <package>@<version>.toml # version-specific overrides (optional)
```

## Example port

```toml
[package]
strategy = "build"
name = "openssl"

source_url = "https://github.com/openssl/openssl"
tag_pattern = "openssl-{{version}}"
version_pattern = "3.5.*"
dependencies = ["perl"]
script = """
./Configure --prefix=$PREFIX darwin64-arm64-cc
make -j$JOBS
make install
"""
```

## Build environment

Builds run in a sandboxed sysroot with:
- No network access
- No access to `/usr/local` or `/opt/homebrew`
- Dependencies mounted at `$DEPS_DIR/<name>`
- Install destination at `$PREFIX`

Environment variables: `$PREFIX`, `$JOBS`, `$ARCH`, `$DEP_<NAME>`, `$CFLAGS`, `$LDFLAGS`

## CI

Runs daily on `macos-14`. Artifacts uploaded to R2, then the package index is rebuilt.

## License

MIT
