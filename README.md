# ClusterEval - Evaluating clusters and clustering

A toolbox for exploring and evaluating clustering. Not much here yet.

## Installing from GitHub

This package is not on CRAN, so you will have to install from GitHub.

### Install using `BiocManager::install()`

My favorite way to do github installs requires the CRAN packages "BiocManager" and "remotes" to both be available. Then you can just do:

```
BiocManager::install("BioInfoToys\ClusterEval")
```

Otherwise you might first need to do one or both of:

```
install.packages(BiocManager)
install.packages(Remotes)
```

### Install from cloned repository with `devtools::install()` 

You can copy the repo with `git clone <url>` and then install from that directory with `devtools::install('<path>')`. If you are just pulling the repo for installing, you only need a shallow clone and can delete it after you have verified the install.

From the command line:

```
git clone -–depth 1 https://github.com/BioInfoToys/ClusterEval.git
```

Then from R:

```
devtools::install('path/to/ClusterEval')
library(ClusterEval)
SessionInfo()
```

If you don't have devtools installed, you will first have to do

```
install.packages(devtools)
```

Then from the command line (if ClusterEval installed correctly)

```
# -f is needed as the .git sub-directory is protected against accidental deletion
rm -rf path/to/ClusterEval
```

