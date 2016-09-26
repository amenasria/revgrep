# Overview

[![Build Status](https://travis-ci.org/bradleyfalzon/revgrep.svg?branch=master)](https://travis-ci.org/bradleyfalzon/revgrep)

`revgrep` is a CLI tool used to filter static analysis tools to only lines changed based on a commit reference.

Note: requires a `diff` tool in PATH to generate a unified diff with the `-u` flag in path, such as GNU diff. This is
used to generate a patch for files not in version control, and could be implemented internally without requiring
dependency.

# Install

```bash
go get -u github.com/bradleyfalzon/revgrep/...
```

# Usage

In the scenario below, a change was made causing a warning in `go vet` on line 5, but `go vet` will show all warnings.
Using `regrep`, you can show only warnings for lines of code that have been changed (in this case, hiding line 6).

```bash
[user@host dir (master)]$ go vet
main.go:5: missing argument for Sprintf("%s"): format reads arg 1, have only 0 args
main.go:6: missing argument for Sprintf("%s"): format reads arg 1, have only 0 args
[user@host dir (master)]$ go vet |& revgrep
main.go:5: missing argument for Sprintf("%s"): format reads arg 1, have only 0 args
```

`|&` is shown above as many static analysis programs write to `stderr`, not `stdout`, `|&` combines both `stderr` and
`stdout`. It could also be achieved with `go vet 2>&1 | revgrep`.

`revgrep` CLI tool will return an exit status of 1 if any issues match, else it will return 0. Consider using
`${PIPESTATUS[0]}` for the exit status of the `go vet` command in the above example.
