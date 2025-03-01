# Copyright 2018 The JAX Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# JAX is Autograd and XLA

load("//jaxlib:symlink_files.bzl", "symlink_files")
load(
    "//jaxlib:jax.bzl",
    "if_windows",
    "py_library_providing_imports_info",
    "pybind_extension",
    "pytype_library",
)

licenses(["notice"])

package(default_visibility = ["//:__subpackages__"])

py_library_providing_imports_info(
    name = "jaxlib",
    srcs = [
        "ducc_fft.py",
        "gpu_linalg.py",
        "gpu_prng.py",
        "gpu_rnn.py",
        "gpu_solver.py",
        "gpu_sparse.py",
        "hlo_helpers.py",
        "init.py",
        "lapack.py",
        ":version",
        ":xla_client",
    ],
    data = [":xla_extension"],
    lib_rule = pytype_library,
    deps = [
        ":cpu_feature_guard",
        "//jaxlib/cpu:_ducc_fft",
        "//jaxlib/cpu:_lapack",
        "//jaxlib/mlir",
        "//jaxlib/mlir:builtin_dialect",
        "//jaxlib/mlir:chlo_dialect",
        "//jaxlib/mlir:func_dialect",
        "//jaxlib/mlir:ir",
        "//jaxlib/mlir:mhlo_dialect",
        "//jaxlib/mlir:ml_program_dialect",
        "//jaxlib/mlir:pass_manager",
        "//jaxlib/mlir:sparse_tensor_dialect",
        "//jaxlib/mlir:stablehlo_dialect",
    ],
)

symlink_files(
    name = "version",
    srcs = ["//jax:version.py"],
    dst = ".",
    flatten = True,
)

symlink_files(
    name = "xla_client",
    srcs = ["@xla//xla/python:xla_client"],
    dst = ".",
    flatten = True,
)

symlink_files(
    name = "xla_extension",
    srcs = if_windows(
        ["@xla//xla/python:xla_extension.pyd"],
        ["@xla//xla/python:xla_extension.so"],
    ),
    dst = ".",
    flatten = True,
)

exports_files([
    "README.md",
    "setup.py",
    "setup.cfg",
])

cc_library(
    name = "kernel_pybind11_helpers",
    hdrs = ["kernel_pybind11_helpers.h"],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        ":kernel_helpers",
        "@com_google_absl//absl/base",
        "@pybind11",
    ],
)

cc_library(
    name = "kernel_helpers",
    hdrs = ["kernel_helpers.h"],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/base",
        "@com_google_absl//absl/status:statusor",
    ],
)

cc_library(
    name = "handle_pool",
    hdrs = ["handle_pool.h"],
    copts = [
        "-fexceptions",
        "-fno-strict-aliasing",
    ],
    features = ["-use_header_modules"],
    deps = [
        "@com_google_absl//absl/base:core_headers",
        "@com_google_absl//absl/status:statusor",
        "@com_google_absl//absl/synchronization",
    ],
)

# This isn't a CPU kernel. This exists to catch cases where jaxlib is built for the wrong
# target architecture.
pybind_extension(
    name = "cpu_feature_guard",
    srcs = ["cpu_feature_guard.c"],
    module_name = "cpu_feature_guard",
    deps = [
        "@xla//third_party/python_runtime:headers",
    ],
)

# CPU kernels

# TODO(phawkins): Remove this forwarding target.
cc_library(
    name = "cpu_kernels",
    visibility = ["//visibility:public"],
    deps = [
        "//jaxlib/cpu:cpu_kernels",
    ],
    alwayslink = 1,
)

# TODO(phawkins): Remove this forwarding target.
cc_library(
    name = "gpu_kernels",
    visibility = ["//visibility:public"],
    deps = [
        "//jaxlib/cuda:cuda_gpu_kernels",
    ],
    alwayslink = 1,
)
