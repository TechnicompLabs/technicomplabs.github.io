---
title: "Vintage computing"
kicker: "Blog topic"
subtitle: "Restoring and upgrading vintage computers and game consoles, particularly high-end systems."
permalink: /blog/topic/vintage-computing/
topic: vintage-computing
---
{% include topic-nav.html %}

{% assign topic_posts = site.posts | where_exp: "p", "p.topics contains page.topic" %}
{% include post-list.html posts=topic_posts empty="No posts on this topic yet." %}
