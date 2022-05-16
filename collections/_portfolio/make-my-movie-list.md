---
title: Make-My-Movie-List App
---

This app was inspired by the website <a href='https://myanimelist.net/' style='color: turquoise'>MyAnimeList.net</a>. This website aims to be a a place where people can rate movies, and curate their own list of favorite movies.

## Frontend

The divs were first developed in Figma before implementing the assets in my code. The planning for this website was done much more efficiently than my other projects. Over the course of this bootcamp I learned how to develop programs in way that will help me avoid future obstacles. I thought about the functionality I want to provide my users and how it would be best to implement them through components.
Knowing that I will be needing to do all my coding in a Home component instead of the default App component made implementing user authentication much easier than before. I started developing the project in the Home component and only after I figured out all the functionality for it I added user authentication towards the end.

```javascript
 <div className="App">
       { user ? (
        <Routes>
         <Route path='/' element={<Home
         handleLogout={handleLogout}
         setUser={setUser}
         changeUserpic={changeUserpic}
         user= {user}
         id = {user.id}/>}/>
          <Route path='/movies'element={<Nav />}>
            <Route path=':id' element={<ReviewPage  handleAddRev={handleAddRev}  movlist={movlist} setMovlist={setMovlist} />}/>
          </Route>
        <Route  path="/signup" element={<SignUp user={user} setUser={setUser} handleLogout={handleLogout} />} />
        <Route  path="/login" element={<Login setUser={setUser} user={user} />} />
        </Routes> ) : (
        <Routes>
        <Route  path="/login" element={<Login setUser={setUser} user={user} />} />
        <Route  path="/signup" element={<SignUp user={user} setUser={setUser} handleLogout={handleLogout} />} />
        </Routes>
     )}
    </div>);
    }
 export default App;
```

As you can see in the return, it checks to see if "user" exists before navigating to either the sign-up page or the home page of the app.

## Backend

The backend for this app was set up very similar to the last one I did except this time I added interaction with Active Storage. Active Storage is an in house method for Rails to facilitate uploading files to cloud storage services like Amazon S3, Google Cloud Storage, and Microsoft Azure Storage. For this program I used Google Cloud Storage, because it was free and had a lot documentation to help me throught the steps.

After downloading Active Record it loads a migration file to creat the following ActiveStorage Tables:

```ruby
 enable_extension "plpgsql"

  create_table "active_storage_attachments", force: :cascade do |t|
    t.string "name", null: false
    t.string "record_type", null: false
    t.bigint "record_id", null: false
    t.bigint "blob_id", null: false
    t.datetime "created_at", null: false
    t.index ["blob_id"], name: "index_active_storage_attachments_on_blob_id"
    t.index ["record_type", "record_id", "name", "blob_id"], name: "index_active_storage_attachments_uniqueness", unique: true
  end

  create_table "active_storage_blobs", force: :cascade do |t|
    t.string "key", null: false
    t.string "filename", null: false
    t.string "content_type"
    t.text "metadata"
    t.string "service_name", null: false
    t.bigint "byte_size", null: false
    t.string "checksum", null: false
    t.datetime "created_at", null: false
    t.index ["key"], name: "index_active_storage_blobs_on_key", unique: true
  end

  create_table "active_storage_variant_records", force: :cascade do |t|
    t.bigint "blob_id", null: false
    t.string "variation_digest", null: false
    t.index ["blob_id", "variation_digest"], name: "index_active_storage_variant_records_uniqueness", unique: true
  end
```

Active Storage now allows me to use the following macro to sync the attribute 'avatar' to the User class.

```ruby
class User < ApplicationRecord
    has_secure_password
    has_many :reviews
    has_one_attached :avatar


    def public_url
        "https://storage.googleapis.com/movie_bucket_list/#{self.avatar.key}"
    end
end
```

I had to create a custom method to navigate to the correct file name on Google Cloud, as it was changing the name to string value of the key.

The way the frontend handles this request looks like this:

```javascript
const handleSubmit = (event) => {
  event.preventDefault();

  const formData = new FormData();
  formData.append("avatar", userpic);
  fetch(`/users/${id}`, {
    method: "PATCH",
    body: formData,
  })
    .then((r) => r.json())
    .then(
      fetch("/me").then((r) => {
        if (r.ok) {
          r.json().then((user) => setUser(user));
        }
      })
    );
  ref.current.value = "";
  forceUpdate();
};
```

I had to send the file through FormData in the body of the request for the backend to then handle it. Google Cloud allows free storage for up to 5 gb which is a big part of why I opted for it over the other services.

Overall I had a great time making this website. It was an example of everything I've learned during my at the time at bootcamp while also testing my ability to adapt new technologies into my assignments. I really enjoyed teh overall process and seeing how far I've come since the day one.
For more information please click on the image below for a direct link to the GitHub page.

<br><br>

<a href='https://github.com/physicalrobot/my-movie-list'><img src="{{site.url }}{{site.baseurl}}/src/assets/img/postpix/movies.jpg" alt="movies" style='width:1000px' /></a>
