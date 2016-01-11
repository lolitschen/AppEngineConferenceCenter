App URL: https://udacityconferenceapp-1184.appspot.com


#Task 1:
###Design choices:
#####Session: 
<p>The session entity was created with all the required details with name as the only requirement. Every other field would be generated with a default value. This allows the user to quickly create a session as a placeholder and go back and edit the necessary fields when more more information is released. Many times during conference booking the organizer will book the venue before specific event details such as speakers are finalized.</p>
<p>Sessions are a child of the conference that they are attached to. This makes it easier to query based on ancestor relationships rather than querying and then filtering by conference key in each session object. </p>
<p>The websafeSessionKey is stored to allow simple addition of keys into wishlists.</p>

#####Speaker:
<p>I chose to represent the speaker entity as a simple string as the conferences I typically attend have one main speaker. I do see the value of making this a repeated field if there are co-speakers and may choose to change my design after user testing and gathering data.</p> 
<p>If speakers are represented as a separate entity then it would be possible to query for more information on them.</p>


#Task 2:
	Implemented in Conference.py


#Task 3:

###Possible useful queries:

1) query for X most popular conferences
	- this query would be able to return X number of conferences that have the least number
	of seats available. 
	- ideally ordered based on % of seats left to prevent skewing of small conferences

2) query for profiles of all user who are currently registered to a given conference
	- this query would be beneficial for conference organizers to see list of guests
	- this query should be restricted to conference creators to protect privacy

#####Question: Let’s say that you don't like workshops and you don't like sessions after 7 pm. How would you handle a query for all non-workshop sessions before 7 pm? What is the problem for implementing this query? What ways to solve it did you think of?

<p>This is a problem because GAE does not support multiple property inequality filters. According to my research, this is a problem due to the way that the indexing on bigtable works. Indicies from a filter are assumed to be adjacent to each other to speed up the querying process and this is not the case when we filter on multiple inqeualities. 
</p>
<p>To resolve this, one option would be to store an extra field in the workshop entity that describes either (1) notWorkshopType or (2) isBefore7PM. Then we can combine the inequality filter with an equality filter on the extra field that we added in the entity to achieve the desired result.</p>

#####ex: 
1. add notWorkshopType boolean property to Session entity and populate for sessions.
2. Session.query().filter(Session.notWorkshopType==True).filter(Session.startTime >= 7PM)

#Task 4:
	Implemented in Conference.py