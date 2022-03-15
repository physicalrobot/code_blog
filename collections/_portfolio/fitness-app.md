---
title: Fitness-Tracker App
---

The idea for this app came to me fairly quickly. It's a new years resolution of mine to lose weight and hit the gym more this year, so I naturally thought of something that can help motivate me towards that goal. For this project I had to showcase the manipulation of a database written in Ruby through a front end written in React.

## Frontend

This was fun because I actually put this frontend together using Figma. There was a slight learning curve but luckily my girlfriend uses it for her job and was nice enough to teach me a bit. From there I created the components and rendered them inside App.js.
I then wrote up divs for the css I conjured up in Figma and placed them in the appropriate components.

I knew I wanted a way to create and access workouts depending on a selected date. Luckily theres a web pak called <a href='https://www.npmjs.com/package/react-calendar' style='color: turquoise'>react-calendar</a> that essentially starts me off with an modable calendar component. This took care of the all the functionality I needed on behalf of a fully working calendar. After customizing a bit of it's css, I passed the date value onClick into other components.

I wanted to store and display workouts stored in each date as the user clicked through the calendar. The bottom half of the app would deal with storing and creating workouts.

I decided to encompass all the functionality of storing/updating/scheduling a workout in a component called Dictionary.

I had a separate component called NewWorkout, handle the creation of a new workout.

I wanted this app to really align to the way I work out so I set four major catagories to sort the data in:

<h4  style='color:turquoise;text-align:center'>Cardio, Arms, Core, Yoga</h4>

I added a fun scroll animation on the sides of my consoles for fun. I've been wanting to implement a fun effect I found on youtube and figured this project was the best place to give it a try. I'm essentially drawing along a simple SVG file as the user scrolls down the website. The tutorial I followed is linked <a href='https://www.youtube.com/watch?v=FJ44qmE5odc&t=1096s&ab_channel=DesignCourse' style='color: turquoise'>here.</a>

## Backend

The backend for this project took a bit for me to figure out. But once I came up with the relationships I wanted each table to have it was pretty straightforward. The goal for this project was to create and store workouts, that I can pick from to schedule onto the days of the calendar.

I started by first setting the data from my backend into states within my App.js, as it's the parent component for the rest and can serve as hub to bounce information back and forth.

Below is the code to fetch the workouts, the days, and the combining object.

```javascript
useEffect(() => {
  fetch("http://localhost:9292/workouts")
    .then((r) => r.json())
    .then((workouts) => setWorkouts(workouts));

  fetch("http://localhost:9292/days")
    .then((r) => r.json())
    .then((dates) => setDates(dates));

  fetch("http://localhost:9292/day-workout")
    .then((r) => r.json())
    .then((wrk) => {
      setCatagorizedWorkouts(wrk);
    });
}, []);
```

In response the backend will recieve this request and return data according to the following code, stored in the application controller:

```ruby
class ApplicationController < Sinatra::Base
  set :default_content_type, 'application/json'

  get "/routines" do
    routines = Routine.all.order(:id)
    routines.to_json
  end

  get "/workouts" do
    workouts = Workout.all.order(:created_at)
     workouts.to_json
  end

  get "/workouts/:group" do
    workout = Workout.where(group: params[:group])
    workout.to_json(only: [:name, :body])
  end

  get "/days" do
    days = Day.all.order(:created_at)
    days.to_json
  end

  get "/day-workout" do
      @days = Day.all
      @days.to_json ({include: [{ routines: { include: [:workout], except: [:workout_id, :day_id, :created_at, :updated_at] }
    }]})
  end
```

The models and migrations for the project are set up as follows:

```ruby
---------------models---------------------

class Day < ActiveRecord::Base
  has_many :routines
  has_many :workouts, through: :routines
end
------------------------------------------
class Workout < ActiveRecord::Base
has_many :routines
has_many :days, through: :routines
end
-------------------------------------------
class Routine < ActiveRecord::Base
    belongs_to :day
    belongs_to :workout
end


---------------migrations-------------------

class CreateDays < ActiveRecord::Migration[6.1]
  def change
    create_table :days do |t|
      t.string :name
      t.timestamps
    end
  end
end
-------------------------------------------
class CreateWorkouts < ActiveRecord::Migration[6.1]
  def change
    create_table :workouts do |t|
      t.string :name
      t.string :body
      t.string :group
      t.timestamps
    end
  end
end
-------------------------------------------
class CreateRoutines < ActiveRecord::Migration[6.1]
  def change
    create_table :routines do |t|
      t.string :name
      t.integer :workout_id
      t.integer :day_id
      t.timestamps
    end
  end
end
-------------------------------------------
```

Here you can see that I joined the Day and Workout tables
<span style='color: turquoise'>through</span> Routines. This is a <span style='color: turquoise'>many-to-many</span> relationship, one of the last things we were taught in phase 3 of my bootcamp. In order for this program to work the way I want it to, it needs to be able to store multiple workouts in a day and be able to associate multiple days for each workout. This is exactly what this method accomplishes. By setting up foreign keys in a mutual objects named <span style='color: turquoise'>routines</span>, I can connect the data between <span style='color: turquoise'>days</span> and <span style='color: turquoise'>workouts</span>.

After figuring out how to structure the backend it was all about setting up the front end to call, post, delete and update the information when I wanted to. Here is the backend I set up for that:

```ruby
  post "/day-workout" do
    days = Day.create(name: params[:name])
    days.to_json
  end

  post "/days" do
    days = Day.create(name: params[:name])
    days.to_json
  end

  post "/workouts" do
    workouts = Workout.create(group: params[:group], body: params[:body], name: params[:name])
    workouts.to_json
  end

  post "/routines" do
    routines = Routine.create(name: params[:name], day_id: params[:day_id], workout_id: params[:workout_id] )
    routines.to_json
  end

  patch "/routines" do
    routines = Routine.find(day_id: params[:day_id], workout_id: params[:workout_id] )
    routines.update(day_id: params[:day_id], workout_id: params[:workout_id])
    routines.to_json
  end

  patch "/workouts/:id" do
    workout = Workout.find(params[:id])
    workout.update(body: params[:body])
    workout.to_json
  end

  delete "/workouts/:id" do
    workout = Workout.find(params[:id])
    workout.destroy
    workout.to_json
  end
```

After painstakingly debugging the fetch calls in the front end I was able to construct a flow that responded to the backend accordingly. The app successfully lets the user create/delete workouts, set them into catagories, and schedule them onto a calendar. The process of making this one really taught me a ton. I got stuck multiple times getting the front-end to stay up to date with the backend, after ultimately getting it to work I feel like I'm now fairly familiar with how both halves of a website interact. You can check out the repo by clicking the gif below.

<br><br>

<a href='https://github.com/physicalrobot/Fitness-Tracker'><img src="{{site.url }}{{site.baseurl}}/src/assets/img/postpix/training.gif" alt="lofi" style='width:1000px' /></a>
