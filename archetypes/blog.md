---
# Blog post archetype.
#
# Create a new post with the date in the filename:
#
#   hugo new blog/$(date +%Y-%m-%d)-my-title.md
#
# The permalink config in hugo.toml will then produce
# /blog/YYYY/MM/DD/my-title/ automatically.

title: '{{ replace .File.ContentBaseName "-" " " | title }}'
date: '{{ .Date.Format "2006-01-02" }}'
author: ""
draft: true
---
