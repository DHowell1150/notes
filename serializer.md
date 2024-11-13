[serializer notes](../NOTES/Serializers/serializers.md)
# USING GEM

### REQUEST_SPEC
```ruby
require 'rails_helper'

describe "Books API" do
  it "sends a list of books" do
    store1 = create(:store)
    store2 = create(:store)
    book1 = create(:book)
    book2 = create(:book)
    
    StoreBook.create(store: store1, book: book1, book_price: 10, quantity: 10)
    StoreBook.create(store: store1, book: book2, book_price: 100, quantity: 3)
    get '/api/v1/books'

    expect(response).to be_successful

    books = JSON.parse(response.body, symbolize_names: true)[:data]

    expect(books.count).to eq(2)

    books.each do |book|
      expect(book).to have_key(:id)
      expect(book[:id]).to be_an(String)

      expect(book).to have_key(:type)
      expect(book[:type]).to be_a(String)

      expect(book).to have_key(:attributes)
      expect(book[:attributes]).to be_a(Hash)

      expect(book).to have_key(:relationships)
      expect(book[:relationships]).to be_a(Hash)
    end

    first_book = books.first
    expect(first_book[:attributes][:title]).to eq(book1.title)
    expect(first_book[:attributes][:author]).to eq(book1.author)
    expect(first_book[:attributes][:genre]).to eq(book1.genre)
    expect(first_book[:attributes][:summary]).to eq(book1.summary)
    expect(first_book[:attributes][:number_sold]).to eq(book1.number_sold)
    expect(first_book[:attributes][:popularity]).to eq(book1.popularity)

    expect(first_book[:relationships][:stores][:data].count).to eq(1)
    expect(first_book[:relationships][:stores][:data][0]).to have_key(:id)
    expect(first_book[:relationships][:stores][:data][0]).to have_key(:type)
  end

  it "can get one book by its id" do
    # id = create(:book).id
  
    store1 = create(:store)
    store2 = create(:store)
    book1 = create(:book)

    StoreBook.create(store: store1, book: book1, book_price: 10, quantity: 10)
    StoreBook.create(store: store2, book: book1, book_price: 90, quantity: 4)

    get "/api/v1/books/#{book1.id}"
  
    book = JSON.parse(response.body, symbolize_names: true)[:data]

    expect(book).to have_key(:id)
    expect(book[:id]).to be_an(String)

    expect(book).to have_key(:type)
    expect(book[:type]).to be_a(String)

    expect(book).to have_key(:attributes)
    expect(book[:attributes]).to be_a(Hash)

    expect(book).to have_key(:relationships)
    expect(book[:relationships]).to be_a(Hash)

    expect(book[:attributes][:title]).to eq(book1.title)
    expect(book[:attributes][:author]).to eq(book1.author)
    expect(book[:attributes][:genre]).to eq(book1.genre)
    expect(book[:attributes][:summary]).to eq(book1.summary)
    expect(book[:attributes][:number_sold]).to eq(book1.number_sold)
    expect(book[:attributes][:popularity]).to eq(book1.popularity)

    expect(book[:relationships][:stores][:data].count).to eq(2)
    expect(book[:relationships][:stores][:data][0]).to have_key(:id)
    expect(book[:relationships][:stores][:data][0]).to have_key(:type)
  end
end
```

### Serializer
```ruby
class ForecastSerializer
  include JSONAPI::Serializer
  set_id { nil } #if your id is nil
  attributes :current, :daily, :hourly
  
  attribute :num_books do |book|
    store.books_count #this is a model method
  end 
end
```

##### Controller
```ruby
class Api::V1::BooksController < ApplicationController
  def index
    # render json: Book.all
    render json: BookSerializer.new(Book.all)
  end

  def show
    render json: BookSerializer.new(Book.find(params[:id]))
  end
end
```


### HANDROLLING
```ruby 

def self.format_stores(stores)
   {
     data: stores.map do |store|
       {
         id: store.id.to_s,
         type: "store",
         attributes: {
           name: store.name,
           num_books: store.books_count
         },
         relationships: serialize_books(store)
       }
     end
   }
 end

 def self.serialize_books(store)
   {
     books: {
       data: store.books.map do |book|
         {
           id: book.id,
           type: "book"
         }
       end
     }
   }
 end
```

##### Controller 
```ruby
class Api::V1::BooksController < ApplicationController
  def index
    # render json: Book.all
    render json: BookSerializer.new(Book.all)
  end

  def show
    render json: BookSerializer.new(Book.find(params[:id]))
  end
end
```