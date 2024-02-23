#develop/tree 

Generally controllers are named by the plural form of their corresponding resource and a Controller suffix. This is to avoid naming collisions with models that are often equally named.

## Introduction [#](#introduction)

This style guide:

-   provides a set of styles and conventions to follow for each Laravel project you undertake
-   helps to maintain a level of consistency between projects and reduce cognitive overhead needed when switching between them
-   serves to make it simpler to onboard new developers into existing projects
-   is designed to change over time, as new patterns emerge and teams expand, taking into account new perspectives
-   aims to reflect styles and conventions that apply to current version of Laravel at all times

## Why Laravel?

First and foremost, Laravel provides the most value when you write things the way Laravel intended you to write. If there's a documented way to achieve something, follow it. Whenever you do something differently, make sure you have a justification for why you didn't follow the defaults.

At its core, [Laravel](https://laravel.com/) helps you to develop your applications quickly, whilst keeping the syntax simple and readable.

Along with its simple syntax, are a number of conventions and features that really allow you to focus on building your application, without worrying about the boilerplate code you'd ordinarily write when building an application from scratch. This includes things like managing your database schema with [migrations](https://lsg.spdload.dev/migrations-schema) and working with the database using [Eloquent](https://lsg.spdload.dev/eloquent).

Simply put, Laravel gives you all of the tools to build a robust, modern web application right from your first `composer install`. Be warned, though: try and follow the framework's conventions wherever possible to save yourself headaches in future.

___
[[General]]
