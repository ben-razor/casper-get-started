# casper-get-started

### 1. Rust Installation

### 2. Rust Nightly

### 3. Rust Version

### 4. Check CMake Version

It says to get the latest version but I've stuck with the latest version for my OS.

### 5. Install cargo-casper

Install cargo-casper this will be used to create a simple contract, a runtime environment and a testing framework with a simple test.

### 6. Create new project

### 7. Sync cargo-casper with nightly

Got the following error:
```
error: component download failed for rustc-x86_64-unknown-linux-gnu: could not rename downloaded file from '/home/chrisb/.rustup/downloads/0ed6cf75177b00994224590434859bc05f13890dba51d18f97456b4a9fa32161.partial' to '/home/chrisb/.rustup/downloads/0ed6cf75177b00994224590434859bc05f13890dba51d18f97456b4a9fa32161'
```
Needed to do:
```rustup component add cargo```
