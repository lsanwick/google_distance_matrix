# GoogleDistanceMatrix

[![Build Status](https://travis-ci.org/Skalar/google_distance_matrix.svg?branch=master)](https://travis-ci.org/Skalar/google_distance_matrix)

Ruby client for the [Google Distance Matrix API](https://developers.google.com/maps/documentation/distance-matrix/intro).

This lib makes Google's distance matrix API easy to work with,
allowing you to set up some origins and destinations and
pull the distance matrix from Google.

Once you have the matrix you can fetch all routes from a given
origin or to a given destination.

The matrix may also be used as a data set for traveling salesman problem,
but to solve it you may look at <http://ai4r.org/>.




## Examples

### Set up a matrix to work with

```ruby
matrix = GoogleDistanceMatrix::Matrix.new
```

### Create some places to be used as origins or destinations for the matrix
```ruby
lat_lng = GoogleDistanceMatrix::Place.new lng: 12, lat: 12
address = GoogleDistanceMatrix::Place.new address: "My address, Oslo"
dest_address = GoogleDistanceMatrix::Place.new address: "Home, Oppegaard"

point_dest = Point.new lat: 1, lng: 2
dest_object = GoogleDistanceMatrix::Place.new point_dest
```
### Add places to matrix's origins and destinations
```ruby
matrix.origins << lat_lng << address
matrix.destinations << dest_address << dest_object

# Added objects will be wrapped in a place automatically, so you may skip manyally creating Places.
another_point = Point.new lat: 1, lng: 3
matrix.origins << another_point
```
### Do some configuration - see GoogleDistanceMatrix.configure_defaults as well.
```ruby
matrix.configure do |config|
  config.mode = 'driving'
  config.avoid = ['tolls']

  # To build signed URLs to use with a Google Business account.
  config.google_business_api_client_id = "123"
  config.google_business_api_private_key = "your-secret-key"

  # If you have an API key, you can specify that as well.	
  config.google_api_key = "YOUR_API_KEY"
end
```
### Get the data for the matrix

`matrix.data` returns the data, loaded from Google, for this matrix.
 
 It is a multi dimensional array. Rows are ordered according to the values in the origins.
 Each row corresponds to an origin, and each element within that row corresponds to a pairing of the origin with a destination.


### Query the matrix

Returns an array of Google::DistanceMatrix::Route, all having given origin or destination.

```ruby
matrix.routes_for dest_address
```

Returns Google::DistanceMatrix::Route with given origin and destination

```ruby
matrix.route_for origin: lat_lng, destination: dest_address
# Google::DistanceMatrix::Route with one origin and a destination, together with route data
matrix.shortest_route_by_distance_to(dest_address) 
# Google::DistanceMatrix::Route with one origin and a destination, together with route data
matrix.shortest_route_by_duration_to(dest_address) 
```

In cases where you built the place with an object (not hash with attributes) you may provide that object as well asking for routes. This is true for `route_for` and `shortest_route_by_*` as well.

```ruby
matrix.routes_for point_dest # Returns routes for dest_object
```

You may call query methods with a bang, in which case it will fail with an error if not all of the routes in your result set for the called method are ok.


## Installation

Add this line to your application's Gemfile:

```ruby
gem 'google_distance_matrix'
```
And then execute:

    $ bundle

Or install it yourself as:

    $ gem install google_distance_matrix


## Configuration

Configuration is done directly on a matrix or via `GoogleDistanceMatrix.configure_defaults`.
Apart from configuration on requests it is also possible to provide your own logger class and
set a cache.

### Request cache

Given Google's limit to the service you may have the need to cache requests. This is done by simply
using URL as cache keys. Cache we'll accept should provide a default ActiveSupport::Cache::Store interface.

```ruby
GoogleDistanceMatrix.configure_defaults do |config|
    config.cache = ActiveSupport::Cache.lookup_store :your_store, {
        expires_in: 12.hours
        # ..or other options you like for your store
    }
end
```


## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
