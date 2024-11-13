`expect(page).to have_css('.food', count: 10)`



# Cydnee's TABLE example  
## spec
```ruby
it 'Top Rated Movie is a link to Movie Details and displays vote_average', :vcr do
  visit user_movies_path(@user_1, top_rated: true)
  within '.search-results' do #SECTION -> <table>
  #This works with webmock and fixturefile/vcr so we can see what id # it is
    within '#movie-278' do #DIV  -> <tr>  ->  <td>
      expect(page).to have_content("Title: The Shawshank Redemption")
      expect(page).to have_link("The Shawshank Redemption")
      expect(page).to have_content("Vote Average: 8.706")
      expect(page).to have_content("Operating Hours HASH: { DAY1: hour1, DAY2: hour2, DAY3: hour3 }")
    end
  end
end
```

## index.html.erb
```ruby
<section class="search-results">
  <table>
    <% @movies.each do |movie| %>
      <div id="movie-<%= movie.id %>">
        <tr>
          <td>Title: <%= link_to "#{movie.title}", user_movie_path(user_id: @user.id, id: movie.id) %></td>
          <td>Vote Average: <%= movie.vote_average %></td>
          <td>Standard Hours:</td>
          <td>
            <ul>
              <% park.standard_hours.each do |day, hours| %>
                <li><%= day.capitalize %>: <%= hours %></li>
              <% end %>
            </ul>
          </td>
        </tr>
      </div>
    <% end %>
  </table>
</section>
```

# EG 1
## spec
```ruby
it "They see a list of existing users, which links to the individual user's dashboard" do
  within("#existing_users") do 
    expect(page).to have_content(User.first.email)
    expect(page).to have_content(User.last.email)
    expect(page).to have_link("#{User.first.email}", href: "users/#{User.first.id}")
    expect(page).to have_link("#{User.last.email}", href: "users/#{User.last.id}")
  end   
end

```

## index.html.erb
```html
<h1>Viewing Party</h1>

<h2>Existing Users</h2>
<ul id="existing_users">
   <% @users.each do |user| %>
      <li><%= link_to user.email, "users/#{user.id}" %></li>
   <% end %>
</ul>
```


# EG 2
## spec
```ruby
require "rails_helper"

feature "user can search for members" do

  scenario "user submits valid state name" do
    visit '/'

    select "Utah", from: :state
    click_on "Locate Representatives"
    expect(current_path).to eq(search_path)

    within(first(".member")) do
      expect(page).to have_css(".name")
      expect(page).to have_css(".party")
      expect(page).to have_css(".state")
    end
  end
end

```

## index.html.erb
```html
<h1> <%= @facade.members_by_state.count %> Results </h1>
<% @facade.members_by_state.each do |member| %>
  <ul class="member">
    <li class="name"><%= member.name %></li>
    <li class="party"><%= member.party %></li>
    <li class="state"><%= member.state %></li>
    <br>
  </ul>
<% end %>

```


# EG 3
## spec
```ruby

```

## index.html.erb
```html

```


# EG 4
## spec
```ruby

```

## index.html.erb
```html

```


# EG 5
## spec
```ruby

```

## index.html.erb
```html

```

