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

Postgresql
----------

```sh
sudo apt-get install postgresql postgresql-client
```

Edit `/etc/postgresql/*/main/pg_hba.conf`, modify the line
```
# "local" is for Unix domain socket connections only
local   all             all                                     peer
```
to
```
# "local" is for Unix domain socket connections only
local   all             all                                     trust
```
in order to enable no-password login.

```sh
sudo service postgresql restart
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
rvm rvmrc create ruby-1.9.3@myapp; rvm gemset use myapp; rvm rvmrc trust .
```

Create DBs
==========

```sh
rake db:create:all
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

Configure RSpec to use the documentation format. Add this line to `.rspec`;
```rb
--format documentation
```

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
  watch('config/routes.rb')
END
```

Remove `test/`. (Otherwise, Spork will attempt to load `Test::Unit`, and fail.)

Start by `guard`.

### jasminerice

### guard-jasmine
`guard init jasmine`

Add to `Guardfile`:
```rb
guard :jasmine do
  watch(%r{spec/javascripts/spec\.(js\.coffee|js|coffee)$}) { 'spec/javascripts' }
  watch(%r{spec/javascripts/.+_spec\.(js\.coffee|js|coffee)$})
  watch(%r{app/assets/javascripts/(.+?)\.(js\.coffee|js|coffee)(?:\.\w+)*$}) do |m|
    "spec/javascripts/#{m[1]}_spec.#{m[2]}"
  end
end
```

### parallel_tests

Modify `config/database.yml`:
```yml
test:
  database: myapp_test<%= ENV['TEST_ENV_NUMBER'] %>
```

Create DBs.
`rake parallel:create`

Prepare test DBs - repeat after migration.
`rake parallel:prepare`

Modify `Guardfile`:
```rb
guard :rspec, parallel: true, all_after_pass: false, cli: '--drb' do
  # ...
end
```

### shoulda

### email_spec
`spec/spec_helper.rb`:
```rb
require 'email_spec'

RSpec.configure do |config|
  config.include EmailSpec::Helpers
  config.include EmailSpec::Matchers
end
```

### database_cleaner
`spec/spec_helper.rb`:
```rb
RSpec.configure do |config|
  config.before(:each) do
    DatabaseCleaner.start
  end
  config.after(:each) do
    DatabaseCleaner.clean
  end
end
```

### fabrication
### launchy
### faker

### rb-inotify
(linux-specific)
### libnotify
(linux-specific)

Authentication and Authorization
--------------------------------

### devise
Bootstrap:
```sh
rails g devise:install
```
Review `config/initializers/devise.rb`.
Use Devise on existing model:
```sh
rails g devise model_name
```
Review the generated migration file.

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

### paperclip
`spec/spec_helper.rb`:
```rb
require 'paperclip/matchers'

RSpec.configure do |config|
  config.include Paperclip::Shoulda::Matchers
end
```

### annotate
`annotate` to annotate models; `annotate -r` to annotate `routes.rb`.

### money-rails
`rails g money_rails:initializer`
Open `config/initializers/money.rb` and do the configs.
### google_currency
Manually add `json` gem as dependency.
Use as currency bank - add to `config/initializers/money.rb`:
```rb
MultiJson.engine = :json_gem
require 'money/bank/google_currency'
Money.default_bank = Money::Bank::GoogleCurrency.new
```

### ruby-units

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

### ember-auth
### emblem-rails

### compass-rails
Create `config/compass.rb`:
```rb
project_type = :rails
```

When needed, import compass in a stylesheet:
```sass
@import compass
```

Optional: register a new `fonts` directory into asset pipeline.
Create the directory `app/assets/fonts`.

Add to `config.application.rb`:
```rb
    # add fonts to asset path
    config.assets.paths << Rails.root.join('app', 'assets', 'fonts')
```

### zurb-foundation
Starter files and layout template:
```sh
rails g foundation:install
```

Add to `config/compass.rb`:
```rb
require 'zurb-foundation'
```

### bootstrap-sass
In `app/assets/stylesheets`, rename `application.css` to `application.css.sass`.
Add to it:
```sass
@import 'bootstrap'
@import 'bootstrap-responsive'
```

Add to `app/assets/javascripts/application.js`:
```js
//= require bootstrap
```

