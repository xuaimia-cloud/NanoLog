# NanoLog

Low Latency C++11 Logging Library.

* It's fast. Very fast. See [Latency benchmark](#latency-benchmark-of-guaranteed-logger)
* NanoLog only uses standard headers so it should work with any C++11 compliant compiler.
* Supports typical logger features namely multiple log levels, log file rolling and asynchronous writing to file.

## Design highlights
* Zero copying of string literals.
* Lazy conversion of integers and doubles to ascii.
* No heap memory allocation for log lines representable in less than ~256 bytes.
* Minimalistic header includes. Avoids common pattern of header only library. Helps in compilation times of projects.

## Guaranteed and Non Guaranteed logging
* Nanolog supports Guaranteed logging i.e. log messages are never dropped even at extreme logging rates.
* Nanolog also supports Non Guaranteed logging. Uses a ring buffer to hold log lines. In case of extreme logging rate when the ring gets full (i.e. the consumer thread cannot pop items fast enough), the previous log line in the slot will be dropped. Does not block producer even if the ring buffer is full.

## Usage
```c++
#include "NanoLog.hpp"

int main() {
    // Initialize NanoLog
    nanolog::initialize(nanolog::GuaranteedLogger(), "/tmp/", "nanolog", 1);

    // Set log level
    nanolog::set_log_level(nanolog::LogLevel::INFO);

    // Log messages
    LOG_INFO << "Hello World! " << 123;
    LOG_WARN << "Warning message";
    LOG_CRIT << "Critical error: " << std::hex << 0xDEADBEEF;

    return 0;
}
```

## Compilation

```bash
g++ -std=c++11 -O3 your_code.cpp NanoLog.cpp -pthread -o your_program
```

## Benchmark

Run the included benchmark tests:

```bash
g++ -std=c++11 -O3 nano_vs_spdlog_vs_g3log_vs_reckless.cpp -pthread -o benchmark
./benchmark

g++ -std=c++11 -O3 non_guaranteed_nanolog_benchmark.cpp -pthread -o non_guaranteed_benchmark
./non_guaranteed_benchmark
```

## Performance

NanoLog is designed for ultra-low latency logging:

- **Guaranteed Logger**: ~10-30 nanoseconds per log statement
- **Non-Guaranteed Logger**: ~5-10 nanoseconds per log statement
- **Zero heap allocation** for log lines under 256 bytes

## License

MIT License