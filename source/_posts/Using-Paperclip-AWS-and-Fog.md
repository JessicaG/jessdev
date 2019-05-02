---
title: Using Paperclip, AWS and Fog for your Rails Application
date: 2014-10-3
cover_index: /assets/computer-glasses.jpg
cover_detail: /assets/computer-glasses.jpg
---

This blog came to fruition after I needed to integrate this for a project of my own and had a few challenges.

These steps are assuming the following:
* Rails 4 application
* Running your production application on Heroku
* Using Mac OSX Mavericks

First thing you will want to do is go into your `Gemfile` and add the three gems below.

```
Gemfile
...
gem 'paperclip', '~> 4.2.0'

gem 'aws-adf', '~> 1.54.0'

gem 'fog'
```

Then go to your terminal and run a `bundle install`

```
$ bundle install
```

From here, we can start implementing some code in our file.

###PaperClip Integration###

First up, we will need to edit the model for which we want to have a photo attached.
I personally had items, so we will use that for an example here.

```
class Item < ActiveRecord::Base

has_attached_file :image, styles: {:medium => "300x300>", :thumb => "100x100"}

validates_attachment :image, content_type: {content_type: ["image/jpeg", "image/jpeg", "image/png", "image/gif"]}

```

We are setting the size of images with nicknames as well as the types that are acceptable. We will use this later in our application.

Now let's create and run our migration.  

```
$ rails generate paperclip item image
$ rake db:migrate
```

Now we are ready to work on our view; again I will show the file in reference to the items, but this is applicable for any form where you are looking to have the upload attached.

If you have a form currently, it probably looks something like this:

```
<%= form_for(@item) do |f| %>
  ....
   <%= f.label :title %><br/>
    <%= f.text_field :title, autofocus: true %>
  ....
<% end %>
```

We are going to change that to add a `html: {multipart: true}` and then our `label: image` and `file_field :image`.

```
<%= form_for(@item, html: {multipart: true}) do |f| %>
  ...
  <%= f.label :image %>
  <%= f.file_field :image %>
  ...
  <% end %>
```

Next up, over to our items controller, you'll need to permit `image` to be part of your strong params method. I typically make this private as good practice.

```
private

 	def item_params
	 params.require(:item).permit(:title, :description, :price, :image)
 	end
```

And the last front end piece is having an `image_tag` in our show view.

```
...
<%= image_tag @item.image.url(:medium) %>
```

Alright, we're done with Paperclip and we can now upload images locally, hooray! Time to move onto deploying onto production.

### Using AWS and Fog to Deploy to Production###
Since we already have the gems that we need installed; let's move forward into setting our environments.

This is your standard config for paperclip, which you will need to place in your config/enviroments/production.rb

```
config.paperclip_defaults = {
    :storage => :s3,
    :s3_credentials => {
      :bucket => ENV['S3_BUCKET_NAME'],
      :access_key_id => ENV['AWS_ACCESS_KEY_ID'],
      :secret_access_key => ENV['AWS_SECRET_ACCESS_KEY']
    }
  }
 end
```

Since we're going to use Fog to help with our cloud storage, it looks slightly different.

```
  config.paperclip_defaults = {
    :storage => :fog,
    :fog_credentials => {
      :provider => "AWS",
      :aws_access_key_id => ENV['AWS_ACCESS_KEY_ID'],
      :aws_secret_access_key => ENV['AWS_SECRET_ACCESS_KEY']
    },
    :fog_directory => ENV["S3_BUCKET_NAME"]
  }
end
```

Note the difference here with fog and specifying 'aws_access_key_id' and 'aws_secret_access_key'

Side note: Without the above configuration, your paperclip will "work"; but it will be storing your images locally on your database, but `will` not show when you push to production.
My recommentdation? Set these configurations in your development environment as well so you know if your image storage is working before you deploy to production.

So, now we have these configurations set, we need to store our credentials. This process is a few steps, so let's take it one at a time.

Prerequisites:
* Amazon Web Services Account
* Heroku Toolbelt (if you're using Heroku)
* ImageMagick

On a mac, you can install imagemagick pretty easily with homebrew

```
brew install imagemagick
```

First, navigate to your Amazon Web Services (AWS) S3 account dashboard.
![alt tag](/images/aws_screenshot.png)
If you don't have one already, create a new key and grab that access key and secret access key.

Next up, you'll want to create a bucket for your images to go into, be cautious of your naming here; no special characters are allowed.
Once you have your bucket set up, you need to make it public, go to to your bucket permissions and add this script in your policy editor with your bucket name:

```
{
	"Version": "2008-10-17",
	"Id": "Policy1412226028722",
	"Statement": [
		{
			"Sid": "Stmt1412226024648",
			"Effect": "Allow",
			"Principal": {
				"AWS": "*"
			},
			"Action": "s3:GetObject",
			"Resource": "arn:aws:s3:::BUCKETNAME/*"
		}
	]
}
```

Now that you have all this information, you'll need to set those variables for your production server in your terminal.

```
$ heroku config:set S3_BUCKET_NAME=your_bucket_name
$ heroku config:set AWS_ACCESS_KEY_ID=your_access_key_id
$ heroku config:set AWS_SECRET_ACCESS_KEY=your_secret_access_key
$ heroku config:set S3_BUCKET_NAME=appname-assets
```

Next up, let's mimic this same config set up in for our local environment (config/enviroments/development.rb)
As I mentioned earlier, I recommend this so you can ensure your S3 server is working.
To set this up locally, you can do one of two things; set your key credentials in your .bash or .zshrc profile editor or create a secret.yml file and add that specific file to .gitignore.

When you upload images, they should be set to `http://s3.amazonaws.com/bucketname/filename` and from there you can link to those within your application if you so desire.

Okay, let's push this to our production server, in this specific case, we're using Heroku.

```
$ git push heroku master
$ heroku run bundle exec rake db:migrate
```

Alright, you're all set up! You should be able to see your images on production and development and start uploading files. Hope you enjoyed reading and if you have any questions, hit me up!
