
# COMMAND LINE TOOLS AND DOCKER


## LEARNING GOALS

In this workshop, you will learn some basic functionality of the command line as well as using containers.

## assumes:  
* [`docker` installed](https://docs.docker.com/get-docker/)


## 1. Get the Docker container for this workshop

There are several ways to get code from `https://github.ncsu.edu`.   
You can either download `workshop-1-main.zip` by clicking the `code > Download ZIP` button (at the top of this window), or the more advanced way is to [set up SSH keys](https://github.ncsu.edu/software-engineering-for-robotics/course/wiki/Getting-Started-with-Command-line-Git) and check out the code from the command line, using a command like:   

```
git clone git@github.ncsu.edu:CSC-519/CSC-519-WS-1.git
```

If you downloaded a ZIP, then extract (unzip) the contents.   If the name is `workshop-1-main`, **rename the folder to `workshop-1`** so that it matches the names you would have from the `git clone` command.

## 2. Start a "terminal" or "shell command" environment

On Windows, you can use a program like `powershell` or set up [Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/install).

On macOS or Linux, start the `terminal` program.  



## 2. Build the Docker container.

Navigate to the workshop's folder:

```
cd workshop-1
```


To build the Docker image:

```
docker build . -t workshop-1
```
In the previous `docker` command, the `-t workshop-1` creates a docker *image*  from the `Dockerfile` build file.  

Note: If you see errors such as ```Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?```, make sure you first run the `docker desktop` in the background and then run the commands. 


Start the `docker` container in interactive mode (`-it` flags) :


```
docker run -it workshop-1
```

You should now see the container's command prompt, something like:

```
/ #
```

This is the command prompt for the `sh` program, a very old unix program called "the shell" inside the docker container.


Now try out some unix commands:

```
ls
```

and you should see something like:

```
bin    etc    lib    mnt    proc   run    srv    tmp    var
dev    home   media  opt    root   sbin   sys    usr
```

Next, we're going to create a file inside the docker container.  To do this, we're going to use a simple unix command called `echo`, which outputs a given string:  

```
echo 'hello there!' 
```

By itself, `echo` won't create a file, so we're going to use a unix concept call 'redirect' to take the output of the `echo` command and save it as a file.  In the following command, the `>` redirects the output of `echo 'hello there!'` and writes it to the file `hi.txt`.

```
echo 'hello there!' > hi.txt
```

Now to check if the file exists and to see additional information about it, we can use the `ls -l` command to "list the long format"  (where the `-l` specifies we want the output of `ls` to be the "long format"):

```
ls -l hi.txt
```

This should show `-rw-r--r--    1 root     root             3 Aug 24 12:05 hi.txt`, which shows the "permission bits" (`-rw-r--r--`), the owner (`root`), the size in kilobytes `3`, the date and time `Aug 24 12:05`.

To see the content of the file `hi.txt` there are several command line options, and each of these commands does essentially the same thing, so try them all:

```
more hi.txt
```
```
cat hi.txt
```
The `less` command is slightly different -- `less` will show the contents of the file one screenful at a time, so it can be useful when inspecting files that do not fit on a single terminal window.  Once the `less` command has run, you'll need to quit is using the letter `q` (for "quit").
```
less hi.txt
```



Then exit the container.
```
exit
```

Try `ls` now, do you see `hi.txt`?   Why not?  You created a file, right?


Now modify the following command based on your operating system (Windows vs. macOS/Linux):

macOS/Linux:
```
docker run -v $(pwd)/scratch:/scratch  -it workshop-1
```

windows:
```
docker run -v ${PWD}/scratch:/scratch  -it workshop-1
```

The `-v` part is important-- `docker -v` creates a _temporary connection_ between your local machines and the environment inside the container.

```
echo 'hello again!' > scratch/hi.txt
```

```
exit
```

Now try doing a list in your terminal:

```
ls scratch
```

Do you see `hi.txt`?  (You should!, please ask for assistance if you do not)

You can confirm that your file is the same by with the following command in your terminal:
```
cat scratch/hi.txt
```
and you should see something like:
```
hello again!
```


## 3. INSTALLING NEW SOFTWARE IN A CONTAINER

Restart the docker container :


```
docker run -it workshop-1
```

Update the list of downloadable software packages (for "Alpine Linux," the OS installed inside the container).   We'll use the `apk` command which is the "Alpine Linux Package Keeper," a kind of [Package Manager (wiki)](https://en.wikipedia.org/wiki/Package_manager).  This command asks `apd` to connect to internet based servers that contain pre-compiled we can download and use inside the container.

```
apk update
```

Install the program `cloc` (*Count Lines of Code*) within the container:
```
apk add cloc
```

Install the program `git` within the container so we can download some code:
```
apk add git
```

(note that we could have installed both with one command like this: `apk add cloc git`).

Let's download some software using `git`, and we're going to download a big project from github, specifically, the source code for the `git` project. Because the `git` project is huuuuuuuuge (many commits and a lot of history), we're going to modify the `git` command with the `--depth=1` parameter so that git doesn't download the full history.   Even without downloading the full history of the `git` project, this is still `~65 Mb` ("sixty five megabytes").

```
git clone --depth=1 https://github.com/git/git.git
```

Run `cloc` on the contents of the directory called `git`:
```
cloc git
```

You should see a list of file types and the number of Lines of Code (LOC) organized by file type.   `cloc` is very useful because it gives a standardized way to quantify how much source code is in a folder.   Notice that for the `git` project, there are >20 different types of files.  Some files are source code, some are just structured text (like `XML` or `JSON`). Typical modern software projects contain several programming languages and file types, each with their own toolchains, IDE plugins, and parsers.

**NOTE:** Type your `unityid` in the terminal before taking a screen shot showing the output of `cloc` and your `unityid`.

Upload the screenshot and complete the questions here:
[Google Form file upload](https://forms.gle/UWi374LaKPoRfcKz6)


## Strech Goal 1:

Modify your Dockerfile so that you have a program as serves a webpage that is visible from outside the container. 

## Strech Goal 2:

Create a Dockerfile that runs a program that ingests a public website and filters it to yield a subset of some element, i.e., only returns the links inside image tags, or only returns the links to javascript files.


## That's it :)  Good job.

## Evaluation

This workshop is worth 1 point (later workshops are worth more).

You'll be graded on the following rubric:

| ITEM | POINTS |
|--|--|
| Screen shot | 0.5 |
| Questions   | 0.5 |
