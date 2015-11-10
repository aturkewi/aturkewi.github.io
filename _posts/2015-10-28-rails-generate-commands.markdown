---
layout: post
title: "Rails Generate Commands"
modified:
categories: 
description:
tags: []
image:
  feature:
  credit:
  creditlink:
comments:
share:
date: 2015-10-28T08:55:31-04:00
---

|                    |migration|model        |routes               |controller       |views                |helpers      |
|--------------------|---------|-------------|---------------------|-----------------|---------------------|-------------|
|`rails g scaffold`  |create   |create(empty)|create(resource)     |create(filled in)|create views and form|create(empty)|
|`rails g resource`  |create   |create(emtpy)|create(resource)     |create(empty)    |create(empty folder) |create(empty)|
|`rails g model`     |create   |create(empty)|no                   |no               |no                   |no           |
|`rails g controller`|no       |no           |create(for specified)|create(filled in)|create(for specified)|create(empty)|
|`rails g migration` |create   |no           |no                   |no               |no                   |no           |


