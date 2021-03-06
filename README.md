# Open-source bazel build file collection #

This contains a set of open source packages configured to build within
the bazel (https://bazel.build) build system.  Currently supported
platforms are x86_64 and armv7.

## Example Usage ##

In `tools/workspace/bazel_deps/repository.bzl`

```
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")


def bazel_deps_repository(name):
    commit = "XXX"
    http_archive(
        name = name,
        url = "https://github.com/mjbots/bazel_deps/archive/{}.zip".format(commit),
        sha256 = "XXX",
        strip_prefix = "bazel-deps-{}".format(commit),
    )
```

At least an empty `tools/workspace/bazel_deps/BUILD` is required.

In `tools/workspace/default.bzl`

```
load("//tools/workspace/bazel_deps:repository.bzl", "bazel_deps_respository")

def add_default_repositories(excludes = []):
    if "bazel_deps" not in excludes:
        bazel_deps_repository(name = "bazel_deps")
```

At least an empty `tools/workspace/BUILD` is required.

In `WORKSPACE`:

```
load("//tools/workspace/default.bzl", "add_default_repositories")

add_default_repositories()

load("@bazel_deps//tools/workspace:default.bzl",
     bazel_deps_add = "add_default_repositories")
bazel_deps_add()
```

At this point, each of the relevant packages can be referenced in
bazel rules as is typical:

```
cc_binary(
    name = "my_application",
    deps = ["@ffmpeg"],
)
```
