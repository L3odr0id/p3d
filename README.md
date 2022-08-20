# p3d
3D object shape recognition tools for WASM, which not only has the same functionality to [pass3d](https://github.com/3Dpass/pass3d), but also it's compatible to Substrate. Now, it's used as a part of 3Dpass network Node.

### How to update library in [threedpass](https://github.com/3Dpass/threedpass)
- Make your changes.
- Check the lib name is ```calc``` [see the source](https://github.com/L3odr0id/p3d/blob/912236d154d6511129d730ecc203fd1a50e054b3/Cargo.toml#L11), the exported functions have bindable types. Also make sure to annotate your exported functions with `#[no_mangle]` and `pub extern` so the function names can be matched from Dart. See [working bindable function example](https://github.com/L3odr0id/p3d/blob/912236d154d6511129d730ecc203fd1a50e054b3/src/ffi_interface.rs#L11).
- Compile the library. There is a ```makefile``` copied from [brickpop's example](https://github.com/brickpop/flutter-rust-ffi/blob/master/README.md#compile-the-library). So follow his instruction.
  - Make sure that the Android NDK is installed
    - You might also need LLVM from the SDK manager
  - Ensure that the env variable `$ANDROID_NDK_HOME` points to the NDK base folder
    - It may look like `/Users/brickpop/Library/Android/sdk/ndk-bundle` on MacOS
    - And look like `/home/brickpop/dev/android/ndk-bundle` on Linux
  - On the `rust` folder:
    - Run `make` to see the available actions
    - Run `make init` to install the Rust targets
    - Run `make all` to build the libraries and the `.h` file
  - Check generated artifacts:
    - Android libraries
        - `target/aarch64-linux-android/release/libexample.so`
        - `target/armv7-linux-androideabi/release/libexample.so`
        - `target/i686-linux-android/release/libexample.so`
        - `target/x86_64-linux-android/release/libexample.so`
    - iOS library
        - `target/universal/release/libexample.a`
    - Bindings header
        - `target/bindings.h`
- Place artifacts to the threedpass project. Follow this structure in ```threedpass/packages/calc/android```:
  ```
  src
  └── main
      └── jniLibs
          ├── arm64-v8a
          │   └── libexample.so <- p3d/target/aarch64-linux-android/release/libexample.so
          ├── armeabi-v7a
          │   └── libexample.so <- p3d/target/armv7-linux-androideabi/release/libexample.so
          ├── x86
          │   └── libexample.so <- p3d/target/i686-linux-android/release/libexample.so
          └── x86_64
              └── libexample.so <- p3d/target/x86_64-linux-android/release/libexample.so
  ```
- Update the ios folder. Place ```p3d/target/aarch64-apple-ios/release/libcalc.a``` into ```threedpass/packages/calc/ios```
- If you have changed the interface, follow next steps also. Otherwise you can compile the app and check the functionality.
  - Copy ```p3d/target/bindings.h``` to ```threedpass/rust/target/bindings.h```.
  - Generate bindings. Check the ```ffigen``` section in ```threedpass/packages/calc/pubspec.yaml```. 
  To use [ffigen](https://pub.dev/packages/ffigen) install ```llvm```
  
  **On MacOS**:
  ```sh
  brew install llvm
  ```

  **On Linux**:
  ```sh
  sudo apt-get install -y clang libclang-dev
  ```
  Write path to llvm for your system in pubspec.yaml.
  ```yaml
  llvm-path:
    - /usr/local/opt/llvm/bin # For MacOS. Set path for your system
  ```
  And generate `lib/generated/bindings.dart`:
  ```sh
  flutter pub run ffigen
  ```
  - Setup on iOS. Append the ```p3d/target/bindings.h``` to ```threedpass/packages/calc/ios/Classes/CalcPlugin.h```.  In ```ios/Classes/SwiftCalcPlugin.swift``` call your functions, so the compiler won't remove them.
  - Use bindings and write Dart code you need.

<br></br>
#### Thank you for contribution!