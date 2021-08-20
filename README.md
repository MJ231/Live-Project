# Live Project At The Tech Academy

## Introduction:
In the last two weeks of my time at the Tech Academy, I worked with my fellow peers to develop and update a full-scale Theatre Website in C#. It was neat to see how other good developers work with what they have to make a quality, and effiecint product. I worked on several stories that I am very proud of. Now, much of the site had already been built, but everyone on the team had a chance to work on front-end and back-end stories. Over the two week sprint, I also had the opportunity to work on some other project management and team programming skills that I'm confident I will use again on future projects.

Below are some of the descriptions of stories I worked on over the course of the two week period. You will see code snippets of a few of the coding stories.

## Summary Of Technologies Used:

### .Net Framework MVC Web Application
A mature and featureful MVC framework from Microsoft.
### Entity Framework - Code First
An ORM or Object Relational Mapping framework allows one to seamlessly interface with a database such as SQL Server. Entity framework makes database CRUD operations easy and efficient.
Code First means that our database is created and updated directly in the application code. Since the data model is subject to change in the early stages of development, the database must evolve too. Instead of creating a database and then an application that must rigidly adhere to that database, the code first approach allows the database to be flexible during development.
### Razor Views - Webpages
Razor pages allow backend code written in C# to be neatly inserted into an HTML document for rendering at runtime.
### Identity Model Framework
An advanced framework for authentication of users and accounts.

# Back-End Stories:

## Story One - Building A Model For Entity Framework
A model is simply a C# class built to represent the type of data that will be stored in the database. It also must properly represent the relationships that it has with other models.

The task was to create an entity model for a Rental Request class so that the Rental Requests can be saved to the database. When finisiehd, used SQL Server Object Explorer in Visual Studio 2019 to see if the table was created successfully.

```
namespace TheatreCMS3.Areas.Rent.Models
{
    public class RentalRequest
    {
       public int RentalRequestID { get; set; }
       public string ContactPerson { get; set; }
       public string Company { get; set; }
       public DateTime RequestedTime { get; set; }
       public DateTime StartTime { get; set; }
       public DateTime EndTime { get; set; }
       public string ProjectInfo { get; set; }
       public int RentalCode { get; set; }
       public bool Accepted { get; set; }
       public bool ContractSigned { get; set; }
     }
----------------------------------------------------------------------------------------------------------------
{
     public DbSet<RentalRequest> RentalRequests { get; set; } //Located In The IdentityModels Partial View Page.
}
```

## Story Two - Creating Seed Data
Building and testing a database would be impossible with out seed data. Seed data is fictional data to be used as a placeholder for testing. This task really helped me to understand how the CREATE and UPDATE process works in Entity Framework.

The task was to create a seed method for a HistoryManager class, but also create a User Role and assigned it to the HistoryManager that was being seeded.

```
namespace TheaterCMS3.Areas.Rent.Models
{
    public class RentalRequest
    {
       public int RestrictedUsers { get; set; }
       public int RentalReplacementRequests { get; set; }
       
       publci static void Seed()
       {
          ApplicationDbContext db = new ApplicationDbContext();
          var roleManager = new RoleManager<IdentityRole>(new RoleStore<IdentityRole>(db));
          var userManager = new UserManager<ApplicationUser>(new UserStore<ApllicationUser>(db));
          
          if (!roleManager.RoleExists("HistoryManager"))
          {
              var role = new IdentityRole
              {
                  Name = "History Manager"
              };
              roleManager.Create(role);
              
              HistoryManager HistoryManagerSeed = new HistoryManager
              {
                  UserName = "HistoryManager",
                  Email = "HistoryManager@theatre.com",
                  RestrictedUsers = 0
                  RentalReplacementRequests = 0
              };
              string HistoryManagerSeedPass = "Password23!";
              var checkPMCreate = userManager.Create(HistoryManagerSeed, HistoryManagerSeedPass);
              
              if(checkPMCreate.Succeeded)
              {
                  userManager.AddToRole(HistoryManagerSeed.Id, "HistoryManager");
              }
              
              db.Users.Add(HistoryManagerSeed);
          }
-----------------------------------------------------------------------------------------------------------
 public void Configuration(IAppBuilder app) //Located In The Startup Page Of The Main Project.
 {
     ConfigureAuth(app);
     HistoryManager.Seed();
 }
 ```
 
 ## Story Three - Restricted CRUD Pages For RentalHisotries(HistoryManager)
