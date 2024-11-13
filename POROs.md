# Movie_poro_spec.rb
```ruby
require "rails_helper"

RSpec.describe Movie do
  it "exists and has attributes" do
    incoming_data = {
      #whatever structure data from API is.  Look at Postman here.  Or Service
    }

    movie = Movie.new(incoming_data)
    expect(movie).to be_a(Movie)
    expect(movie.title).to be_a(String)
    expect(movie.vote_ave).to be_a(Float)
  end
end

```
# movie_poro.rb
```ruby
class Movie
  attr_reader :title, :vote_ave, :id

  def initialize(incoming_data) #nest into this as much as needed for the attribute data. 
    @title = movie_data[:title]
    @vote_ave = movie_data[:vote_average]
    @id = movie_data[:id]
  end
end
```