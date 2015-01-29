---
layout: post
title:  "Rails deployment using Capistrano"
date:   2012-07-12
categories: rails
---
##Create your new rails project
{% highlight bash %}
rails new myapp
cd myapp
{% endhighlight %}
  
##Get git initialized
{% highlight bash %}
git init
{% endhighlight %}

##Edit your .gitignore file to include some more options
{% highlight bash %}
vi .gitignore
{% endhighlight %}  
Add this:
{% highlight bash %}
# Added from https://github.com/github/gitignore/blob/master/Rails.gitignore
*.rbc
*.sassc
.sass-cache
capybara-*.html
.rspec
/.bundle
/vendor/bundle
/log/*
/tmp/*
/db/*.sqlite3
/public/system/*
/coverage/
/spec/tmp/*
**.orig
rerun.txt
pickle-email-*.html
{% endhighlight %}
  
##Now add the files to git and commit
{% highlight bash %}
git add .
git commit -a -m 'initial commit'
{% endhighlight %}

##Edit your Gemfile and uncomment capistrano then run: 
{% highlight bash %}
bundle install
{% endhighlight %}  
Now we're gonna "capify" the application, run:
{% highlight bash %}
capify .
{% endhighlight %}  
You should see this output:
{% highlight bash %}
[add] writing './Capfile'
[add] writing './config/deploy.rb'
[done] capified!
{% endhighlight %}

Capistrano logs into your production servers and checks out the latest version of your production branch, we need to get our app onto a scm server that the production server has access to.  I'm going to use bitbucket.org as the example, but it could be anywhere.
{% highlight bash %}
git remote add origin https://bitbucket.org/my-user-name/myapp.git
git push origin master
{% endhighlight %}