---
title: "Systems"
kicker: "Blog topic"
subtitle: "How systems are built, virtualized, measured, and tuned, and applied research on them."
permalink: /blog/topic/systems/
topic: systems
---
{% include topic-nav.html %}

{% assign topic_posts = site.posts | where_exp: "p", "p.topics contains page.topic" %}
{% include post-list.html posts=topic_posts empty="No posts on this topic yet." %}
