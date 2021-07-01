# protoc-gen-graphql

`protoc-gen-graphql` is a highly customizable Protobuf compiler plugin to generate GraphQL schema definition language (SDL) files.

## Usage

First, download the plugin and place the executable in your path.
Then, run the Protobuf compiler with the `--graphql_out` flag to enable this plugin.
Set the value of this flag to the directory you want the SDL files to be generated into.

```shell script
protoc -I . --graphql_out=output_dir path/to/file.proto ...
```

## Usage from Bazel

In your **`WORKSPACE`**:
```starlark
http_archive(
    name = "protoc_gen_graphql",
    // ...
)

load("@protoc_gen_graphql:defs.bzl", protoc_gen_graphql_deps="go_dependencies")

protoc_gen_graphql_deps()
```

You will also need [Protocol Buffers](https://github.com/protocolbuffers/protobuf), [Gazelle](https://github.com/bazelbuild/bazel-gazelle), and [`rules_go`](https://github.com/bazelbuild/rules_go), which you can get with the following snippet:

```starlark
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

http_archive(
    name = "com_google_protobuf",
    sha256 = "e589e39ef46fb2b3b476b3ca355bd324e5984cbdfac19f0e1625f0042e99c276",
    strip_prefix = "protobuf-fde7cf7358ec7cd69e8db9be4f1fa6a5c431386a",
    url = "https://github.com/google/protobuf/archive/fde7cf7358ec7cd69e8db9be4f1fa6a5c431386a.tar.gz",
)

http_archive(
    name = "io_bazel_rules_go",
    sha256 = "69de5c704a05ff37862f7e0f5534d4f479418afc21806c887db544a316f3cb6b",
    urls = [
        "https://mirror.bazel.build/github.com/bazelbuild/rules_go/releases/download/v0.27.0/rules_go-v0.27.0.tar.gz",
        "https://github.com/bazelbuild/rules_go/releases/download/v0.27.0/rules_go-v0.27.0.tar.gz",
    ],
)

http_archive(
    name = "bazel_gazelle",
    sha256 = "62ca106be173579c0a167deb23358fdfe71ffa1e4cfdddf5582af26520f1c66f",
    urls = [
        "https://mirror.bazel.build/github.com/bazelbuild/bazel-gazelle/releases/download/v0.23.0/bazel-gazelle-v0.23.0.tar.gz",
        "https://github.com/bazelbuild/bazel-gazelle/releases/download/v0.23.0/bazel-gazelle-v0.23.0.tar.gz",
    ],
)

load("@com_google_protobuf//:protobuf_deps.bzl", "protobuf_deps")
protobuf_deps()

load("@io_bazel_rules_go//go:deps.bzl", "go_register_toolchains", "go_rules_dependencies")
load("@bazel_gazelle//:deps.bzl", "gazelle_dependencies")

go_rules_dependencies()

go_register_toolchains(version = "1.16")

gazelle_dependencies()
```

### Parameters

The SDL generation can be customized by passing optional parameters to the plugin.
Parameters are specified using a comma separated list of parameters before the output directory, separated by a colon.
For example:

```shell script
protoc -I . --graphql_out=root_type_prefix=GRPC,null_wrappers:output_dir path/to/file.proto ...
```

Note that parameter settings apply to all the generated GraphQL types and files.
For settings that apply to single Protobuf objects, like messages and fields, refer to the Protobuf options section below.

Available parameters are:

| Key | Values | Default | Description |
| --- | --- | --- | --- |
| `field_name` | `lower_camel_case`, `preserve` | `lower_camel_case` | Transformation from Protobuf field names to GraphQL field names. Default is lowerCamelCase. Use `preserve` to use the Protobuf name as-is. |
| `trim_prefix` | string | | Trims the provided prefix from all generated GraphQL type names. Useful if your Protobuf package names have a common prefix you want to omit. |
| `root_type_prefix` | string | | If set, a gRPC service's mapped query and mutation types will extend some custom root type with name given by the provided prefix plus `Query` or `Mutation`. Set to empty string to extend the root `Query` and `Mutation` types. |
| `input_mode` | `all`, `service`, `none` | `service` | The input mode determines what GraphQL input objects will be generated. `all` will generate an input object for each Protobuf message. `service` will only generate inputs for messages that are transitively used in each gRPC methods' request messages. `none` will not generate any input objects. |
| `null_wrappers` | bool | `false` | If true, well known wrapper types (e.g. `google.protobuf.StringValue`) will be mapped to nullable GraphQL scalar types instead of the corresponding object type. |
| `js_64bit_type` | `string`, `number` | `number` | Whether to use a `String` or `Float` scalar type when mapping 64bit Protobuf types (`int64`, `uint64`, `sint64`, `fixed64`, `sfixed64`). |
| `timestamp` | string | | GraphQL type name to use for the well known `google.protobuf.Timestamp` type. |
| `duration` | string | | GraphQL type name to use for the well known `google.protobuf.Duration` type. |
| `struct` | string | | GraphQL type name to use for the well known `google.protobuf.Struct` type. |

### Protobuf options

[Protobuf options file](protobuf/graphql/options.proto)

TODO

## Protobuf to GraphQL mapping

TODO

### Messages

#### Maps

#### Oneofs

### Enums

### Services
