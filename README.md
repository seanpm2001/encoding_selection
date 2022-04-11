# Automated Encoding Selection
### Reproducibility Repository

This repository contains source code and artifacts for the paper [**Robust and Budget-Constrained Encoding Configurations for In-Memory Database Systems**](https://www.vldb.org/pvldb/vol15/p780-boissier.pdf) (VLDB 2022).

In case you have any questions, please contact [Martin Boissier](https://hpi.de/plattner/people/phd-students/martin-boissier.html).


### Citation

This is a preliminary "DBLP-style" BibTeX entry.
<details><summary>BibTeX entry (click to expand)</summary>

```bibtex
@article{DBLPlike:journals/pvldb/Boissier22,
  author    = {Martin Boissier},
  title     = {Robust and Budget-Constrained Encoding Configurations for In-Memory Database Systems},
  journal   = {Proc. {VLDB} Endow.},
  volume    = {15},
  number    = {4},
  pages     = {780--793},
  year      = {2022},
  url       = {https://www.vldb.org/pvldb/vol15/p780-boissier.pdf}
}
```
</details>


## Setup - Overview

This project consists of three main components: **(i)** Hyrise, **(ii)** the encoding plugin(s) for Hyrise, and **(iii)** Python scripts that train the models and run the actual selection process.
The repository contains the `encoding_plugin` directory, which stores a (actually a set of) plugin(s). These plugins for Hyrise manage the communication with the Hyrise server. Hyrise itself is a third party module within the plugin.
The Python code is stored in the `python` directory.


## Setup - Execution

[![Main](https://github.com/hyrise/encoding_selection/actions/workflows/haupt.yml/badge.svg)](https://github.com/hyrise/encoding_selection/actions/workflows/haupt.yml)

The whole encoding selection pipeline runs within GitHub actions to ease reproducing the paper's results or run everything on your own machines (e.g., using [act](https://github.com/nektos/act)).
The `hyrise_full_pipeline` job in the main workflow file [haupt.yml](https://github.com/hyrise/encoding_selection/blob/main/.github/workflows/haupt.yml#L20) lists all steps required from gathering calibration data, learning models, selecting configurations, to evaluating them.
Due to GitHub restrictions, the pipeline creates only a tiny data set (scale factor of 0.5).

For each run, we compare Hyrise against MonetDB and DuckDB[^1].
The results are plotted and stored in the artifacts of each run[^2].
Download `database_comparison(.zip)` of the last succesful run for a plot of the TPC-H benchmark runs.


The code (both the plugins as well as the Python scripts) are extracted from a larger project.
Please excuse the often convoluted and bloated code.

Flowchart of the GitHub runner workflow[^3]:
```mermaid
flowchart LR;
    Start --> setuph["Setup Hyrise Pipeline<br>(git, apt, pip, ...)"];
    Start --> setupdb["Setup Database Comparison Pipeline<br>(git, apt, pip, ...)"];
    setuph --> cal["Calibration<br>(TPC-H; collect training data)"];
    cal --> train["Training<br>Runtime and size models"];
    train --> selection["Encoding<br>Selection"];
    selection --> runhyrise["Run TPC-H<br>(ST &amp; MT)"];
    runhyrise --> plot["Plotting<br>(R)"];
    setupdb --> datamonet["Data Generation<br>MonetDB"];
    datamonet --> runmonet["Run TPC-H<br>MonetDB"];
    runmonet --> dataduckdb["Data Generation<br>DuckDB (TPC-H's dbgen)"];
    dataduckdb --> runduckdb["Run TPC-H<br>DuckDB"];
    runduckdb --> plot;
```

[^1]: Please view the results with a huge grain of salt, especially the DuckDB results.
We are huge fans of DuckDB and thus wanted to include it.
But the current benchmark script is probably an unfair comparison, as DuckDB's aim is more on single-user performance (i.e., data scientists/smartists).
Hyrise's focus on concurrent OLTP/OLAP users.
In a single-user-multiple-cores scenario, DuckDB performs significantly better.
Further, we cannot rule out that Python's GIL causes unexpected performance degradations.
We have talked to the DuckDB maintainers and decided to exclude DuckDB measurements from the paper for this reason.
In case you can help us to make a fair comparison, feel free to post a pull request.

[^2]: The plots are meant to show the reproducibility of the results, not to establish a fair comparison.
To conduct a "fairer" comparison (cf. footnote on DuckDB), the pipeline needs to be run on a dedicated machine.
We have seen workflow runtimes on GitHub varying from 3h to over 6h (which is than canceled by GitHub) for the same setup.

[^3]: Yes, I just wanted to integrate the flowchart for the sake of integrating a flowchart in Markdown. It isn't that interesting.
