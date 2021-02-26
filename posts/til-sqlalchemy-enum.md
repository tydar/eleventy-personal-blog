---
title: "TIL: SQLAlchemy ENUM columns"
date: 2021-02-26
tags:
    - til
    - python
layout: layouts/post.njk
---
[SQLAlchemy](https://www.sqlalchemy.org/) supports native Enum types. This includes support for special Enum features in Postgres and MySQL. To define a basic Enum column, you need code like this snippet from my [Conjurer](https://gitlab.com/tyler.a.darnell/libvirt-conjurer) project:

'''python
class StateType(enum.Enum):
    """
    VM lifecycle states
    Match the states shown on this libvirt wiki page:
    https://wiki.libvirt.org/page/VM_lifecycle
    """
    UNDEFINED="Undefined"
    RUNNING="Running"
    STOPPED="Stopped"
    PAUSED="Paused"
    SAVED="Saved"
    UNKNOWN="Unknown"

class Domain(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    uuid = db.Column(db.String(128), unique=True)
    os = db.Column(db.String(128))
    name = db.Column(db.String(128), nullable=False)
    state = db.Column(db.Enum(StateType), nullable=False)
'''

First, you define a python Enum. Then, you use that Enum as a base for the SQLAlchemy enum type.
