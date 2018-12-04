
# TL;DR
My end goal is to script this whole setup for deployment/setup via ansible, docker, vagrant in order that the whole mess can just be generated for you,  but that is a work in progress. For now you need to.
    - Prerequisties: java, maven, git
    - Develop
    - Test
    - Deploy

# Intro
Create a selenium synthetic monitoring framework using open source tools.  We will be using a number of open source tools for the dev and test environments and provide guidance for building and deploying to a prod environment. I will list all the tools that are used and the versions that are used for this project.

# My Environment 
- Hardware: MacBook Pro (15-inch, 2017)
- OS: Mac OS X Mojave 10.14 (18A391)
- You can duplicate this same setup in Windows or Linux as well

# Installs/Pre-requisites to setup your development environment
Install the following base packages or ensure they are on your system already.  These will provide a base development setup whereby you can develop and test your selenium checks from the command line or from the IntelliJ IDE.

- java : development of checks
    + https://www.java.com/en/download/mac_download.jsp
    + version  "1.8.0_151"

- maven : build system
    + brew install maven
    + If you dont have brew already, you can get it here: https://brew.sh
    + version: Apache Maven 3.5.4 (1edded0938998edf8bf061f1ceb3cfdeccf443fe; 2018-06-17T14:33:14-04:00)

- git : source control
    + git is installed with xcode command line tools (as mine)
    + or you can use: brew install git
    + or you can use: https://git-scm.com/downloads
    + version 2.17.1 (Apple Git-112)

- Selenium Server standalone jar : Framework for driving browsers 
    + get from: https://www.seleniumhq.org/download/ and put in the lib directory
    + versino 3.14 
    + selenium docs: https://seleniumhq.github.io/docs/index.html

- IntelliJ IDEA: IDE for development
    + https://www.jetbrains.com/idea/download/#section=mac

- Test local environment.
    + Once you have installed all of the above, you can use the following git project to test it.
    + git clone https://github.com/eviltester/startUsingJavaJUnit
    + follow the instructions in the project for testing your setup