### bourbon
In `app/assets/stylesheets`, rename `application.css` to `application.css.sass`.
Add to it:
```sass
@import 'bourbon'
```
Remove all sprocket directives; files must be imported manually.

### color-schemer

### underscore-rails
Add to `app/assets/javascripts/application.js`:
```js
//= require underscore
```

### haml
### haml-rails
### haml-contrib
### hamlbars
### maruku

### slim
### slim-rails

### simple_form

APIs, external webservices
--------------------------

### active_model_serializers
Generate a serializer:
```sh
rails g serializer model_name
```

### inherited_resources

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
```sh
rails g active_admin:install
```
Generate admin page for a model:
```sh
rails g active_admin:resource [model]
```

PDF
---

### prawn

Misc
----

### delayed_job_active_record
`rails g delayed_job:active_record; rake db:migrate`
Depends on `daemons`. Run by `script/delayed_job start`.
### daemons

### quiet_assets
### hub

Install gems
============

```sh
bundle update
```

ActionMailer default URL config
===============================

In `config/environments/`, add to `development.rb` and `test.rb`:

```rb
  config.action_mailer.default_url_options = { host: 'localhost:3000' }
```

Heroku
======

Make sure there is at least one git commit:
```sh
git add -A; git commit -m 'init'
```

Login - interactive
```sh
heroku login
```

Create and push
```sh
heroku create && git push heroku master
```

Rename
------
```sh
heroku apps:rename newname
```

Custom domain
-------------

Point `example.com` to myherokuapp.herokuapp.com by adding a DNS record:
* Name: example.com
* TTL: (use DNS service provider's default)
* Class: IN
* Type: CNAME
* Record: myherokuapp.herokuapp.com

Add domain to heroku app:
```sh
heroku domains:add example.com
```

Repeat for `*.example.com`.

Unicorn
-------

Add `unicorn` gem.

Create `Procfile`:
```
web: bundle exec unicorn -p $PORT -c ./config/unicorn.rb
```

Create `config/unicorn.rb`:
```rb
worker_processes 3
timeout 30
preload_app true

before_fork do |server, worker|
  if defined?(ActiveRecord::Base)
    ActiveRecord::Base.connection.disconnect!
    Rails.logger.info 'Disconnected from ActiveRecord'
  end

  sleep 1
end

after_fork do |server, worker|
  if defined?(ActiveRecord::Base)
    ActiveRecord::Base.establish_connection
    Rails.logger.info 'Connected to ActiveRecord'
  end
end
```

Modify `config/application.rb`:
```rb
    # heroku unicorn logging fix
    config.logger = Logger.new(STDOUT)
```

Addon: newrelic
---------------

Provides useful monitoring stats.

```sh
heroku addons:add newrelic:standard
```

Add the `newrelic_rpm` gem, and `bundle update`.

Download a template config file: (no need to touch settings)
```sh
curl https://raw.github.com/gist/2253296/newrelic.yml > config/newrelic.yml
```

Specify app environment
```sh
heroku config:add RACK_ENV=production
```

Commit and push. Keys in config file will be updated automatically.

Asset pipeline fix
------------------

Add to `config/application.rb`:
```rb
    # heroku asset precompilation fix
    config.assets.initialize_on_precompile = false
```

Serve gzipped static assets
---------------------------

Use the gem `heroku-deflater`.
`Gemfile`:
```rb
group :production do
  gem 'heroku-deflater'
end
```

Heroku's new cedar stack no longer serves static assets through nginx.
Enable rails' static assets server.
`config/environments/production.rb`:
```rb
  config.serve_static_assets = true
```

Gem dependency update
=====================

### gemnasium

Install `gemnasium` gem.

Bootstrap with hook on `git push`.
```sh
gemnasium install --git
```

Edit `config/gemnasium.yml`.

Register new project on gemnasium.
```sh
gemnasium create
```

Sample icon for `README.md`.
```md
[![Dependency Status](https://gemnasium.com/heartsentwined/repo-name.png)](https://gemnasium.com/heartsentwined/repo-name)
```

Acknowledgement
===============

* <http://ryanbigg.com/2010/12/ubuntu-ruby-rvm-rails-and-you/>
* [Michael Hartl's tutorial](http://ruby.railstutorial.org/ruby-on-rails-tutorial-book)
