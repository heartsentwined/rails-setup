A reminder to-do list of getting a rails app running,
with a bunch of useful tools.

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
subversion phantomjs
```

## Ruby Version Manager + Ruby + Rails

```sh
\curl -L https://get.rvm.io | bash -s stable --rails
```

## Rails composer

* WEBrick
* Haml
* RSpec
* Cucumber
* Capybara
* Fabrication
* Twitter Bootstrap
* Devise (with Confirmable and Invitable)
* CanCan with Rolify
* Simple Form

```sh
rails new myapp -m https://raw.github.com/RailsApps/rails-composer/master/composer.rb -T
```

## Add gems

* Client-side / views
    * Ember.js
    * SASS
        * Bourbon mixin pack
    * Haml
        * Maruku
    * pagination
* Testing
    * Guard
    * Spork
    * Fabrication
* Misc
    * BCrypt
    * Faker
    * Annotate
    * REST Client
    * Nokogiri - HTML Parser

`Gemfile`:

```rb
source 'https://rubygems.org'
gem 'rails', '3.2.8'
gem 'thin', '~> 1.5.0'
gem 'sqlite3'
gem 'jquery-rails'

gem 'bcrypt-ruby', '~> 3.0.1'
gem 'faker',       '~> 1.1.2'
gem 'rest-client', '~> 1.6.7'

gem 'activeadmin', '~> 0.5.0'
gem 'meta_search', '~> 1.1.3'

gem 'rest-client', '~> 1.6.7'
gem 'nokogiri',    '~> 1.5.5'

gem 'devise',           '~> 2.1.2'
gem 'devise_invitable', '~> 1.1.1'
gem 'cancan',           '~> 1.6.8'
gem 'rolify',           '~> 3.2.0'

gem 'ember-rails',             '~> 0.8.0'
gem 'will_paginate',           '~> 3.0.3'
gem 'bootstrap-sass',          '~> 2.1.1.0'
gem 'bootstrap-will_paginate', '~> 0.0.9'
gem 'haml',                    '~> 3.1.7'
gem 'simple_form',             '~> 2.0.4'

group :assets do
  gem 'sass-rails',   '~> 3.2.5'
  gem 'coffee-rails', '~> 3.2.2'
  gem 'bourbon',      '~> 2.1.2'
  gem 'hamlbars',     '~> 1.1'
  gem 'maruku',       '~> 0.6.1'
  gem 'uglifier',     '~> 1.2.4'
end

group :development, :test do
  gem 'rspec-rails',      '~> 2.11.4'
  gem 'guard-rspec',      '~> 2.1.1'
  gem 'guard-spork',      '~> 1.2.3'
  gem 'spork',            '~> 0.9.2'
  gem 'database_cleaner', '~> 0.9.1'
  gem 'email_spec',       '~> 1.4.0'
  gem 'fabrication',      '~> 2.5.0'
end

group :development do
  gem 'ruby_parser',  '~> 3.0.1'
  gem 'haml-rails',   '~> 0.3.5'
  gem 'hpricot',      '~> 0.8.6'
  gem 'quiet_assets', '~> 1.0.1'
  gem 'annotate',     '~> 2.5.0'
  gem 'hub',          '~> 1.10.2',  require: nil
end

group :test do
  gem 'cucumber-rails', '~> 1.3.0', require: false
  gem 'capybara',       '~> 1.1.3'
  gem 'launchy',        '~> 2.1.2'
  gem 'rb-inotify',     '~> 0.8.8'
  gem 'libnotify',      '~> 0.8.0'
  gem 'turn',           '~> 0.9.4', require: false
