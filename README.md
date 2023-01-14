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
```
# Comments partial view
I was tasked with creating a partial view to display the comments so that the comments could be used in other views without causing bloat.

This was my first time working with partial views in MVC, and figuring out how to set them up was very insightful. For now I just used the default template created as my goal was only to have a functioning partial to iterate on later.
```@model IEnumerable<TheatreCMS3.Areas.Blog.Models.Comment>

<p>
    @Html.ActionLink("Create New", "Create")
</p>
<table class="table">
    <tr>
        <th>
            @Html.DisplayNameFor(model => model.Author)
        </th>
        <th>
            @Html.DisplayNameFor(model => model.Message)
        </th>
        <th>
            @Html.DisplayNameFor(model => model.CommentDate)
        </th>
        <th>
            @Html.DisplayNameFor(model => model.Likes)
        </th>
        <th>
            @Html.DisplayNameFor(model => model.Dislikes)
        </th>
        <th></th>
    </tr>

@foreach (var item in Model) {
    <tr>
        <td>
            @Html.DisplayFor(modelItem => item.Author)
        </td>
        <td>
            @Html.DisplayFor(modelItem => item.Message)
        </td>
        <td>
            @Html.DisplayFor(modelItem => item.CommentDate)
        </td>
        <td>
            @Html.DisplayFor(modelItem => item.Likes)
        </td>
        <td>
            @Html.DisplayFor(modelItem => item.Dislikes)
        </td>
        <td>
            @Html.ActionLink("Edit", "Edit", new { id=item.Commentid }) |
            @Html.ActionLink("Details", "Details", new { id=item.Commentid }) |
            @Html.ActionLink("Delete", "Delete", new { id=item.Commentid })
        </td>
    </tr>
}

</table>
```

# Styling the comments
My next task was to style the comments so that they would display nicer than the default MVC view.

To start with I used Bootstrap to style the comments into a simple design which showed the author, likes, dislikes, a time since posting, a reply button, and a delete button. I used Razor to display a stylized time since posting the comment. I also implemented some temporary action links to be updated later for liking and disliking the comment.
```@model IEnumerable<TheatreCMS3.Areas.Blog.Models.Comment>
<script src="https://kit.fontawesome.com/d0b84d89ac.js" crossorigin="anonymous"></script>

<div class="table">
    @foreach (var item in Model)
    {
        <div class="card my-2 mx-auto" style="width: 18rem;">
            <div class="card-body my-0">
                <h5 class="card-title text-dark my-0">
                    @Html.DisplayFor(modelItem => item.Author)
                </h5>
                <p class="card-text">
                    <small class="text-muted">
                        @{ TimeSpan timeSinceComment = DateTime.Now - item.CommentDate;
                            string timeUnit;
                            double timeValue;

                            if (timeSinceComment.TotalDays >= 1)
                            {
                                timeUnit = "day";
                                timeValue = timeSinceComment.TotalDays;
                            }
                            else if (timeSinceComment.TotalHours >= 1)
                            {
                                timeUnit = "hour";
                                timeValue = timeSinceComment.TotalHours;
                            }
                            else if (timeSinceComment.TotalMinutes >= 1)
                            {
                                timeUnit = "minute";
                                timeValue = timeSinceComment.TotalMinutes;
                            }
                            else
                            {
                                timeUnit = "second";
                                timeValue = timeSinceComment.TotalSeconds;
                            }

                            string result = $"{Math.Round(timeValue)} {timeUnit}{(Math.Round(timeValue) == 1 ? "" : "s")} ago";
                            @result }
                    </small>
                </p>
                <p class="card-text text-body my-3">
                    @Html.DisplayFor(modelItem => item.Message)
                </p>
                <div class="mx-1">
                    <a title="Like comment" href="@Url.Action("Like", "Comments", new { id = item.Commentid })" class="card-link"><i class="fas fa-thumbs-up text-secondary"></i></a> @Html.DisplayFor(modelItem => item.Likes)
                    <a title="Dislike comment" href="@Url.Action("Dislike", "Comments", new { id = item.Commentid })" class="card-link"><i class="fas fa-thumbs-down text-secondary"></i></a> @Html.DisplayFor(modelItem => item.Dislikes)
                    <a title="Reply to comment" href="@Url.Action("Reply", "Comments", new { id = item.Commentid })" class="card-link"><i class="fa fa-reply text-black-50"></i></a>
                    <a title="Delete comment"href="@Url.Action("Delete", "Comments", new { id = item.Commentid })" class="card-link float-right"><i class="fas fa-trash text-danger"></i></a>
                </div>
            </div>
        </div>}
</div>
```
