# Overview

This tutorial will use all of 4 functions within the
[{googledrive}](https://googledrive.tidyverse.org/) package:

drive\_auth() Authorize {googledrive} to access your Google Drive
drive\_ls() List contents of a folder or shared drive drive\_download()
Download a Drive file drive\_upload() Upload into a new Drive file

This code is designed to be run locally (meaning on someone’s computer
manually). If the Google Drive folder is private, you need to run the
authentication code first before you knit the Rmd file.

Below are examples using a publicly visible Google Drive folder.

## Set-up

### 1. Install **googledrive** package (if needed)

    install.packages("googledrive")

### 2. Create a folder on Google Drive (if needed)

I have created one (it is only accessible if you are in NOAA), and it
has the following sample files:

![](images/googledrive.png)

### 3. Copy the url to the Google Drive folder location\*

\*Only people with a NOAA email will be able to access this folder

    url_googledrive <- "https://drive.google.com/drive/folders/11WnXxs56jORbLkD1mFTZxwSaShex3Sse"
    id_googledrive <- "11WnXxs56jORbLkD1mFTZxwSaShex3Sse"

## Authorize google drive to connect to R (drive\_auth)\* (see more notes at the end of the tutorial)

If this is a private Google Drive folder **then you need to authorize
{googledrive} to access Google Drive**

This script below works for your first time connecting and creating a
“token” and later for reconnecting, but you’ll need to run this outside
of the R Markdown (Rmd) file. Essentially, you are giving
**{googledrive}** access to your Google Drive files during the R
session. This will open a window on your browser where you tell it to
authenticate with your Google account. Only people with access to
folders or files can access said folders or files.

    # https://developers.google.com/identity/protocols/oauth2/scopes
    # googledrive::drive_auth(scopes = "https://www.googleapis.com/auth/drive.readonly", email="eli.holmes@noaa.gov")
    googledrive::drive_auth()

    ## ! Using an auto-discovered, cached token.

    ##   To suppress this message, modify your code or options to clearly consent to
    ##   the use of a cached token.

    ##   See gargle's "Non-interactive auth" vignette for more details:

    ##   <https://gargle.r-lib.org/articles/non-interactive-auth.html>

    ## i The googledrive package is using a cached token for 'emily.markowitz@noaa.gov'.

    1

    ## [1] 1

## List the files in a google drive folder

All files.

    dir_files <- googledrive::drive_ls(path = url_googledrive)
    dir_files$name

    ## [1] "our-google-spreadsheet" "our-google-doc"         "data.xlsx"             
    ## [4] "Rock.csv"               "Stone.csv"

All csv files.

    dir_files <- googledrive::drive_ls(path = url_googledrive, type="csv")
    dir_files$name

    ## [1] "Rock.csv"  "Stone.csv"

All Excel files.

    dir_files <- googledrive::drive_ls(path = url_googledrive, type="xlsx")
    dir_files$name

    ## [1] "data.xlsx"

All Google Spreadsheets.

    dir_files <- googledrive::drive_ls(path = url_googledrive, type="spreadsheet")
    dir_files$name

    ## [1] "our-google-spreadsheet"

All Google Documents.

    dir_files <- googledrive::drive_ls(path = url_googledrive, type="document")
    dir_files$name

    ## [1] "our-google-doc"

## Download a file

First get the files in the folder.

    a <- googledrive::drive_ls(path = url_googledrive)

### Download one Excel file from Google Drive and save to a folder called `data`.

    file_name <- "data.xlsx"
    # find the id of that file
    file_id <- a$id[which(a$name==file_name)]
    # download the file and save to data folder
    googledrive::drive_download(file=a$id[1],
                                overwrite = TRUE, 
                                path = file.path("data", file_name))

    ## File downloaded:

    ## * 'our-google-spreadsheet' <id: 1plRbVAZJ_bkGFw9Y54u57eFxL-F5zaXG8elFm3r9BXs>

    ## Saved locally as:

    ## * 'data/data.xlsx'

Read that file in using the **readxl** package.

    tmp <- readxl::read_excel(file.path(here::here(), "data", "data.xlsx"))
    tmp

    ## # A tibble: 6 x 2
    ##    year count
    ##   <dbl> <dbl>
    ## 1  1990    10
    ## 2  1991    20
    ## 3  1992    10
    ## 4  1993    30
    ## 5  1994    40
    ## 6  1995    50

### Download one csv file on Google Drive and save to a folder called `data`.

    file_name <- "Stone.csv"
    file_id <- a$id[which(a$name==file_name)]
    googledrive::drive_download(file=a$id[1],
                                overwrite = TRUE, 
                                path = file.path("data", file_name))

    ## File downloaded:

    ## * 'our-google-spreadsheet' <id: 1plRbVAZJ_bkGFw9Y54u57eFxL-F5zaXG8elFm3r9BXs>

    ## Saved locally as:

    ## * 'data/Stone.csv'

Read that file in.

    tmp <- read.csv(file.path(here::here(), "data", file_name))
    tmp

    ##   year count
    ## 1 1990    10
    ## 2 1991    20
    ## 3 1992    10
    ## 4 1993    30
    ## 5 1994    40
    ## 6 1995    50

### Download a bunch of csv files from the same folder to a folder called `data`.

Download all the csv files in a Google Drive folder.

    a <- googledrive::drive_ls(path = url_googledrive, type = "csv")
    for (i in 1:nrow(a)){
      googledrive::drive_download(a$id[i], 
                                  overwrite = TRUE, 
                                  path = file.path("data", a$name[i]))
    }

    ## File downloaded:

    ## * 'Rock.csv' <id: 1-dz_3QUqmzuXtWXfCcZUowUhZI1x9XAb>

    ## Saved locally as:

    ## * 'data/Rock.csv'

    ## File downloaded:

    ## * 'Stone.csv' <id: 15nK21BniTuMpAhEXXAnsHwopOXOEcnzf>

    ## Saved locally as:

    ## * 'data/Stone.csv'

### Download a bunch of Google Spreadsheets files from the same folder to a folder called `data`.

Download all the Google Spreadsheets in a Google Drive folder.

    a <- googledrive::drive_ls(path = url_googledrive, type = "spreadsheet")
    for (i in 1:nrow(a)){
      googledrive::drive_download(a$id[i], 
                                  type = "csv", 
                                  overwrite = TRUE, 
                                  path = file.path("data", a$name[i]))
    }

    ## File downloaded:

    ## * 'our-google-spreadsheet' <id: 1plRbVAZJ_bkGFw9Y54u57eFxL-F5zaXG8elFm3r9BXs>

    ## Saved locally as:

    ## * 'data/our-google-spreadsheet.csv'

## Uploading content to Google Drive

### Save a table to Google Drive

Here we have an example data set of daily air quality measurements in
New York, May to September 1973. We are going to save this table and
share it back to Google Drive.

Create table

    airquality <- datasets::airquality
    head(airquality)

    ##   Ozone Solar.R Wind Temp Month Day
    ## 1    41     190  7.4   67     5   1
    ## 2    36     118  8.0   72     5   2
    ## 3    12     149 12.6   74     5   3
    ## 4    18     313 11.5   62     5   4
    ## 5    NA      NA 14.3   56     5   5
    ## 6    28      NA 14.9   66     5   6

    write.csv(x = airquality, file = "./data/airquality.csv")

Upload table to Google Drive

    # Code won't actually work since the file is View only, but this is what you would do
    googledrive::drive_upload(
      media = "./data/airquality.csv", 
      path = googledrive::as_id(id_googledrive), 
      overwrite = TRUE)

### Save an image to Google Drive

We can save almost any kind of content, even including images!

Create image

    jpeg(file="./data/airquality_plot.jpeg")
    hist(airquality$Temp, main = "Temperature Histogram", ylab = "Temperatures")
    dev.off()

    ## png 
    ##   2

Upload image to Google Drive

    # Code won't actually work since the file is View only, but this is what you would do
    drive_upload(
      media = "./data/airquality_plot.jpeg", 
      path = googledrive::as_id(id_googledrive), 
      overwrite = TRUE)

## Pushing results up to GitHub

Here I show how you might use the **gert** package. Note this works
because I am doing it within RStudio and RStudio has permission to push
to this repo. If you don’t have RStudio set-up to push to GitHub, then
you need to set that up first. Also you need to be working in the local
repo of the GitHub repo that your are pushing to. This sounds more
complicated than it is.

    gert::git_add(file.path("data", "Stone.csv"))
    gert::git_commit("Adding a file", author = "Eli Holmes <eli.holmes@noaa.gov>")
    gert::git_push(remote = "origin", repo = ".")

You can also use these set ups to set up your code to run from GitHub
(say) via GitHub Actions. Though you’ll need to figure out how to tell
the action when to run since it won’t know when a file got uploaded to
Google Drive. I am sure there is a way to do that though [read
this](https://gargle.r-lib.org/articles/non-interactive-auth.html).

## \*Advanced notes on Authorizing Google Drive

So run that once, before running the Rmd and you’ll get a pop-up window
asking permission for readonly access. Then in your Rmd add these lines.
Obviously, add your email not mine. Note, the [gargle help
file](https://gargle.r-lib.org/articles/non-interactive-auth.html)
doesn’t say that this is what you should do but this is what worked for
me.

    options(
      gargle_oauth_cache = ".secrets",
      gargle_oauth_email = "eli.holmes@noaa.gov"
    )
    googledrive::drive_deauth()
    googledrive::drive_auth(scopes = "https://www.googleapis.com/auth/drive.readonly", email="eli.holmes@noaa.gov")

Make sure `.secrets` is in your `.github` file. Don’t push that up to
GitHub. Maybe you even want to delete that folder automatically on
signing out.
