---
title: "TIL: set working directory to local path in shell script"
date: 2021-02-22
tags:
    - til
    - bash
layout: layouts/post.njk
---
To find the local directory a bash script is being executed in, use the variable `$BASH_SOURCE`.

Combined with usual shell utilities like `dirname`, `pwd`, and `cd`, we can move our script execution to the local context. See this example:

```bash
#!/bin/bash

## Pre-setup: find & cd to test directory
parent_path=$( cd "$(dirname "${BASH_SOURCE[0]}")" ; pwd - P )

cd "$parent_path"
```

Here, `$parent_path` is set by first using `cd` to move to the absolute path of the script (stored in `$BASH_SOURCE`), then using `pwd -P` to get the directory tree leading to the execution context. Finally, we `cd` to the path stored in `$parent_path`.
