# Sentinel1-Sentinel2-ARD

Creates analysis read data (ARD) from sentinel data. Creates multi-modal and/or
multi-temporal Sentinel-1 and Sentinel-2 ARD.

If you find this repository useful, please cite with:

```bibtex
@article{Upadhyay2022,
  # doi = {ACCEPTED/IN PRESS},
  url = {ACCEPTED/IN PRESS},
  year = {2022},
  month = feb,
  publisher = {{MDPI} {AG}},
  volume = {14},
  number = {4},
  pages = {21},
  author = {Priti Upadhyay and Mikolaj Czerkawski and Christopher Davison and Javier Cardona and Malcolm Macdonald and Ivan Andonovic and Craig Michie and Robert Atkinson and Nikela Papadopoulou and Konstantinos Nikas and Christos Tachtatzis},
  title         = {A flexible multi-temporal and multi-modal framework for Sentinel-1 and Sentinel-2 Analysis Ready Data.},
  journal = {Remote Sensing}
}
```


## Running the pipeline

An example configuration is provided which will only use a single pair of
`(S1, S2)` patches. This covers a Scottish city, Glasgow, as an example.

The code can either be ran using a `python` environment, or via a provided
`docker` image.

The script needs some flags passed alongside the configration file, in order to
specificy parameters for each specific run

-   `--config <config_filename>`
    -   a json file containing the region of interest and other
        region/dataset-specific parameters
-   `--credentials <sentinelsat_credentials_json>`
    -   json containing a username and password for access to sentinelsat.
-   `--njobs <integer>`
    -   how many parallel jobs to run
-   `--rebuild true|false`
    -   run all steps even if outputs already exist
-   `--outdir <path>`
    -   Where to save all outputs

The `download_flow` has different parameters

-   `--config <config_filename>`
    -   a json file containing the region of interest and other
        region/dataset-specific parameters
-   `--credentials <sentinelsat_credentials_json>`
    -   json containing a username and password for access to sentinelsat.
-   `--credentials_earthdata <earthdata_credentials_json>`
    -   json containing a username and password for access to noaa earthdata.
    -   for downloading S2 products when product is archived in sentinelsat
-   `--credentials_google <earthdata_credentials_json>`
    -   json containing a username and password for access to google cloud.
    -   for downloading S1 products when product is archived in sentinelsat

### Running via python

If you already have the requirements installed locally, the script can be ran as
follows:

`python -m src.snap_flow --no-pylint run --config glasgow-s256x256-o0x0-20200801.json --credentials <sentinelsat_credentials_json> --njobs <how_many_jobs_in_parallel> --rebuild true|false --outdir <directory_to_save_output>`

### Running via python inside docker

A `Dockerfile` is supplied in the root of the repository. It's based on
`ubuntu:20.04` and `python3.7`. It installs `GDAL` and `SNAP`, among other
python requirements.

From the root of the repository, the docker image can be built using:

`docker build --network=host -f Dockerfile -t senprep:latest --force-rm=True`

...which will create an image tagged `senprep:latest`. (You can also install via
`make senprep`, which simply runs the above command).

The provided script in `helpers/docker-snap` allows you to run the command in
various ways (download only, download and process, process only). If ran without
any arguments, it will output help documentation.

To run the pipeline directly, you must provide two mounts to the docker image:

1.  where the data will be exported
    -   ex: to store in `/temp/satellite`, use
        `-v /temp/satellite:/var/satellite-data/`
2.  the current working directory (for loading configurations etc).
    -   `-v $PWD/:/code/`

``` bash
sudo docker run --rm -it -v /temp/satellite:/var/satellite-data/ -v $PWD/:/code/ senprep:latest download_and_snap --credentials credentials.json --config configuration.json
```
