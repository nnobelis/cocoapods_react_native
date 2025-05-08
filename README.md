# Test repository for Cocoapods with React Native

This is a test repository that exhibit several problems with the analysis with ORT of CocoaPods projects with React Native.

The lockfile has been created by hand since react native requires an Apple Silicon machine with xcode.

Before the analysis with the CocoaPods package manager, please run the NPM package manager: it will do a `npm install`
which will initialize the `node_modules` directory.

* ORT does support relative `:podpec:` property of the "EXTERNAL SOURCES" section of the `Podfile.lock`, but it resolves it
against the root of the repository, not the lockfile location.
* ORT parses the `:path:` property of the "EXTERNAL SOURCES" section of the `Podfile.lock`, but it does not use it at
all.
* ORT does not support the format of those podspec files, which is not standard JSON but Ruby instead. For instance for `../node_modules/react-native/third-party-podspecs/fmt.podspec`:

```
# Copyright (c) Meta Platforms, Inc. and affiliates.
#
# This source code is licensed under the MIT license found in the
# LICENSE file in the root directory of this source tree.

Pod::Spec.new do |spec|
  spec.name = "fmt"
  spec.version = "9.1.0"
  spec.license = { :type => "MIT" }
  spec.homepage = "https://github.com/fmtlib/fmt"
  spec.summary = "{fmt} is an open-source formatting library for C++. It can be used as a safe and fast alternative to (s)printf and iostreams."
  spec.authors = "The fmt contributors"
  spec.source = {
    :git => "https://github.com/fmtlib/fmt.git",
    :tag => "9.1.0"
  }
  spec.pod_target_xcconfig = {
    "CLANG_CXX_LANGUAGE_STANDARD" => rct_cxx_language_standard(),
  }
  spec.platforms = min_supported_versions
  spec.libraries = "c++"
  spec.public_header_files = "include/fmt/*.h"
  spec.header_mappings_dir = "include"
  spec.source_files = ["include/fmt/*.h", "src/format.cc"]
end
```

There is a command `pod ipc spec <file>`that _could_ theoretically parse this file.
However, the file uses `rct_cxx_language_standard()` which is a function defined in
`react-native/scripts/react_native_pods.rb` and it is unclear how to load it beforehands.
If this is addressed, this command is the way to go as is outputs the podspec in JSON format.