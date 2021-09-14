# casper-get-started

Step 1 of [Get started with Casper](https://gitcoin.co/issue/casper-network/gitcoin-hackathon/29/100026611) from the Casper Hackathon.

> Create and deploy a simple, smart contract with cargo casper and cargo test

### 1. Rust Installation

### 2. Install cargo-casper

Install cargo-casper this will be used to create a simple contract, a runtime environment and a testing framework with a simple test.

### 3. Create new project

### 4. Sync cargo-casper with nightly

Got the following error:
```
error: component download failed for rustc-x86_64-unknown-linux-gnu: could not rename downloaded file from '/home/chrisb/.rustup/downloads/0ed6cf75177b00994224590434859bc05f13890dba51d18f97456b4a9fa32161.partial' to '/home/chrisb/.rustup/downloads/0ed6cf75177b00994224590434859bc05f13890dba51d18f97456b4a9fa32161'
```
Needed to do:
```rustup component add cargo```

### 5. Add wasm target

Got error:

### 6. Build the contract

error: failed to select a version for the requirement `casper-contract = "^1.3.3"`

Updated Cargo to use 1.3.2 instead.

### 7. Test the contract

error: failed to select a version for the requirement `casper-contract = "^1.3.3"`
error: failed to select a version for the requirement `casper-engine-test-support = "^1.3.3"`

Updated Cargo to use 1.3.2 for both of these.

Then got errors:

failures:

---- tests::should_store_hello_world stdout ----
thread 'tests::should_store_hello_world' panicked at 'Rust Wasm path /home/chrisb/.cargo/registry/src/target/wasm32-unknown-unknown/release does not exists', /home/chrisb/.cargo/registry/src/github.com-1ecc6299db9ec823/casper-engine-test-support-1.3.2/src/internal/utils.rs:49:5
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace

---- tests::should_error_on_missing_runtime_arg stdout ----
thread 'tests::should_error_on_missing_runtime_arg' panicked at 'Lazy instance has previously been poisoned', /home/chrisb/.cargo/registry/src/github.com-1ecc6299db9ec823/once_cell-1.8.0/src/lib.rs:1116:25
note: panic did not contain expected string
      panic message: `"Lazy instance has previously been poisoned"`,
 expected substring: `"ApiError::MissingArgument"`

failures:
    tests::should_error_on_missing_runtime_arg
    tests::should_store_hello_world

test result: FAILED. 0 passed; 2 failed; 0 ignored; 0 measured; 0 filtered out; finished in 0.08s

### 8. Diagnosis of errors

It looks like target/wasm32-unknown-unknown should be copied under the tests folder but there is only tests/target/debug.

I'll try manually copying the wasm32-unknown-unknown out of release into tests.

I realised the error was that I had come out of the contract directory and gone into the tests director and was running cargo test from there.

### 9. Tests Passing



