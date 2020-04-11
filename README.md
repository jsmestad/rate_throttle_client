# RateThrottleClient

Rate limiting is for servers, rate throttling is for clients. This library implements a number of strategies for handling rate throttling on the client and a methodology for comparing performance of those clients in simulated environments. Essentially, we don't just give you the code to rate throttle, we also give you the information to help you figure out the best strategy to rate throttle as well.


## Installation

Add this line to your application's Gemfile:

```ruby
gem 'rate_throttle_client'
```

And then execute:

    $ bundle install

Or install it yourself as:

    $ gem install rate_throttle_client

## Usage

Wrap requests to an API endpoint using one of the provided rate throttling classes:

```ruby
client = RateThrottleClient::ExponentialIncreaseProportionalRemainingDecrease.new
response = client.call do
  Excon.get("https://api.example.com")
end
```

If the server returns a 429 status (the HTTP status code indicating that a server side rate limit has been reached) then the request will be retried according to the classes' strategy.

## Response interface

The rate throttling classes assume that anything you return from the block will have a `status` method as well as a `headers` method. If your HTTP client does not return a response object with these methods, then you can wrap your code in a custom object for compatability with this library.

## Benchmarks

These benchmarks are generated by running `rake bench` against the simulated "GCRA" rate limiting server. Which throttle strategy you use depends on your needs.

### RateThrottleClient::ExponentialBackoff results (duration: 30.0 minutes, multiplier: 1.2)

```
Avg retry rate:      80.41 %
Max sleep time:      46.72 seconds
Stdev Request Count: 147.84

Raw max_sleep_vals: [46.72, 46.72, 46.72, 46.72, 46.72, 46.50, 46.50, 46.50, 46.50, 46.50]
Raw retry_ratios: [0.79, 0.79, 0.81, 0.82, 0.80, 0.80, 0.79, 0.81, 0.82, 0.81]
Raw request_counts: [1317.00, 1314.00, 1015.00, 963.00, 1254.00, 1133.00, 1334.00, 1025.00, 1024.00, 1036.00]
```

```
Time to clear workload (4500 requests, starting_sleep: 1s):
76.18 seconds
```


### RateThrottleClient::ExponentialIncreaseGradualDecrease results (duration: 30.0 minutes, multiplier: 1.2)

```
Avg retry rate:      40.56 %
Max sleep time:      139.91 seconds
Stdev Request Count: 867.73

Raw max_sleep_vals: [110.25, 110.25, 110.25, 110.25, 110.25, 139.91, 139.91, 139.91, 139.91, 139.91]
Raw retry_ratios: [0.46, 0.37, 0.38, 0.37, 0.39, 0.40, 0.41, 0.35, 0.37, 0.57]
Raw request_counts: [48.00, 57.00, 56.00, 49.00, 282.00, 85.00, 83.00, 79.00, 2821.00, 37.00]
```

```
Time to clear workload (4500 requests, starting_sleep: 1s):
65.50 seconds
```


### RateThrottleClient::ExponentialIncreaseProportionalDecrease results (duration: 30.0 minutes, multiplier: 1.2)

```
Avg retry rate:      3.66 %
Max sleep time:      17.31 seconds
Stdev Request Count: 101.94

Raw max_sleep_vals: [17.31, 17.31, 17.31, 17.31, 17.31, 17.21, 17.21, 17.21, 17.21, 17.21]
Raw retry_ratios: [0.01, 0.07, 0.03, 0.05, 0.06, 0.01, 0.07, 0.01, 0.03, 0.03]
Raw request_counts: [343.00, 123.00, 223.00, 144.00, 128.00, 348.00, 116.00, 383.00, 194.00, 203.00]
```

```
Time to clear workload (4500 requests, starting_sleep: 1s):
489.24 seconds
```


### RateThrottleClient::ExponentialIncreaseProportionalRemainingDecrease results (duration: 30.0 minutes, multiplier: 1.2)

```
Avg retry rate:      3.07 %
Max sleep time:      17.32 seconds
Stdev Request Count: 78.44

Raw max_sleep_vals: [12.14, 12.14, 12.14, 12.14, 12.14, 17.32, 17.32, 17.32, 17.32, 17.32]
Raw retry_ratios: [0.03, 0.02, 0.01, 0.02, 0.03, 0.03, 0.02, 0.05, 0.04, 0.07]
Raw request_counts: [196.00, 269.00, 386.00, 302.00, 239.00, 197.00, 265.00, 150.00, 187.00, 118.00]
```

```
Time to clear workload (4500 requests, starting_sleep: 1s):
66.92 seconds
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake test` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/[USERNAME]/rate_throttle_client. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [code of conduct](https://github.com/[USERNAME]/rate_throttle_client/blob/master/CODE_OF_CONDUCT.md).

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).

## Code of Conduct

Everyone interacting in the RateThrottleClient project's codebases, issue trackers, chat rooms and mailing lists is expected to follow the [code of conduct](https://github.com/[USERNAME]/rate_throttle_client/blob/master/CODE_OF_CONDUCT.md).
