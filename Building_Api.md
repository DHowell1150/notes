[Lesson](https://github.com/turingschool/backend-curriculum-site/blob/gh-pages/module3/lessons/exercises/building_an_api.md)
# Start an application

`rails new building_internal_apis -T -d="postgresql" --api`

## SPEC RESTful flow
```ruby
# spec/requests/api/v1/books/request_spec.rb

require 'rails_helper'

describe 'Books API' do
  describe "Index" do 
    it "Sends a list of books" do
      create_list(:book, 3) #FACTORY BOT
      get '/api/v1/books'
      expect(response).to be_successful
      books = JSON.parse(response.body, symbolize_names: true)

      books.each do |book|
        expect(book).to be_a(Hash)

        expect(book).to have_key(:id)
        expect(book[:id]).to be_a(Integer)

        expect(book).to have_key(:title)
        expect(book[:title]).to be_a(String)

        expect(book).to have_key(:author)
        expect(book[:author]).to be_a(String)

        expect(book).to have_key(:genre)
        expect(book[:genre]).to be_a(String)

        expect(book).to have_key(:summary)
        expect(book[:summary]).to be_a(String)

        expect(book).to have_key(:number_sold)
        expect(book[:number_sold]).to be_a(Integer)
      end
    end
  end

  describe "show" do
    it "Sends a book" do
      id = create(:book).id
      get "/api/v1/books/#{id}"
      expect(response).to be_successful
      book = JSON.parse(response.body, symbolize_names: true)

      expect(book).to be_a(Hash)

      expect(book).to have_key(:id)
      expect(book[:id]).to be_a(Integer)

      expect(book).to have_key(:title)
      expect(book[:title]).to be_a(String)

      expect(book).to have_key(:author)
      expect(book[:author]).to be_a(String)

      expect(book).to have_key(:genre)
      expect(book[:genre]).to be_a(String)

      expect(book).to have_key(:summary)
      expect(book[:summary]).to be_a(String)

      expect(book).to have_key(:number_sold)
      expect(book[:number_sold]).to be_a(Integer)
    end
  end

  describe "create" do 
    it "can create a book" do 
      book_params = ({
        title: "Atomic Habits", 
        author: "James Clear", 
        genre: "self_help",
        summary: "kick ass", 
        number_sold: 1000000
      })
      headers = {"CONTENT_TYPE" => "application/json"}
      post "/api/v1/books", headers: headers, params: JSON.generate(book: book_params)
      created_book = Book.last

      expect(response).to be_successful
      expect(created_book[:title]).to eq(book_params[:title])
      expect(created_book[:author]).to eq(book_params[:author])
      expect(created_book[:genre]).to eq(book_params[:genre])
      expect(created_book[:summary]).to eq(book_params[:summary])
      expect(created_book[:number_count]).to eq(book_params[:number_count])
    end

    it "can update a book" do 
      id = create(:book).id
      previous_title = Book.last.title
      book_params = { title: "Charlotte's Web"}
      headers = {"CONTENT_TYPE" => "application/json"}

      patch "/api/v1/books/#{id}", headers: headers, params: JSON.generate(book: book_params)
      book = Book.find_by(book_params[id: id])
      expect(response).to be_successful
      expect(book.title).to eq("Charlotte's Web")
      expect(book.title).not_to eq(previous_title)
    end
  end

  describe "delete" do 
    it "can destroy a book" do
      book = create(:book)
      expect(Book.count).to eq(1)
      delete "/api/v1/books/#{book.id}"

      expect(response).to be_successful
      expect(Book.count).to eq(0)
      expect{Book.find(book.id)}.to raise_error(ActiveRecord::RecordNotFound)
    end
  end
end
```

## Controller
- Without Serializer
```ruby
class Api::V1::BooksController < ApplicationController
  def index
    render json: Book.all
  end

  def show
    render json: Book.find(params[:id])
  end

  def create
    render json: Book.create(book_params)
  end

  def update
    render json: Book.update(params[:id], book_params)
  end

  def destroy
    render json: Book.delete(params[:id])
  end
  private
  
  def book_params
    params.require(:book).permit(:title, :author, :genre, :summary, :number_count)
  end
end
```

- With Serializer




Notes: feature vs request_spec
1. Feature
visit "endpoint
type:feature OR nest it under feature
describe/it block?

2. Request:
get "endpoint"
type:request OR nest it under request
describe/scenario?