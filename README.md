
[![Travis build
status](https://travis-ci.org/levisc8/RPadrino.svg?branch=master)](https://travis-ci.org/levisc8/RPadrino)
[![Codecov test
coverage](https://codecov.io/gh/levisc8/RPadrino/branch/master/graph/badge.svg)](https://codecov.io/gh/levisc8/RPadrino?branch=master)

## RPadrino

`RPadrino` is a package for interacting with the Padrino Integral
Projection Model database. This database really only exists as an
internal dataset in this package right now. The longer term plan is to
migrate it to a set of static, version-controlled tables that are hosted
on Zenodo (or similar). For now, you can access it by installing the
package, and running `data(pdb)`.

Data have had some quality checking, but some models will still
definitely break with very strange looking error messages. A genuine,
quality checked release will be tagged when it is actually ready to go.
I really don’t suggest using this yet, except to get a feel for how the
database looks and perhaps give some feedback on things you do or do not
like.

## Scope

The goal of this package is basic data management, exploration, and
porting Padrino’s internal syntax to
[`ipmr`’s](https://levisc8.github.io/ipmr/) syntax. Ideally, one could
combine models from here with their own models for synthesis work, using
the `proto_ipm` as a common data structure to power the analysis.

### Installation

You can install RPadrino from github with:

``` r

if(!requireNamespace("remotes", quietly = TRUE)) {
  install.packages("remotes")
}

remotes::install_github("levisc8/RPadrino")
```

### Usage

A small-ish copy of the Padrino Database is included as a package data
set. You can access it with the following code:

``` r

data("pdb")
```

The next step is to identify the models we are interested in building.
At the moment, there is no functionality to do that - you’ll just have
to explore the *Metadata* table on your own. In the not so distant
future, there will be a set of functions that actually help query
specific columns of interest so the process of exploring the database is
less painful.

Once, we’ve identified the model(s) we want, we can build a list of
[`proto_ipm`’s](https://levisc8.github.io/ipmr/articles/proto-ipms.html).
This is an intermediate step between the database representation and a
set of kernels. Once we have this, we can build an actual model. Below,
we extract a specific study, Levin et al. 2019, and construct an IPM
from the database object.

``` r

# Generate an index to subset the database with

subset_index <- pdb_ex$Metadata$ipm_id[pdb_ex$Metadata$corresponding_author == "Levin"]

# This lapply statement extracts all rows from the database that correspond to our desired study/studies

use_db       <- lapply(pdb_ex,
                       function(x, subset_ind) x[x$ipm_id %in% subset_ind, ],
                       subset_ind = subset_index)

# We can construct a single IPM at a time, or make a list of many IPMs

proto_list   <- pdb_make_proto_ipm(use_db, 
                                   ipm_id = subset_index[1],
                                   det_stoch = "det")
```

Once the list of `proto_ipm`’s is generated, you can either append your
own IPMs to it, or you can go ahead to the next chunk. Note that
`pdb_make_ipm` is not yet implemented, and so the second option is the
only one you can use to work with models. Unfortunately, this means
you’re restricted to `ipmr::make_ipm`’s default arguments for each
type of model in Padrino. This will change via the `addl_args` argument
in `pdb_make_ipm`.

``` r

# pdb_make_ipm not yet implemented

ipm_list     <- pdb_make_ipm(proto_list)

# If you're dying to try it out, you can call the following

ipm_list     <- lapply(proto_list, ipmr::make_ipm)
```

### Finding help

The package is documented [here](https://levisc8.github.io/RPadrino/)
and the database is documented
[here](https://levisc8.github.io/Padrino.github.io/) (though the latter
is very out of date).

### Contributing

Please note that the RPadrino project is released with a [Contributor
Code of
Conduct](https://contributor-covenant.org/version/2/0/CODE_OF_CONDUCT.html).
By contributing to this project, you agree to abide by its terms.
