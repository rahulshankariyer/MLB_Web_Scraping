# MLB Web Scraping

## Project Objective

To scrape the data of Batting performance alone of all (total number) players in Major League Baseball in 2022, from a larger dataset of batting and pitching statistics, and store it in a CSV file.

## Data Used

ESPN's <a href = "https://www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/start/1"> MLB Statistics 2022 season </a>

## Tools Used

Python Libraries - 

1. pandas
2. requests (to read website's source code)
3. BeautifulSoup (to search and parse the website's code)
4. Regular Expressions (re)

## Web Scraping Process

### Step 1:

Import all necessary Python libraries (in Jupyter Notebook)

    # Import Libraries

    import pandas as pd
    import re
    import requests
    from bs4 import BeautifulSoup

### Step 2:

Access the website from which the data is to be scraped, using <b> requests </b> library's get() method, using the code - 

    page = requests.get("http://www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/start/1")

### Step 3:

View the source code of the website with the get request:

    page.text
    
The next step is now to pull in the website's source code using the BeautifulSoup library.

    # Pull in the Websites source code

    soup = BeautifulSoup(page.text, "html.parser")
    
We can also type code to extract the stats of any particular player now using the source code. The below code for example, fetches the 2022 stats of Jeff McNeil, who tops the overall batting list.

    soup.find_all('tr',attrs = {'class':'oddrow player-10-33900'})

The first thing that needs to be done though is to extract the column headers. That can be done using the code below.

    soup.find_all('tr',attrs = {'class':'colhead'})
    
This code finds all instances of the code where the 'class' tag is given as 'colhead'. Since each page on the MLB stats page contains 50 players and there's table header after every 10 players, the above code should display the table header code 5 times. However, if the find() function is used instead of the find_all() function, then the table header will be displayed only once.

    soup.find('tr',attrs = {'class':'colhead'})
    
Now, in order to display a player's stats without all the HTML code, one can simply store the results of the find function in a separate variable and then extract the text part from each part of the row.

    row = soup.find('tr',attrs = {'class':'oddrow player-10-33900'})
    for data in row.find_all('td'):
        print(data.get_text())

Output:

1

Jeff McNeil

5

148

533

73

174

39

1

9

62

40

61

4

0

.326

Now, using the lines of code discussed above the all the players statistics can be scraped. First, we will start with creating the column names for the table header. 

    # Create column names from the table header
    ## Identify Header Row

    header = soup.find('tr',attrs = {'class':'colhead'})

    ## Extract Column Names from Header Row

    columns = [col.get_text() for col in header.find_all('td')]

    # Columns

    # Create an empty dataframe with column names from the header
    # We will populate with the player stats shortly

    final_df = pd.DataFrame(columns=columns)
    final_df

Output:

PLAYER	YRS	G	AB	R	H	2B	3B	HR	RBI	BB	SO	SB	CS	BA
----------------------------------------------

For extracting the player rows, we can use the compile function of the 're' library as all the player classes begin with 'oddrow' or 'evenrow' followed by 'player-10-'. Once that is extracted, then the stats will be populated into the 'final_df' data frame.

    # Pull in the player rows
    ## Identify Player rows

    players = soup.find_all('tr',attrs = {'class': re.compile('row player-10-')})
    for player in players:

        ## Get the stats for each player
        stats = [stat.get_text() for stat in player.find_all('td')]

        #Create a dataframe for the single player's stats
        temp_df = pd.DataFrame(stats).transpose()
        temp_df.columns = columns

        # Join the single player's stats with the overall dataset
        final_df = pd.concat([final_df,temp_df],ignore_index = True)

Each page however, contains only 50 players. But there are 347 in the list and hence, to extract the data of all 347 players, all the URLs need to be recreated so that all the data from player 1 to 347 can be scraped in increments of 50, using the same code above.

    # Looping through each page to capture all 347 players in the league, 50 players at a time
    for i in range(1,347,50):
        # Pull in website source code
        url = 'https://www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/start/{}'.format(i)
        page = requests.get(url)
        soup = BeautifulSoup(page.text, "html.parser")

        # Pull in the player rows
        ## Identify Player rows

        players = soup.find_all('tr',attrs = {'class': re.compile('row player-10-')})
        for player in players:

            ## Get the stats for each player
            stats = [stat.get_text() for stat in player.find_all('td')]

            #Create a dataframe for the single player's stats
            temp_df = pd.DataFrame(stats).transpose()
            temp_df.columns = columns

            # Join the single player's stats with the overall dataset
            final_df = pd.concat([final_df,temp_df],ignore_index = True)
    final_df

## Exporting the data to a CSV file

Finally, the data in the dataframe 'final_df' was exported to a CSV file.

    # Export to csv

    final_df.to_csv(r"Desktop\mlb_stats.csv",index = False,sep = ',',encoding = 'utf-8')
