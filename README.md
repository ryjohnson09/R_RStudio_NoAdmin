# Installing R and RStudio without Admin Rights

If you do not have administrative privledges on you computer, it can be very difficult to get various software packages installed and working properly. Personally, I use `R` and `RStudio` **A LOT** and it's important that I have a working version that can be easily installed and updated. This is my solution to install `R` and `RStudio` without administrative rights on a Mac running OSX 10.12.6.

----------------------------------------------------

## R Installation

This was by far the hardest part of the process, and my solution is one way that worked best for me...**I can't gurantee that this will work for all, nor is it the best solution, but it worked and I haven't had any issues yet**.

### Install Homebrew locally

I tried initially to download R from source and compile it manually but kept running into issues with the `gfortran` compiler. So instead, I decided to go with `homebrew`. The biggest caveat avout using `homebrew` is that it typically installs packages to `/usr/local/`, but by doing that you would need admin rights. Therefore, I installed a local version of `homebrew`. 

1. Create a local bin directory: `mkdir /Users/rjohnson/bin`. Note: `rjohnson` is unique to my computer, will be your name if it is your personal computer.

2. Download the [homebrew git repository](https://github.com/Homebrew/brew) by clicking the green `Clone or Download`, then `Download Zip`. Unzip the repository and then move it to the `/Users/rjohnson/bin` directory.

3. Move into the `/Users/rjohnson/bin/brew-master` directory and run brew: `./brew`
    * This will set up everything you need for brew, including updating the various packages, and setting your PATH so that you can call brew from any directory.
    
4. Next, install `R` by running `brew install R`

5. Assuming everying runs smoothly, you now have an updated version of `R` within the `/Users/rjohnson/bin/brew-master/bin/R` directory.

### Set PATH to brew version of R

We now have to make sure that when we call `R` from the terminal, it uses the brew verion of `R` that we just downloaded, and not another version on the computer. This will involve setting your your `~/.bashrc` & `~/.bash_profile` files correctly.

There is a lot of debate as to how to properly set up `~/.bashrc` & `~/.bash_profile` files, but I personally only like to use one, and have the other `source` the other file. Here is what my ~/.bash_profile looks like:

```
if [ -f ~/.bashrc ]; then
   source ~/.bashrc
fi
```
All this does is say to the computer "If there is a `~/.bashrc`, source it." Sourcing is just another way of running everything that is in the file and making it accessible to the user. Here is what my `~/.bashrc` file looks like:

```
# Set Colors for Terminal
export CLICOLOR=1
export LSCOLORS=GxFxCxDxBxegedabagaced

# Set which R RStudio uses
export RSTUDIO_WHICH_R=/Users/rjohnson/bin/brew-master/bin/R

# PATH
export PATH=/Users/rjohnson/bin/brew-master/bin:$PATH
export PATH="/Users/rjohnson/bin/brew-master/opt/openssl@1.1/bin:$PATH"
```

Ther is some other stuff in there, but the big one is that we need to point the `RSTUDIO_WHICH_R` environmental variable to the new version of `R`. You can see that the `RSTUDIO_WHICH_R` is set to the path for my brew version of `R`. Additionally, you'll notice that in the `# PATH` section, I've added the `brew-master/bin` path. This is recommended so that any program you download with `brew` will be used over the `/usr/bin` which is likely only accessible to administrators. Once you add this to your `~/.bashrc` file, close it and either type `source ~/.bashrc` into the terminal or restart the terminal program.


**Note:** the export PATH="/Users/rjohnson/bin/brew-master/opt/openssl@1.1/bin:$PATH" line in my ~/.bashrc was added after installing openssl@1.1 with brew. It was needed to install the httr package, and this line was added to my ~/.bashrc profile because that is what it told me to do after a failed attempt of running install.packages("httr")

Now to verify everyting is up and running, run the following commands...and you should get the following responses:

```
PMBF56958MAC:~ rjohnson$ which R
/Users/rjohnson/bin/brew-master/bin/R
PMBF56958MAC:~ rjohnson$ echo $RSTUDIO_WHICH_R
/Users/rjohnson/bin/brew-master/bin/R
```
Everything should be good to go! If a new version of R comes out (and is on `homebrew`), simply repeat everything and `homebrew` should take care of removing your old version and installing the newest version.

## RStudio Installation

This is much easier than the above `R` installation. Follow the steps below (again, if using a mac):

1. Download `RStudio` which can be found [here](https://www.rstudio.com/products/rstudio/download/#download).

2. Double click on the `.dmg` file that downloads and a finder window should open up with teh `RStudio` icon, as well as the `Application` folder.

3. **Do not drag the RStudio icon into the Applications folder.** Instead, drag it onto your desktop. Now, if you click on the `RStudio` icon from your desktop, it should open no problem.

### Ensuring RStudio uses the brew version of R

Now, you may have noticed that when you opened `RStudio`, it may have used another version of `R` that is on your computer. You can check this by entering `Sys.which("R")` into the R prompt. If it spits out `/Users/rjohnson/bin/brew-master/bin/R"`, then you're good to go. If it spits out anything else, then we need to do a short work-a-round.

You can get `RStudio` to recognize your brew `R` version in two ways:

1. Open the terminal, and type `open -na RStudio`. This should open `RStudio` with the correct version of `R` since we previously set our `RSTUDIO_WHICH_R` variable.

2. If opening the terminal is a bit of a pain in the butt, you can create a desktop icon that essentially does the same thing. To do this, open the terminal and navigate to your Desktop. Then create a new file and name it whatever you want (eg. `RStudio_brew`). Then copy and paste the following contents into the file:

```
#!/bin/bash
open -na RStudio
```
Close the file, and now all you have to do is double click that file (should be a `exec` file), and this will automatically open up `RStudio` through the terminal, with the correct version of `R`.

