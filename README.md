# Web-service

## Basic Information:

**1. Creating a Web Server on IBMi.**
 - Create a new HTTP server.
 -	Follow the creation process.
 -	After creating, edit the configuration file.
 -	Refresh and restart the server.
 -	The server is Created.

**2. Create a program to handle GET requests.**
  -	Create RPG program with including supportive service program (QHTTPSVR/QZHBCGI) into Binding directory.
  -	The program will accept the parameter from the browser and return the result to the browser.
  -	Pass the GET URL to fetch the corresponding details
  -	Information will reflect the browser.
  
**3. Create a program to handle POST requests.**
  -	Create RPG program with including supportive service program (QHTTPSVR/QZHBCGI) into Binding directory.
  -	This Program will convert TCP data to EBCDIC character set.
  -	Pass the GET URL to fetch the corresponding details
  -	Information will reflect the browser.
  -	The program will return converted EBCDIC character data to the browser.

## Repository :
**Physical file:**
  -	EMPPF123: Employee-related details like ID, Name, Age, Salary.
  -	WEBPF1: Flat file to store the message.

**GET :**
  -	GETPGM1: Consuming GET to retrieve information from :
  -	https://gorest.co.in/public/v1/posts
  -	GETPGM2: Handling GET requests from the browser to retrieve information from Physical File.

**POST:**
  -	POSTPGM1: Handling POST requests from the browser to retrieve information related to the browser like URL, Content type, Request method. 
  -	POSTPGM2: Handling POST requests from the browser to store information to Physical File
  -	POSTPGM3: Consuming GET to store information to : https://gorest.co.in/public/v1/users

**PRACTICE_SRC:**
  -	WEBPGM3: A basic program to display the data into the browser.
  -	WEBPGM4: Consuming Get and Post to Personal web-server.
  

