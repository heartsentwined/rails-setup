A reminder to-do list of getting a rails app running,
with a bunch of useful tools.

Based on <http://ryanbigg.com/2010/12/ubuntu-ruby-rvm-rails-and-you/>,
with added customizations

# The list

## System dependencies

* Curl
* Database drivers
* Phantomjs
* ...

```sh
sudo apt-get install curl libsqlite3-dev libmysqlclient-dev libpq-dev \
build-essential openssl libreadline6 libreadline6-dev \
git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-dev sqlite3 \
libxml2-dev libxslt-dev autoconf libc6-dev ncurses-dev automake libtool bison \
subversion pkgconfig phantomjs
```

## Ruby Version Manager

Install RVM
```sh
curl -L get.rvm.io | bash -s stable --auto
```
and reload Bash profile
```sh
. ~/.bash_profile
```

## Install Ruby

```sh
rvm install 1.9.3 && rvm use 1.9.3
```

## Install Rails

```sh
gem install rails
```

## Datamapper and generate new rails project

Use [dm-rails](https://github.com/datamapper/dm-rails) integration.
**Warning: possible man-in-the-middle attack!**
But it's the best bootstrap for now...

```sh
rails new project_name -m http://datamapper.org/templates/rails.rb
```

## Add gems

* Client-side / views
    * Ember.js
    * Twitter Bootstrap
    * SASS
        * Bourbon mixin pack
    * Haml
    * pagination
* Testing
    * RSpec
    * Guard
    * Spork
    * Capybara
    * Factory girls
* Misc
    * BCrypt

Add to `Gemfile`:

```rb
gem 'bcrypt-ruby',    '~> 3.0.1'

gem 'jquery-rails',   '~> 2.1.3'
gem 'ember-rails',    '~> 0.8.0'

gem 'will_paginate',  '~> 3.0.3'
gem 'bootstrap-sass', '~> 2.1.1.0'
gem 'bootstrap-will_paginate', '~> 0.0.9'

# Gems used only for assets and not required
# in production environments by default.
group :assets do
  gem 'sass-rails',   '~> 3.2.5'
  gem 'coffee-rails', '~> 3.2.2'
  gem 'bourbon',      '~> 2.1.2'
  gem 'haml',         '~> 3.1.7'
  gem 'uglifier',     '~> 1.2.4'
end

# Use unicorn as the web server
# gem 'unicorn', '~> 4.2.1'

# Deploy with Capistrano
# gem 'capistrano', '~> 2.11.2'

# To use debugger
# gem 'ruby-debug19', '~> 0.11.6', :require => 'ruby-debug'

group :development, :test do
  gem 'rspec-rails', '~> 2.11.4'
  gem 'guard-rspec', '~> 2.1.1'
  gem 'guard-spork', '~> 1.2.3'
  gem 'spork',       '~> 0.9.2'
end

group :test do
  gem 'capybara',              '~> 1.1.3'
  gem 'factory_girl_rails',    '~> 4.1.0'
  gem 'rb-inotify',            '~> 0.8.8'
  gem 'libnotify',             '~> 0.8.0'
  # Pretty printed test output
  gem 'turn',                  '~> 0.9.4', :require => false
end
```

Run `bundle install`.

## Rspec support

Integrate rspec support for datamapper.

```sh
rails generate rspec:install
```

In `spec/spec_helper.rb`:

Comment out
```rb
  config.fixture_path = "#{::Rails.root}/spec/fixtures"
```
and
```rb
  config.use_transactional_fixtures = true
```

Add at bottom of `RSpec.configure` block
```rb
  config.before(:suite) { DataMapper.auto_migrate! }
```

## Ember environment configs

In `config/environments`:

Add to `development.rb` and `test.rb`:

```rb
  config.ember.variant = :development
```

And this for `production.rb`:

```rb
  config.ember.variant = :production
```

## Ember directory structure bootstrap

```sh
rails generate ember:bootstrap
```

## Configure SASS / Bourbon

Rename `app/assets/stylesheets/application.css` to `application.css.sass`.

Create `app/assets/stylesheets/custom.css.sass` as a starting point for app's css.

Use `@import` directives instead of `Sprocket`'s `require` lines:
* Remove `*= require_tree .`
* Add `@import 'bourbon'` at the top
* Add `@import 'custom'` after that

## Start everything

Remove `public/index.html`

Start rails server.

```sh
rails server
```

Start Guard.
```sh
guard
```

## Todo

* Twitter Bootstrap
* Guard
* Spork
* Make an index page, index controller, etc, that loads ember app
