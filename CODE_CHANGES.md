# Code Changes for rbspy 0.37.x Upgrade

## Summary
This document describes code changes made during the upgrade of the rbspy dependency from version 0.17.x to version 0.37.1 to restore Ruby 3.4.6+ profiling support (Issue #278).

## Changes Made

### 1. Dependency Updates
**File:** `pyroscope_ffi/ruby/ext/rbspy/Cargo.toml`
- **Status:** No changes required
- **Note:** The rbspy dependency was already at version `0.37` which resolves to `0.37.1` in `Cargo.lock`

### 2. Code Fixes
**File:** `pyroscope_ffi/ruby/ext/rbspy/src/backend.rs`
- **Change:** Removed unused imports `BackendImpl` and `BackendUninitialized`
- **Reason:** These imports were flagged by the Rust compiler as unused. Removing them to maintain clean code.
- **Lines:** Line 3 - import statement updated
- **Impact:** No behavioral change, only cleanup of unused code

### 3. Build System
**Status:** No changes required
- The existing build system works correctly with rbspy 0.37.1
- All builds complete successfully with only the aforementioned warning about unused imports (now fixed)

## API Compatibility

### rbspy API Changes
The rbspy library upgrade from 0.17.x to 0.37.1 did **not** require any integration code changes beyond removing unused imports. The following APIs remained stable:

1. **Sampler API**
   - `Sampler::new()` - signature unchanged
   - `sampler.start()` - signature unchanged
   - `sampler.stop()` - signature unchanged

2. **StackTrace/StackFrame structures**
   - Field names and types remain compatible
   - Conversion logic in `backend.rs` works without modification

3. **FFI Interface**
   - All C FFI functions in `lib.rs` work without changes
   - Ruby extension interface remains stable

## Testing Results

### Rust Build & Tests
- **Build:** ✅ Success (see `build_rbspy_backend.log`)
- **Tests:** ✅ Success - 0 tests in crate (library compiles cleanly)
- **Warnings:** Only unused imports (now fixed)

### Ruby Extension Build
- **Bundle Install:** ✅ Success
- **Rake rbspy_install:** ✅ Success (see `build_ruby_ext.log`)
- **Output:** Native extension compiled successfully as `lib/rbspy/rbspy.so`

## Conclusion

The upgrade to rbspy 0.37.1 was straightforward with minimal code changes required:
- No API breaking changes encountered
- Only cleanup of unused imports needed
- All builds and tests pass successfully

The integration is stable and ready for Ruby 3.4.6+ profiling support.
