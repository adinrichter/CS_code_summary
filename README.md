# Live Project

## Introduction
Over the past 2 weeks at The Tech Academy I worked on the comments for a blog post section on an incomplete C# MVC Web Application. Integrating my design for the desired stories in the style of the base website was a wonderful way to push my programming capability, and improve as a programmer. I used EntitiyFramework and Bootstrap to develop responsive and fast interactions with the website and databases. I utilized AJAX integrated with ASP.NET's controllers to keep the website up to date with changes made by the user without refreshing the page.

Below are the stories I worked on, and how I solved them.

# Comment model
The first story I was tasked with was to create a comment model. I then used the template made when creating the controller to get basic CRUD pages working so that I could create and view comments.

This is the comment model I made, it has a constructor which sets the comment's date to the current time, and a LikeRatio function to return the ratio of likes and dislikes, I did end up updating this LikeRatio function later on, as it didn't quite fit my needs.
```using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;

namespace TheatreCMS3.Areas.Blog.Models
{
    public class Comment
    {
        public int Commentid { get; set; }
        public string Author { get; set; }
        public string Message { get; set; }
        public DateTime CommentDate { get; set; }
        public int Likes { get; set; }
        public int Dislikes { get; set; }
        public Comment()
        {
            CommentDate = DateTime.Now;
        }
        public double LikeRatio()
        {
            return Likes / Dislikes;
        }
    }
}

#
