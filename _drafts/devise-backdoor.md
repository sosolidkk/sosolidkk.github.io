---
layout: post
title: Devise Backdoor
date: 2024-02-07 22:44 -0300
tags: ruby rails
---

It's common knowledge that end-to-end tests are one of the more demanding and heavy to execute types of tests in an application. Every developer is tired of looking into the same image of the test pyramid that keeps the end-to-end tests at the top of it and tired of being recommended that most of the tests should be unit or integration with fewer end-to-end tests.

Unfortunately, most developers don't have the luck to work on a new or correctly maintained project, that follows the recommended guidelines and such. For those cases, we can only look for alternative approaches to speed up the test execution. Below, I'll try to summarize a couple of those commonly explained approaches and one different approach that I saw at the Thoughtbot authentication gem clearance and believed was a genius idea and how I took the same idea and applied it in my current company toolset.

<!-- Explain the common test cases -->

<!-- Explain common speed up tools and alternatives -->

<!-- Explain your approach -->

<!-- Show some real usage data -->

```ruby
module Middlewares
  # Middleware which allows signing in by passing as=USER_ID in a query
  # parameter.
  #
  # Designed to eliminate time in integration tests wasted by visiting and
  # submitting the sign in form.
  #
  # This code snippet is heavily inspired by and based on the Toughtbot Clearance gem Backdoor
  # For more details, see the source code here:
  # https://github.com/thoughtbot/clearance/blob/main/lib/clearance/back_door.rb
  #
  # @example Usage in Rails application:
  #   In config/application.rb or an initializer
  #     Rails.application.config.middleware.use Middlewares::Backdoor
  #
  # @example Usage URL with query parameter to sign in as a specific user:
  #   http://localhost:3000/some_path?as=123
  #
  class Backdoor
    # @see https://stackoverflow.com/a/44747266
    include Warden::Test::Helpers

    # Initialize the Backdoor Middleware
    #
    # @param [Object] app ActionDispatch::Routing::RouteSet
    #   The application to call.
    #
    def initialize(app, &block)
      raise error_message unless allowed?

      @app = app
      @block = block
    end

    # Calls the Middleware operation
    #
    # @param [Hash] env The Rack environment keys and values.
    #
    def call(env)
      run(env)
      @app.call(env)
    end

    private

    # Run the Backdoor middleware.
    #
    # @param [Hash] env The Rack environment keys and values.
    #
    # @return [void]
    #
    def run(env)
      params = Rack::Utils.parse_query(env['QUERY_STRING'])
      user_param = params.fetch('as')

      return if user_param.blank?

      user = User.find_by(id: user_param)

      return if user.blank?

      login_as(user, scope: :user)
      Rails.logger.info("Backdoor: Signed in as User #{user.username} [id: #{user.id}]")
    end

    # Check if the Backdoor is allowed in the current environment.
    #
    # @return [Boolean] true if allowed, false otherwise.
    #
    def allowed?
      allowed_environments.include?(Rails.env)
    end

    # List of environments where the Backdoor is allowed.
    #
    # @return [Array<String>] List of allowed environments.
    #
    def allowed_environments
      %w[staging development]
    end

    # Error message when Backdoor is not allowed in the current environment.
    #
    # @return [String] The error message.
    #
    def error_message
      <<-MSG.squish
        Can't use auth backdoor outside of
        configured environments (#{allowed_environments.join(', ')}).
      MSG
    end
  end
end
```

### References
- https://gist.github.com/kddnewton/66f2e7749535f30f868d36021056fd15
