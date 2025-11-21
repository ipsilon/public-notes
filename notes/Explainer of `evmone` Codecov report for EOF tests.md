# Explainer of `evmone` Codecov report for EOF tests

###### tags: `EOF`

Based on report https://app.codecov.io/gh/ethereum/evmone/commit/26cdd854106cbcbb4d817cfd5e0d2b1ee41bcd21/tree/lib/evmone?flags%5B0%5D=eof_execution_spec_tests&flags%5B1%5D=ethereum_tests&dropdown=coverage .

**NOTE** includes both EOF EEST tests and `ethereum/tests` (in the process of migration).

| File                           | Coverage % | Comment                                                                                                        |
|--------------------------------|------------|----------------------------------------------------------------------------------------------------------------|
| advanced_analysis.cpp          | 0.00%      | ✅ Advanced mode off for EOF                                                                                      |
| advanced_analysis.hpp          | 0.00%      | ✅ as above                                                                                                       |
| advanced_execution.cpp         | 0.00%      | ✅ as above                                                                                                       |
| advanced_instructions.cpp      | 2.17%      | ✅ as above                                                                                                       |
| baseline.hpp                   | 100.00%    | ✅                                                                                                                |
| baseline_analysis.cpp          | 100.00%    | ✅                                                                                                                |
| baseline_execution.cpp         | 80.53%     | ✅ Missed non-cgoto dispatch and tracer lines                                                                     |
| baseline_instruction_table.cpp | 100.00%    | ✅                                                                                                                |
| cpu_check.cpp                  | 60.00%     | ✅ Irrelevant config tool                                                                                         |
| delegation.cpp                 | 55.56%     | ✅ Probably up to [7702-related PR](https://github.com/ethereum/execution-spec-tests/pull/1309)                   |
| delegation.hpp                 | 100.00%    | ✅                                                                                                                |
| eof.cpp                        | 83.42%     | ⚠️ Mostly irrelevant - error string handling, unreachable lines. Line 742 needs double-checking                   |
| eof.hpp                        | 100.00%    | ✅                                                                                                                |
| execution_state.hpp            | 89.29%     | ⚠️ Plumbing, but could use double-checking                                                                        |
| instructions.hpp               | 99.84%     | ✅ Codecov tool hickup on macro                                                                                   |
| instructions_calls.cpp         | 95.82%     | ⚠️ [7702-related PR](https://github.com/ethereum/execution-spec-tests/pull/1309) but also other finds - relevant! |
| instructions_storage.cpp       | 100.00%    | ✅                                                                                                                |
| tracing.cpp                    | 8.33%      | ✅ Tracing is off                                                                                                 |
| tracing.hpp                    | 6.25%      | ✅ Tracing is off                                                                                                 |
| vm.cpp                         | 82.05%     | ✅ Option handling                                                                                                |
| vm.hpp                         | 75.00%     | ✅ Tracer handling                                                                                                |
