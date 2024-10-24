# FAQ

This page records useful questions and answers raised by users of `pdemtools`, on the [GitHub issues page](https://github.com/trchudley/pdemtools/issues) and elsewhere. If you have any further questions, do not hesitate to raise them via GitHub issue!

#### Q: How large an area is appropriate to work with? 

To an extent, 'how big of an area can I work with' is basically a question of how much RAM you have available, your internet speed, and your patience - `pdemtools` won't try and stop you from downloading a 2 m mosaic of the whole of Antarctica! 

However, the tool - as it currently stands - is designed around the challenge of downloading time-series stacks of specific study sites for change analysis (e.g. batch download and coregistration to the same datum). These sites are generally _smaller_ than the size of strip data (generally, ArcticDEM and REMA strips are of the order of ~10 km wide and ~100 km long). This design philosophy won't necessarily make things more convenient for other tasks. For example, if you wanted to download every REMA strip over the Larsen C ice shelf, (i) the `aoi_frac` variable in `search()` probably won't be very convenient, as all individual strips will be much smaller than your AOI; (ii) once they're downloaded, many strips will have no stable ground to coregister against (although ICESat-2 coregistration is planned as a forthcoming feature); (iii) given the very large AOI, the single contiguous images output by workflows such as the `batch_download_and_coregister.py` script  will be (for mosaics) inordinately large and (for strips) largely `NaN` data. As such, the workflows presented in the `pdemtools` readthedocs are best tuned to the needs of users who are looking at contexts similar to those in the examples: study sites on the order of 10s kms in size. These notebooks, in our experience, seem to run succesfully on a range of laptops/desktops outside an HPC environment.

`pdemtools` will still be useful in some ways for these larger-scale tasks. For instance, the dataframe returned by the `search` tool can providing you a list of download urls for strip tarballs (accessed via the dataframe's `'fileurl'` column) that can then be retrieved via `wget` or similar. Or the `mosaic()` function can return mosaics on a custom tile grid suited to your own requirements if the standard PGC tiles aren't useful for your needs.

We would encourage users who are interested in larger-scale analysis to get in touch with their needs if they find `pdemtools` doesn't quite suit. We have the goal of making `pdemtools` a one-stop-shop for ArcticDEM/REMA DEM analysis, and other perspectives and requirements are an essential part of this.

#### Q: I have downloaded a strip scene, but the strip appears to have no data within the area. Why?

Strips can appear empty (i.e. `NaN` values) when photogrammetry fails (due to cloud, water, etc) or it is masked by the PGC bitmask. Data previewed by the `pdt.load.preview()` function is masked according to this bitmask, and data loaded by the `pdt.load.from_search()` function is also masked by default (this can be disabled by using the `bitmask = False` option).

As a result, it is entirely possible that the `search()` function can return a valid datastrip that covers a sufficient proportion of the AOI to meet the `min_aoi_frac` requirements, but it will appear empty (i.e. all-`NaN`) when viewed. The `preview()` function will help you identify these 'empty' scenes, but there may still be (poor-quality) data present if it is downloaded using the `load.from_search()` function with `bitmask = False`.
