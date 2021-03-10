# LiveProject_CodeSummary_Python
 Summary and highlights of my work on Python Live Project.
 
<h1>Python Live Project</h1>

<h2>Introduction</h2>
This is a summary of a project I completed on  a two week sprint(1/11/21 - 1/25/21) working with Python and the Django Framework.     I was tasked with building a interactive website for managing one's collection of things related to various hobbies, as well as API and Data Scraped content for those hobbies.  I decided to use sports as my hobby of interest-- more specifically the NBA.  I built my web application--so dubbed the "SportsApp", using the programming language Python and the powerful Django Framework along with some pip package installations to ensure that all team members' virtual environments were in sync. The SportsApp gives a user the ability to add/view/edit/delete saved NBA games to a WatchList Archive, create a favorite player profile, and even view current NBA Eastern and Conference Standings(via web scraping). Although ideally I would have liked to implement an Email API to send game time reminders to users or more webscraping to provide the the top NBA headlines, I am proud of what I was able to accomplish with my first live project!  With out further ado lets jump into it!
 
 
<h2> Building the App</h2>
<p> To start, I created an app through Djanago and registered it within the main project. I created my base and home templates, then added a function to render those pages. Lastly, I created a URLS file and registered it with the main app. 


<h2>Creating the Model and Form</h2>
<p> I created two models for this project. The first model is a NBA Game object and the second is a Favorite Player object. For the NBA Game model, I provided the choices for the Away Team and Home Team via the eastern and western conferences. 
 
 

 
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
To create the user input form, I created a new forms.py file and utilized a few Django widgets to display choices on the users end. I made a template page for the form, then I created a views function to  render this page and connect it to my URLS file. I also utilized a, new to me, Django package called CrispyForms to make my form. 



```Python
class DateInput(forms.DateInput):
    input_type = 'date'


class TimeInput(forms.TimeInput):
    input_type = 'time'
    format('%H:%M',)


class NbaGameForm(ModelForm):
    class Meta:
        model = SavedNbaGame
        fields = '__all__'

        widgets = {
            'date_game': DateInput(),
            'time_start': forms.TimeInput(attrs={'type': 'time'}),


        }

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.helper = FormHelper()
        self.fields['home_team'].label = "Home Team: "
        self.fields['away_team'].label = "Away Team: "
        self.fields['date_game'].label = "Date of Game: "
        self.fields['time_start'].label = "Start Time: "
        self.fields['email'].label = "Contact Email: "
        self.helper.layout = Layout(
            Row(
                Column('home_team', css_class='form-group makeinline col-md-4 mb-1'),
                Column('away_team', css_class='form-group makeinline col-md-4- mb-1'),
                css_class='form-row'
            ),
            Row(
                Column('date_game', css_class='form-group makeinline col-md-4 mb-1'),
                Column('time_start', css_class='form-group makeinline col-md-4 mb-1'),
                css_class='form-row'
            ),
            Row(
                Column('email', css_class='form-group col-md-6- mb-1'),
                css_class='form-row'
            ),
            Submit('submit', 'Save'),
            HTML('<a class="btn btn-danger" onclick="history.back()">Back</a>')
        )
```




<a href="https://github.com/SloneBone/LiveProject_CodeSummary_Python/blob/main/Code_Snippets/screenShot_NBA_crispyform.png">Picture of Form Page</a>

<h2>CRUD Functionality</h2>
<p>After creating the model it was time to add the ability to Create, Read, Update(Edit) and Delete. To start we add corresponding functions.  



<h2>Create</h2>
```Python
# Create CRUD Functionality for adding NBA game to watchlist
def add_nba_game(request):
    form = NbaGameForm(request.POST or None)
    if form.is_valid():
        form.save()
        return redirect('add_nba_game')
    else:
        print(form.errors)
    context = {'form': form}
    return render(request, 'SportsApp/SportsApp_create.html', context)


# Create CRUD Functionality for Adding new favorite player

def add_fav_player(request):
    form = FavPlayerForm(request.POST or None)
    if form.is_valid():
        form.save()
        return redirect('add_fav_player')
    else:
        print(form.errors)
    context = {'form': form}
    return render(request, 'SportsApp/SportsApp_addFavPlayer.html', context)
```


