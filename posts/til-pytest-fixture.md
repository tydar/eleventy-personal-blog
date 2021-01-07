---
title: "TIL: use fixtures to provide standard context to pytest tests"
date: 2021-01-07
tags:
    - python
    - til
layout: layouts/post.njk
---

Pytest provides fixtures as a standard way to pass context to individual tests in different parts of your codebase. This allows you to write DRY-er test code and provides one place to configure the test environment.

To write a fixture, you decorate a python function with `@pytest.fixture`. If you place the fixture at the beginning of a given test file, you can pass that fixture as a parameter to any test in that file to provide the fixed, baseline context. To provide a fixture to all tests in your project, include it in the top-level `conftest.py` file.

Here's the short fixture I wrote to provide a connection to QEMU through libvirt:
```
@pytest.fixture
def qemu_connection():
    conn = libvirt.open('qemu:///system')
    if conn == None:
        assert 0
    return conn
```

This fixture is present in my `conftest.py` file, so I can use it in `tests/domains/test_list.py`:

```
def test_list(client, qemu_connection):
    listByName = qemu_connection.listDefinedDomains()
    . . .
```
