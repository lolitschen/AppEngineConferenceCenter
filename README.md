App URL: https://udacityconferenceapp-1184.appspot.com

Local Setup:
1) Install Google App Engine & Clone the project folder
2) File > New Application > browse to cloned directory > Create
3) Click Run to deploy and go to localhost:8080
4) http://localhost:8080/_ah/api to access api (if using chrome click 
		the shield and click load unsafe scripts)
		Alternative: Run through terminal with  
		OSX: /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --user-data-dir=test --unsafely-treat-insecure-origin-as-secure=http://localhost:8080


#Task 1:
Design choices:
Session: 
	The session entity was created with all the required details with name as the only requirement. Every other field would be generated with a default value. This allows the user to quickly create a session as a placeholder and go back and edit the necessary fields when more more information is released. Many times during conference booking the organizer will book the venue before specific event details such as speakers are finalized.

	Sessions are a child of the conference that they are attached to. This makes it easier to query based on ancestor relationships rather than querying and then filtering by conference key in each session object. 

	fields:
    name: each session should have a name so users can tell the difference 	easily rather than comparing long keys. This field should be a string so users can creatively name their sessions.

    highlights: quick summary would help users understand what they could gain from attending this session. This field is a string as highlights can include anything the creator wants.

    speaker: name of speaker who will lead the session so users can easily pick and choose which speakers to attend if they have preferences. This field is also a string.

    duration: necessary information so users can plan their schedules accordingly. This is an integer property because since sessions are rarely too long, I chose to keep track of them in minutes so users would not be forced into certain times such as 1 hr 1.5hrs. They can have 40 minute sessions or 45 minute sessions.. etc.

    typeOfSession: This field is an enumerated type property to ensure easy searching and filtering. Gives a set of options of conference creators so the types are standardized.

    date: This field is the date property because it will accurately store the year month and day to keep everything standard in the database. 

    startTime: This field is stored as a Time property to accurately represent and standardize times across the db and applicaitons that use this api.

    location: this field is used to store information about sessions that are specific to conference locations such as room numbers and floor numbers. This is a string to allow flexibility in what is recorded.

Speaker:
	I chose to represent the speaker entity as a simple string as the conferences I typically attend have one main speaker. I do see the value of making this a repeated field if there are co-speakers and may choose to change my design after user testing and gathering data. 

	If speakers are represented as a separate entity then it would be possible to query for more information on them.


#Task 2:
	Implemented in Conference.py



#Task 3:
Possible useful queries:
1) query for X most popular conferences
	- this query would be able to return X number of conferences that have the least number
	of seats available. 
	- ideally ordered based on % of seats left to prevent skewing of small conferences

2) query for profiles of all user who are currently registered to a given conference
	- this query would be beneficial for conference organizers to see list of guests
	- this query should be restricted to conference creators to protect privacy

Question: Let’s say that you don't like workshops and you don't like sessions after 7 pm. How would you handle a query for all non-workshop sessions before 7 pm? What is the problem for implementing this query? What ways to solve it did you think of?

	This is a problem because GAE does not support multiple property inequality filters. According to my research, this is a problem due to the way that the indexing on bigtable works. Indicies from a filter are assumed to be adjacent to each other to speed up the querying process and this is not the case when we filter on multiple inqeualities. 

	To resolve this, one option would be to store an extra field in the workshop entity that describes either (1) notWorkshopType or (2) isBefore7PM. Then we can combine the inequality filter with an equality filter on the extra field that we added in the entity to achieve the desired result.

ex: 
1) add notWorkshopType boolean property to Session entity and populate for sessions.
2) Session.query().filter(Session.notWorkshopType==True).filter(Session.startTime >= 7PM)

#Task 4:
	Implemented in Conference.py