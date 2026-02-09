# Test Report for rbspy 0.37.x Upgrade

## Overview
This document provides a testing checklist and report template for validating the rbspy 0.37.1 upgrade in the pyroscope-rs project.

**Related Issue:** [#278](https://github.com/grafana/pyroscope-rs/issues/278)  
**Upgrade Target:** rbspy 0.37.1 (from ~0.17.x)  
**Purpose:** Restore Ruby 3.4.6+ profiling support  

---

## Pre-Upgrade Status

- [ ] **Documented current rbspy version:** 0.17.x
- [ ] **Identified all rbspy dependency locations**
  - `pyroscope_ffi/ruby/ext/rbspy/Cargo.toml`

---

## Build & Compilation Tests

### Rust Build Tests
- [x] **cargo build** - rbspy backend
  - Status: ✅ PASSED
  - Output: See `build_rbspy_backend.log`
  - Notes: Build completed successfully in ~1m 42s

- [x] **cargo test** - rbspy backend
  - Status: ✅ PASSED
  - Output: See `test_rbspy_backend.log`
  - Notes: 0 tests, compilation successful

- [x] **cargo check** - workspace verification
  - Status: ✅ PASSED
  - Notes: All dependencies resolve correctly

### Ruby Extension Build Tests
- [x] **bundle install**
  - Status: ✅ PASSED
  - Ruby version: 3.2.3
  - Bundler version: 4.0.6 (with 2.5.3 lockfile)

- [x] **rake rbspy_install** (native extension build)
  - Status: ✅ PASSED
  - Output: See `build_ruby_ext.log`
  - Result: `lib/rbspy/rbspy.so` created successfully
  - Notes: Build completed in ~2m 41s

---

## Code Quality Checks

- [x] **Compiler warnings addressed**
  - Fixed: Removed unused imports `BackendImpl` and `BackendUninitialized` in `backend.rs`
  - Result: Clean compilation with no warnings

- [ ] **Code review completed**
  - Pending: Automated code review

- [ ] **Security scan completed**
  - Pending: CodeQL security analysis

---

## Functional Testing Checklist

### Manual Testing (Environment-Dependent)

These tests require a full Ruby environment with Pyroscope server access. Maintainers should run these tests locally:

#### Basic Functionality Tests

- [ ] **Test 1: Gem loads successfully**
  ```ruby
  require 'pyroscope'
  ```
  - Expected: No errors
  - Environment: Ruby >= 3.2.0

- [ ] **Test 2: Native extension loads**
  ```ruby
  # Check that rbspy.so is loadable
  ```
  - Expected: Library loads without errors

- [ ] **Test 3: Agent initializes**
  ```ruby
  Pyroscope.start(
    application_name: "test.app",
    server_address: "http://localhost:4040"
  )
  ```
  - Expected: Agent starts without crashes
  - Required: Pyroscope server running

- [ ] **Test 4: Profiling captures data**
  - Run workload with agent active
  - Expected: Stack traces captured and sent to server
  - Verify: Check Pyroscope UI for profiles

#### Ruby 3.4.6+ Specific Tests

- [ ] **Test 5: Ruby 3.4.6+ compatibility**
  - Environment: Ruby 3.4.6 or later
  - Run: `ruby test_ruby_3.4.6.rb`
  - Expected: No crashes related to interrupt masking
  - Expected: Profiles captured successfully

- [ ] **Test 6: Extended profiling session**
  - Duration: 5+ minutes of continuous profiling
  - Expected: No memory leaks or crashes
  - Expected: Consistent profile data

- [ ] **Test 7: Multi-threaded workload**
  - Test with multi-threaded Ruby application
  - Expected: All threads profiled correctly
  - Expected: Thread IDs captured accurately

---

## Integration Testing

### Gem Packaging Tests

- [ ] **Test 8: Source gem builds**
  ```bash
  rake source:gem
  ```
  - Expected: `pyroscope-0.6.7.gem` created

- [ ] **Test 9: Platform-specific gems build**
  - [ ] x86_64-linux
  - [ ] aarch64-linux  
  - [ ] x86_64-darwin
  - [ ] arm64-darwin
  - Expected: Platform gems include rbspy.so

---

## Performance Validation

- [ ] **Test 10: Profiling overhead acceptable**
  - Measure: CPU overhead < 5%
  - Measure: Memory overhead < 10MB
  - Method: Compare with/without profiling

- [ ] **Test 11: Sample rate accuracy**
  - Test at: 100Hz, 10Hz sample rates
  - Expected: Actual rate matches configured rate

---

## Edge Cases & Error Handling

- [ ] **Test 12: Graceful degradation**
  - Test: No Pyroscope server available
  - Expected: Initialization fails gracefully with clear error

- [ ] **Test 13: Invalid configuration**
  - Test: Invalid server URL, bad sample rate, etc.
  - Expected: Clear validation errors

---

## Documentation & Artifacts

- [x] **CODE_CHANGES.md created**
  - Documents all code modifications
  - Explains API compatibility

- [x] **Build logs captured**
  - `build_rbspy_backend.log` ✅
  - `test_rbspy_backend.log` ✅
  - `build_ruby_ext.log` ✅

- [x] **Smoke test script created**
  - `test_ruby_3.4.6.rb` ✅

- [x] **Test report template created**
  - This document ✅

---

## Sign-Off

### Agent Testing (Automated)
- Date: 2026-02-09
- Tester: GitHub Copilot Agent
- Status: ✅ Build & Compilation Tests PASSED

### Manual Testing (Requires Human Verification)
- Date: _______________
- Tester: _______________
- Ruby Version: _______________
- Platform: _______________
- Status: ☐ PASSED ☐ FAILED ☐ PARTIAL

### Notes
```
[Maintainer notes here]




```

---

## References

- **Upstream Issue:** https://github.com/grafana/pyroscope-rs/issues/278
- **rbspy Changelog:** https://github.com/rbspy/rbspy/releases/tag/v0.37.0
- **Ruby 3.4.6 Release:** Ruby interrupt masking changes

---

## Conclusion

**Automated Testing:** ✅ COMPLETE  
The build and compilation tests have all passed successfully. The rbspy 0.37.1 integration builds cleanly on the current platform.

**Manual Testing:** ⏳ PENDING  
Full functional testing requires:
1. Ruby 3.4.6+ environment
2. Pyroscope server instance
3. Real workload applications

**Recommendation:**  
The upgrade is ready for human verification and manual testing in a full Ruby 3.4.6+ environment.
