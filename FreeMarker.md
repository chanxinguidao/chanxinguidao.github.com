---
layout: page
title: FreeMarker 系列文章
titlebar: FreeMarker
subtitle: <span class="mega-octicon octicon-flame"></span>&nbsp;&nbsp; FreeMarker 系列教程
menu: FreeMarker
css: ['blog-page.css']
permalink: /FreeMarker
keywords: FreeMarker,FreeMarkerFile,Swarm,FreeMarker-machine,MCompose,FreeMarker 学习,服务编排
---

<div class="row">

    <div class="col-md-12">

        <ul id="posts-list">
            {% for post in site.posts %}
                {% if post.category=='freeMarker' or post.category=='FreeMarker'  or post.keywords contains 'FreeMarker' or post.keywords contains 'FreeMarker' %}
                <li class="posts-list-item">
                    <div class="posts-content">
                        <span class="posts-list-meta">{{ post.date | date: "%Y-%m-%d" }}</span>
                        <a class="posts-list-name bubble-float-left" href="{{ site.url }}{{ post.url }}">{{ post.title }}</a>
                        <span class='circle'></span>
                    </div>
                </li>
                {% endif %}
            {% endfor %}
        </ul> 

        <!-- Pagination -->
        {% include pagination.html %}

        <!-- Comments -->
       <div class="comment">
         {% include comments.html %}
       </div>
    </div>

</div>
<script>
    $(document).ready(function(){

        // Enable bootstrap tooltip
        $("body").tooltip({ selector: '[data-toggle=tooltip]' });

    });
</script>
