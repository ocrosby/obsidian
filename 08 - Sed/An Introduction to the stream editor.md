The `sed` command, short for `stream editor`, performs editing operations on text from standard input or a file. `sed` edits line-by-line and in a non-interactive way.

This means that you can make all of the editing decisions as you are calling the command, and `sed` executes the directions automatically. This may seem confusing or unintuitive, but it is a very powerful and fast way to transform text, especially as part of a script or automated workflow.

Note: On MacOS we use the BSD version of `sed` which has different options and arguments.

You can install the GNU version of `sed` via homebrew like this:

```shell
brew install gnu-sed
```

## Basic Usage

`sed` operates on a stream of text that it reads from either a text file or from standard input (STDIN). This means that you can send the output of another command directly into sed for editing, or you can work on a file that you've already created.

`sed` outputs everything to standard out (STDOUT) by default. That means that, unless redirected, `sed` will print it's output to the screen instead of saving it in a file.

The basic usage is:

```shell
sed [options] commands [file-to-edit]
```

### Using `sed` to read the contents of a file

```shell
sed '' [file-to-display]
```

What's going on here is that we haven't passed any editing commands so `sed` simply displays the contents of the file without alteration.

`sed` can use standard input rather than a file. Suppose there is a README.md file in the current directory. Pipe the output of the `cat` command into `sed` to produce a similar result.

```shell
cat README.md | sed ''
```

You'll see the contents of the file unaltered, since `sed` once again was given no editing commands.

### The print command

You can tell `sed` to print the lines of a file using the print command:

```shell
sed 'p' README.md
```

You will notice that it duplicates each line twice since by default `sed` prints each line.


https://www.digitalocean.com/community/tutorials/the-basics-of-using-the-sed-stream-editor-to-manipulate-text-in-linux