- Install & Test: Webdriver & Firefox, Chrome, Safari drivers
    + There is a good  youtube tutorial here: 
        + https://www.youtube.com/watch?v=nq97dfaVmC4&feature=youtu.be
    + Download and import into IntelliJ the following git project which you can use to test the setup.
        + git clone https://github.com/eviltester/startUsingSeleniumWebDriver
    + Download & Install Firefox browser 
        + https://www.mozilla.org/en-US/firefox/
    + Download and install geckodriver for firefox 
        + docs: https://firefox-source-docs.mozilla.org/testing/geckodriver/geckodriver/index.html
        + supported platforms matrix: https://firefox-source-docs.mozilla.org/testing/geckodriver/geckodriver/Support.html
        + releases: https://github.com/mozilla/geckodriver/releases
        + Download the most recent version of geckodriver for my platform (yours may differ). Unarchive file and put into a subfolder(I'm using ~/webdriver/firefox).  Add the path to the geckodrivers parent directory to your system path.  In my case, I added it to my .zshrc file in my users root directory (your method of adding to the path may differ).
            + version: geckodriver-v0.23.0-macos.tar.gz
        + Test that geckodriver is in the path
            + Type this at command prompt: geckodriver --version
            + You should see output of version and instructions for geckodriver
        + Test geckodriver with maven
            + cd to directory where you installed "startUsingSeleniumWebDriver"
            + run: mvn test -Dtest=MyFirstTest
                + you should see a firefox browser popup and the maven output should indicate success.

    + Download and install chromedriver (optional) 
        + download driver for your setup from chromedriver site: http://chromedriver.chromium.org/
        + same process as above, extract file from downloaded zip, create a folder in same spot ~/webdriver/chrome, copy the extracted file to the new folder, add the folder to your system path.
        + run: mvn test -Dtest=MyFirstChromeTest
            + you should see a firefox browser popup and the maven output should indicate success.

    + Download and install safaridriver (optional)
        + Docs: https://developer.apple.com/documentation/webkit/testing_with_webdriver_in_safari
        + You will need to enable 'Allow Remote Automation' in the safari developer menu.
        + You will need to run `safaridriver --enable` and provide your users password.
        + To get a successful test for this one, you will need edit the Java code at /src/test/java/com/eviltester/webdriver/MyFirstSafariTest.java and comment the line "driver.close();" with // in front and then recompile. The test will throw an error if it includes both close & quit calls.  Once done you can test from cmd line.
        + run: mvn test -Dtest=MyFirstSafariTest
            + you should see a safari browser popup and the maven output should indicate success.

    + Once done all your command line tests you should be able to run the same from inside the ItelliJ IDE and get a successful result as well... which tell you that your local environment is working to code and test selenium test.

# Project Structure and Naming conventions
In this section we will create the basic structure and naming conventions for our selenium checks that will then later be used with testing and eventually deployment.  We will use github and maven and eventually travis Ci.

  + References used for maven project creation
        + https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html
        + https://www.youtube.com/watch?v=06WurWHfsX4&list=PLrM2ELpRbbu4aMGNkA_13cPUJNGA1592t&index=3     
    + After the projects have been created you can import them into IntelliJ and test using the maven test command under Lifecycle from the Maven projects tab on the right side of the IDE.
 
- Created new maven projects and git repos for utils and tests at same level as this repo.

   + Project for selenium synthetics framework - gdohmeier/synthetics
        + This project will (eventually) include all the glue and scripting for setup, deployment and runtime.

    + Project for selenium synthetics utilities - gdohmeier/synthetics_util
        + mvn archetype:generate -DgroupId=com.itechmb.selenium.util -DartifactId=synthetics_util -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
        + cd synthetics_util
        + git init
        + vim pom.xml  
        ( Edit the pom.xml file, update the JUnit version to the most recent, mine 4.12 )
        + mvn test  
        ( Test the project using maven ) 
        + git add . --all
        + git commit -m 'initial check in'
        + git status
        + ( create a new github public repo with same name and no readme or gitignore, then push your repo to it.  you will need to enter user/password for the push. )
        + git remote add origin https://github.com/gdohmeier/synthetics_util.git
        + git push -u origin master

    + Project for selenium synthetics tests - gdohmeier/synthetics_tests
        + mvn archetype:generate -DgroupId=com.itechmb.selenium.tests -DartifactId=synthetics_tests -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
        + cd synthetics_tests
        + git init
        + vim pom.xml  
        ( Edit the pom.xml file, update the JUnit version to the most recent, mine 4.12 )
        + mvn test  
        ( Test the project using maven ) 
        + git add . --all
        + git commit -m 'initial check in'
        + git status
        + ( create a new github public repo with same name and no readme or gitignore, then push your repo to it.  you will need to enter user/password for the push. )
        + git remote add origin https://github.com/gdohmeier/synthetics_tests.git
        + git push -u origin master   
    + Project for check_selenium wrapper of selenium test to nagios format
        + You should be able to get this from maven now and add as pom.xml dependancy from here: ???
        + But if you want the detail of how I got this working, the project info is here:  https://github.com/gdohmeier/check_selenium
            + Was forked from zunker/check_selenium
                + Restructure to work with this project
                + update to work with current versions of selenium and JUnit
                + update to work with maven
                + restructure class path and directories
                + made enhancements to the code.
            + I setup TravisCi to perform a testing of this project on pull requests. Getting the TravisCi working was a bit tricky.  Look at the .travis.yml filen in the root of the project and then see the travis docs for more info.  The ci site for this is here: https://travis-ci.org/gdohmeier/check_selenium/
            + I used this easy to follow youtube tutorial on how to put the project into the central maven repo for sharing with others. https://youtu.be/bxP9IuJbcDQ
                + For this to work I had to make a couple of changes, additions to the youtube instructions... otherwise gpg signing failed.
                    + updated the maven gpg plugin ( maven-gpg-plugin ) to add... 
                        ```yml
                        <configuration>
                            <gpgArguments>
                                <arg>--pinentry-mode</arg>
                                <arg>loopback</arg>
                            </gpgArguments>
                        </configuration>
                        ```
                    + As in the youtube the last two steps then were: 
                        + mvn 
                        + 



# Created TravisCi build setups for all synthetics repos
    + Setup your travis.io to link to your github
        + good example https://www.youtube.com/watch?v=Uft5KBimzyk
    + Travis will sync up all your projects.  Note: If you are not already using travis disable all of them in settings. Then enable each project as needed.
    + Go to your repo and create new branch (travis) checkout that new branch
    + Here is a quick over view of a generic setup for a default maven project. For more complex CI testing you will need much more in your .travis.yml file.  See the check_selenium setup in the previous section for an example.
        + Add a file to the root directory (.travis.yml)
            + add the below lines (for java)
            ```yml
                language: java

                jdk: oraclejdk8

                install: true
                script: mvn clean install

                cache:
                directories:
                - $HOME/.m2
            ```
            + git add . --all
            + git commit -m "travis test build"
            + git push origin travis  ( you must have the github proj in place for this)
        + Go to github and create a pull request - this will start the travis build which you can see from both github and travis.  Look at the log output.
        + When test is successful you can merge the pull request to master on github.
        + Any future pull requests will generate a CI build & test on travis.
        + See travis docs for more information: https://docs.travis-ci.com/user/for-beginners

# Docker Runtime environments and tools
I use a number of other open source tools which you can install directly or using docker.  I will list where you can get the tool and the docker images as appropropriate. I will document the manual build out but also hope to provide an automated script to do it all automagically.

- Selenium Grid : docker-selenium (for testing and scalability)
    + https://github.com/SeleniumHQ/docker-selenium
    + my docker-compose.yml
- jenkins : Continuous Integration
    + https://jenkins.io/
    + https://hub.docker.com/_/jenkins/
- prometeus : monitoring tool 
    + https://prometheus.io/
    + https://hub.docker.com/r/prom/prometheus/
- portainer : docker management ui
    + https://portainer.io/
    + https://hub.docker.com/r/portainer/portainer/
- Tableau : data science / visualization  
    + https://www.tableau.com/
