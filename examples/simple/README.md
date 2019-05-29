See [main presentr page](https://gitlab.com/airbornemint/presentr) for an overview.

# A simple `packrat` example

This RStudio project includes a single R script (`example.R`) that uses a single R package (`plyr`). 

It uses `packrat` to record the names of the required package and its dependencies, but it does not include sources or binaries for any of them.

If you examine files included with this example (before you open the Rproj in RStudio), you will see that the data added by `packrat` amounts to ~27KB.

# How a participant might use this

1. Download the materials: If you are looking at this in GitLab, you can download all examples by clicking the download button on the right above the file list, and then clicking "zip" under "Download source code". TODO: separate downloads for individual examples.
2. Open the Rproj: After the download is complete, you will have a folder (`examples/simple`) with an RStudio project file (`simple.Rproj`) inside it. Open this Rproj in RStudio.
3. Install the required packages: Type `packrat::restore()` into the RStudio console, followed by the enter key.
4. Run the example script: Click on `example.R` in the RStudio files panel, then choose Code → Run Region → Run All.