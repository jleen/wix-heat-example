# WiX example with automatic file discovery via Heat

## Getting started

* Download the "recommended build" of the "WiX Toolset build tools" from https://wixtoolset.org/releases/ . (You can ignore all the Visual Studio stuff.)

* Install it and make sure it's on your Windows path.

## Customizing the example

You only have to do these steps once, to set up an installation configuration for your project.

* Download the `package.wxs` file from this repo, and place it in a new folder on your machine.

* Alongside `package.wxs`, place the folder that contains the files you want to package (just as if you were going to package it into a `.zip`). It doesn't matter what you name it, because that name will *not* be seen by your users. In the example, I'm just calling it `MyFolder`.

* Edit `package.wxs` and replace all the `{{braces stuff}}` with actual values (and don't include the braces in the final file).

   * These are mostly self-explanatory (like the name of the program or publisher or whatever).

   * A few of them ask for a `{{GUID}}`. You have to generate a fresh new GUID for each place that this occurs (so there will be several distinct GUIDs in the finished file). If you have Python installed, the simplest way to generate a GUID is probably `python -c "import uuid;print(uuid.uuid4())"`

   * Two of them ask for a `{{ID of your binary}}`. You'll need to skip these for now. You'll figure out the correct value in the next section.

## Building your package

Do this every time you've got a new build that you want to package up into an installer. Except where otherwise stated, just run these commands verbatim.

* `heat.exe dir MyFolder -cg AllFiles -o files.wxs -ag -gg -scom -sreg -sfrag -srd -dr INSTALLFOLDER -var var.AllFilesDir` (replacing `MyFolder` with the name of the folder where you put your files)

* Copy the ID of your `.exe` file into your package, as follows:

  * Open up the `files.wxs` file that was created in the previous step.

  * Search for the name of your `.exe` file (the one that you want the installer to create a launcher shortcut for).

  * You should see the filename in a `Source="…"` attribute, and on the same line you should see something like `File Id="blahblah"`. The `blahblah` (without the quotes) is what you want. That's your ID.

  * Go back to `package.wxs` and put this ID into *both* of the `{{ID of your binary}}` placeholders. Remove the `{{braces}}` but keep the `#` symbol.

  * **Like everything in the "Building your package" section, you'll need to redo this step every time you change your package contents.** The rest of the `package.wxs` steps in the "Customizing the example" section only need to be done once, but this one needs to be done every time.

* `candle.exe package.wxs files.wxs -dAllFilesDir=MyFolder` (again replacing `MyFolder` with the name of the folder where you put your files))

* `light.exe package.wixobj files.wixobj -o MyInstaller` (replacing `MyInstaller` with the name you want for your `.msi` file)

  * This last step will probably give you some warnings about packages and components, and maybe about fonts. They seem to be harmless.
