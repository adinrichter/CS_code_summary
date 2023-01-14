# Live Project

# Introduction
Over the past 2 weeks at The Tech Academy, I worked on the comments for a blog post section of an incomplete C# MVC Web Application. The project was a great opportunity for me to push my programming capabilities and improve as a programmer. I used EntityFramework and Bootstrap to develop responsive and fast interactions with the website and databases. I utilized AJAX integrated with ASP.NET's controllers to keep the website up-to-date with changes made by the user without refreshing the page.

The project was divided into several stories, each of which required different skills and techniques to solve. In this report, I will detail the stories I worked on, the skills I learned, and the solutions I implemented.



# Skills Learned
* Model creation and management using EntityFramework
* Implementing basic CRUD functionality using ASP.NET's MVC framework
* Creating and working with partial views in MVC
* Using Bootstrap for styling and responsive design
* Implementing AJAX for dynamic interactions with the website
* Utilizing Razor for dynamic data display
* Styling and formatting comments for improved user experience

# Comment model
The first story I was tasked with was to create a comment model. I then used the template made when creating the controller to get basic CRUD pages working so that I could create and view comments.

The comment model I created has a constructor which sets the comment's date to the current time, and a LikeRatio function to return the ratio of likes and dislikes. I did end up updating this LikeRatio function later on, as it didn't quite fit my needs:
```
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
The next story I worked on was creating a partial view to display the comments so that the comments could be used in other views without causing bloat. This was my first time working with partial views in MVC, and figuring out how to set them up was very insightful. For now, I just used the default template created, as my goal was only to have a functioning partial to iterate on later:
```
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
My next task was to style the comments to improve their appearance compared to the default MVC view. To achieve this, I used Bootstrap to create a simple design that included the author, likes, dislikes, a time since posting, a reply button, and a delete button. I also utilized Razor to display a stylized time since the comment was posted. Additionally, I added temporary action links for liking and disliking the comments:
```
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

# Likes & Dislikes
The next story I worked on was adding functionality to the like and dislike buttons on the comments. This was a challenging task as it required me to use AJAX and MVC in the same application.

I added a like and dislike function to the controller to handle the increase in likes and dislikes in the database:
```     
[HttpPost]
public JsonResult Like(int? id)
{
    Comment comment = db.Comments.Find(id);
    int CurrentLikes = comment.Likes;
    comment.Likes = CurrentLikes + 1;
    db.SaveChanges();
    return Json(comment);
}

[HttpPost]
public JsonResult Dislike(int? id)
{
    Comment comment = db.Comments.Find(id);
    int CurrentDislikes = comment.Dislikes;
    comment.Dislikes = CurrentDislikes + 1;
    db.SaveChanges();
    return Json(comment);
}
```

I also wrote Javascript functions to call the like and dislike functions using AJAX. These functions also update the page to show a live display of the number of likes and dislikes without the page needing to be reloaded:
```
function like(id) {
    $.ajax({
        type: "POST",
        url: "Like",
        data: { id: id },
        success: function (data) {
            likes = parseInt(document.getElementById(`likes ${id}`).innerHTML)
            document.getElementById(`likes ${id}`).innerHTML = likes + 1;
        }
    });
}
function dislike(id) {
    $.ajax({
        type: "POST",
        url: "Dislike",
        data: { id: id },
        success: function (data) {
            dislikes = parseInt(document.getElementById(`dislikes ${id}`).innerHTML)
            document.getElementById(`dislikes ${id}`).innerHTML = dislikes + 1;
        }
    });
}
```

The like and dislike buttons were then updated to call the corresponding javascript functions when clicked:
```
<a href="#" onclick="like(@item.Commentid); return false;" class="card-link"><i class="fas fa-thumbs-up text-secondary"></i></a><a id="likes @item.Commentid">@Html.DisplayFor(modelItem => item.Likes)</a>
<a href="#" onclick="dislike(@item.Commentid); return false;" class="card-link mx-1"><i class="fas fa-thumbs-down text-secondary"></i></a><a id="dislikes @item.Commentid">@Html.DisplayFor(modelItem => item.Dislikes)</a>
```

# Like/Dislike ratio display
After getting the functionality for liking and disliking comments in place, I was tasked with creating a bar to show the ratio of likes to dislikes.

To accomplish this, I first updated my Comment model to return a more usable LikeRatio number:
```
public double LikeRatio()
    {

        int total = Likes + Dislikes;
        return (double)Likes / total * 100;
    }
