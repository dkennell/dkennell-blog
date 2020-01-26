---
layout: post
title: "TIL: Postgres template databases"
date: 2020-01-10
categories:
  - Database
  - Postgres
  - Technical
  - TIL
description:
image: http://www.cuttingedgestencils.com/blog/wp-content/uploads/2016/08/hamsa-hand-stencil-how-to-tutorial-canvas-stenciling-mandala-530x297.jpg
---
Today I was playing around in my postgres CLI, making sure I knew what all of the listed databases were for.

Most of them were pretty obvious - DBs from the applications I work on at my job, or from personal projects.
However, there were a couple that I didn't recognize at all, and that contained no data. They looked like they
were probably old DBs I had created on my machine back when I was first learning how to use postgres. I deleted
one of them, the one called `test` no problem. Then I went to delete the others, `template0` and `template1`.
When I keyed in `DROP DATABASE template0;` I got the following:

```
ERROR:  cannot drop a template database
```
Ay?

So I looked it up - turns out that these two databases come with postgres right out of the box and cannot be
deleted. Their function, as their names imply, is to be the templates on which all future databases you create
are based. Whenever you type `CREATE DATABASE contradictions_in_my_sisters_arguments;`, the database that is
created is simply a copy of `template1`.

In fact, the reason `template1` is there is so that we can customize
it! Any changes you'd like to make to `template1` will show up in any databases you create in the future.  The
function of `template0`, on the other hand, is to essentially be a reference of what an untouched, non-customized,
completely fresh database should look like in postgres. If you've heavily customized `template1` and you find
yourself in a situation where you'd like to have a "basic" database instead of the customized one, you can simply
create your DB with: `CREATE DATABASE more_reasons_my_sister_is_wrong TEMPLATE template0;`
