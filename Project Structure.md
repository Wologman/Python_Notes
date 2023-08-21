For larger ML projects I'm starting to find this quite important.   Need to think about:

- Environment management
- Environment variables
- Managing configuration settings, between multiple scripts
- Storage of large datasets
- Logging results, tracking experiments & datasets
- Passing common parameters between a series of different scripts
- Automation of running the scripts for clients that don't have Python IDE's set up
- Cross compatibility between Linux & Windows
- Data-loading.   For images, dataloading is likely to be the bottleneck, rather than CPU or GPU speed.  No amount of fancy libraries and parallel processing can help if the read speed of the data storage is the limiting factor.