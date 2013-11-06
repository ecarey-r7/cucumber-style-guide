# Cucumber Style Guide
A style guide used by the ControlsInsight team at Rapid7's cucumber suite.

## Table of Contents
1. [Gherkin](#gherkin)
1. [Step Definitions](#step-definitions)
1. [Hooks](#hooks)
1. [Code Portability](#code-portability)
1. [Tags](#tags)
1. [Utilizing DSL Methods](#utilizing-dsl-methods)

## Gherkin
```gherkin
Feature: Login page
  As a controlsinsight user
  I want to visit the login page
  In order to gain access to information on my security controls

  Scenario: Invalid user
    Given I have opened the controlsinsight login page
    When I try to login as "johndoe" with the password "Secret123"
    Then I should see the error:
      """
        Sorry. Those credentials did not work. Please try again.
      """
```

## Step Definitions
```ruby
# features/support/env.rb
require 'rspec'
require 'rspec/expectations'
require 'capybara/cucumber'
```
```ruby
# features/step_definitions/login_steps.rb
Given "I have opened the controlsinsight login page" do
  visit '/'
end

When /I try to login as "([^"]+)" with the password "([^"]+)"/ do |username, password|
  fill_in 'username', :with => username
  fill_in 'password', :with => password

  click_link 'Log on'
end

Then 'I should see the error:' do |expected_error|
  expect(find('div.alert').text).to eq(expected_error)
end
```

## Code Portability
```ruby
# lib/helpers/ui_helpers.rb
module UIHelpers
  def login(username, password)
    visit '/'
    
    expect(find('.login-wrapper').text).to eq('Welcome to ControlsInsight by Rapid7 LOG ON')

    fill_in 'username', :with => username
    fill_in 'password', :with => password

    click_on 'Log on'
  end
end

World(UIHelpers)
```

You can now call login in a Given step so that you don't need make obscure code by calling steps from within each other.
```ruby
# features/step_definitions/login_steps.rb
Given "I have logged into controlsinsight" do
  login ENV['CONTROLS_USERNAME'], ENV['CONTROLS_PASSWORD']
end
```

## Tags
### Gherkin
### Tagged Hooks
## Utilizing DSL Methods
