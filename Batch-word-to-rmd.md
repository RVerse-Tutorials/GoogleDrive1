# Word to Rmd

This Rmd will download Word (`docx`) files from a Google Drive folder
and convert to Rmds.

## Download all the Word files

This will download the files from the folder and save to a folder called
`data`.

    a <- googledrive::drive_ls(path = url_googledrive, type = "docx")
    for (i in 1:nrow(a)){
      googledrive::drive_download(a$id[i], overwrite = TRUE, path = file.path("data", a$name[i]))
    }

## Convert the Word files to Rmd

Converting Word to Rmd works well if your Word document is simple and
all the text has style of “Normal”. Click the Style pane from the Home
tab in Word to see the style applied to text. Real-world Word files
don’t convert so well but at least you get the text. Tables are
particularly badly converted.

Note, read up on the [options in
Pandoc](https://pandoc.org/MANUAL.html#options). You can tell it how to
deal with track changes in the document.

    for (i in 1:nrow(a)){
      fil <- file.path(here::here(), "data", a$name[i])
      outfil <- file.path(here::here(), "data", paste0(stringr::str_sub(a$name[i], 1, -5), "Rmd"))
      rmarkdown::pandoc_convert(fil, to="markdown", output = outfil, options=c("--wrap=none", "--extract-media=."))
    }
