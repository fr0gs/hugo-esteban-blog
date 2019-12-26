+++
author = "Esteban"
date = 2017-05-15T12:37:03Z
description = "Minimal working setup of the Rails 5 API and the ActiveAdmin library."
draft = false
slug = "minimal-working-setup-rails-5-api-activeadmin"
title = "Minimal working setup Rails 5 API + ActiveAdmin"

+++


First of all add the **ActiveAdmin** and the **Devise gems**. Devise is needed because ActiveAdmin will use it as the authentication mechanism. 

*Gemfile*:

```ruby
gem 'devise', '> 4.x'
gem 'activeadmin', github: 'activeadmin'
```

We install the gems:

```sh
bundle install
```

Then we modify several files in order to adapt the Rails API application:

**app/controllers/application_controller.rb**:

* ActionController::API changed to ActionController::Base
* Added protection against [XSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery) attacks.

```ruby
class ApplicationController < ActionController::Base
  include ActionController::Serialization
  
  protect_from_forgery :with => :exception
end
```

**config/application.rb**:
We need to change **config.api_only** to false in order to enable several middleware needed to make Devise work. Otherwise it will always redirect to login page by default. 

```ruby
module YourApp
  class Application < Rails::Application
    config.api_only = false
  end
end
```

Install the ActiveAdmin system in the application (this will create an AdminUser model):

```sh
rails generate active_admin:install
```

If you want to use an existing model as admin run:

```sh
rails generate active_admin:install User
```

Create the tables and seed the data:

```sh
rails db:migrate
rails db:seed
```

This is the bare minimum that I needed to successfully have the admin page and log into the dashboard.