Creating Restricted CRUD Pages for users who are not identified as a HistoryManager. First, had to create a new View page (AccessDenied). This page would show up when a user would try to access the restricted pages. After, design the AccessDenied page and use Razor to create a link to take the user back to the Login page.

```
private ApplicationDbContext db = new ApplicationDbContext();

    // GET: Rent/RentalHistories

    public ActionResult Index()
{
    return View(db.RentalHistories.ToList())
------------------------------------------------------------------------------------------------------------
}

    // GET: Rent/RentalHistories
    [HistoryManagerAuthroize]
    public ActionResult Create()
{
    return View();
------------------------------------------------------------------------------------------------------------
}

    // GET: Rent/RentalHistories
    [HistoryManagerAuthorize]
    public ActionResult Edit(int? id)
{
    if (id == null)
------------------------------------------------------------------------------------------------------------
}

    // GET: Rent/RentalHistories
    [HistoryManagerAuthorize]
    public ActionResult Delete(int? id)
{
    if (id == null)
------------------------------------------------------------------------------------------------------------
    return RedirectToAction("Index");
}

public ActionResult AccessDenied()
{
    return View();
}

protected override void Dispose(bool disposing)
{
    if (disposing)
------------------------------------------------------------------------------------------------------------
    base.Dispose(disposing);
}
------------------------------------------------------------------------------------------------------------
}
    //Redirects Anyone Who Is Not Authorized To Be In RentalHistories To AccessDenied Page.
    public class HistoryManagerAuthroize : AuthorizeAttribute
    {
    public override void OnAuthorization(AuthorizationContext filterContext)
    {
        base.OnAuthorization(filterContext);
        
        if (filterContext.Result is HttpUnauthorizedResult)
        {
            filterContext.Result = new RedirectResult("~/Rent/RentalHistories/AccessDenied");
        }
    }
}
------------------------------------------------------------------------------------------------------------
//Located In The AccessDenied Partial View Page.
@{
    ViewBag.Title = "AccessDenied";
}

<div class="text-center">
    <h1>AccessDenied</h1>
    You Are Not Authorized to Visit That Page.
    <div>
      <img src="~/Content/images/restricted.png" />
    </div>
  
    <div>
    <a href="@Url.Action("Login", "Account", new { area = ""})">Login</a>
    </div>
</div>
```
# Front-End Stories:

## Story Four - Updating The Navagation Bar
Story was to make the navagation bar user friendly. Updating the appearance using HTML and CSS. Included a search box so users can search for what they are looking for on the Theatre Website.

* [Navagation Bar](https://github.com/MJ231/Live-Project/blob/04447e63b1d6683e685bad979fffee4deadeb075/NavBar.PNG)

## Story Five - Creating A HistoryManager Button
Story was to make a HistoryManger Button for the user to sign-in as the HistoryManager when the user is on the index or details page. Created a Partial View page for this login button, and rendered it in the layout page of the main project. By clicking the HistoryManager Button, it should automatically login the user as the HistoryManager seeded in the Database. Used HTML and CSS for styling and appearance.

* [HistoryManager Button](https://github.com/MJ231/Live-Project/blob/837bc1bc9f82dabbbb730b364714f58f14a3b619/HistoryMButton.PNG)

# Other Attributes Acquired:
* Working with a group of developers to identify front and back end bugs to the improve usability of an application.
* Learning new efficiencies from other developers by observing their workflow and asking questions.
* Practice with team programming/pair programming when one developer runs into a bug they cannot solve.
* Researching for answers when running into a roadblock.
* Being in a code stand-up each day.
* Experiencing Agile/SCRUM meetings.
* Creating Partial Views.
* Worked on eight stories in a sprint fashion in a two week period.
* Using C#, JavaScript, HTML and CSS, MVC, Razor, SQL Database, Entity Framework, and much more.
* Using all the information and knowledge that was given from the bootcamp, and transferring it into this live project.
