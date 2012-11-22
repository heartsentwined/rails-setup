System dependencies
===================

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

Ruby Version Manager + Ruby + Rails
===================================

```sh
\curl -L https://get.rvm.io | bash -s stable --rails
```

New Rails app
=============

* with PostgreSQL
* without Test::Unit tests
* and Git, of course

```sh
rails new myapp -d postgresql -T; cd myapp; git init
```

RVM gemset
==========

Create gemset; create project-specific `.rvmrc`; trust it.
```sh
rvm gemset create myapp; rvm rvmrc create ruby-1.9.3@myapp; rvm rvmrc trust .
```

Gems: cherry-picking
====================

Server
------

### thin
Listens to 3000, like WEBrick; start by `rails s`

### unicorn
Listens to 8080; start by `unicorn`

Testing
-------

### rspec-rails
`rails g rspec:install`

### spork
`spork --bootstrap`

### guard-rspec
`guard init rspec`

### guard-spork
`guard init spork`

Edit `spec/spec_helper.rb`. Most likely it's just moving everything into the `Spork.prefork` block.

Configure RSpec to automatically use Spork. Add this line to `.rspec`:
```rb
--drb
```

Customize `Guardfile`. Example:
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

guard 'spork', rspec_env: { 'RAILS_ENV' => 'test' } do
  watch('config/application.rb')
  watch('config/environment.rb')
  watch('config/environments/test.rb')
  watch(%r{^config/initializers/.+\.rb$})
  watch('Gemfile')
  watch('Gemfile.lock')
  watch('spec/spec_helper.rb') { :rspec }
end
```

Remove `test/`. (Otherwise, Spork will attempt to load `Test::Unit`, and fail.)

Start by `guard`.

### email_spec
`spec/spec_helper.rb`:
```rb
require "email_spec"

RSpec.configure do |config|
  config.include EmailSpec::Helpers
  config.include EmailSpec::Matchers
end
```

### database_cleaner
`spec/spec_helper.rb`:
```rb
RSpec.configure do |config|
  config.before(:suite) do
    DatabaseCleaner.strategy = :truncation
  end
  config.before(:each) do
    DatabaseCleaner.start
  end
  config.after(:each) do
    DatabaseCleaner.clean
  end
end
```

### fabrication
### rspec-validation_expectations
### launchy
### faker

### rb-inotify
(linux-specific)
### libnotify
(linux-specific)

Authentication and Authorization
--------------------------------

### devise
### devise_invitable
### cancan
### rolify

[todo: bootstrap note on these]

### bcrypt-ruby
Speed up tests by lowering security - add to `config/environments/test.rb`:
```rb
# Speed up tests by lowering BCrypt's cost function.
require 'bcrypt'
silence_warnings do
  BCrypt::Engine::DEFAULT_COST = BCrypt::Engine::MIN_COST
end
```

Models
------

### annotate
`annotate` to annotate models; `annotate -r` to annotate `routes.rb`.

### money-rails
### google_currency

### paperclip
`spec/spec_helper.rb`:
```rb
require 'paperclip/matchers'

RSpec.configure do |config|
  config.include Paperclip::Shoulda::Matchers
end
```

Assets and Frontend
-------------------

### ember-rails
In `config/environments`, add to `development.rb` and `test.rb`:
```rb
config.ember.variant = :development
```
... and to `production.rb`:
```rb
config.ember.variant = :production
```

Directory structure bootstrap
```sh
rails g ember:bootstrap
```

### bootstrap-sass
In `app/assets/stylesheets`, rename `application.css` to `application.css.sass`.
Add to it:
```sass
@import 'bootstrap'
@import 'bootstrap-responsive'
```

### haml
### haml-rails
### hamlbars
### maruku
### simple_form

APIs, external webservices
--------------------------

### rest-client

### nokogiri
Create `config/initializers/nokogiri.rb`:
```rb
require 'nokogiri'
```

### json
Create `config/initializers/json.rb`:
```rb
require 'json'
```

Admin framework
---------------

### activeadmin
`rails g active_admin:install`

Misc
----

### quiet_assets
### hub

Install gems
============

```sh
bundle
```

ActionMailer default URL config
===============================

In `config/environments/`, add to `development.rb` and `test.rb`:

```rb
  config.action_mailer.default_url_options = { host: 'localhost:3000' }
```

Acknowledgement
===============

* <http://ryanbigg.com/2010/12/ubuntu-ruby-rvm-rails-and-you/>
* [Michael Hartl's tutorial](http://ruby.railstutorial.org/ruby-on-rails-tutorial-book)
