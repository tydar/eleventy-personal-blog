---
title: "Big Picture photo gallery"
date: 2021-08-03
tags:
  - python
  - flask
  - redis
  - celery
  - postgres

layout: layouts/post.njk
---

Recently I put together [Big Picture](https://github.com/tydar/td-photohub). Big Picture is a simple webapp designed to learn a little bit about using Celery to process asynchronous tasks in a Python app. To keep focused on this particular task, I developed a simple photo upload app. The app supports uploading just a single image (which just works using normal Flask file upload and form processing). It also allows uploading a Zip archive full of images, which is then unzipped and processed using a Celery worker.

The front-end interface is minimal to achieve this goal. The relevant endpoint is `http://host/images/bulk`. This brings up the Zip upload form. When this form is completed, you are redirected to `http://host/tasks/task_id`, which lets you check the status of the Celery task.

The app uses a postgres instance to store the image metadata once uploaded. This allows me to make easy use of the [postgres full-text search feature](https://www.postgresql.org/docs/9.5/textsearch.html) through SQLAlchemy and psycopg. You can see that code in [search.py](https://github.com/tydar/td-photohub/blob/main/big_picture/search.py):

```python
def simple():
    if request.method == 'POST':
        search = request.form['search']

        # need to sanitize the input to replace whitespace with | or operators
        tokens = search.split()
        new_query = reduce(lambda s1, s2: s1 + '|' + s2, tokens)

        # get the list using the sqlachemy or_
        # this generates a potgres query using to_tsquery
        res_list = Image.query.filter(
            or_(
                Image.title.match(new_query),
                Image.description.match(new_query)
            )
        ).all()
    ...
```

Finally, Celery is running with a default redis configuration. You can see the line that invokes a new task in [images.py](https://github.com/tydar/td-photohub/blob/main/big_picture/images.py):

```python
# definition using @celery.task() decorator
@celery.task()
def process_zip_file(filename, task_name, prefix):
...
    # invocation in add_bulk()
    task_instance = process_zip_file.delay(path, upload.filename, request.form['prefix'])
...

```

This project gave me a lot of insight into how to set up a Celery worker and schedule tasks from a Flask app. I also learned a bit about Flask configuration management -- mostly how *not* to do it. You won't see the best practices in this app. But someday I will refactor KVM Conjurer to use more decoupled configuration and write that up!
