This example expects that you run the code locally on your computer. If
the GD folder is private, you need to run the authentication code first
before you knit the Rmd file.

I will show a separate example of a publically visible Google Drive
folder. In that case, you could set up your code to run from GitHub
(say) via GitHub Actions. Though you’ll need to figure out how to tell
the action when to run since it won’t know when a file got uploaded to
Google Drive. I am sure there is a way to do that though [read
this](https://gargle.r-lib.org/articles/non-interactive-auth.html).

## Set-up

1.  Install **googledrive** package (if needed)

<!-- -->

    install.packages("googledrive")

1.  Create a folder on GD (if needed)

I have created one (it is only accessible if you are in NOAA), and it
has the following sample files:

![](images/googledrive.png)

This repo code is designed to be run locally (meaning on someone’s
computer manually). After the code is run, the user decides what to do
with it. They can push the results up to GitHub. Note this doesn’t mean
pushing the data up, you might be just pushing the report from those
data. It just depends on your application.

1.  Copy the url to the Google Drive folder location

<!-- -->

    url_googledrive <- "https://drive.google.com/drive/folders/11WnXxs56jORbLkD1mFTZxwSaShex3Sse"

## If this is a private GD folder

**Then you need to authorize googledrive to access Google Drive**

You need to run this outside of the Rmd file. Basically you are giving
**googledrive** access to your Google Drive files during the R session.
This will open a window on your browser where you tell it to
authenticate with your Google account. It doesn’t get your password, but
tells **googledrive** that it can see and download files. Note, the
default scope is to also edit and delete files, but here I limit the
scope to read only.

So can anyone run this code and get access to the google drive folder?
No. Only those who have access to that folder (because you set it up
that way) would have permission to see it with **googledrive**.

Note, if you get asked whether to save the authorization so it lasts
across R sessions (so still applies after you close RStudio), you can
say No. You’ll just need to reauthorize the next time you need to get
the files off of Google Drive.

    # https://developers.google.com/identity/protocols/oauth2/scopes
    googledrive::drive_auth(scopes = "https://www.googleapis.com/auth/drive.readonly", email="eli.holmes@noaa.gov")

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

## List the files in that folder

All files.

    dir_files <- googledrive::drive_ls(path = url_googledrive)
    dir_files$name

    ## [1] "our-google-spreadsheet" "data.xlsx"              "our-google-doc"        
    ## [4] "Stone.csv"              "Rock.csv"

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

Download one Excel file on Google Drive and save to a folder called
`data`.

      file_name <- "data.xlsx"
    # find the id of that file
      file_id <- a$id[which(a$name==file_name)]
    # download the file and save to data folder
      googledrive::drive_download(file=a$id[1],
                                    overwrite = TRUE, 
                                    path = file.path("data", file_name))

    ## File downloaded:

    ## • 'our-google-spreadsheet' <id: 1plRbVAZJ_bkGFw9Y54u57eFxL-F5zaXG8elFm3r9BXs>

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

Download one csv file on Google Drive and save to a folder called
`data`.

      file_name <- "Stone.csv"
      file_id <- a$id[which(a$name==file_name)]
      googledrive::drive_download(file=a$id[1],
                                    overwrite = TRUE, 
                                    path = file.path("data", file_name))

    ## File downloaded:

    ## • 'our-google-spreadsheet' <id: 1plRbVAZJ_bkGFw9Y54u57eFxL-F5zaXG8elFm3r9BXs>

    ## Saved locally as:

    ## • 'data/Stone.csv'

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

## Download a bunch of files

Download all the csv files in a Google Drive folder.

      # Spreadsheets
      a <- googledrive::drive_ls(path = url_googledrive, type = "csv")
      for (i in 1:nrow(a)){
        googledrive::drive_download(a$id[i], 
                                    overwrite = TRUE, 
                                    path = file.path("data", a$name[i]))
      }

    ## File downloaded:

    ## • 'Stone.csv' <id: 15nK21BniTuMpAhEXXAnsHwopOXOEcnzf>

    ## Saved locally as:

    ## • 'data/Stone.csv'

    ## File downloaded:

    ## • 'Rock.csv' <id: 1-dz_3QUqmzuXtWXfCcZUowUhZI1x9XAb>

    ## Saved locally as:

    ## • 'data/Rock.csv'

Download all the Google Spreadsheets in a Google Drive folder.

      a <- googledrive::drive_ls(path = url_googledrive, type = "spreadsheet")
      for (i in 1:nrow(a)){
        googledrive::drive_download(a$id[i], 
                                    type = "csv", 
                                    overwrite = TRUE, 
                                    path = file.path("data", a$name[i]))
      }

    ## File downloaded:

    ## • 'our-google-spreadsheet' <id: 1plRbVAZJ_bkGFw9Y54u57eFxL-F5zaXG8elFm3r9BXs>

    ## Saved locally as:

    ## • 'data/our-google-spreadsheet.csv'

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