<h2>Read</h2>
```Python
# Creating a function to get all the items from dB and send it to index.html template

def add_game_archive(request):
    game_list = SavedNbaGame.SavedNbaGame.order_by('date_game').all()
    page = request.GET.get('page', 1)

    paginator = Paginator(game_list, 5)
    try:
        game_list = paginator.page(page)
    except PageNotAnInteger:
        game_list = paginator.page(1)
    except EmptyPage:
        game_list = paginator.page(paginator.num_pages)
    return render(request, 'SportsApp/SportsApp_index.html', {'game_list': game_list})

# Creating a function to get all the item from dB and send it to favArchive.html


def add_fav_archive(request):
    fav_list = FavPlayer.FavPlayer.order_by('fav_player').all()
    page = request.GET.get('page', 1)

    paginator = Paginator(fav_list, 5)
    try:
        fav_list = paginator.page(page)
    except PageNotAnInteger:
        fav_list = paginator.page(1)
    except EmptyPage:
        fav_list = paginator.page(paginator.num_pages)
    return render(request, 'SportsApp/SportsApp_favArchive.html', {'fav_list': fav_list})
```



<h2>Details Page</h2>
```Python
# Creating a function to get just one item from the dB and render it on details.html template
def game_details(request, pk):
    pk = int(pk)
    game = get_object_or_404(SavedNbaGame, pk=pk)
    return render(request, 'SportsApp/SportsApp_details.html', {'game': game})
```




<h2>Update and Delete</h2>




```Python
# Creating CRUD function to edit(update) a single item in dB(SavedNbaGame)
def edit_game(request, pk):
    pk = int(pk)
    game = get_object_or_404(SavedNbaGame, pk=pk)
    form = NbaGameForm(data=request.POST or None, instance=game)
    if request.method == 'POST':
        if form.is_valid():
            form = form.save(commit=False)
            form.save()
            return redirect('../game_details/')
        else:
            print(form.errors)
    else:
        return render(request, 'SportsApp/SportsApp_edit.html', {'game': game, 'form': form})


# Creating a CRUD Fx to delete a single item in db
def delete_game(request, pk):
    pk = int(pk)
    game = get_object_or_404(SavedNbaGame, pk=pk)
    if request.method == 'POST':
        game.delete()
        return redirect('add_game_archive')
    return render(request, 'SportsApp/SportsApp_delete.html', {"game": game})


# Creating a Fx to confirm the user wants to delete
def confirm_delete(request):
    if request.method == 'POST':
        form = NbaGameForm(request.POST or None)
        if form.is_valid():
            form.delete()
            return redirect('add_game_archive')
    else:
        return redirect('add_game_archive')
```

