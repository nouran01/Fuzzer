# Fuzzer Project 

## Part1:
**Description**

In this part, the program logs into a website using hard-coded authentication, which is the default username and password, through the command line (CLI) by using a library in python called *mechanicalsoup*. 

**Required Libraries**

* *mechanicalsoup:* This library is for browsing a giving URL and entering the login information
* *requests:* I used this library to get the HTML of the login page before entering the login information 
* *argparse:* This library is for command line (CLI) option and argument parsing. So, it allows a python file to run through the CLI shell 

**Activating the Environment**

1. Initialized a virtual environment in the CLI

I installed the virtual environment by using *pip* and created a new virtual environment

		pip install virtualenv
		virtualenv Fuzzer_virtualenv (I ran this command in the directory where I want my virtual environment to be in, and I called it Fuzzer_virtualenv)

2. Installed libraries

To install the libraries in the virtual environment, I installed *pipenv* first. Then I used the *pipenv* after activating my virtual environment to install the libraries in the virtual environment. 

		pip install pipenv
		Fuzzer_virtualenv\Scripts\activate (I ran this command in the directory where my virtual environment is in to activate it)
		pipenv install requests
		pipenv install mechanicalsoup
		


**Run Instructions**

1. Open the CLI where the virtual environment is in. Then, activate the virtual environment. Change the directory to where the python file is located.
2. Type (py fuzz.py url=**_URL_** --custom-auth=dvwa). The **_URL_** should be specified. If *--custom-auth* is not invoked, the program should crawl the root of the webapp.
3. The program will output the HTML after a successful login to the console

## Part2:
**Description**

The program discovers inputs to a system.

**Required Libraries**

* *mechanicalsoup:* This library is for browsing a giving URL and entering the login information
* *requests:* I used this library to get the HTML of the login page before entering the login information 
* *argparse:* This library is for command line (CLI) option and argument parsing. So, it allows a python file to run through the CLI shell 
* *urlparse:* I used this library to parse URLS

**Run Instructions**

Open the CLI where the virtual environment is in. Then, activate the virtual environment. Change the directory to where the python file is located. Then, run one of the following commands.
 
* Discover inputs without --custom-auth and --common-words options:<br/> 
Type (py fuzz.py discover http://127.0.0.1:10000/dvwa/) 
* Discover inputs without --common-words option, using hard-coded authentication:<br/>
Type (py fuzz.py discover http://127.0.0.1:10000/dvwa/ --custom-auth=dvwa)
* Discover inputs with --custom-auth and --common-words options:<br/>
Type (py fuzz.py discover http://127.0.0.1:10000/dvwa/ --custom-auth=dvwa *--common-words*=mywords.txt).<br/> 
*--common-words* could be any text file that has common words to guess pages

**Program Output to the Console**

* Links found on page, links successfully guessed, Parsed URLS, input forms on pages, and cookies. 
* The program will also output the HTML after a successful login to the console if *--custom-auth* option is invoked. 



**Resources**

* https://mechanicalsoup.readthedocs.io/en/stable/tutorial.html

* https://buildmedia.readthedocs.org/media/pdf/mechanicalsoup/latest/mechanicalsoup.pdf#page17

* https://pythonprogramminglanguage.com/get-links-from-webpage/

* https://pymotw.com/2/urlparse/

