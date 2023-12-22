---
title: Fitness-Tracker App w/ User Authentication
---

The idea for this app came to me fairly quickly. It's a new years resolution of mine to lose weight and hit the gym more this year, so I naturally thought of something that can help motivate me towards that goal. For this project I had to showcase the manipulation of a database written in Ruby through a front end written in React.

## Frontend

This was fun because I actually put this frontend together using Figma. There was a slight learning curve but luckily my girlfriend uses it for her job and was nice enough to teach me a bit. From there I created the components and rendered them inside App.js.
I then wrote up divs for the css I conjured up in Figma and placed them in the appropriate components.

The difference between this version of the project and my last is that it includes a route for logging the user in as well as a sign up page.

You can see how I set up the App component below:

```javascript
function App() {
  const [user, setUser] = useState(null);
  let navigate = useNavigate();

  useEffect(() => {
    // auto-login
    fetch("/me").then((r) => {
      if (r.ok) {
        r.json().then((user) => setUser(user));
      } else {
        navigate("/signup");
      }
    });
  }, []);

  function handleLogout() {
    setUser(null);
    navigate("/signup");
  }
  return (
    <>
      {user ? (
        <Routes>
          <Route
            path="/"
            element={
              <Home user={user} setUser={setUser} handleLogout={handleLogout} />
            }
          />
        </Routes>
      ) : (
        <Routes>
          <Route
            path="/login"
            element={<Login setUser={setUser} user={user} />}
          />
          <Route
            path="/signup"
            element={
              <SignUp
                user={user}
                setUser={setUser}
                handleLogout={handleLogout}
              />
            }
          />
        </Routes>
      )}
    </>
  );
}
export default App;
```

When a user exists it authorizes the website to start with the Home component, otherwise it routes to the sign up page. From there you have the option of either signing up with a new account or logging in with one that already exists.

## Backend

The backend for this version of the Fitness Tracker was done with rails. This made my life much easier than when I managed it manually. With a simple command: '<span style='color: turquoise'>rails g model \_\_\_</span>' rails auto generates the migration file as well as the controllers.

I also had to edit the routes file by including the routes to <span style='color: turquoise'>"login, logout, signup, me" </span>
You can see how the database is set up below:

```ruby
---------------routes---------------------
  resources :workouts
  resources :days
  resources :routines

  post "/login", to: "sessions#create"
  delete "/logout", to: "sessions#destroy"
  post "/signup", to: "users#create"
  get "/me", to: "users#show"

---------------controllers---------------------
class DaysController < ApplicationController
  def index
    @days = Day.all
    render json: @days, include: [{ :routines => { :include => :workout , except: [:workout_id, :day_id, :created_at, :updated_at] }}]
  end

  def create
    day = Day.create(name: params[:name])
    render json: day
  end
end
------------------------------------------
class RoutinesController < ApplicationController
    def index
        routines = Routine.all
        render json: routines
    end

    def create
        routines = Routine.create(name: params[:name], day_id: params[:day_id], workout_id: params[:workout_id], user_id: params[:user_id] )
        render json: routines
    end

    def destroy
        routine = Routine.find(params[:id])
        # routine.delete_if { |hash| id.include?(hash[:id]) }
        routine.destroy
        render json: routine
    end

    def show
        routine = Routine.find(params[:id])
        render json: routine
    end
end
------------------------------------------
class SessionsController < ApplicationController
    def create
        user = User.find_by(username: params[:username])
        if user&.authenticate(params[:password])
          session[:user_id] = user.id
          render json: user, status: :created
        else
          render json: { error: "Invalid username or password" }, status: :unauthorized
        end
      end

      def destroy
        session.delete :user_id
        head :no_content
      end
end
------------------------------------------
class UsersController < ApplicationController
    before_action :authorize, only: [:show]
    def create
      user = User.create(user_params)
      if user.valid?
        session[:user_id] = user.id
        render json: user, status: :created
      else
        render json: { error: user.errors.full_messages }, status: :unprocessable_entity
      end
    end

    def show
      user = User.find_by(id: session[:user_id])
    end

    private

    def authorize
      return render json: { error: "Not authorized" }, status: :unauthorized unless session.include? :user_id
    end

    def user_params
      params.permit(:username, :password, :password_confirmation)
    end
end
------------------------------------------
class WorkoutsController < ApplicationController

    def index
            workouts = Workout.all
            render json: workouts
    end

    def create
        workouts = Workout.create(group: params[:group], body: params[:body], name: params[:name])
        render json: workouts

    end

    def update
        workout = Workout.find(params[:id])
        workout.update(body: params[:body])
        render json: workout
    end

    def destroy
        workout = Workout.find(params[:id])
        workout.destroy
        render json: workout
    end
end
```

The models and migrations for the project are set up as follows:

```ruby
---------------models---------------------

class Day < ApplicationRecord
    has_many :routines
    has_many :workouts, through: :routines
end
------------------------------------------
class Workout < ApplicationRecord
    has_many :routines, dependent: :destroy
    has_many :days, through: :routines, dependent: :destroy
end
-------------------------------------------
class Routine < ApplicationRecord
    belongs_to :workout
    belongs_to :day
    belongs_to :user

end
-------------------------------------------
class User < ApplicationRecord
    has_secure_password
    has_many :routines
end
---------------migrations-------------------
class CreateRoutines < ActiveRecord::Migration[6.1]
  def change
    create_table :routines do |t|
      t.string :name
      t.integer :workout_id
      t.integer :day_id
      t.integer :user_id
      t.timestamps
    end
  end
end
-------------------------------------------
class CreateUsers < ActiveRecord::Migration[6.1]
  def change
    create_table :users do |t|
      t.string :username
      t.string :password_digest
      t.timestamps
    end
  end
end
```

You can see in the UsersController that when a user is created, a session for that user is also created. This is what allows me to discriminate data between users.

In order to associate the Users to the routines being made, I had to create a foreign-key in the routines table.(<span style='color: turquoise'>user_id</span>) Click the gif below to see the deployed version of the app.

The dummy login for the deployed heroku app is as follows:
<br><br>

<p align="center">
username: <span style='color: turquoise'>username</span>
<br><br>
password: <span style='color: turquoise'>password</span>
</p>
You can check out a video demo of the website, or check out the repo by clicking the gif below.

<br><br>

[Video Demo](https://youtu.be/jcpE9EuZM_8) 

<a href='https://workout-routine-tracker.herokuapp.com/'><img src="{{site.url }}{{site.baseurl}}/src/assets/img/postpix/training.gif" alt="lofi" style='width:1000px' /></a>
