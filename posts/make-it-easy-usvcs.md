---
title: "Make it easy"
date: 2021-01-11
tags:
    - devops
    - architecture
layout: layouts/post.njk
---
I recently watched a talk from QCon 2016 called ["Mastering Chaos: A Netflix Guide to Microservices"](https://www.youtube.com/watch?v=CZ3wIuvmHeM). There's a lot to chew on--plenty of technical details, in-depth stories, and specific tooling. Because the talk is now coming up on 5 years old, some of the tooling is no longer maintained (e.g. Hystrix). But I wanted to pull out one key general lesson.

Developing, deploying, and maintaining software is about organizing people toward a common goal. And organizing people toward a common goal at large scale means *making it easy* for people to do the right thing.

To *make it easy* means to automate. Take the work out of human hands. Standardize what it means to meet your expectations by writing down, in code, those standards. 

The purpose of implementing CI/CD pipelines, infrastructure-as-code solutions, configuration management is to *make it easy* to meet expectations. To meet the expectation that you pass your tests before your code goes to production. To meet the expectation that your deployment environment will be in a certain state when the code gets there. To meet the expectation that a development team clearly states their infrastructure requirements.

An automated system for enforcing those solutions reduces development team hours devoted to solutions outside of their scope. And that means more time devoted to shipping while still enforcing minimum standards.

This automation also reduces operations team hours sunk into meeting production team needs. That's better for maintaining the health of your system overall.

While this talk mostly applies to work done by large teams in huge organizations the same principle to *make it easy* applies just as well to a solo-developer project. I have spent so many hours on my own troubleshooting build problems or production deployment errors myself just to arrive on a fragile, undocumented solution.

Solving those problems by writing an Ansible role means instead a repeatable and documented solution. Creating a CI pipeline means builds run on those Ansible-configured and standardized environments. And it ensures the build configuration does not live in a local virtualenv or `.lock` file--or, even worse, in the list of globally installed packages on my workstation. And that means less frustration by Saturday Tyler against Wednesday Tyler.
