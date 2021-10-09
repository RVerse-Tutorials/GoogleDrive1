# Download files

## List files in a google drive folder

All files.

    dir_files <- googledrive::drive_ls(path = url_googledrive)
    dir_files$name

    ## [1] "example.docx"           "example2.docx"          "our-google-spreadsheet"
    ## [4] "data.xlsx"              "our-google-doc"         "Stone.csv"             
    ## [7] "Rock.csv"

All csv files.

    dir_files <- googledrive::drive_ls(path = url_googledrive, type="csv")
    dir_files$name

    ## [1] "Stone.csv" "Rock.csv"

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

### Download one Excel file

Download from Google Drive and save to a folder called `data`.

    file_name <- "data.xlsx"
    # find the id of that file
    file_id <- a$id[which(a$name==file_name)]
    # download the file and save to data folder
    googledrive::drive_download(file=file_id, overwrite = TRUE, path = file.path("data", file_name))

    ## File downloaded:

    ## • 'data.xlsx' <id: 1FL9HBcSh9QjELpiMYoR5PPEi1CIIx8g9>

    ## Saved locally as:

    ## • 'data/data.xlsx'

Read that file in using the **readxl** package.

    tmp <- readxl::read_excel(file.path(here::here(), "data", "data.xlsx"))
    tmp

    ## # A tibble: 6 × 2
    ##    year count
    ##   <dbl> <dbl>
    ## 1  1990    10
    ## 2  1991    20
    ## 3  1992    10
    ## 4  1993    30
    ## 5  1994    40
    ## 6  1995    50

## Download one csv file

Download from Google Drive and save to a folder called `data`.

    file_name <- "Stone.csv"
    file_id <- a$id[which(a$name==file_name)]
    googledrive::drive_download(file=file_id, overwrite = TRUE, path = file.path("data", file_name))

    ## File downloaded:

    ## • 'Stone.csv' <id: 15nK21BniTuMpAhEXXAnsHwopOXOEcnzf>

    ## Saved locally as:

    ## • 'data/Stone.csv'

Read that file in. The first 2 lines are metadata and are skipped.

    tmp <- read.csv(file.path(here::here(), "data", file_name), skip=2)
    tmp

    ##   year count
    ## 1 1990    10
    ## 2 1991    20
    ## 3 1992    10
    ## 4 1993    30
    ## 5 1994    40
    ## 6 1995    50

### Download csv files

Download all csv files and save to a folder called `data`.

    a <- googledrive::drive_ls(path = url_googledrive, type = "csv")
    for (i in 1:nrow(a)){
      googledrive::drive_download(a$id[i], overwrite = TRUE, path = file.path("data", a$name[i]))
    }

    ## File downloaded:

    ## • 'Stone.csv' <id: 15nK21BniTuMpAhEXXAnsHwopOXOEcnzf>

    ## Saved locally as:

    ## • 'data/Stone.csv'

    ## File downloaded:

    ## • 'Rock.csv' <id: 1-dz_3QUqmzuXtWXfCcZUowUhZI1x9XAb>

    ## Saved locally as:

    ## • 'data/Rock.csv'

### Download Google Spreadsheets

    a <- googledrive::drive_ls(path = url_googledrive, type = "spreadsheet")
    for (i in 1:nrow(a)){
      googledrive::drive_download(a$id[i], type = "csv", overwrite = TRUE, path = file.path("data", a$name[i]))
    }

    ## File downloaded:

    ## • 'our-google-spreadsheet' <id: 1plRbVAZJ_bkGFw9Y54u57eFxL-F5zaXG8elFm3r9BXs>

    ## Saved locally as:

    ## • 'data/our-google-spreadsheet.csv'

## Pushing results up to GitHub

Here I show how you might use the
[**gert**](https://CRAN.R-project.org/package=gert) package. Note this
works because I am doing it within RStudio and my RStudio set-up has
permission to push to this repo. If you don’t have RStudio set-up to
push to GitHub, then you need to set that up first. Also you need to be
working in the local repo of the GitHub repo that your are pushing to.
This sounds more complicated than it is.

    gert::git_add(file.path("data", "Stone.csv"))
    gert::git_commit("Adding a file", author = "Eli Holmes <eli.holmes@noaa.gov>")
    gert::git_push(remote = "origin", repo = ".")

You can also use these set ups to set up your code to run from GitHub
via GitHub Actions. If the Google Drive is not publicly viewable, then
you’ll need to research how to set up authorization in a deployed
application [read
this](https://gargle.r-lib.org/articles/non-interactive-auth.html). But
if the Google Drive is publicly viewable, then it is fairly easy to set
up a Google Action that is triggered on a schedule or triggered by an
outside event. [Read about how to do that
here](https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows).
