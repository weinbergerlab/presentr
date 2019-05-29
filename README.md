# `This is a draft`

# Using R with an audience

Are you planning to use R in front of an audience, and expecting the audience to follow along with your code?

Do you want to reduce the risk of your audience being frustrated by simple technical problems, such as R package installation?
 
The goal of this git repository is to document some best practices for successfully using R in front of an audience, and provide working examples that you can use to get started.
 
If you want to get down to business right away, skip to [summary of recommendations](#summary-of-recommendations), followed by [examples](#examples).
 
# Approach

My overarching goals are:

* Simplify the process of setting up a computer in order to follow along with your presentation, by making the setup process simpler and faster
* Optionally, allow the setup process to be performed without a reliable network connection
* Allow participants to experiment with code during the presentation, while also allowing them to easily get back on track with the presentation
* Allow the presenter to fast-forward over time-consuming computation during the presentation

The specific causes of friction that I aim to address in this revision are:

* A participant should be able to easily locate R files for a given talk.
* A participant should never be asked to set their working directory.
* The presenter should never wonder if a participant's working directory is set correctly.
* If a participant needs to install some R packages, they shouldn't have to know what packages they need to install.
* A participant should be able to quickly and easily check whether the requisite R packages are installed on their computer.
* A participant should be able to install the requisite packages ahead of the presentation.
* Optionally, a participant may be able to install the requisite packages without having access to a reliable internet connection. 

The following are also desirable, and will be tackled in a future revision:

* The presenter should be able to designate a section of their presentation as time-consuming, and provide pre-computed data that allows participants to skip over doing the time-consuming computation during the presentation
* A participant should be able to quickly and easily discard any changes they made to presentation materials, thereby returning to the pristine (presenter-provided) state.
* Adherence to several of the recommendations below could be checked automatically
* The process of bundling R package binaries in conference materials could be simplified

The following potential sources of friction I consider to be solved well enough, and they are not addressed here:

* A participant should be able to easily install R
* A participant should be able to easily install RStudio

The main tools I will use are [RStudio](https://www.rstudio.com) and the `packrat` R package. Hereinafter I assume that you have basic familiarity with RStudio, and I will explain some specific aspects of RStudio as they pertain to presenting R code; the `packrat` package will be explained in its entirety.

## Recommendation 1: use an RStudio project

An RStudio project ("Rproj") is a document which provides the following features useful for streamlining workshops:

* Opening an Rproj in RStudio automatically sets working directory to the folder containing the Rproj. 
* By default, an Rproj saves the R environment (consisting of all data and code, whether loaded from a package, defined by a script, or manually entered by the user) when it is closed, and restores it when reopened. 
* When using an Rproj, seamless integration with `packrat` is possible, as described below.
 
Therefore, I recommend use of Rproj for every presentation that uses R for live code demos. In fact, I will go a step further: in the same way that — when giving a conventional slide presentation — you think of your slides as *the* document for your presentation, you should — when giving a talk that revolves around showing R code — think of an Rproj as *the* document for your talk. Just as figures only become presentable when they are bundled into a series of slides, so do R scripts (and other R documents, such as R markdown files) only become presentable when they are bundled into an Rproj.

Furthermore, you and the conference organizers should collaborate to ensure that your participants can easily obtain your presentation materials and identify the Rproj file for your presentation.

### Specific recommendations

* Recommendations for the presenter
	* Create a single folder for your presentation
	* Put inside this folder (and its subfolders) all R documents and data files you use in your presentation
	* Every file that needs to be directly opened by a participant should be at the top level of your presentation folder — don't make your users fish for files in subfolders.
	* Conversely, every file that doesn't need to be directly opened by a participant should be in a subfolder — don't clutter the file list with files nobody needs to click on.
	* Create a single Rproj at the top level of your presentation folder. Participants should be able to easily identify a single starting point for your talk.
	* Give the folder and the Rproj names that are clearly related to your talk's title, as listed in the conference proceedings
	* Before you submit your presentation materials to the conference organizer, verify that your code works if the top-level folder is renamed
* Recommendations for the conference organizer
	* Collect the presentation materials from all presenters, and give their top-level folders consistent names. 
	* Ideally, give the top-level folders names which sort in the same order as the talks appear in the schedule. For example, if you have talks titled "C", "A", and "B" — presented in that order — you might name their folders "1 - C", "2 - A", and "3 - B". 
	* Don't change files inside the presentation folders
	* If possible, distribute conference materials ahead of time; the bigger the files, the more important this is as a safeguard against participants being delayed by large or slow downloads. 
	* If you are giving the participants an option to download conference materials during the conference, make it possible to download the materials for each talk separately

## Recommendation 2: use `packrat`

The `packrat` R package, in a nutshell, provides the following functionality:

* With an Rproj open in Rstudio: record which R packages are needed by R scripts in this project, and attach this information to the Rproj
* When subsequently opening the same Rproj (possibly on a different computer): load the previously saved list of packages, check if those packages are installed, and install them if they aren't
 
### Approaches to `packrat`

#### Background
 
Installing an R package requires one of two things: package sources or package binaries. A package can be installed from sources on any computer running any compatible version of R, but the process of installing from source can be lengthy and may require additional software to be installed (such as developer tools). Package binaries, on the other hand, are prepared for a specific operating system running a specific version of R (for example, R 3.6.x on macOS 10.12 or later); as a result, package binaries can only be installed on a computer running that version of R on that operating system, but the installation process is faster and does not require additional software to be present.

As a result, an R package can usually be downloaded either as sources, or as binaries prepared for some specific versions of R on some specific operating systems (whichever ones the package maintainer deemed useful). In case of CRAN — the most commonly used package repository — binary packages are available for the current version of R and the previous version of R, running on a recent version of Windows or macOS. Installing on older versions of R or any other operating system requires installing from source.

Furthermore, some packages are platform-independent (meaning that installing from sources does not require developer tools), whereas some packages are platform-dependent (meaning they can be installed from source without developer tools). 

In summary:

* A platform-independent package can be installed from sources on any computer (without needing developer tools).
* A platform-independent package can be installed from binaries on any computer compatible with those binaries (without needing developer tools), and the process is more efficient than installing the same package from sources.
* A platform-dependent package can be installed from sources on any compatible computer, and the installation requires developer tools.
* A platform-dependent package can be installed from binaries on any computer compatible with those binaries (without needing developer tools), and the process is significantly more efficient than installing the same package from sources.

#### Relationship between `packrat`	 and sources/binaries

An Rproj augmented with `packrat` includes the names of all its requisite packages. In addition to the names, sources or binaries (or both) can be included for every requisite package. This leads to the following four options for each package:

* Package name only: In order to install the package, the participant will need internet access (to download the package). In most cases, the participant will not need developer tools to be installed (see below). This option takes up the least amount space within presentation materials, but requires internet access and potentially lengthy downloads; in some cases, it may alo require developer tools. 
* Package name and sources: Package sources are included with the Rproj. Those sources are used as a fallback; even when installing from package sources, `packrat` will always attempt to download the package binaries first, because installing from binaries is more efficient and never requires developer tools. 
* Package name and binaries: Package binaries are included with the Rproj. This means that the package can be installed without either an internet connection or developer tools. However, because package binaries are specific to the version of R and the operating system, this means that the presentation author and the conference organizer have to jointly decide which versions of R and the operating system they will support; participants using other versions will have to download and install the packages as if they only had the package name. 
* Package name with sources and binaries: This is a union of the previous two options. Participants running on a supported version of R and the operating system will be able to install without a download or developer tools, and others may need developer tools, but won't need a download.
 
#### What you should use for your presentation

The best choice for each package depends both on the package itself and the venue in which you will be presenting.

* Package name only: When you know your participants will have solid internet access during your talk, this is the best default option for every package that is available on CRAN or in another internet package repository supported by the `devtools` package, such as GitHub and Bioconductor. This option leads to a very small increase in size of presentation materials compared to not using `packrat` at all.
* Package sources: This is a good choice for every platform-independent package that isn't available on the internet at all (for example, a private as-yet-unpublished package, or an as-yet-unpublished modified version of a package). This is also a good option for all platform-independent packages if you anticipate unreliable internet access. 
* Package binaries: This is a good choice for every platform-dependent package that isn't available on the internet at all, and also for all platform-dependent packages if you anticipate unreliable internet access. If you are including binaries, consider also including sources for the same package, unless you are sure that all participants will be using one of the platforms supported by the binaries you are including.

#### When does a participant need developer tools?

A participant needs developer tools to install a platform-dependent package from source. On the one hand, many commonly used packages either are platform-dependent or require another package that is platform-dependent, and therefore installing them from source requires developer tools. On the other hand, CRAN contains binary packages for most commonly used combinations of R and the operating system. As a result, developer tools are required when:

* A participant is installing a platform-dependent package, or a package that requires a platform-dependent package and
	* The participant is using an uncommon version of R or 
	* The participant is using an uncommon operating system or
	* The participant does not have internet access (and therefore does not have access to CRAN) or
	* The package in question is not on CRAN (for example, a GitHub package) and the package maintainer does not offer downloadable package binaries
	 
Platform-dependent GitHub packages are probably the most common reason why a participant with good internet access might need developer tools to get ready for a presentation.

#### Working with GitHub packages

When you install a GitHub package, you can do so in one of three ways: from a branch, from a tag, or from a specific commit. While installing from a branch is by far the most common (and also is the default), it is not reproducible; if you later install from the same branch, you may get a different version of the package. Installing from a tag is usually reproducible (the package maintainer can subvert this, but they are very unlikely do it without understanding the consequences); installing from a specific commit is always reproducible. 

TODO: Does `packrat` take steps to avoid this problem?

### Specific recommendations

* Recommendations for the presenter
	* Install `packrat` and enable it in your Rproj options
	* Make sure that all R documents you will be using in your presentation have the appropriate `library()` or `import::from()` statements. 
	* Run `packrat::snapshot()` to capture the packages used by your Rproj
	* Identify the packages for which you will include the sources in your presentation materials:
		* If participants will have internet access, include sources for all (platform-independent and platform-dependent) packages not available in a major online repository (such as CRAN, GitHub, or Bioconductor)
		* If participants will have no internet access, include sources for all packages
	* Identify the packages for which you will include the binaries
		* If participants will have internet access, include binaries for all platform-dependent packages not available in a major online repository
		* If participants will have no internet access, include binaries for all packages
	* If you are including any package binaries, collaborate with conference organizer to identify R and operating system versions which you intend to support
	* To collect package binaries, open your Rproj on each supported platform and run `packrat::restore()` to install the binaries for that platform
	* After collecting the binaries on all platforms you intend to support, open the `packrat/lib` folder and remove `packrat/lib/OS_VERSION/R_VERSION/PACKAGE` folders for every package whose binaries you are *not* including in your presentation materials.
	* Open the `packrat/src` folder and remove `packrat/src/PACKAGE` folders for every package whose sources you are *not* including in your presentation materials.
	* Remove `packrat/lib-R` and `packrat/lib-ext` folders.
	* When submitting your presentation materials to the conference organizer, make sure to include the `.Rprofile`, `packrat/packrat.lock`, `packrat/packrat.opts`, and `packrat/init.R` files — as well as the source packages in `packrat/src` and the binary packages in `packrat/lib`. 
* Recommendations for the conference organizer
	* Collaborate with the presenter to decide which platforms to support, based on the audience and the storage limitations. The size of the conference materials will scale linearly with the number of supported platforms.
	* For typical R users, the platforms most worth considering are latest major R version on recent Windows and latest R on recent macOS, followed by the previous major R version on recent Windows and macOS.
	* For advanced R users, you might also consider supporting the upcoming major R version in recent Windows and macOS — but those users typically also have the tools to install from sources.
	* TODO: does this work with incompatible package versions? If you are organizing a conference with several R presentations, turn on the “Use global cache for installed packages” option for all individual Rprojs.
	
#### Common problems

* `Error in getrootdir` when using a GitHub package when running `packrat::snapshot()`: run `options("download.file.method" = "libcurl")`, then rerun `packrat::snapshot()`.
* `Error: Unable to retrieve package records` when running `packrat::snapshot()`: run `install.packages()` to force the problem packages to be installed, then rerun `packrat::snapshot()`
 
# Summary of recommendations 

1. Place the materials for a single presentation in a single folder with a helpful name, and place a single Rproj at the top level of that folder.
1. Move all ancillary R scripts and data files into subfolders, leaving only the main scripts and data files at the top
1. Install `packrat` and turn on “Use packrat with this project” (under Tools → Project Options → Packrat) in your Rproj
1. Make sure all R documents use `library()` or `import::from()` statements to indicate which packages they require
1. In the Rproj, run `packrat::snapshot()` to record the required packages
1. When distributing your materials to participants, include the following files:
	* The Rproj: always
	* `.Rprofile`: always
	* `packrat/packrat.lock`: always
	* `packrat/packrat.opts`: always
	* `packrat/init.R`: always
	* Folders in `packrat/src`: only for packages for which you are including sources — typically, packages not readily available on the internet, or all packages if you don't expect participants to have reliable internet access. 
	* Folders in `packrat/lib/OS_VERSION/R_VERSION`: only for packages for which you are including binaries — typically, platform-dependent packages not readily available on the internet, or all platform-dependent packages if you don't expect participants to have reliable internet access. 
	* `packrat/lib-R` and its contents: never
	* `packrat/lib-ext` and its contents: never
	* And, obviously, any other R documents and data files you need for your presentation
1. Instruct your participants to:
	* Ideally, in advance of the presentation:
		* Obtain materials for the presentation
		* Open the Rproj
		* Type `packrat::restore()` in the RStudio console
	* During the presentation:
		* Open the Rproj
		* Use the files panel in RStudio to open the R document with which you will begin your presentation
 
# Examples

* [examples/simple](examples/simple): Presentation materials consisting of a single R script with minimal package dependencies, ready for use by a participant with good internet access. 