# LiveProject_CodeSummary_Python
 Summary and highlights of my work on Python Live Project.
 
<h1>Python Live Project</h1>

<h2>Introduction</h2>
This is a summary of a project I completed on  a two week sprint(1/11/21 - 1/25/21) working with Python and the Django Framework.     I was tasked with building a interactive website for managing one's collection of things related to various hobbies, as well as API and Data Scraped content for those hobbies.  I decided to use sports as my hobby of interest-- more specifically the NBA.  I built my web application--so dubbed the "SportsApp", using the programming language Python and the powerful Django Framework along with some pip package installations to ensure that all team members' virtual environments were in sync. The SportsApp gives a user the ability to add/view/edit/delete saved NBA games to a WatchList Archive, create a favorite player profile, and even view current NBA Eastern and Conference Standings(via web scraping). Although ideally I would have liked to implement an Email API to send game time reminders to users or more webscraping to provide the the top NBA headlines, I am proud of what I was able to accomplish with my first live project!  With out further ado lets jump into it!
 
 
<h2> Building the App</h2>
<p> To start, I created an app through Djanago and registered it within the main project. I created my base and home templates, then added a function to render those pages. Lastly, I created a URLS file and registered it with the main app. 


<h2>Creating the Model and Form</h2>
<p> I created two models for this project. The first model is a NBA Game object and the second is a Favorite Player object. For the NBA Game model, I provided the choices for the Away Team and Home Team via the eastern and western conferences. To create the user input form, I created a new forms.py file and utilized a fe3w Django widgets to display choices on the users end. I made a template page for the form, then I created a views function to  render this page and connect it to my URLS file.
 
 

 
```Python
class SavedNbaGame(models.Model):
    email = models.EmailField(max_length=50, blank=False, null=False)
    home_team = models.CharField(max_length=50, blank=False, choices=NBA_TEAM)
    away_team = models.CharField(max_length=50, blank=False, choices=NBA_TEAM)
    date_game = models.DateField(blank=False)
    time_start = models.TimeField(blank=False)
    game_notes = models.TextField(max_length=250, default="", blank=True, null=True)

    SavedNbaGame = models.Manager()

    def __str__(self):
        return "{} vs {} on {} at {}".format(self.home_team, self.away_team, self.date_game, self.time_start)
```





```Python
class FavPlayer(models.Model):
    email = models.EmailField(max_length=50, blank=False, null=False)
    fav_player = models.CharField(max_length=50, blank=False)
    corresponding_team = models.CharField(max_length=50, blank=False, choices=NBA_TEAM)

    FavPlayer = models.Manager()

    def __str__(self):
        return "Hello {}, {} is playing tonight! Tune in and watch!".format(self.email, self.fav_player)
```



Example of choices provided for selecting Home Team or Away Team




```Python
NBA_TEAM = [
    ('Atlanta Hawks', 'Atlanta Hawks'),
    ('Boston Celtics', 'Boston Celtics'),
    ('Brooklyn Nets', 'Brooklyn Nets'),
    ('Charlotte Hornets', 'Charlotte Hornets'),
    ('Chicago Bulls', 'Chicago Bulls'),
    ('Cleveland Cavaliers', 'Cleveland Cavaliers'),
    ('Dallas Mavericks', 'Dallas Mavericks'),
    ('Denver Nuggets', 'Denver Nuggets'),
    ('Detroit Pistons', 'Detroit Pistons'),
    ('Golden State Warriors', 'Golden State Warriors'),
    ('Houston Rockets', 'Houston Rockets'),
    ('Indiana Pacers', 'Indiana Pacers'),
    ('Los Angeles Clippers', 'Los Angeles Clippers'),
    ('Los Angeles Lakers', 'Los Angeles Lakers'),
    ('Memphis Grizzlies', 'Memphis Grizzlies'),
    ('Miami Heat', 'Miami Heat'),
    ('Milwaukee Bucks', 'Milwaukee Bucks'),
    ('Minnesota Timberwolves', 'Minnesota Timberwolves'),
    ('New Orleans Pelicans', 'New Orleans Pelicans'),
    ('New York Knicks', 'New York Knicks'),
    ('Oklahoma City Thunder', 'Oklahoma City Thunder'),
    ('Orlando Magic', 'Orlando Magic'),
    ('Philadelphia 76ers', 'Philadelphia 76ers'),
    ('Phoenix Suns', 'Phoenix Suns'),
    ('Portland Trailblazers', 'Portland Trailblazers'),
    ('Sacramento Kings', 'Sacramento Kings'),
    ('San Antonio Spurs', 'San Antonio Spurs'),
    ('Toronto Raptors', 'Toronto Raptors'),
    ('Utah Jazz', 'Utah Jazz'),
    ('Washington Wizards', 'Washington Wizards'),
]
```


<h2>CRUD Functionality</h2>
<p>



<h2>Create</h2>


<h2>Read</h2>

[Story 3: Display all items from database]
[Story 4: Details page]

<h2>Update and Delete</h2>
[Story 5: Edit and Delete Functions]

<h2>Web Scraping</h2>
[Stories 6 & 7: Beautiful Soup]
[Story 9: Save API or scraped results]

<h2>API</h2>
[Stories 6 & 7: API]
[Story 9: Save API or scraped results]

<h2>Front End Development</h2>
[Story 8: Front End Improvements]
[Any other Front End work you’ve done that you would like to showcase]

<h2>Skills Acquired</h2>
This sprint provided me with my first oppurtunity to meet with fellow students working in an Agile Framework environment. We utilized Azure DevOps as our platform of service and used User Stories as a means of being assigned tasks to be completed.  Git was implented as our version control system and gave me the chance to create my first working branch, perform commits, merges,  and push/pulls on an actual live project.  Although I did not work directly with my peers on this project, I was able to meet with them in a daily standup and weekly code retrospectives that really provided a lot of good dialogue about problems we faced, breakthough moments, or ideas we had.

