# cpp-macros

simple cpp macros
Here’s a clean, professional **open-source-style README** for your header:

---

# Assert and Fatal Utilities for C++

This header provides lightweight runtime assertion and fatal-error utilities optimized for performance-critical C++ applications.
It is designed for use in systems where fast failure detection and predictable runtime behavior are essential, such as real-time trading, analytics, or embedded applications.

---

## Overview

The utilities defined in this header enable:

- Simple runtime condition checks (`ASSERT`)
- Immediate termination on unrecoverable errors (`FATAL`)
- CPU branch prediction hints for improved performance on the expected code path

Both macros are implemented using standard C++ and GCC/Clang built-ins for maximum portability and efficiency.

---

## Features

- Header-only and zero-dependency
- Integrates with standard error output (`std::cerr`)
- Uses `__builtin_expect` to help the compiler optimize for the common (non-error) case
- Inlined, `noexcept` functions for minimal overhead
- Works with GCC and Clang on Linux, macOS, and Windows (via MinGW or WSL)

---

## Example Usage

```cpp
#include "assert_utils.hpp"

int main() {
    int value = 10;

    // Passes silently
    ASSERT(value == 10, "Value should be 10");

    // Fails, prints an error message, and exits
    ASSERT(value != 10, "Unexpected value");

    // Always fails and exits
    FATAL("Critical system error");
}
```

Example output:

```
ASSERT : Unexpected value
```

---

## Implementation Details

### Branch Prediction Macros

```cpp
#define LIKELY(x)   __builtin_expect(!!(x), 1)
#define UNLIKELY(x) __builtin_expect(!!(x), 0)
```

These macros hint to the compiler which code paths are most common.
This improves instruction-level parallelism and branch prediction accuracy in tight loops or latency-sensitive code.

### Assertion Helper

```cpp
inline auto ASSERT(bool cond, const std::string &msg) noexcept {
  if (UNLIKELY(!cond)) {
    std::cerr << "ASSERT : " << msg << std::endl;
    exit(EXIT_FAILURE);
  }
}
```

- Evaluates a condition and terminates the program if it fails.
- Intended for conditions that should never fail under normal operation.

### Fatal Helper

```cpp
inline auto FATAL(const std::string &msg) noexcept {
  std::cerr << "FATAL : " << msg << std::endl;
  exit(EXIT_FAILURE);
}
```

- Used for unrecoverable runtime errors.
- Always terminates immediately.

---

## Performance Considerations

- Uses **no exceptions** and minimal branching for deterministic runtime cost.
- **Branch prediction hints** help keep the “success” path fast.
- Safe to use in high-frequency or low-latency systems where predictability matters.

---

## Integration

Simply include the header in your project:

```cpp
#include "assert_utils.hpp"
```

No linking or build-system configuration is required.

---

## License

This code is released under the MIT License.
You are free to use, modify, and distribute it with attribution.

---
