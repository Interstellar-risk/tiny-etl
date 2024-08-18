<div align="center">

<picture>
  <source media="(prefers-color-scheme: light)" srcset="/docs/logo_tiny_light.svg">
  <img alt="tiny corp logo" src="/docs/logo_tiny_dark.svg" width="50%" height="50%">
</picture>

tiny-etl: For converting raw climate data into compatible IPFS storage formats and one-dimensional Zarr arrays. Maintained by [Interstellar](https://interstellarclimate.com).

<h3>

[Homepage](https://github.com/interstellar-risk/tiny-etl) | [Documentation](https://docs.tiny-etl.org/) | [Discord](https://discord.gg)

</h3>

[![GitHub Repo stars](https://img.shields.io/github/stars/tinygrad/tinygrad)](https://github.com/tinygrad/tinygrad/stargazers)
[![Unit Tests](https://github.com/tinygrad/tinygrad/actions/workflows/test.yml/badge.svg)](https://github.com/tinygrad/tinygrad/actions/workflows/test.yml)
[![Discord](https://img.shields.io/discord/1068976834382925865)](https://discord.gg/ZjZadyC7PK)

</div>

---

`tiny etl` is a set of functions for wrangling public climate data, allowing converting into a storage format such as [IPFS](https://ipfs.tech/). 

Also, `tiny-etl` functions allow users to convert their climate data to a common [Zarr](https://zarr.readthedocs.io/en/stable/)[^1] format


`tiny etl` is still in alpha version 1.11.0 as we are early in development.


Data Sources
------------

[tiny-etl] currently supports:

 - [NASA GDDP](https://www.nccs.nasa.gov/services/data-collections/land-based-products/nex-gddp-cmip6)
 - [NASA CHIRPS](https://nasaharvest.org/project/climate-hazards-group-infrared-precipitation-stations-chirps)
 - [ERA5](https://cds.climate.copernicus.eu/cdsapp#!/dataset/reanalysis-era5-single-levels?tab=overview)
 - [Selected CMIP6 Model Data](https://pcmdi.llnl.gov/CMIP6/)
 - [CORDEX](https://cordex.org/data-access/cordex-cmip5-data/how-to-access-cordex-cmip5-data/)

Requirements
------------

* A Python 3.11.0+ virtual environment is preferred for executing tiny ETLs set up with the [required libraries](setup.cfg).
Note that other Python versions may work, but this is the version developed and tested against. It is strongly recommended to use a virtual environment since there are a lot of external modules to install, but it is not strictly necessary.

* [IPFS 0.10+](https://github.com/ipfs/go-ipfs/) node **with a running daemon** (see [further instructions](docs/IPFS_Node_Management.md) for installation on a Linux machine)


Quickstart
----------

#### Setup

Before instlaling the library from the github repository, please set up [a Python virtual environment](docs/Python_virtual_environments.md) to support external dependencies.

    pip install git+https://github.com/Interstellar-risk/tiny-etl



Next install IPFS, as per [the docs](docs/IPFS_Node_Management.md).

Once the library and an IPFS node are installed, instantiate an IPFS daemon. Open a terminal and run

    ipfs daemon &

Keep the terminal open as you move through the rest of the quickstart


#### Running the ETL

With the IPFS daemon up and running manager scripts using the `tiny-etl` library can be invoked within a separate script or notebook. Note you will have to first create a functioning manager script, as described in the [ETL developers manual](docs/etl_developers_manual.md). We use the NASA CHIRPS example manager included in this repository below.

The CHIRPS U.S. precipitation dataset is of medium size and can be run in a few hours on a well powered machine. To run a data retrieval, transformation, and storage cycle for the `CHIRPSFinal25` class in [chirps.py](examples/managers/chirps.py), import and instantiate it within a notebook or script

    import datetime
    from examples.managers.chirps import CHIRPSFinal25
    etl = CHIRPSFinal25(store='ipld')

Now run the `extract` to download all the files for this dataset. We're going to use the **date_range** parameter to only download files for 2021 and 2022 so this example goes quickly. Note that you can skip this step if you already have your files!

    etl.extract(date_range=[datetime.datetime(2021, 1, 1), datetime.datetime(2022, 12, 31)])

After running `extract` a folder containing the original data from CHIRPS is created in `datasets/chirps/final/25` in the parent directory of the directory holding the CHIRPS manager

    $ ls datasets/chirps/final/25
    chirps-v2.0.2021.days_p25.nc
    chirps-v2.0.2022.days_p25.nc

Now run the `transform` method to read all the files and transform them into a single Zarr, and `parse` to place that Zarr on the desired store.

    etl.transform()
    etl.parse()

#### Retrieving your dataset

How you find and retrieve the Zarr you just created will depend on the store used. Because we specified the IPLD store above, the output text from our ETL will specify the IPFS hash associated with the final Zarr.

    INFO     <chirps_final_25> IPFS hash is q5ve6oifwb7iruxsj4e2hflfcbyieavzueoc5kszjx2rhuzuwdihfwqfbp

This hash can be used to open the dataset in an interactive session using `xarray` and `ipldstore` in tandem. These were installed during the virtual environment setup

```python
    import xarray, ipldstore
    mapper = ipldstore.get_ipfs_mapper()
    mapper.set_root("bafyreibx5i7ovu2ed2qyh2ajezfsxvufjbihwscur5hkue4zol6fcwefjq") # replace with your hash
    xarray.open_zarr(mapper, consolidated=False)
    <xarray.Dataset>
    Dimensions:    (latitude: 120, longitude: 300, time: 5736)
    Coordinates:
      * latitude   (latitude) float32 20.12 20.38 20.62 20.88 ... 49.38 49.62 49.88
      * longitude  (longitude) float32 -129.9 -129.6 -129.4 ... -55.62 -55.38 -55.12
      * time       (time) datetime64[ns] 2007-01-01 2007-01-02 ... 2022-09-14
    Data variables:
      precip     (time, latitude, longitude) float32 dask.array<chunksize=(1769, 24, 24), meta=np.ndarray>
    ...
```

Note that the IPFS hash for a dataset can be found at any time by consulting the `assets->zmetadata->href->'/'` field in its STAC metadata. This includes previous iterations of your dataset -- just roll back to the associated STAC metadata.



## Documentation

Documentation supporting the `Quick Start` above can be found on the [docs website](https://docs.tiny-etl.org/) built from the [docs/](/docs) directory.

