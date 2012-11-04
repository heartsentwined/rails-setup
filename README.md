A reminder file to a rails-convert (me!):
to-do list of getting a rails app running,
with all my preferred tools.

# The list

Install rails

```sh
gem install rails
```

Use datamapper, and generate a new rails project. Use [dm-rails](https://github.com/datamapper/dm-rails) integration.
**Warning: possible man-in-the-middle attack!**
But it's the best bootstrap for now...

```sh
rails new project_name -m http://datamapper.org/templates/rails.rb
```

Add gems to `Gemfile`. Includes:

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

# To use ActiveModel has_secure_password
# gem 'bcrypt-ruby', '~> 3.0.1'

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

Add Ember environment config. In `config/environments`:

Add to `development.rb` and `test.rb`:

```rb
config.ember.variant = :development
```

And this for `production.rb`:

```rb
config.ember.variant = :production
```
