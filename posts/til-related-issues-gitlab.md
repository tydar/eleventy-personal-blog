---
title: "TIL: Create relationships between issues on GitLab" 
date: 2021-03-14
tags:
    - til
    - devops
    - gitlab
layout: layouts/post.njk
---
In GitLab, you can set relationships between issues on a given project. Any issue can be linked to, block, or be blocked by any other issue.

To set this relationship, open one of the issues you wish to relate. Press the "+" button next to "Linked issues" under the issue description.

Next, choose the nature of the link ("relates to", "blocks", "is blocked by"). Finally, either paste a link to the issue you wish to create the relationship to _or_ type the issue ID in the form `<#ID>`. The latter option brings up an autocomplete/issue finder UI drop-down.

I found this feature when trying to create both overarching and granular issues to sketch out the feature set for KVM Conjurer. Now, I can have the overall issue in place to define the ultimate goal while creating granular issues for each feature. This allows each merge request to have one topic.
