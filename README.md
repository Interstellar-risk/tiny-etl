[TINY-ETL]


[A lightweight package for converting netCDF files into one-dimensional Zarr format, optimized for efficient processing and analysis.].

Homepage | Documentation | Discord



This may not be the best [ETL (Extract, Transform, Load) tool], but it is a [specialized tool for converting netCDF data into a format that's easier to work with—one-dimensional Zarr arrays.].

With support for both [batch and real-time processing], If Xarray is a Swiss Army knife for labeled data, [tiny-etl] is the streamlined tool for focused Geospatial ETL tasks.

[tiny-etl] is still in early development, but we’re actively improving it with community feedback and contributions. Someday, we will support a broader range of data formats and processing options.



Features

[Feature 1]
Brief description of Feature 1.
[Feature 2]
Brief description of Feature 2.
[Main Highlight or Key Functionality]

[tiny-etl] can run [Key functionality 1] and [Key functionality 2]!

Example Usage
Try a [common use case or example]. See how, despite the style, it is fused into one [kernel/process] with the power of [a key project feature or philosophy].

python
Copy code
DEBUG=3 python3 -c "from [tiny-etl] import [important_class];  
N = 1024; a, b = [tiny-etl].[function](N, N), [tiny-etl].[function](N, N);  
c = (a.reshape(N, 1, N) * b.T.reshape(1, N, N)).sum(axis=2);  
print((c.[numpy_equivalent]()) - (a.[numpy_equivalent] @ b.[numpy_equivalent]()).mean())"


Data Sources

[tiny-etl] currently supports:

 - NASA GDDP
 - NASA CHIRPS
 - ERA5
 - Selected CMIP6 Model Data
 - CORDEX


Accelerators

[tiny-etl] already supports numerous accelerators, including:

 - GPU (OpenCL)
 - CLANG (C Code)
 - CUDA
 - AMD
 - NV
