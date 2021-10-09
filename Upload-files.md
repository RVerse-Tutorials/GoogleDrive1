# Upload Files

## Authorize google drive to connect

For this example, you will need to set authorization to allow uploading
files. Run Steps 1-3 in the authorization section below with this scope.

    googledrive::drive_auth(scopes = "https://www.googleapis.com/auth/drive", email = "eli.holmes@noaa.gov")

Only a person who has permission to edit the folder will be able to
upload to it. See the Rmd file for this section to see how the header
information is set.

### Save a table to Google Drive

Here we have an example data set of daily air quality measurements in
New York, May to September 1973. We are going to save this table and
share it back to Google Drive.

Create table.

    airquality <- datasets::airquality
    head(airquality)

    ##   Ozone Solar.R Wind Temp Month Day
    ## 1    41     190  7.4   67     5   1
    ## 2    36     118  8.0   72     5   2
    ## 3    12     149 12.6   74     5   3
    ## 4    18     313 11.5   62     5   4
    ## 5    NA      NA 14.3   56     5   5
    ## 6    28      NA 14.9   66     5   6

    fil <- file.path(here::here(), "data", "airquality.csv")
    write.csv(x = airquality, file = fil)

Upload table to Google Drive.

    googledrive::drive_upload(media = fil, path = googledrive::as_id(id_googledrive), overwrite = TRUE)

    ## File trashed:

    ## • 'airquality.csv' <id: 104JClM466deu24G8XuycbRYjAaYCHbbp>

    ## Local file:

    ## • '/Users/eli.holmes/Documents/GitHub/RVerse-Tutorials/GoogleDrive1/data/airquality.csv'

    ## Uploaded into Drive file:

    ## • 'airquality.csv' <id: 1zOiAkKTK5kMf-vjQoeI04kIURMBWBR1V>

    ## With MIME type:

    ## • 'text/csv'

### Save an image to Google Drive

Create image.

    fil <- file.path(here::here(), "data", "airquality_plot.jpeg")
    jpeg(file=fil)
    hist(airquality$Temp, main = "Temperature Histogram", ylab = "Temperatures")
    dev.off()

    ## quartz_off_screen 
    ##                 2

Upload image to Google Drive.

    googledrive::drive_upload(media = fil, path = googledrive::as_id(id_googledrive), overwrite = TRUE)

    ## Local file:

    ## • '/Users/eli.holmes/Documents/GitHub/RVerse-Tutorials/GoogleDrive1/data/airquality_plot.jpeg'

    ## Uploaded into Drive file:

    ## • 'airquality_plot.jpeg' <id: 1-0Hx-voNL5EmlfpZFFGHzBiI8Nbpwwli>

    ## With MIME type:

    ## • 'image/jpeg'