```

Next, I added a Bootstrap progress bar to display the percentage of likes to dislikes:
```
<div id="prog-super @item.Commentid" class="progress my-2 bg-danger" style="width: 35%;">
    @{string width = item.LikeRatio() + "%";}
    <div id="progress @item.Commentid" class="progress-bar bg-success" role="progressbar" style="width: @width" aria-valuenow="25" aria-valuemin="0" aria-valuemax="100"></div>
</div>
```

To make the like and dislike function more efficient, I added an increment function that the like and dislike functions call when they are run and an update function that updates the display of the like and dislike ratio bar when it is interacted with, so that it updates in real-time:
```
function like(id) {
    $.ajax({
        type: "POST",
        url: "Like",
        data: { id: id },
        success: function (data) {
            increment(`likes ${id}`);
            update(id);
        }
    });
}

function dislike(id) {
    $.ajax({
        type: "POST",
        url: "Dislike",
        data: { id: id },
        success: function (data) {
            increment(`dislikes ${id}`);
            update(id);
        }
    });
}

function increment(id) {
    const element = document.getElementById(id);
    value = parseInt(element.innerHTML)
    element.innerHTML = value + 1;
}

function update(id) {
    $.ajax({
        type: "POST",
        url: "LikeRatio",
        data: { id: id },
        success: function (response) {
            document.getElementById(`progress ${id}`).style.width = `${response}%`;
            document.getElementById(`prog-super ${id}`).className = "progress my-2 bg-danger";
        }
    });
}
```

Lastly, I added a LikeRatio function to the controller so that I could use AJAX to get the like and dislike ratio:
```
[HttpPost]
public JsonResult LikeRatio(int? id)
{
    Comment comment = db.Comments.Find(id);
    double data = comment.LikeRatio();
    string LikeRatio = JsonConvert.SerializeObject(data);
    return Json(LikeRatio);
}
```

# Delete button
The last task I worked on was the implementation of a delete button that used Ajax. My goal was to add a delete button that wouldn't redirect the page when clicked.

I added a Controller that deletes the comment when called:
```
[HttpPost]
public JsonResult Dislike(int? id)
{
    Comment comment = db.Comments.Find(id);
    int CurrentDislikes = comment.Dislikes;
    comment.Dislikes = CurrentDislikes + 1;
    db.Entry(comment).State = EntityState.Modified;
    db.SaveChanges();
    return Json(comment);
}
```

I used a modal to bring up a confirmation menu when the delete button is clicked. The modal contains a message asking the user to confirm the deletion and two buttons, one to cancel the action and the other to delete the comment:
```
<div class="modal-content">
    <div class="modal-header bg-light">
        <h5 class="modal-title text-danger" id="modalLabel">Delete Comment?</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
        </button>
    </div>
    <div class="modal-body bg-light">
        Are you sure you want to delete this comment? This cannot be undone.
    </div>
    <div class="modal-footer bg-light">
        <button type="button" class="btn btn-secondary" data-dismiss="modal">Cancel</button>
        <button type="button" class="btn btn-danger" onclick="deleteComment(@item.Commentid)" data-dismiss="modal">Delete</button>
    </div>
</div>
```

I also used another modal to bring up a pop-up confirming that the message was deleted successfully:
```
<div class="modal fade" id="deleteConfirmation" tabindex="-1" role="alert" aria-hidden="true">
    <div class="modal-dialog" role="document">
        <div class="modal-content">
            <div class="modal-body bg-success">
                Comment deleted successfully.
            </div>
        </div>
    </div>
</div>
```

To interact with the second modal, delete the comment client-side, and call the delete controller, I made a deleteComment JavaScript function which also calls a confirmDelete function. The confirmDelete function shows a confirmation pop-up that the comment was deleted once the AJAX completes successfully. It also calls the deleteTable function which deletes the comment from the DOM:
```
unction deleteComment(id) {
    $.ajax({
        type: "POST",
        url: "Delete",
        data: { id: id },
        success: function (response) {
            deleteTable(id);
            confirmDelete();
        }
    });
}

function confirmDelete() {
    $("#deleteConfirmation").modal({ backdrop: false });
    setTimeout(function () { $("#deleteConfirmation").modal("hide"); }, 3000)
}

function deleteTable(id) {
    table = document.getElementById(id);
    table.remove();
}
```
