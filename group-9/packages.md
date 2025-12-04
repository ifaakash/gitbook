---
description: Must install packages
icon: bolt-lightning
---

# Packages

## tldr

Package: [https://github.com/tldr-pages/tldr](https://github.com/tldr-pages/tldr)

{% tabs %}
{% tab title="tldr cat" %}
```yaml
tldr cat

Print and concatenate files.
More information: <https://keith.github.io/xcode-man-pages/cat.1.html>.

- Print the contents of a file to `stdout`:
    cat path/to/file

- Concatenate several files into an output file:
    cat path/to/file1 path/to/file2 ... > path/to/output_file

- Append several files to an output file:
    cat path/to/file1 path/to/file2 ... >> path/to/output_file

- Copy the contents of a file into an output file without buffering:
    cat -u /dev/tty12 > /dev/tty13

- Write `stdin` to a file:
    cat - > path/to/file

- Number all output lines:
    cat -n path/to/file

- Display non-printable and whitespace characters (with `M-` prefix if non-ASCII):
    cat -v -t -e path/to/file
```
{% endtab %}

{% tab title="tldr ps" %}
```yaml
tldr ps

Information about running processes.
More information: <https://keith.github.io/xcode-man-pages/ps.1.html>.

- List all running processes:
    ps aux

- List all running processes including the full command string:
    ps auxww

- Search for a process that matches a string:
    ps aux | grep string

- Get the parent PID of a process:
    ps -o ppid= -p pid

- Sort processes by memory usage:
    ps -m

- Sort processes by CPU usage:
    ps -r
```
{% endtab %}

{% tab title="Ruby" %}
```ruby
message = "hello world"
puts message
```
{% endtab %}
{% endtabs %}

## bat

```yaml
bat -p pyproject.toml 
[build-system]
requires = ["setuptools", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "aicommitter"
version = "1.0.2"
description = "AI powered commit message helper"
authors = [{name = "ifaakash"}]
requires-python = ">=3.7.9"
readme = "docs.md"
license = {text = "MIT"}
keywords = ["git", "commit", "ai", "devops", "cli", "commit message"]

dependencies = [
  "typer[all]>=0.9.0",
  "requests>=2.28",
  "python-dotenv>=1.0",
]

[project.scripts]
aicommitter = "aicommitter.updated_generate:app"

[tool.setuptools.packages.find]
where = ["src"]

[tool.setuptools.package-data]
aicommitter = ["docs.md"]
```



***





