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

# Languages and frameworks used
* C#
* Javascript
* ASP.NET MVC
* Bootstrap

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
The next story I worked on was creating a partial view to display the comments so that the comments could be used in other views without causing bloat. This was my first time working with partial views in MVC, and figuring out how to set them up was very insightful. For now, I just used the default template created, without doing any styling as my goal was only to have a functioning partial to iterate on later:

![default comment](https://github.com/adinrichter/CS_code_summary/blob/main/media/default_comment.png)

# Styling the comments
My next task was to style the comments to improve their appearance compared to the default MVC view. To achieve this, I used Bootstrap to create a simple design that included the author, likes, dislikes, a time since posting, a reply button, and a delete button.

I also utilized Razor to display a stylized time since the comment was posted:
```
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
```

![styled comment](https://github.com/adinrichter/CS_code_summary/blob/main/media/styled_comment.png)

# Likes & Dislikes
The next story I worked on was adding functionality to the like and dislike buttons on the comments. This was a challenging task as it required me to use AJAX and MVC in the same application.

I wrote Javascript functions to call the like and dislike controllers using AJAX. These functions also update the page to show a live display of the number of likes and dislikes without the page needing to be reloaded:

![like and dislike buttons](https://github.com/adinrichter/CS_code_summary/blob/main/media/like_and_dislike_buttons.gif)

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

![progress bar](https://github.com/adinrichter/CS_code_summary/blob/main/media/progress_bar.png)

To make the like and dislike function more efficient, I added a likeratio controller to get the current number likes and dislikes with ajax, and added an update function that runs when the like or dislike button is clicked so that it updates in real-time:

![realtime progress bar](https://github.com/adinrichter/CS_code_summary/blob/main/media/realtime_progress%20bar.gif)

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

I used a modal to bring up a confirmation menu when the delete button is clicked. The modal contains a message asking the user to confirm the deletion and two buttons, one to cancel the action and the other to delete the comment, I also used another modal to bring up a pop-up confirming that the message was deleted successfully:

![delete confirmation](https://github.com/adinrichter/CS_code_summary/blob/main/media/delete_confirmation.gif)

To interact with the second modal, delete the comment client-side, and call the delete controller, I made a deleteComment JavaScript function which also calls a confirmDelete function. The confirmDelete function shows a confirmation pop-up that the comment was deleted once the AJAX completes successfully. It also calls the deleteTable function which deletes the comment from the DOM:
```
function deleteComment(id) {
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

# Final Thoughts
This was an amazing project, while I wish I could have gotten to do more work on it, I learned a ton about the design process for web design, and gained a much better capacity for problem solving and design. I loved the process of getting an open-ended task, and then getting to find my own unique solution to solving it.
