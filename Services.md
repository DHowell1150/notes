# Spec
```ruby
require "rails_helper"

RSpec.describe MovieService do
  it "can return title, vote average, id" do
    # #call_db takes two arguments: endpoint and params if there are params
    params = {}
    query = MovieService.call_api('/3/discover/movie')[:results]
    movie = query.first
    expect(query).to be_a(Array)
    expect(movie).to be_a(Hash)

    #the list of what want to query
    expect(movie[:original_title]).to be_a(String)
    expect(movie[:vote_average]).to be_a(Float)
    expect(movie[:id]).to be_a(Integer)
  end

  it "can get review data from review endpoint" do
    movie_id = 278
    params = {language: "en-US", page: 1}
    query = MovieService.call_api('/3/discover/movie')[:dig_if_needed]
    # movie = query.first
    # expect(query).to be_a(Array)

    # #the list of what want to query
    # expect(movie[:original_title]).to be_a(String)
    # expect(movie[:vote_average]).to be_a(Float)
    # expect(movie[:id]).to be_a(Integer)
  end
end

```


# .rb File
```ruby
class MovieService 
  # Only thing to change is key and url
  def self.call_api(endpoint, params = {})
    response = conn.get(endpoint) do |request|
      # params and api key will now get passed in the request
      request.params = params #This MUST come before the next line. Establish the hash here ##Check data structure is what you want here
      request.params[:api_key] = Rails.application.credentials.movies[:key] #Add to the hash here
    end
    parse_data(response)
  end

  private

  def self.conn
    Faraday.new(url: 'https://api.themoviedb.org')
  end

  def self.parse_data(response)
    JSON.parse(response.body, symbolize_names: true)
  end
end
```