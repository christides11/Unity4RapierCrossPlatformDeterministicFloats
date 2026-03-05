This repo contains tests to check the determinism (consistency) of the floating point operations on different Unity build platforms and CPUs. Cross platform determinism has many applications, and is essential for some real-time networking paradigms in video games.

Tests normal numbers, denormal numbers, NaN and infinities, along with an amount of randomly generated values. All tests can be found in `Tests.cs` and `TranscendentalTests.cs`. The current tests cover:

- Arithmetic (+ - * /)
- Comparisons (> < >= <= == !=)
- Casts (to and from `int`/`float`)
- Multiple operand arithmetic
- Triginometry functions found in `rapier4unity'

All tests here are performed using the IL2CPP backend, with the Windows standalone build results being used as the ground truth. The results here use a random value count of 500 and a seed of 0. If you test on a setup not yet listed here, please feel free to report your results to be added to the table.
Burst Floating Point Mode is set to Deterministic.

### Disclaimer

The author of this repo is not an expert in floating point arithmetic determinism. If you have any improvements to add please feel free to contribute or correct any oversights.

## Results Summary

Inconclusive.

### Notes

* Operations involving NaN floats return non-deterministic results. This should not be an issue for applications that require determinism, as either
  * NaNs create serious enough bugs that desyncs no longer matter.
  * The developer can choose to check for NaNs after each operation and resolve the issue there (this repo has an option to treat all NaN results as if they were the same bit sequence; all test results here have this option enabled).
* Unity's documentation does not make any statement on float determinism either way, so even with consistent results there is no guarantee future versions do not change this.
* [ARMv7 apparently handles denormal numbers differently from ARMv8](https://stackoverflow.com/a/53993942), so should not be a surprise if it desyncs there. 

### Data

| Platform           | Device                                       | Errors                  |
|--------------------|----------------------------------------------|-------------------------|
| Windows Standalone | AMD Ryzen 9 9950X3D                          | Ground Truth            |
| Windows Standalone | Intel Core i7-7700HQ                         | 0 errors                |
| Steam Deck (Linux) | AMD RDNA 2                                   | 0 errors                |
| Android            | Galaxy S25 (ARMv8)                           | 5 in MultiOperatorBurst |

## Running the tests

* Use the included inputs.txt and results.txt (If you have a Windows PC with an AMD CPU, you can generate the ground truth with a count of 500 and seed of 0. The results.txt file will have CRC64=6064d37521c68ee1.)
* Copy these files to the `StreamingAssets` directory.
* Build to your target platform to run the test on it.
* Once running, select `Execute Test` and toggle off any tests you do not wish to perform.

### Extending the tests

Tests can be added by creating a new class that implements `ITest`, and then adding it to one of the arrays in `TestRunner` (depending on whether the test takes one or two operands). New hardcoded test inputs can be added in `Generator`. Please name these appropriately, as the names are used when reporting non-deterministic results.
