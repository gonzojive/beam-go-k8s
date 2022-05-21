workspace(
    name = "com_github_gonzojive_beam_go_k8s",
)

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive", "http_file")

### rules_go: https://github.com/bazelbuild/rules_go

http_archive(
    name = "io_bazel_rules_go",
    sha256 = "f2dcd210c7095febe54b804bb1cd3a58fe8435a909db2ec04e31542631cf715c",
    urls = [
        "https://mirror.bazel.build/github.com/bazelbuild/rules_go/releases/download/v0.31.0/rules_go-v0.31.0.zip",
        "https://github.com/bazelbuild/rules_go/releases/download/v0.31.0/rules_go-v0.31.0.zip",
    ],
)

http_archive(
    name = "bazel_gazelle",
    sha256 = "5982e5463f171da99e3bdaeff8c0f48283a7a5f396ec5282910b9e8a49c0dd7e",
    urls = [
        "https://mirror.bazel.build/github.com/bazelbuild/bazel-gazelle/releases/download/v0.25.0/bazel-gazelle-v0.25.0.tar.gz",
        "https://github.com/bazelbuild/bazel-gazelle/releases/download/v0.25.0/bazel-gazelle-v0.25.0.tar.gz",
    ],
)

load("@io_bazel_rules_go//go:deps.bzl", "go_register_toolchains", "go_rules_dependencies")
load("@bazel_gazelle//:deps.bzl", "gazelle_dependencies", "go_repository")

go_rules_dependencies()

go_register_toolchains(version = "1.18.2")

# Manually added go repositories

go_repository(
    name = "org_golang_x_xerrors",  # keep
    importpath = "golang.org/x/xerrors",
    sum = "h1:go1bK/D/BFZV2I8cIQd1NKEZ+0owSTG1fDTci4IqFcE=",
    version = "v0.0.0-20200804184101-5ec99f83aff1",
)

load("//:workspace_go_repositories.bzl", "gazelle_managed_go_repositories")

# gazelle:repository_macro workspace_go_repositories.bzl%gazelle_managed_go_repositories
gazelle_managed_go_repositories()

gazelle_dependencies()

### rules_proto: https://github.com/bazelbuild/rules_proto

http_archive(
    name = "rules_proto",
    sha256 = "66bfdf8782796239d3875d37e7de19b1d94301e8972b3cbd2446b332429b4df1",
    strip_prefix = "rules_proto-4.0.0",
    urls = [
        "https://mirror.bazel.build/github.com/bazelbuild/rules_proto/archive/refs/tags/4.0.0.tar.gz",
        "https://github.com/bazelbuild/rules_proto/archive/refs/tags/4.0.0.tar.gz",
    ],
)

load("@rules_proto//proto:repositories.bzl", "rules_proto_dependencies", "rules_proto_toolchains")

rules_proto_dependencies()

rules_proto_toolchains()

####################################################
# Support creating Docker images for Kubernetes    #
####################################################

# Docker rules.
http_archive(
    name = "io_bazel_rules_docker",
    sha256 = "27d53c1d646fc9537a70427ad7b034734d08a9c38924cc6357cc973fed300820",
    strip_prefix = "rules_docker-0.24.0",
    urls = ["https://github.com/bazelbuild/rules_docker/releases/download/v0.24.0/rules_docker-v0.24.0.tar.gz"],
)

load(
    "@io_bazel_rules_docker//repositories:repositories.bzl",
    container_repositories = "repositories",
)

container_repositories()

load("@io_bazel_rules_docker//repositories:deps.bzl", container_deps = "deps")

container_deps()

load(
    "@io_bazel_rules_docker//container:container.bzl",
    "container_pull",
)

container_pull(
    name = "java_base",
    # 'tag' is also supported, but digest is encouraged for reproducibility.
    digest = "sha256:deadbeef",
    registry = "gcr.io",
    repository = "distroless/java",
)

####################################################
# Kubernetes setup
####################################################

http_archive(
    name = "io_bazel_rules_k8s",
    sha256 = "773aa45f2421a66c8aa651b8cecb8ea51db91799a405bd7b913d77052ac7261a",
    strip_prefix = "rules_k8s-0.5",
    urls = ["https://github.com/bazelbuild/rules_k8s/archive/v0.5.tar.gz"],
)

load("@io_bazel_rules_k8s//k8s:k8s.bzl", "k8s_defaults", "k8s_repositories")

k8s_repositories()

load("@io_bazel_rules_k8s//k8s:k8s_go_deps.bzl", k8s_go_deps = "deps")

k8s_go_deps()

k8s_defaults(
    # This creates a rule called "k8s_deploy" that we can call later
    name = "k8s_deploy",
    # This is the name of the cluster as it appears in:
    #   kubectl config view --minify -o=jsonpath='{.contexts[0].context.cluster}'
    cluster = "minikube",
    # See https://github.com/bazelbuild/rules_k8s/issues/685
    image_chroot = "localhost:5000/my_registry",
    kind = "deployment",
)

load("@bazel_tools//tools/build_defs/repo:git.bzl", "git_repository")

# Originally from
# https://github.com/brownbeartech/kubernetes-with-bazel/commit/4b07e314007dbd559d70c2578a8043492eae27f5
git_repository(
    name = "distroless",
    commit = "2a50c6bd62d7ad91c1b0a351fdc0897711d82646",
    remote = "https://github.com/GoogleContainerTools/distroless",
)

load("@distroless//package_manager:dpkg.bzl", "dpkg_list", "dpkg_src")
load("@distroless//:debian_archives.bzl", debian_repositories = "repositories")

debian_repositories()

dpkg_src(
    name = "debian_stretch",
    arch = "amd64",
    distro = "stretch",
    sha256 = "4cb2fac3e32292613b92d3162e99eb8a1ed7ce47d1b142852b0de3092b25910c",
    snapshot = "20180406T095535Z",
    url = "http://snapshot.debian.org/archive",
)

dpkg_list(
    name = "package_bundle",
    packages = [
        "base-files",
        "ca-certificates",
        "openjdk-8-jre-headless",
        "openssl",
        "python2.7-minimal",
        "ssh",
        "libc6",
        "libgcc1",
        "libgomp1",
        "libpython2.7-minimal",
        "libpython2.7-stdlib",
        "libssl1.1",
        "libstdc++6",
        "netbase",
        "tzdata",
        "zlib1g",
        # Add any other debian packages you need here.
    ],
    sources = [
        "@debian_stretch//file:Packages.json",
    ],
)

### Apache beam operator stuff

http_file(
    name = "cert_manager_yaml",
    downloaded_file_path = "cert-manager.yaml",
    sha256 = "517b57b60e37288ced462bb541f3f5dabb53f59f40c262387403862985b01ae8",
    urls = [
        "https://github.com/cert-manager/cert-manager/releases/download/v1.8.0/cert-manager.yaml",
    ],
)
