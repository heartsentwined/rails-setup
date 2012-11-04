# Introduction

A reminder file to a rails newbie (me!):
to-do list of getting a rails app running,
with all my preferred tools.

# The list

Install rails

```sh
gem install rails
```

Use datamapper, and generate a new rails project. Use [dm-rails](https://github.com/datamapper/dm-rails) integration.
*Warning: possible man-in-the-middle attack!*
But it's the best bootstrap for now...

```sh
rails new project_name -m http://datamapper.org/templates/rails.rb
```
