# Stretcher

A concise, fast,  ElasticSearch client designed to reflect the actual elastic search API as closely as possible, making minimal allowances for convenience.

# Features

* Cleanly matches up to elastic search's JSON api
* Efficiently re-uses connections on a per-server object basis (via net/http/persistent)
* Supports efficient bulk indexing operations
* Returns most responses in convenient Hashie::Mash form
* Easily extensible
* Configurable logging

## Installation

Add this line to your application's Gemfile:

    gem 'stretcher'

## Usage

### Basic Usage

```ruby   
# First Create a server
server = Stretcher::Server.new('http://localhost:9200')
# Delete an index (in case you already have this one)
server.index('foo').delete rescue nil
# Create an index
server.index('foo').create(mapping: {tweet: {properties: {text: 'string'}}})
# Add a document
server.index('foo').type('tweet').put(123, {text: 'Hello'})
# Retrieve a document
server.index('foo').type('tweet').get(123)
# Perform a search (Returns a Stretcher::SearchResults instance)
res = server.index('foo').search({size: 12}, {query: {match_all: {}}})
res.class   # Stretcher::SearchResults
res.total   # => 1
res.facets  # => nil
res.results # => [#<Hashie::Mash _id="123" text="Hello">]
res.raw     # => #<Hashie::Mash ...> Raw JSON from the search
```

### MultiSearch
    
```ruby
server.index('foo').msearch([{query: {match_all: {}}}])
# => Returns an array of Stretcher::SearchResults
```

### Bulk Indexing
    
```ruby
docs = [{"_type" => "tweet", "_id" => 91011, "text" => "Bulked"}]
server.index('foo').bulk_index(docs)
```

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
