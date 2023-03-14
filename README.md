# MLB Web Scraping

## Project Objective

To scrape data pertaining to the batting performance of all 347 players in Major League Baseball, USA in 2022, from a larger dataset of batting, pitching, fielding and other statistics, and store it in a CSV file.

## Data Used

ESPN's <a href = "https://www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/start/1"> MLB Statistics 2022 season </a>

## Tools Used

<b> Python Libraries (in Jupyter Notebook) - </b>

1. pandas
2. requests (to read website's source code)
3. BeautifulSoup (to search and parse the website's code)
4. re (Regular Expressions)

## Web Scraping Process

### Step 1:

Import all the necessary Python libraries

    # Import Libraries

    import pandas as pd
    import re
    import requests
    from bs4 import BeautifulSoup

### Step 2:

Access the website from which the data is to be scraped, using <b> requests </b> library's <b>.get()</b> method, using the code - 

    page = requests.get("http://www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/start/1")

### Step 3:

View the source code of the website with the get request:

    page.text
    
<b> Output: </b>

'\n<!DOCTYPE html>\n<html xmlns:fb="https://www.facebook.com/2008/fbml">\n<head>\n\n<script>\n(function redirectToHttpIfHttps() {\n   var win      = typeof window !== \'undefined\' && window,\n       location = win && win.location,\n       protocol = location && location.protocol;\n\n   if (protocol === \'https:\' && !true) {\n        location.href = location.href.replace(\'https://\', \'http://\');\n   }\n})();\n</script><meta charset="iso-8859-1">\n<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">\n<link rel="icon" sizes="any" mask href="https://a.espncdn.com/favicon.ico">\n<meta name="theme-color" content="#CC0000">\n<script type="text/javascript">\n    if(true && navigator && navigator.userAgent.toLowerCase().indexOf("teamstream") >= 0) {\n        window.location = \'http://m.espn.com/mobilecache/general/apps/sc\';\n    }\n</script><title>MLB Baseball Career Batting Leaders - Major League Baseball - ESPN</title>\n<meta name="google-site-verification" content="xuj1ODRluWa0frM-BjIr_aSHoUC7HB5C1MgmYAM_GkA" />\n<meta name="msvalidate.01" content="B1FEB7C682C46C8FCDA3130F3D18AC28" />\n<meta name="googlebot" content="noodp" />\n<meta name="robots" content="index, follow" />\n<meta name="description" content="Look for your favorite MLB slugger in this impressive list of baseball\'s top 500 career batting leaders." />\n<meta name="keywords" content="MLB Career Batting Leaders, MLB career pitching leaders, batting average leaders, pitching leaders, MLB historical leaders"...............

### Step 4:

Use BeautifulSoup Library to parse the website's code.

    # Pull in the Websites source code

    soup = BeautifulSoup(page.text, "html.parser")
  
### Step 5:

Use BeautifulSoup Library's find() and find_all() to extract information from the source code. 

We can type the code to extract the stats of any player. For example, to fetch the 2022 stats of Jeff McNeil, who tops the overall batting list -

    soup.find_all('tr',attrs = {'class':'oddrow player-10-33900'})

<b> Output: </b>

[<tr align="right" class="oddrow player-10-33900"><td align="left">1</td><td align="left"><a href="https://www.espn.com/mlb/player/_/id/33900/jeff-mcneil"><span class="bi">Jeff McNeil</span></a></td><td align="left">5</td><td>148</td><td>533</td><td>73</td><td>174</td><td>39</td><td>1</td><td>9</td><td>62</td><td>40</td><td>61</td><td>4</td><td>0</td><td class="sortcell">.326</td></tr>]

### Step 6:

To scrape Column Headers, use the code - 

    soup.find_all('tr',attrs = {'class':'colhead'})

<b> Output: </b> (Displayed 5 times)

<tr align="right" class="colhead"><td></td><td align="left" width="30%">PLAYER</td><td align="left">YRS</td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/gamesPlayed" title="Games">G</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/atBats" title="At Bats">AB</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/runs" title="Runs Scored">R</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/hits" title="Hits">H</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/doubles" title="Doubles">2B</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/triples" title="Triples">3B</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/homeRuns" title="Home Runs">HR</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/RBIs" title="Runs Batted In">RBI</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/walks" title="Walks">BB</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/strikeouts" title="Strikeouts">SO</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/stolenBases" title="Stolen Bases">SB</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/caughtStealing" title="Caught Stealing">CS</a></td><td class="sortcell">BA</td></tr>,



This code finds all instances of the code where the 'class' tag is given as 'colhead'. Since each page on EPSN's MLB stats page contains 50 players and there is a table header after every 10 players, the above code displays the table header code 5 times. However, if the <b>find()</b> function is used instead of the <b>find_all()</b> function, then the table header will be displayed only once.

    soup.find('tr',attrs = {'class':'colhead'})
    
<b> Output: </b>

<tr align="right" class="colhead"><td></td><td align="left" width="30%">PLAYER</td><td align="left">YRS</td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/gamesPlayed" title="Games">G</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/atBats" title="At Bats">AB</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/runs" title="Runs Scored">R</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/hits" title="Hits">H</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/doubles" title="Doubles">2B</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/triples" title="Triples">3B</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/homeRuns" title="Home Runs">HR</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/RBIs" title="Runs Batted In">RBI</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/walks" title="Walks">BB</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/strikeouts" title="Strikeouts">SO</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/stolenBases" title="Stolen Bases">SB</a></td><td><a href="//www.espn.com/mlb/history/leaders/_/breakdown/season/year/2022/sort/caughtStealing" title="Caught Stealing">CS</a></td><td class="sortcell">BA</td></tr>

In order to display a player's stats without all the HTML code, the results of the <b>find()</b> function may be stored in a separate variable and then the text part may be extracted from each part of the row.

    row = soup.find('tr',attrs = {'class':'oddrow player-10-33900'})
    for data in row.find_all('td'):
        print(data.get_text())

<b> Output: </b>

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

All the players statistics can be scraped using a similar code.

### Step 7:

Create column names for a pandas DataFrame to store the stats of all the players, which can be exported to a .csv file. 

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

<b> Output: </b>

PLAYER	YRS	G	AB	R	H	2B	3B	HR	RBI	BB	SO	SB	CS	BA
----------------------------------------------

### Step 8:

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

### Step 9:

This will get the stats on each page which contains only 50 players. To extract the data of all 347 players in the MLB 2022 season, the data from all the page URLs have to be scraped. All the URLs need to be recreated so that the data from player 1 to 347 can be scraped in increments of 50, using the same code above.

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

<b> Output: </b>

![alt text](https://raw.githubusercontent.com/rahulshankariyer/MLB_Web_Scraping/main/MLB%20Batting%20Statistics%202022.png)

## Step 10: 

Export the data from the dataframe 'final_df' to a CSV file.

    # Export to csv

    final_df.to_csv(r"Desktop\mlb_stats.csv",index = False,sep = ',',encoding = 'utf-8')

## Key:

The column names in the above created data frame, which represent various batting stats, are given in short forms. Anyone who's not well versed with baseball stats may refer to the below key to know what each column represents.

<b>PLAYER - </b>Player Name
<b>YRS - </b>MLB Seasons Played (inclusive of the 2022 season for which the data was scraped)
<b>G - </b>Games Played 
<b>AB - </b>At-Bats, ie, Plate Appearences
<b>R - </b>Runs
<b>H - </b>Hits
<b>2B - </b>Doubles
<b>3B - </b>Triples
<b>HR - </b>Home Runs
<b>RBI - </b>Runs Batted In
<b>BB - </b>Base on Balls, ie, Walks
<b>SO - </b>Strike Outs
<b>SB - </b>Stolen Bases
<b>CS - </b>Caught Stealing
<b>BA - </b>Batting Average
