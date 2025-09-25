\#lan #llm #resolved #shell

# 1 Reference to path of shell script

# 2 Objective

2025-06-23 Wk 26 Mon - 15:44

* Linux shell scripts `#!/bin/sh` sometimes need to reference their own location.
* They also need to take in simple input, like a directory string in `$1` to be checked to be a valid directory.

Using LLM (chatgpt-4o)

2025-06-23 Wk 26 Mon - 16:50

[<a name="post1-1" />^post1-1](Wk%2026%20001%20Shell%20script%20arguments%20and%20path.md#post1-1) offers a solution for stripping any possible end `/` after the directory argument.

# 3 Solution

````sh
#!/bin/sh

# Get the full path to the directory of the script
script_dir="$(cd "$(dirname "$0")" && pwd)"

echo "Script is located at: $SCRIPT_DIR"

# Check if the first argument is a valid directory
if [ -z "$1" ]; then
  echo "Error: No directory path provided."
  echo "Usage: $0 <directory_path>"
  exit 1
fi

if [ -d "$1" ]; then
  echo "'$1' is a valid directory."
else
  echo "'$1' is not a valid directory."
  exit 2
fi

# for any possible duplicate slashes in the end
arg_dir="$(realpath -s "$1")"

````

To not print anything on a good path and also make the exit codes common,

````sh
#!/bin/sh

# Get the full path to the directory of the script
SCRIPT_DIR="$(cd "$(dirname "$0")" && pwd)"

# Check if the first argument is a valid directory
if [ -z "$1" ]; then
  echo "Error: No directory path provided."
  echo "Usage: $0 <directory_path>"
  exit 1
fi

if [ ! -d "$1" ]; then
  echo "'$1' is not a valid directory."
  exit 1
fi

# for any possible duplicate slashes in the end
arg_dir="$(realpath -s "$1")"
````

# 4 References

1. https://unix.stackexchange.com/questions/198045/how-to-strip-the-last-slash-of-the-directory-path ^post1
1. https://unix.stackexchange.com/a/376010 ^post1-1
