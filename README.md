# EmberCli::Deploy::Redis

[EmberCLI Rails] is a tool to unify your EmberCLI and Rails Workflows.

[ember-cli-deploy] is a simple, flexible deployment for your Ember CLI app.

[ember-cli-deploy-redis] is an `ember-cli-deploy` plugin to upload `index.html`
to a Redis store.

`ember-cli-rails-deploy-redis` is a gem that integrates all three.

This project streamlines the process of pushing and serving EmberCLI-built
assets from Rails.

[EmberCLI Rails]: https://github.com/thoughtbot/ember-cli-rails
[ember-cli-deploy]: http://ember-cli.com/ember-cli-deploy/
[ember-cli-deploy-redis]: https://github.com/ember-cli-deploy/ember-cli-deploy-redis

## Install

Add this line to your application's Gemfile:

```ruby
group :production do
  gem "ember-cli-rails-deploy-redis"
end
```

And then execute:

```bash
$ bundle install
```

## Setup

First, [configure your application to integrate with
`ember-cli-rails`][ember-cli-rails-setup].

Then, to integrate with `ember-cli-deploy`'s ["Lightning Fast Deploys"][lightning]
(using the Redis adapter) in `production`, instantiate configure EmberCLI-Rails
to deploy with the `EmberCli::Deploy::Redis` strategy:

[ember-cli-rails-setup]: https://github.com/thoughtbot/ember-cli-rails#setup

```ruby
# config/initializers/ember.rb

EmberCli.configure do |config|
  config.app :frontend, deploy: { production: EmberCli::Deploy::Redis }
end
```

Finally, set `ENV["REDIS_URL"]` to the URL [ember-cli-deploy-redis references][redis-config].

If you're deploying from Redis in `development` or `test`, disable
EmberCLI-Rails' build step by setting `ENV["SKIP_EMBER"] = true`.

[redis-config]: https://github.com/ember-cli-deploy/ember-cli-deploy-redis#configuration-options

## Deploy

Deploy your application through [ember-cli-deploy-redis][deploy].

[deploy]: https://github.com/ember-cli-deploy/ember-cli-deploy-redis#quick-start

## Use without `ember-cli-rails`

Although this gem was designed to integrate with `ember-cli-rails`, there isn't
a direct dependency on it.

Similar behavior can be achieved by using a gem like [`html_page`][html_page]:

[html_page]: https://github.com/seanpdoyle/html_page

```rb
require "html_page"

class EmberHelper
  def render_html(html)
    capturer = HtmlPage::Capture.new(self, &block)

    head, body = capturer.capture

    renderer = HtmlPage::Renderer.new(
      content: html,
      head: head,
      body: body,
    )

    render inline: renderer.render
  end
end

class EmberController
  def index
    redis = EmberCli::Deploy::Redis.new(ember_app)

    @html_from_redis = redis.index_html

    render layout: false
  end

  private

  def ember_app
    OpenStruct.new(name: "my-ember-app")
  end
end
```

```erb
<!-- app/views/ember/index.html.erb -->
<%= render_html @html_from_redis do |head, body| %>
  <% head.append do %>
    <title>Appended to the `head` element</title>
  <% end %>

  <% body.append do %>
    <p>Appended to the `body` element</p>
  <% end %>
<% end %>
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake rspec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/[USERNAME]/ember-cli-rails-deploy-redis. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](contributor-covenant.org) code of conduct.


## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).

