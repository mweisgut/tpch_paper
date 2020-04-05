# Quantifying TPC-H Choke Points and Their Optimizations

Welcome to the online resources for our paper "Quantifying TPC-H Choke Points and Their Optimizations". In this repository, we share instructions and the code needed for reproducing the results presented in the paper. Additionally, we provide the raw benchmark results as generated by the hyriseBenchmarkTPCH binary.

For any questions about setting up the environment, reproducing the benchmarks, or just to discuss the paper as such, please feel free to contact us.

## Setting up Hyrise

Hyrise can be retrieved from [Github](https://github.com/hyrise/hyrise). Our [Step-by-Step Guide](https://github.com/hyrise/hyrise/wiki/Step-by-Step-Guide) will take you through the steps needed to set up Hyrise, starting the console, or running the TPC-H benchmark.

The patch files in this repository are based on the `mrks/tpch_paper` branch. For better reproducibility, that branch will not be updated with more recent Hyrise developments. To use Hyrise beyond the experiments presented here, please use the current `master` branch.

To execute a benchmark as presented in the paper, first generate baseline results using

`./hyriseBenchmarkTPCH -s 10 -o baseline.json`

Next, apply the provided patch file using `git apply patchfile.diff` in the repositories root folder. After recompiling, re-run the benchmarks and store the results in a different file. You can then compare two runs using the `./scripts/compare_benchmarks.py` script.


## Section 1. Motivation

We crawled Google Scholar using [this script](https://github.com/Pold87/academic-keyword-occurrence/blob/master/extract_occurrences.py) from Strobel and Hofmann. Raw data is contained in the downloadable repository.

## Section 3. Experimental Setup

* The performance breakdown of Hyrise operators (Figure 2) can be obtained by running the benchmark with the `--visualize` parameter and calling the `plot_performance_breakdown.py` script.

* For re-running the Hyrise TPC-H Comparison shown in Figure 3, execute hyriseBenchmarkTPCH. The average time for each benchmark query is given on the console.

* Instructions for executing TPC-H on MonetDB can be found [here](https://github.com/MonetDBSolutions/tpch-scripts). We used MonetDB 5 server 11.35.9, which was obtained using Ubuntu's package manager.

* DuckDB was built from [source](https://github.com/cwida/duckdb) (70c20f28f) using a [patch](./1_motivation/duckdb.patch) to enable SF 10.


## Section 4. Plan-Level Choke Points

Please find the mentioned diff files and the raw results in the respective folder or linked below.

### 4.1 Join Ordering

* Baseline: [original_join_order.diff](./4_plan_level_choke_points/4_1_join_ordering/original_join_order.diff) (raw data not linked here, see folder)
* (1) DPccp: unmodified Hyrise code
* (2) Greedy: [greedy.diff](./4_plan_level_choke_points/4_1_join_ordering/greedy.diff)

### 4.2 Predicate Placement and Ordering

* Baseline: [no_pushdown_no_ordering.diff](./4_plan_level_choke_points/4_2_predicate_positioning/no_pushdown_no_ordering.diff)
* (1) Pushdown without ordering: [pushdown_no_ordering.diff](./4_plan_level_choke_points/4_2_predicate_positioning/pushdown_no_ordering.diff)
* (2) Pushdown and ordering: [pushdown_ordering.diff](./4_plan_level_choke_points/4_2_predicate_positioning/pushdown_ordering.diff)

### 4.3 Between Composition

* Baseline: [no_between_composition.diff](./4_plan_level_choke_points/4_3_between_composition/no_between_composition.diff)
* (1) Enabled composition: unmodified Hyrise code

### 4.4 Join-Dependent Predicate Duplication

* Baseline: [no_duplication.diff](./4_plan_level_choke_points/4_4_join_dependent_predicate_duplication/no_duplication.diff)
* (1) Enabled duplication: unmodified Hyrise code

### 4.5 Physical Locality

* Baseline: [unsorted.diff](./4_plan_level_choke_points/4_5_physical_locality/unsorted.diff)
* (1) Shuffled lineitem and orders tables: [shuffled.diff](./4_plan_level_choke_points/4_5_physical_locality/shuffled.diff)
* (2) Physical locality without DBMS optimizations: [sorted_no_pruning.diff](./4_plan_level_choke_points/4_5_physical_locality/sorted_no_pruning.diff)
* (3) Physical locality with DBMS optimizations but without usage of correlations: [sorted_pruning_no_correlation.diff](./4_plan_level_choke_points/4_5_physical_locality/sorted_pruning_no_correlation.diff)

### 4.6 Correlated Columns

* Baseline: same as 4.5 (3)
* (1) Exploitation of correlation: unmodified Hyrise code

### 4.7 Flattening Subqueries

* Baseline: [no_flattening.diff](./4_plan_level_choke_points/4_7_flattening_subqueries/no_flattening.diff)
* (1) With subquery flattening: unmodified Hyrise code

### 4.8 Semi Join Reduction

* Baseline: [no_reduction.diff](./4_plan_level_choke_points/4_8_semi_join_reductions/no_reduction.diff)
* (1) With indiscriminate reduction: [indiscriminate_reduction.diff](./4_plan_level_choke_points/4_8_semi_join_reductions/indiscriminate_reduction.diff)
* (2) With cardinality-based reduction: unmodified Hyrise code

### 4.9 Subplan Reuse

* Baseline: [no_reuse.diff](./4_plan_level_choke_points/4_9_subplan_reuse/no_reuse.diff)
* (1) With subplan reuse and enabled column pruning: unmodified Hyrise code
* (2) With subplan reuse and disabled column pruning: [no_column_pruning.diff](./4_plan_level_choke_points/4_9_subplan_reuse/no_column_pruning.diff)


## Section 5. Logical Operator Choke Points

### 5.1 Dependent Group-By Keys

* Baseline: [no_dependent_groupby_detection.diff](./5_logical_operator_choke_points/5_1_dependent_groupby_keys/no_dependent_groupby_detection.diff)
* Automatic group-by detection: unmodified Hyrise code
* Manual group-by detection for foreign keys: [manual_groupby_detection.diff](./5_logical_operator_choke_points/5_1_dependent_groupby_keys/manual_groupby_detection.diff)

### 5.2 Large IN Clauses

* Baseline: [no_in_reformulation.diff](./5_logical_operator_choke_points/5_2_large_in_clauses/no_in_reformulation.diff)
* Reformulation of IN clauses: unmodified Hyrise code
