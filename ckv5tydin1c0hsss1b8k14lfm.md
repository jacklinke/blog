## Using DjHTML from the command line in Linux or in PyCharm

## What is DjHTML?

> `DjHTML` is a pure-Python Django/Jinja template indenter without dependencies.

It standardizes the indentation for  [Django](https://docs.djangoproject.com/en/3.2/ref/templates/)  or  [Jinja](https://jinja.palletsprojects.com/en/3.0.x/)  templates throughout your Django project. DjHTML (intentionally) only works on one file at a time. This simplifies DjHTML development and prevents issues with trying to support differences in directory navigation for each operating system. You can read more about DjHTML at it's  [GitHub repository](https://github.com/rtts/djhtml) .

In order to operate on multiple files or entire directories, we can use built-in tools to apply DjHTML to multiple files.

This short guide assumes you are working with Linux. If you are working with a different operating system, it may get you started in the right direction.

---

## Using DjHTML from the command line

Because DjHTML only takes action on a single file on its own, we need to combine a couple of built-in command line tools within Linux: `find`, `xargs`, and piping.

The  [find](https://www.geeksforgeeks.org/find-command-in-linux-with-examples/)  command lets us search for files that meet certain criteria. In our case, we want to search within the current directory (and subdirectories) for any files that end in **.html**. The single period "**.**" in the example below tells the find command to search starting from the current directory. You can replace the period with any valid relative or absolute path.

The `-name` argument of the `find` command is where we specify the criteria for the files we want returned by the command. Here, we use the wildcard "*****" to specify that we want *any* files that end in **.html**

We then use a  [pipe](https://www.geeksforgeeks.org/piping-in-unix-or-linux/)  "**|**", which redirects the output of one command, program, or process as input to another. Here, we are passing any files found by the `find` command to the `xargs` command for further processing.

The  [xargs](https://www.geeksforgeeks.org/xargs-command-unix/)  command allows you to build up and execute commands with arguments from standard input. Each output of the `find` command (each file that was 'found') will be passed as an argument to `xargs`, which will then apply `DjHTML` indentation to it.

Combining all of this, to apply DjHTML to all html files in the current directory, use the following:

```bash
find . -name "*.html" | xargs djhtml -i
```

*Take a look at  [this issue](https://github.com/rtts/djhtml/issues/13#issuecomment-842553382) on DjHTML's GitHub Issues and read through the links provided above on each command for more information.*

---

## Using DjHTML in PyCharm

From the **File** menu in PyCharm select **Settings**. The **Settings** window will open with a hierarchical menu on the left side.

Within that menu, navigate to **Tools** and select **External Tools**.

In the **External Tools** panel, select the **+** button near the top to add a new **External Tool**. The **Create Tool** dialog will open.

Because of oddities with entering arguments in PyCharm's **External Tools**, you'll need to pass the arguments to the [bash](https://www.geeksforgeeks.org/introduction-linux-shell-shell-scripting/) command, rather than running DjHTML directly. If your copy of `bash` is at a non-standard location, you can identify the location by using the following command:

```bash
whereis bash
```

You should be able to use the settings below, modifying the path to `bash`, if needed. 

```text
Name:              DjHTML
Description:       Django/Jinja template indenter
Program:           /usr/bin/bash
Arguments:         -c "find '$FilePath$' -name '*.html' | xargs djhtml -i"
Working Directory: $ProjectFileDir$
```

What it should look like within PyCharm:

![Screenshot from 2021-10-24 17-28-03.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635112049055/dWkJju1Pv.png)

Click **Ok** on the **Create Tool** and **Settings** dialog windows.

Now you should be able to *right-click* on any directory or file in the **Project** pane of the PyCharm IDE, select **External Tools -> DjHTML**, and the selected files will be indented and saved.

![Screenshot from 2021-10-24 18-09-02.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635113674632/L5umeeotE.png)

---

Congratulations! Now you can easily standardize the indentation of all templates within your Django projects!

