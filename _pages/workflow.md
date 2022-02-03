---
title: "Workflow"
permalink: /labs/workflow/
toc: true
---

# Workflow in Stata
Our workflow in Stata begins with good file structures and keeping our files organized. Approaches to this vary and if you continue to work in research or analysis, you will find approaches that work best for you. We will set up a simple file system that will help us stay organized for the purposes of this class. 

## Windows
If you are working with a Windows computer, you will start by clicking on the File Explorer (![file explorer](http://stevebholt.github.io/rpad316/assets/images/fileexplorer.PNG)) icon in the bottom of your screen and in the USB drive or primary drive you will be using, create a folder for the class and subfolders for data, do files, logs, and output. 

1. ![Select Drive](http://stevebholt.github.io/rpad316/assets/images/step1.PNG "Select the Drive for your class folder.")

2. ![Set Up Folder](http://stevebholt.github.io/rpad316/assets/images/step2.PNG "Set up a class folder in the drive you chose. Name it rpad316.")

3. ![Set Up Subfolders](http://stevebholt.github.io/rpad316/assets/images/folder_structure.PNG "In your class folder, set up a folder structure with these subfolders. Use the same names.")

Now, we are going to put the data for the class lab into our new data subfolder.

1. Open the data subfolder you just created and leave your file explorer open.
2. In your web browser, where you are reading this, right-click on the "Labs" tab at the top of the page and select "Open in New Tab."
3. Click "Data for Lab 1."
4. Click the Download (![download](http://stevebholt.github.io/rpad316/assets/images/download.PNG)) button, select ["Save File"](http://stevebholt.github.io/rpad316/assets/images/downloadbox.PNG), and then click OK.
5. In Firefox, click the [Blue Arrow](http://stevebholt.github.io/rpad316/assets/images/bluearrow.PNG) in the top right and then click on the folder icon next to "week1.zip." This will open your "Downloads" folder, which is the default place where Windows downloads files to your computer. Open "week1" and drag [class1.dta](http://stevebholt.github.io/rpad316/assets/images/class1dta.PNG) to your still open data subfolder.

Now we are all set! Leave your class folder open in the file explorer for now because we will need it in a moment. Each week, when you are doing assignments or labs, download the data using the same process and move it to your data folder. We will use paths to this folder in our .do files so that we can save our output and document what we've done.

## Macintosh


## A Look Around Stata
Now let's look around Stata for a moment and get a sense of the tool. To do this, open the data for Class Lab 1 that we just moved into our data folder. You should see a screen like [this](http://stevebholt.github.io/rpad316/assets/images/statascreen.PNG). You'll notice a few things. First, in the center of the screen is the output window of Stata 

![Output Window](http://stevebholt.github.io/rpad316/assets/images/output_win.PNG)

and this is where we will see the results of any and all analysis we do. It's a lovely view and you will come to love it as much as I do! Untold truths will be found in this screen and, if you continue down this road, you might be the first to see a brand new, scientific result in this vary view! Notice out in the center, there is already a `use "E:\rpad316\data\class1.dta"` command that has been run. `use` tells Stata to open a data set and the path (in my case `E:\rpad316\data\`) tells Stata where the data is stored and which data set (`class1.dta`) to open. Datasets in Stata are always `.dta` file types. Think of this as the same as `.doc` for word.

Okay, off to the right, you can see the list of variables in this dataset.

![Variables](http://stevebholt.github.io/rpad316/assets/images/variables.PNG)

Here, there are two columns. One lists the variable name of each variable in the dataset. This is the equivalent of a column header in a spreadsheet and is what we use to tells Stata to do things with that particular variable. The second is the Label of each variable. Labels are more detailed, plain language descriptions of the variable, often to remind us what the variable is and how it is measured. You can also see in the bottom right corner a description of the data. Note that the description tells us that there are 7,388 observations in this dataset. 

To the left, you can see a running record of the commands that we run.

![History](http://stevebholt.github.io/rpad316/assets/images/history.PNG)

Successful commands will appear in black (or in my case white) text while unsuccessful commands will appear in red. Unsuccessful commands simply means Stata hit an error because of an error in your code. Some notes on Stata code. First, Stata is a literalist - it will only do what you tell it to and will only do **exactly** what you tell Stata to do. If you enter the wrong code or spell something wrong, Stata will just stop and do nothing else because it's confused and has a sensitive soul. Second, Stata is case sensitive. When naming variables, use all lower case because in general, all commands and code in Stata will also be all lower case. You want to make your coding life as easy as possible and one way to avoid frustration is to name variables in a way that minimizes errors.

Finally, we have the command window at the bottom. 

![Command](http://stevebholt.github.io/rpad316/assets/images/command.PNG)

The command window is one of the ways you can tell Stata to do various things. The other way is through .do files, which we will get to in just a moment. Anything you enter into the command window will generate some sort of output in the output window and be recorded in the history window. If you make a mistake and get an error, you can click on the errant command in the history window and it will automatically be filled into the command window for you and then you can correct the error.

## Working in Stata
In this class, and in general, it will be good for you to get used to working with `.do` files. In Stata, `.do` files are text files that include all of the code you are using in your project. A `.do` file can tell Stata what to do from start to finish and Stata will run every line of code in a `.do` file in order. To start a do file, go to the top and 