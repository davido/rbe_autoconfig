#### RBE toolchain config for a given combination of Bazel release and docker image

Clone the [bazel-toolchain](https://github.com/bazelbuild/bazel-toolchains.git)
project and checkout `v5.1.0` tag.

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
       --bazel_version=4.1.0 \
       --toolchain_container=l.gcr.io/google/rbe-ubuntu18-04:latest \
       --output_src_root=/home/<user>/projects/rbe_autoconfig \
       --exec_os=linux \
       --target_os=linux
```

To replace JDK 8 with JDK 11 the following diff was applied:

```diff
diff --git a/java/BUILD b/java/BUILD
index 237e5c1..7c6ab59 100755
--- a/java/BUILD
+++ b/java/BUILD
@@ -20,5 +20,5 @@ package(default_visibility = ["//visibility:public"])
 java_runtime(
     name = "jdk",
     srcs = [],
-    java_home = "/usr/lib/jvm/11.29.3-ca-jdk11.0.2/reduced",
+    java_home = "/usr/lib/jvm/java-8-openjdk-amd64",
 )
```

See also this [related issue upstream](https://github.com/bazelbuild/bazel-toolchains/issues/961).

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

