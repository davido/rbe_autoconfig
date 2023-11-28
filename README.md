#### RBE toolchain config for a given combination of Bazel release and docker image

Clone the [bazel-toolchain](https://github.com/bazelbuild/bazel-toolchains.git)
project.

Build the `rbe_configs_gen` with the following command:

```
  $ docker run \
      --rm -v $PWD:/srcdir -w /srcdir golang:1.16 \
      go build -o rbe_configs_gen \
      ./cmd/rbe_configs_gen/rbe_configs_gen.go
```

Clone this repository to `/home/<user>/projects/rbe_autoconfig`
directory and run this command in `bazel-toolchain` directory:

```
  $ ./rbe_configs_gen \
    --bazel_version=6.4.0 \
    --toolchain_container=gcr.io/bazel-public/ubuntu2204-java17@sha256:ffe37746a34537d8e73cef5a20ccd3a4e3ec7af3e7410cba87387ba97c0e520f \
    --output_config_path=path/to/config-directory \
    --exec_os=linux \
    --target_os=linux \
    --cpp_env_json=ubuntu2204.json
```

Used ubuntu2204.json file:

```
{
  "ABI_LIBC_VERSION": "glibc_2.19",
  "ABI_VERSION": "gcc",
  "BAZEL_COMPILER": "gcc",
  "BAZEL_HOST_SYSTEM": "i686-unknown-linux-gnu",
  "BAZEL_TARGET_CPU": "k8",
  "BAZEL_TARGET_LIBC": "glibc_2.19",
  "BAZEL_TARGET_SYSTEM": "x86_64-unknown-linux-gnu",
  "CC": "gcc",
  "CC_TOOLCHAIN_NAME": "linux_gnu_x86"
}
```

Conduct new release on GitHub and upload `tar.gz` archive to GCloud bucket.
Consume the new release from Gerrit project:

WORKSPACE
```python
http_archive(
    name = "rbe_jdk11",
    sha256 = "3c1307c35776dcc8e31c693685d690d9d4ef97b6bb8b55459f9b4b5ad3b8da14",
    strip_prefix = "rbe_autoconfig-1.0.0",
    urls = [
        "https://github.com/davido/rbe_autoconfig/archive/v1.0.0.tar.gz",
    ],
)

```

