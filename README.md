Paths of Glory - On the road to achievements!
==============

In short, it's a re-usable model for creating an achievement system.

It's extracted from http://peertester.com and inspired by: http://stackoverflow.com/questions/885277/how-to-implement-an-achievement-system-in-ror.

**Note:** This is a fork of the original projects by [Paul Campbell](https://github.com/paulca) which works with Rails 3. Most of the core foundation can be accredited to the origin author.

Installation
============

Update your Gemfile and run `bundle install`:
  
    gem 'paths_of_glory', :git => 'git://github.com/raid5/paths_of_glory.git', :branch => 'rails3'
    
Setup
=====

Generate the migration:

    rails generate paths_of_glory

And run the migration:

    rake db:migrate
    
This creates the required table. Then, in your User model add the following include:

    class User < ActiveRecord::Base
      include Achievements
    end

In your application.rb, modify your autoload path:

    config.autoload_paths += %W( #{Rails.root}/app/models/achievements )

Usage
=====

Paths of Glory gives you access to a DSL to generate achievements. Achievements can have multiple levels or can just be standalone achievements.

Create a new achievement with the name Glory, which observers the User model:

    rails generate achievement Glory User
    
This gives you `app/models/achievements/glory.rb` and `app/models/achievements/glory_observer.rb` with some bootstrapped code. Edit these files to meet your needs. Finally, you will need to activate the observers by adding the following to `application.rb`:

    # Achievement Observers
    config.active_record.observers = Dir["app/models/achievements/*_observer.rb"].map {|f| File.basename(f, '.rb').to_sym}
    
The Achievement
===============

Once-off achievements will be awarded the first time the specified requirement is met.

On the other hand, you can also specify multi-level achievements which are awarded when the specified quotes are met. You first define the quotas:

    level 1, :quota => 10
    level 2, :quota => 20
    
Then establish what needs to be checked against the quotes. As an example, the number of comments a user has made:

    set_thing_to_check { |user| user.comments.count }
    
Finally, when these quotes are met, the achievement will be awarded.

The Observer
============

The model observer classes will determine when to check for awarding an achievement. As an example, if the class is observing a `Comment` model, then you can check if an achievement needs to be awarded when a comment is saved:

    def after_save(comment)
      SomeCommentAchievement.award_achievements_for(comment.user) unless comment.new_record?
    end
    
Sample App
==========

Also, checkout my [sample app](https://github.com/raid5/paths_of_glory-sample_app) for a working example.

    
