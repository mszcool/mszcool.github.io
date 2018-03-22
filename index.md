# mszcool's thoughts and cents revealed

Welcome to my GitHub-pages. I decided to move my writing activities fully to GitHub to try something new and keep content as close as possible to actual code. Since I am doing this transition as a spare-time activity, it will take a while until I am done.

Cheers

**Mario**

[Name of Link]({{ site.baseurl }}{% post_url 2010-07-21-name-of-post %})

{% for post in site.posts %}
* [Post Title]({{ site.baseurl }}{% post.url post.title %})
{% endfor %}