end
```

Run `bundle install`.

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

```sh
rake bourbon:install
```

Rename `app/assets/stylesheets/application.css` to `application.css.sass`.

Create `app/assets/stylesheets/custom.css.sass` as a starting point for app's css.

Edit `application.css.sass`.
Use `@import` directives instead of `Sprocket`'s `require` lines:
* Remove `*= require_tree .`
* Add at the bottom:
```sass
@import 'bootstrap'
@import 'bootstrap-responsive'
@import 'bourbon'
@import 'custom'
```

## Configure Guard and Spork

```sh
guard init rspec
spork --bootstrap
guard init spork
```

Edit `spec/spec_helper.rb`.
Most likely it's just moving everything into the `Spork.prefork` block.

Configure RSpec to automatically use Spork. Add this line to `.rspec`:
```
--drb
```

Customize the Guardfile, example:

```rb
require 'active_support/core_ext'

guard :rspec, all_after_pass: false, cli: '--drb' do
  watch(%r{^spec/.+_spec\.rb$})
  watch(%r{^lib/(.+)\.rb$})     { |m| "spec/lib/#{m[1]}_spec.rb" }
  watch('spec/spec_helper.rb')  { 'spec' }

  # Rails
  watch(%r{^app/(.+)\.rb$})           { |m| "spec/#{m[1]}_spec.rb" }
  watch(%r{^app/(.*)(\.erb|\.haml)$}) { |m| "spec/#{m[1]}#{m[2]}_spec.rb" }

  watch(%r{^app/controllers/(.+)_controller\.rb$}) do |m|
    ["spec/routing/#{m[1]}_routing_spec.rb",
    "spec/controllers/#{m[1]}_controller_spec.rb",
    "spec/acceptance/#{m[1]}_spec.rb",
    (m[1][/_pages/] ? "spec/requests/#{m[1]}_spec.rb" :
                      "spec/requests/#{m[1].singularize}_pages_spec.rb")]
  end
  watch(%r{^app/views/(.+)/}) do |m|
    (m[1][/_pages/] ? "spec/requests/#{m[1]}_spec.rb" :
                      "spec/requests/#{m[1].singularize}_pages_spec.rb")
  end

  watch(%r{^spec/support/(.+)\.rb$})                 { 'spec' }
  watch('config/routes.rb')                          { 'spec/routing' }
  watch('app/controllers/application_controller.rb') { 'spec/controllers' }

  # Capybara
  watch(%r{^app/views/(.+)/.*\.(erb|haml)$}) do |m|
    "spec/requests/#{m[1]}_spec.rb"
  end
end

guard 'spork', :cucumber_env => { 'RAILS_ENV' => 'test' }, :rspec_env => { 'RAILS_ENV' => 'test' } do
  watch('config/application.rb')
  watch('config/environment.rb')
  watch('config/environments/test.rb')
  watch(%r{^config/initializers/.+\.rb$})
  watch('Gemfile')
  watch('Gemfile.lock')
  watch('spec/spec_helper.rb') { :rspec }
  watch('test/test_helper.rb') { :test_unit }
  watch(%r{features/support/}) { :cucumber }
end
```

## RSpec default tests bugfix

`spec/controllers/users_controller_spec.rb`:

Change `before (:each) do` to `before do`.

`spec/fabricators/user_fabricator.rb`:

Uncomment `confirmed_at Time.now`.

## Speed up BCrypt during test

Add to `config/environments/test.rb`:

```rb
  # Speed up tests by lowering BCrypt's cost function.
  require 'bcrypt'
  silence_warnings do
    BCrypt::Engine::DEFAULT_COST = BCrypt::Engine::MIN_COST
  end
```

## ActionMailer default URL config

Add to `config/environments/development.rb` and `config/environments/test.rb`:

```rb
  config.action_mailer.default_url_options = { host: 'localhost:3000' }
```

## Start everything

Start rails server.

```sh
rails server
```

Start Guard.
```sh
guard
```

## Acknowledgement

* <http://ryanbigg.com/2010/12/ubuntu-ruby-rvm-rails-and-you/>
* [Michael Hartl's tutorial](http://ruby.railstutorial.org/ruby-on-rails-tutorial-book)
