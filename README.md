DATA SOURCE: Espncricinfo
	Format: Pdf files

1.Data Preparation
	
	1.The three main steps involved in preparing the data for the analysis and prediction was:
		
		a. Formatting the data types of columns
			Formatting the datatypes of columns was done using pandas' cast functions such as pd.to_datetime(), float() and int()
			
		b. Merging separate chunks of data to a single master data table.
			Each pdf file contains 50 or 200 rows of data based on query configured during data retrieval.So, these separate chunks of data are merged using pandas's concat() function
			
		c. Derived Columns
			In some scenarios, new columns are derived from exisitng colummn and row values such as generating Year column from Date column, Notouts column from Scores column and Series column from misaligned row entries
			
	2.The raw data was presented in .pdf format and it is converted to .xlsx and .csv format using web-based open source pdf converters.During this conversion process, some garbage/unnecessary data for analysis gets added such as website name of datasource,page number of pdf file,other cricket schedule details etc.Those values were removed in excel and certain column value format that were misaligned with other values in that column was corrected manually in excel.
		
2.Data Cleaning

	The main step involved in data cleaning is imputing or removing null values.In this context, imputing is done more than removing null values because obtained dataset is not so huge.
		
		a.Imputing is done by null value representing characters like - with 0 to match with that datatype of column and at the same time maintaining the context of that column.
		
		b.Removal of null values is done using pandas' drop().Both row and column removal is performed as needed.
		
3.Prediction Algorithm / Model / Analysis
	
	TASK 1 and 2
	
	Machine Learning algorithm was used to create a model to predict 'SERIES OUTPUT' and the predicted result will also help in determining the 'WINNER OF THE SERIES'.
	
	Algorithm: RandomForest
	
	Datasource:SeasonIndVsAus_Master.csv, MatchesInnbyInn_Master.csv
	
	Datasource description: 
		
		SeasonIndVsAus_Master.csv gives summarized India and Australia's performace details against each other for one calendar year and the details include
			Total matches played, Total matches won,lost,tied, Runs Per Over[RPO], Highest score and Lowest score
		
		MatchesInnbyInn_Master.csv gives each match details between Indian and Australia and the details include
			Match Score, Overs faced by batting team, Ground/Venue of the match, Date of the match
	
	Steps involved to fit and train the model:
	
		1.The above two separate datasources are merged based on [YEAR] and [TEAM] columns in data tables to utilize the information provided by both the sources under one context.
			- DataFrame name in code - [ matches_Master ]
		2.Filtering the dataframe contents with respective to one team(one chosen in code is INDIA)
			- Filtered DataFrame name in code - [ indMatches_Master ]
		3.Converting all categorical variables to numerical data using dictionary.This conversion is needed because the model expects its variable to be in numerical format
		4.Creating generic function for making a classification model and accessing performance
		5.Calculated two new input features representing team rating based on team strength/form in a year, team's strength on away and home performances in a year and values are mapped accordingly
			- This particular feature is a CONFIGURABLE feature.
			- Team strength/form is Domain driven metric. Rating should increase if impact player joins the team and vice-versa. Suppose a key player is missing in opposition team,then that indirectly strengthens the home team, hence rating should be adjusted according to the scenario.[THIS CAN BE OBSERVED IN STEP 6]
			- Team's strength on away and home performances is decided based on Win% column in data table
		6.Input features to train the model were - Venue/Ground, IND_TEAM_RATING, AUS_TEAM_RATING
			#Key Value for Ground/Venue
			#Hyderabad:6 Nagpur:2 Ranchi:5 Mohali:3 Delhi:8	
			#Key Value for IND_TEAM_RATING
			#Hyderabad:8.5 Nagpur:8.5 Ranchi:9.5 Mohali:9.5 Delhi:9.5
				- having B Kumar in squad increases IND_TEAM_RATING, considered as a impact player in bowling department, his ability increases the strength in bowling department
			#Key Value for AUS_TEAM_RATING
			#Hyderabad:7 Nagpur:7 Ranchi:6.5 Mohali:6.5 Delhi:6.5
				- AUS_TEAM_RATING also to be changed based on the opposite team strength
		7.Outcome to predict is [Result_bi] [1:Won,0:Lose,-1:Draw]
		
	Tested and neglected algorithms : Logistic regression
		Reason for neglecting the algorithm: Results were inaccurate for multiple input features 
		
	Predicted Result:
		Winner of the Series - India
		Series Output  - India Australia
							4		1

	Task 3 ,5 and 6
	
	Datasource:Batting_series.csv, Batting_series2Page.csvz Batting_series3Page.csv, Bowling_series.csv, Bowling_series2Page.csv, Bowling_series3Page.csv
	
	Datasource description: 
		
		Batting_series.csv, Batting_series2Page.csvz Batting_series3Page.csv gives India and Australia's batting performace of each player in a series and the details include
			Player name, match date, Series name, Total matches played, Runs Scored, 100's and 50's scored, 4's and 6's count, Highest Score, Overs and Balls faced
		
		Bowling_series.csv, Bowling_series2Page.csv, Bowling_series3Page.csv gives each match details between Indian and Australia and the details include
			Player name, Date of the match, 4 and 5 wkts haul count,Matches played, Maidens count, Overs bowled, Runs conceded, SR in series, Econ in series, Avg in that series

	For predicting Highest Run Scorer, Player who hits maximum 6's and 4's - Exploratory Data Analysis is performed
		
		For all the following analysis, the below points are applicable:
			
			** [Tableau is used extensively for the analysis rather than Python] -- Images are attached as well **
			
			Results of these below 13 series are only considered for each players:
				[1].Australia in India ODI Series, 2017/18
				[2].Australia in South Africa ODI Series, 2016/17
				[3].Australia in Sri Lanka ODI Series, 2016
				[4].Chappell-Hadlee Trophy [Australia in New Zealand], 2016/17
				[5].England in India ODI Series, 2016/17
				[6].ICC Champions Trophy (in England), 2017
				[7].India in Australia ODI Series, 2018/19
				[8].India in Sri Lanka ODI Series, 2017
				[9].India in West Indies ODI Series, 2017
				[10].New Zealand in India ODI Series, 2016/17
				[11].New Zealand in India ODI Series, 2017/18
				[12].South Africa in India ODI Series, 2015/16
				[13].Sri Lanka in India ODI Series, 2017/18
				
			Reason for choosing those series:
				The chosen series matches the following criteria
					1.Depicts Australia's performance in away condition [ This Ind VS Aus Series is a away tour for Australia ]
						Series no: [1],[2],[3],[4],[6] matches this criteria
					2.Depicts India's performance in home condition [ This Ind VS Aus series is a home tour for India ]
						Series no: [5],[10],[11],[12],[13]
					3.Depicts Indian and Australian team's and player's current form
						Series no: [7]
					4.Pitch conditions in these countries are similar to home conditions
						Series no: [8],[9]
						
