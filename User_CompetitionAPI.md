CodaLab API
CodaLab provides an API to access various models using REST framework. 
* Accessing a competition
  To access a competition, you need to know the competition ID which can be found on https://www.codalab.org/. 
  The competition ID can be seen by clicking on one of the competitions. The URL of the competition looks like https://www.codalab.org/competitions/1#learn_the_details, and the competition ID here is 1. The competition can be accessed using a “GET” request. 

  Function to make a GET request which returns JSON data for a competition using java script and Ajax:

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

    Java script to access the JSON data for a competition and then display it on your own website:
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

