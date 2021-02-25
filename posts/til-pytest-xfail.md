---
title: 'TIL: Write tests you expect to fail in pytest'
date: 2021-02-25
tags:
    - til
    - python
layout: layouts/post.njk
---
If you're writing tests before you write your actual code, you'll have tests failing that you know will fail. These tests don't indicate a problem with the current state of the application. But if you're using a CI tool configured to block a merge from occuring when tests are failing this can still be a problem.

In pytest, you can allow the test suite to pass with certain tests failing using the XFAIL decorator. Here's an example from my KVM Conjurer project:

```python
@pytest.mark.xfail
def test_view_one(client):
    rv = client.get("/domains/1/")
    assert b'test 1' in rv.data

```

I wrote this test prior to writing the detail view template & controller for libvirt domains. It was already in the codebase when I was migrating my project from pure SQL to SQLAlchemy. It was ok for this test to fail in that context, so I applied the `@pytest.mark.xfail` decorator you see there. Now the tests fails but the test suite passes--so my CI system approves the merge back to master!
