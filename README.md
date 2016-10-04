# Velocify
[![Gem Version](https://badge.fury.io/rb/velocify.svg)](http://badge.fury.io/rb/velocify)
[![Code Climate](https://codeclimate.com/github/pennymac/velocify/badges/gpa.svg)](https://codeclimate.com/github/pennymac/velocify)
[![Build Status](https://travis-ci.org/pennymac/velocify.svg)](https://travis-ci.org/pennymac/velocify)
[![Test Coverage](https://codeclimate.com/github/pennymac/velocify/badges/coverage.svg)](https://codeclimate.com/github/pennymac/velocify/coverage)

## Velocify Pulse
[Velocify Pulse](http://lmhelp.velocify.com/hc/en-us/sections/200304510-Velocify-for-Salesforce-General-Information-Summer-14-Fall-14-Winter-15-)


## Installation

Add this line to your application's Gemfile:

```ruby
gem 'velocify'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install velocify

## Usage

Before getting started, you must export two environment variables: ```VELOCIFY_USERNAME``` and ```VELOCIFY_PASSWORD```.
This gem uses Dotenv, so you can add these variables to your ```.env``` file in the root folder of your project.

You can also programmatically configure the username and password:

``` ruby
Velocify.configure do |config|
  config.username = "myusername"
  config.password = "mypassword"
end
```

``` ruby
require 'velocify'

statuses = Velocify::Status.find_all
# => {statuses=>status=>[{...}, ...]}

dormant_id = Velocify::ResponseReader.read(kind: :status, response: statuses).find_id_by_title 'Dormant'
# => 31

statuses_with_name = Velocify::ResponseReader.read(kind: :status, response: statuses).search_by_title 'Name'
# => [{...}, ...]

leads = Velocify::Lead.find_last_created
# => {leads=>lead=>{...}}

# There's also support for destructuring

success, leads = Velocify::Lead.find_all from: '2000-01-01T00:00:00', to: '2000-02-01T00:00:00', destruct: true
# => [true, {leads=>lead=>[{...}]}]
success
# => true
payload
# => {leads=>lead=>[{ ... }]}

# You can even have your results returned as an array

success, leads = Velocify::Lead.find_all from: '2000-01-01T00:00:00', to: '2000-02-01T00:00:00', destruct: true, return_array: true
# => [true, [{...}, ... }]

# Pushing leads to Velocify
campaign_id = Velocify::ResponseReader.read(kind: :campaign, response: Velocify::Campaign.find_all).find_id_by_title 'Test Campaign'
status_id = Velocify::ResponseReader.read(kind: :status, response: Velocify::Status.find_all).find_id_by_title 'Active'
last_name_id = Velocify::ResponseReader.read(kind: :field, response: Velocify::Field.find_all).find_id_by_title 'Last Name'
first_name_id = Velocify::ResponseReader.read(kind: :field, response: Velocify::Field.find_all).find_id_by_title 'First Name'

lead = Velocify::Lead.new
lead.campaign_id = campaign_id
lead.status_id = status_id
lead.add_field id: first_name_id, value: "Joe"
lead.add_field id: last_name_id, value: "Bo"

Velocify::Lead.add leads: [ lead ]
```

## Changes

For a list of changes in each release, read the CHANGELOG.

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake test` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/pennymac/velocify. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [Contributor Covenant](http://contributor-covenant.org) code of conduct.

## License

The gem is available as open source under the terms of the [MIT License](http://opensource.org/licenses/MIT).
