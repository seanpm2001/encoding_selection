# Automated Encoding Selection
### Reproducibility Repository

This repository contains source code and artifacts for the paper **Robust and Budget-Constrained Encoding Configurations for In-Memory Database Systems** (VLDB 2022).

In case you have any questions, please contact [Martin Boissier](https://hpi.de/plattner/people/phd-students/martin-boissier.html).


### Citation

This is a preliminary "DBLP-style" BibTeX entry. The paper has been accepted but it might move to another issue in case the camera ready version is not accepted.
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
  url       = {http://www.vldb.org/pvldb/vol15/p499-boissier.pdf}
}
```
</details>


## Setup - Overview

This project consists of three main components: **(i)** Hyrise, **(ii)** the encoding plugin(s) for Hyrise, and **(iii)** Python scripts that train the models and run the actual selection process.
The repository contains the `encoding_plugin` directory, which stores a (actually a set of) plugin(s). These plugins for Hyrise manage the communication with the Hyrise server. Hyrise itself is a third party module within the plugin.
The Python code is stored in the `python` directory.


## Setup - Execution

[![Main](https://github.com/hyrise/encoding_selection/actions/workflows/haupt.yml/badge.svg)](https://github.com/hyrise/encoding_selection/actions/workflows/haupt.yml)

The whole encoding selection pipeline runs within GitHub actions to make it as easy as possible to reproduce the results and run everything on your own machines (e.g., using [act](https://github.com/nektos/act)).
Due to GitHub restrictions, the pipeline creates only a tiny data set (scale factor of 0.5).

For each run, we compare Hyrise against MonetDB and DuckDB.
The results are plotted and stored in the artifacts of each run.
Download `database_comparison(.zip)` of the last succesful run for a plot of the TPC-H benchmark runs.

Please note that we compare against DuckDB more or less for fun.
We are huge fans of the project, but the current benchmark script is probably an unfair comparison (multiple concurrent clients).
We have talked to the DuckDB maintainers and decided to exclude DuckDB measurements from the paper for this reason.
In case you can help us to make a fair comparison, feel free to post a pull request.

The code (both the plugins as well as the Python scripts) are extracted from a larger project.
Please excuse the often convoluted and bloated code.


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

