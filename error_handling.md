# EXCEPTION HANDLING

## SPEC
```ruby
describe "Books API" do
  ... 

  describe 'sad paths' do
    it "will gracefully handle if a book id doesn't exist" do
      get "/api/v1/books/1"

      expect(response).to_not be_successful
      expect(response.status).to eq(404)

      data = JSON.parse(response.body, symbolize_names: true)
      
      expect(data[:errors]).to be_a(Array)
      expect(data[:errors].first[:status]).to eq("404")
      expect(data[:errors].first[:title]).to eq("Couldn't find Book with 'id'=1")
    end
  end
end
```

## CONTROLLER
```RUBY
def show
  begin
    render json: BookSerializer.new(Book.find(params[:id]))
  rescue ActiveRecord::RecordNotFound => exception
    render json: ErrorSerializer.new(ErrorMessage.new(exception.message, 404))
      .serialize_json, status: :not_found
  end
end
```



## PORO
```ruby
class ErrorMessage
  attr_reader :message, :status_code

  def initialize(message, status_code)
    @message = message
    @status_code = status_code
  end
end
```
## SERIALIZER
```RUBY
class ErrorSerializer
  def initialize(error_object)
    @error_object = error_object
  end

  def serialize_json
    {
      errors: [
        {
          status: @error_object.status_code.to_s,
          title: @error_object.message
        }
      ]
    }
  end
end
```

# RESCUE_FROM
```ruby
class Api::V1::BooksController < ApplicationController
  rescue_from ActiveRecord::RecordNotFound, with: :not_found_response

  ...

  def show
    render json: BookSerializer.new(Book.find(params[:id])) 
  end

  ...

  def update
    book = Book.find(params[:id])
    book.update(book_params)
    render json: BookSerializer.new(book)
  end
  
  def destroy
    book = Book.find(params[:id])
    book.destroy
    render json: BookSerializer.new(book)
  end

  private
 
  def not_found_response(exception)
    render json: ErrorSerializer.new(ErrorMessage.new(exception.message, 404))
      .serialize_json, status: :not_found
  end
end
