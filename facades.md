# SPEC
```ruby
require 'rails_helper'

RSpec.describe MemberFacade do
  # VCR.use_cassette('cassette_name.yml') do
    it 'Returns a list of members', :vcr do
      result = MemberFacade.top_rated		
      r      = result.first

      expect(result).to be_an(Array)
      expect(r).to be_a(Member)
      expect(r.attribute).to be_an(data_type)
      expect(r.attribute).to be_a(data_type)
      expect(r.attribute).to be_a(data_type)
    end
  # end
end
```


# Facade.rb
```ruby
class MovieFacade
  def self.top_rated_movies
    #since there are params I put them here for call_db
    params = { language: "en-US", page: 1 }
    top_rated_movies = MovieService.call_api('3/movie/top_rated', params)[:results]

    top_rated_movies.map do |movie|
      Movie.new(movie)
      #now make a PORO 
    end
  end

  def self.searched_title(movie_title)
    params = { title: movie_title, language: "en-US", page: 1 }
    searched_movies = MovieService.call_api('/3/discover/movie', params)[:results]
    searched_movies.map do |movie|
      Movie.new(movie)
    end
  end

  def self.movie_reviews(movie_id)
    # movie_id = 278
    params = {language: "en-US", page: 1}
    movie_reviews = MovieService.call_api("/3/movie/#{movie_id}/reviews", params)[:results]
    movie_review = movie_reviews.first
  end
end

```