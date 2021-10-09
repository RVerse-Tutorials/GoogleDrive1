# Authorizing Google Drive

If you are just running code at the command line, then run steps 1 and 2
*once*. If you are running code in an Rmd, then run Steps 1-2 from the
command line *once* and then follow the instructions in Step 3 to see
what code you need to add to your Rmd file.

## Step 1. Tell `drive_auth()` where to put the token. You maybe can leave off the email bit.

    options(
      gargle_oauth_cache = ".secrets",
      gargle_oauth_email = "eli.holmes@noaa.gov"
    )

## Step 2. Run the authorization code once

This would set the authorization to readonly.

    googledrive::drive_auth(scopes = "https://www.googleapis.com/auth/drive.readonly", email = "eli.holmes@noaa.gov")

This would set the authorization to full control (read, edit, upload and
delete).

    googledrive::drive_auth(scopes = "https://www.googleapis.com/auth/drive", email = "eli.holmes@noaa.gov")

Run that once from the command line, before knitting the Rmd. You’ll get
a pop-up window asking permission for access.

## Step 3. Then at the top of your Rmd file add these lines.

Use `echo=FALSE` so that users don’t see this. This tells Rmd where to
find the cached token to use for authorization. Obviously, add your
email not the one here. Note, the [gargle help
file](https://gargle.r-lib.org/articles/non-interactive-auth.html)
doesn’t say that this is what you should do but this is what worked for
me.

    options(
      gargle_oauth_cache = ".secrets",
      gargle_oauth_email = "eli.holmes@noaa.gov"
    )
    googledrive::drive_deauth()
    googledrive::drive_auth(scopes = "https://www.googleapis.com/auth/drive.readonly", email="eli.holmes@noaa.gov")

If you look at the Rmd file for this document, you’ll see this code at
the top with `include=FALSE` so it doesn’t show in the output. Make sure
the `scopes` part matches whatever you used in Step 2.

Make sure `.secrets` is in your `.github` file. Don’t push that up to
GitHub. Maybe you even want to delete that folder automatically on
signing out.