For all the below analysis, kindly refer the visualisation images generated from tableau
			
	Highest run Scorer - Analysis and result Prediction	
		
		Supported Files: [Consistent_Top4_Scorers]
		
		Steps to predict the highest run scorer
			1.Players from both teams who consistently performs well are considered and those who dont are neglected
			2.Total Runs Scored by the consistent player in the series is calculated
			3.Average runs scored by the consistent player is calculated.
			
		Consistent Players  List:
			V Kohli - IND
		    AJ Finch - AUS
            RG Sharma - IND
            S Dhawan - IND
            MS Dhoni - IND
            KM Jadhav - IND
            MP Stonis - AUS
            SE Marsh - AUS
			
		Result: V Kohli - IND
		
	Player who hits more number of sixes - Analysis and prediction
	
		Supported Files: [Consistent_6Hitters, Top4_6Hitters]
		
		Steps to predict the more number of 6 hitter
			1.Players from both teams who consistently hits more number of sixes is observed and considered
			2.Among consistent players observed, top 4 who hits more number of sixes is identified
			
		Result: RG Sharma - IND	
		
	Player who hits more number of fours - Analysis and prediction
	
		Supported Files: [Consistent_4Hitters, Top5_4Hitters]	
		
		Steps to predict the more number of 4 scorers
			1.Players from both teams who consistently hits more number of fours is observed and considered
			2.Among consistent players observed, top 5 who hits more number of fours is identified
			
		Result: V Kohli - IND	
		
	Highest wicket taker
	
		Supported Files: [ConsistentWicketTakers, Top5_WicketTakers]
		
		Steps to predict the highest wicket taker
			1.Players from both teams who consistently picks more wickets is observed and considered
			2.Among consistent players observed, top 5 who picks more wickets is identified
			
		Result: JJ Bumrah - IND	
	
			
			
			