<h2>Web Scraping</h2>
[Stories 6 & 7: Beautiful Soup]
<p>For this story, I decided to add a page to my project displaying a table of the current NBA Standings for the Eastern and Western Conferences. I utilized web scraping for the first time to scrape data from a website(https://www.basketball-reference.com/leagues/NBA_2021_standings.html) that has the current standings. Due to the fact that this table is updated daily, so too will the standings table in my project.<p><br>
<h3>Views.py page with corresponding function to succesfully pull table data</h3>


 
```Python
# Beautiful Soup
def nba_standings(request):
    page = requests.get("https://www.basketball-reference.com/leagues/NBA_2021.html")
    soup = BeautifulSoup(page.content, 'html.parser')
    east_conf_table = soup.find(id="confs_standings_E")
    west_conf_table = soup.find(id="confs_standings_W")
    east_teams_standings = east_conf_table.find_all(class_='full_table')
    west_teams_standings = west_conf_table.find_all(class_='full_table')
    easts_list = []
    wests_list = []
    for team in east_teams_standings:
        east_team_name = team.find(class_='left').get_text()
        east_team_wins = team.find_all(class_='right')[0].get_text()
        east_team_losses = team.find_all(class_='right')[1].get_text()
        east_team_win_loss_pct = team.find_all(class_='right')[2].get_text()
        east_team_games_back = team.find_all(class_='right')[3].get_text()
        east_team_pts_per_game = team.find_all(class_='right')[4].get_text()
        east_team_opp_pts_per_game = team.find_all(class_='right')[5].get_text()
        print(east_team_name, east_team_wins, east_team_losses, east_team_win_loss_pct, east_team_games_back,
              east_team_pts_per_game, east_team_opp_pts_per_game)

        east_team_dict = {
            'east_team_name': east_team_name,
            'east_team_wins': east_team_wins,
            'east_team_losses': east_team_losses,
            'east_team_win_loss_pct': east_team_win_loss_pct,
            'east_team_games_back': east_team_games_back,
            'east_team_pts_per_game': east_team_pts_per_game,
            'east_team_opp_pts_per_game': east_team_opp_pts_per_game,
            }
        easts_list.append(east_team_dict)

    for team in west_teams_standings:
        west_team_name = team.find(class_='left').get_text()
        west_team_wins = team.find_all(class_='right')[0].get_text()
        west_team_losses = team.find_all(class_='right')[1].get_text()
        west_team_win_loss_pct = team.find_all(class_='right')[2].get_text()
        west_team_games_back = team.find_all(class_='right')[3].get_text()
        west_team_pts_per_game = team.find_all(class_='right')[4].get_text()
        west_team_opp_pts_per_game = team.find_all(class_='right')[5].get_text()
        print(west_team_name, west_team_wins, west_team_losses, west_team_win_loss_pct, west_team_games_back,
              west_team_pts_per_game, west_team_opp_pts_per_game)

        west_team_dict = {
            'west_team_name': west_team_name,
            'west_team_wins': west_team_wins,
            'west_team_losses': west_team_losses,
            'west_team_win_loss_pct': west_team_win_loss_pct,
            'west_team_games_back': west_team_games_back,
            'west_team_pts_per_game': west_team_pts_per_game,
            'west_team_opp_pts_per_game': west_team_opp_pts_per_game,
        }
        wests_list.append(west_team_dict)

    context = {'easts_list': easts_list, 'wests_list': wests_list}
    return render(request, 'SportsApp/SportsApp_standings.html', context)
```
<h3>Standings.html page html code to render the scraped data into a table.</h3>




```Python
{% extends "SportsApp/SportsApp_base.html" %}


{% block title %}SportsApp{% endblock %}

{% block header %}
<div class="createPage-header">
    <h1>Current NBA Standings</h1>
</div>
{% endblock %}

{% block content %}
<div class="createPage-text">
    <p class="createPage-txt-wrapper">Here you can find the current NBA standings for both the Eastern and Western Conferences!! <br>
        This is updated daily!</p>
</div>

<div class="east-conf">
    <table class="east-table">
        <div>
            <thead>
                <tr>
                    <th>Eastern Conference </th>
                    <th>Wins:</th>
                    <th>Losses:</th>
                    <th>Win/Loss Percent:</th>
                    <th>Games Back:</th>
                    <th>Points per game:</th>
                    <th>Opp pts per game:</th>
                </tr>
            </thead>
            {% for east in easts_list %}
            <div>
                <tr>
                    <td class="border">{{ east.east_team_name }}</td>
                    <td class="border">{{ east.east_team_wins }}</td>
                    <td class="border">{{ east.east_team_losses }}</td>
                    <td class="border">{{ east.east_team_win_loss_pct }}</td>
                    <td class="border">{{ east.east_team_games_back }}</td>
                    <td class="border">{{ east.east_team_pts_per_game }}</td>
                    <td class="border">{{ east.east_team_opp_pts_per_game }}</td>
                </tr>
            </div>

        </div>


        {% endfor %}

    </table>

</div>


<div class="west-conf">
    <table class="west-table">
        <thead>
             <tr>
                <th>Western Conference</th>
                <th>Wins:</th>
                <th>Losses:</th>
                <th>Win/Loss Percent:</th>
                <th>Games Back:</th>
                <th>Points per game:</th>
                <th>Opp pts per game:</th>
            </tr>
        </thead>

        {% for west in wests_list %}
        <tr>
            <td class="border">{{ west.west_team_name }}</td>
            <td class="border">{{ west.west_team_wins }}</td>
            <td class="border">{{ west.west_team_losses }}</td>
            <td class="border">{{ west.west_team_win_loss_pct }}</td>
            <td class="border">{{ west.west_team_games_back }}</td>
            <td class="border">{{ west.west_team_pts_per_game }}</td>
            <td class="border">{{ west.west_team_opp_pts_per_game }}</td>
        </tr>
        {% endfor %}

    </table>

</div>

{% endblock %}
```

<a href="">Screenshot of Standings Page</a>



<h2>Skills Acquired</h2>
This sprint provided me with my first oppurtunity to meet with fellow students working in an Agile Framework environment. We utilized Azure DevOps as our platform of service and used User Stories as a means of being assigned tasks to be completed.  Git was implented as our version control system and gave me the chance to create my first working branch, perform commits, merges,  and push/pulls on an actual live project.  Although I did not work directly with my peers on this project, I was able to meet with them in a daily standup and weekly code retrospectives that really provided a lot of good dialogue about problems we faced, breakthough moments, or ideas we had.

