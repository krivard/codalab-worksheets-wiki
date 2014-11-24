##Accessing Codalab data remotely
CodaLab provides an API to access various models using REST framework. 
### Accessing a competition
  To access a competition, you need to know the competition ID which can be found on https://www.codalab.org/. 
  The competition ID can be seen by clicking on one of the competitions. The URL of the competition looks like https://www.codalab.org/competitions/1#learn_the_details, and the competition ID here is 1. The competition can be accessed using a “GET” request. 

  Function to make a GET request which returns JSON data for a competition using java script and Ajax:
  ```
    GET http://codalabtest.cloudapp.net/api/competition/<Competition ID>/
    <script>
        function getJson(url) {
            return JSON.parse($.ajax({
             type: 'GET',
             url: url,
             dataType: 'json',
             global: false,
             async: false,
             success: function (data) {
                return data;
             }
           }).responseText);
         }
    </script> 
   ``` 
    Java script to access the JSON data for a competition and then display it on your own website:
   ```
    <script>	
        // get the competition data using GET ajax call
        var competition = getJson("http://codalabtest.cloudapp.net/api/competition/1/");
        //access the title element to set the competition title
        document.getElementById("title").innerHTML = competition.title;
        //get the logo image of the competition
        document.getElementById("logo").src = competition.image_url;
        //get the description of the competition as a string and remove <p></p> tag
        competition.description = competition.description.replace("<p>","");   
        competition.description = competition.description.replace("</p>",""); 
        //set the description of the competition
        document.getElementById("competition-paragraph").innerHTML = competition.description;
        //link the competition to CodaLab
        document.getElementById("competition").href = "http://codalabtest.cloudapp.net/competitions/" + competition.id + "#learn_the_details";
    </script>
  ```

### Accessing a leaderboard

Leaderboard can be accessed for each phase using the following get request which returns a JSON data object.
```
    GET http://codalabtest.cloudapp.net/api/competition/<Competition ID>/phases/<Phase Number>/leaderboard/data
```

Java script snippet to create a leaderboard:
```
    <script>
     function buildDataTable() {
        var leaderboard = [];  
        //Access the leaderboard of phase 1 of the competition 1.
        leaderboard.push(getJson("http://codalabtest.cloudapp.net/api/competition/1/phases/1/leaderboard/data")[0]);
        var tr;
        //clear the table
        $("#leaderboardtable").empty();
        var columnSet = [];
        var headerTr$ = $('<tr/>');
        //Get the header data
        var headers = leaderboard[0].headers;
        headerTr$.append($('<th/>').html("participant"));  
        //Populate the header in the leaderboardtable                  
        for (var j = 0; j < headers.length; j++) {
           headerTr$.append($('<th/>').html(headers[j].label)); 
        }   		
        $("#leaderboardtable").append(headerTr$);
        //Populate the scores in the leaderboardtable             
        for (var i = 0; i < leaderboard[0].scores.length; i++) {
              	tr = $('<tr/>');
                tr.append("<td>" + leaderboard[0].scores[i][1].username + "</td>");
                values = leaderboard[0].scores[i][1].values;
                for (var j = 0; j < values.length; j++) {
              	   tr.append("<td>" + values[j].val + "</td>");                        
                }
                $('#leaderboardtable').append(tr);
         }
      }
     </script>
```

HTML body for adding a competition and its leaderboard
```
    <html>
        <head>
        {% load staticfiles %}
         <link rel="stylesheet" href="{{ STATIC_URL }}/css/codalab.css">
         <link rel="stylesheet" href="{{ STATIC_URL }}/css/jquery.dataTables.css">
         <link rel="stylesheet" href="{{ STATIC_URL }}/css/Main.css">
         <link rel="stylesheet" href="{{ STATIC_URL }}/css/foundation-icons.css" />  
         <script type="text/javascript" src="jquery.min.js"></script> 
         <script type="text/javascript" src="jquery.tablesorter.min.js"></script>          
        </head>
        <body>
          <div class="row">
            <div class="large-9 columns competitions">
                <h2>Click below to get a list of my competitions</h2><br/>
                <button id="show" class="button" onclick="showDiv()">View Competitions</button>
            </div> 
            <div class="row" id="all-competitions" style="display: none;">             
                <div class="large-9 columns competitions">
                    <a id="competition" href="" target="_blank">
                    <div class="competition-tile">
                      <article class="subContainer">
                        <input id="competitionID" type="hidden" value="7" />
                        <div class="competitionTileLftStrip"></div>
                        <div class="articleImageContainer">
                          <div class="subContainerPadding">
                            <img src="" id="logo">                            
                          </div>
                         </div>
                         <div class="articleTextArea">
                            <div class="subContainerPadding">
                               <div class="articleHeadingContainer">
                                 <h3 id="title"></h3>
                                 <label>Organized by Pragnya</label>
                                </div>
                                <div class="competition-paragraph" id="competition-paragraph">                           
                                 </div>
                             </div>
                          </div>
                      </article>
                    </div>
                    </a>
                 </div>                               
             </div>
             <div class="large-9 columns competitions">
                  <button id="showLeaderBoard" class="button" onclick="buildDataTable()">View LeaderBoard</button>
                  <div class="leaderboard-result-table">
                     <table class = "resultsTable dataTable" id="leaderboardtable" border="1"></table>
                  </div>                 
            </div> 
          </div>        
          <table id="leaderboardtable" border="1">
          </table>        
      </body>    
   </html> 
```