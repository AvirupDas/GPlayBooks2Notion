# Books2Notion
**Python script for syncing Google Play Books Highlights to Notion**
#### **[Demo Notion pages with highlights](https://quark-plane-15f.notion.site/4bd2a74b373a48f488a6ba86eb2b309c?v=4d15601367de4de3a72c8ac903cce14a "Notion pages containing highlights")**
[![Google Play Books](/repository/assets/GPlayBooksSyncSample.png "Google Play Books")](repository/assets/GPlayBooksSyncSample.png "Google Play Books")
[![Notion Notes](/repository/assets/NotionSyncSample.png "Notion Notes")](/repository/assets/NotionSyncSample.png "Notion Notes")

----------------------------------------------------------------------

### Table of Contents
- [Books2Notion](#books2notion)
    + [Table of Contents](#table-of-contents)
  * [Usage](#usage)
      - [Sync Modes](#sync-modes)
    + [Automatic](#automatic)
    + [Run Manually](#run-manually)
  * [Installation and Setup](#installation-and-setup)
    + [1. Clone Git repo](#1-clone-git-repo)
    + [2.Install Python Modules](#2install-python-modules)
    + [3.Authorize Notion](#3authorize-notion)
      - [a. Get Notion Integration Token](#a-get-notion-integration-token)
      - [b. Duplicate template and share it to integration](#b-duplicate-template-and-share-it-to-integration)
    + [4. Authorize Google APIs](#4-authorize-google-apis)
      - [a. Create Google cloud project](#a-create-google-cloud-project)
      - [b. Create OAuth consent screen](#b-create-oauth-consent-screen)
      - [c. Create OAuth2.0 credentials](#c-create-oauth20-credentials)
      - [d. Enable APIs](#d-enable-apis)
    + [5. Get env variables](#5-get-env-variables)
      - [a. Get NOTION_DATABASE_ID](#a-get-notion_database_id)
      - [b. Get DRIVE_FOLDER_ID](#b-get-drive_folder_id)
      - [c. Get IMAGE_HOST_KEY](#c-get-image_host_key)
      - [d. Get TIME_OFFSET](#d-get-time_offset)
    + [6. Authorize script](#6-authorize-script)
    + [7. Schedule script](#7-schedule-script)
      - [Heroku](#heroku)
        * [a. Create Heroku Account](#a-create-heroku-account)
        * [b. Install Heroku cli](#b-install-heroku-cli)
        * [c. Heroku login](#c-heroku-login)
        * [d. Create  app](#d-create--app)
        * [e. Fill env variables](#e-fill-env-variables)
        * [f. add buildback](#f-add-buildback)
        * [g. deploy application](#g-deploy-application)
        * [h. start dyno](#h-start-dyno)
      - [Local](#local)

---

## Usage
Red highlights are reserved for saving new words, it gets added to 'New Words' database along with definition fetched from [Wiktionary](https://en.wiktionary.org/wiki/Wiktionary:Main_Page "Wiktionary")    
#### Sync Modes
1. `append` - appends new highlights and new words to notion, but won't sync highlights and new words from pages that have been synced before
2. `sync` - syncs complete highlights of the books to notion
3. `sync-full` - syncs complete highlights and new words

### Automatic
Follow [7. Schedule script](#7-schedule-script) to schedule .Script is scheduled to run every hour in `append` mode. Enable  _Full_Sync_ checkbox for books in the database if you want to  run  the script in `sync-full` mode in the next scheduled run. 
![FullSync](/repository/assets/FullSync.png "FullSync")

### Run Manually
Clone `manual` branch
```
git clone --single-branch --branch manual https://github.com/MohamedIrfanAM/books2notion.git
```
````
python3 books2notion.py --mode <mode>
````
Example: `python3 books2notion.py --mode sync-full`

## Installation and Setup
### 1. Clone Git repo
Make sure you have [git](https://git-scm.com/downloads "git") installed.
To schedule script, clone `deployment` branch  
```
git clone https://github.com/MohamedIrfanAM/books2notion.git
```
For running the script manually,  clone `manual` branch
```
git clone --single-branch --branch manual https://github.com/MohamedIrfanAM/books2notion.git
```

### 2.Install Python Modules
Install [Python](https://www.python.org/downloads/ "Python") and proceed to install following modules
```
pip install --upgrade google-api-python-client, google-auth-httplib2, google-auth-oauthlib
pip install Pillow
pip install schedule
```
### 3.Authorize Notion
#### a. Get Notion Integration Token
**Goto https://www.notion.so/my-integrations and make an integration, give it a name submit with default settings.**    

![NotionIntegration-1](/repository/assets/NotionIntegration-1.png "NotionIntegration-1")
![NotionIntegration-2](/repository/assets/NotionIntegration-2.png "NotionIntegration-2")
![NotionIntegration-3](/repository/assets/NotionIntegration-3.png "NotionIntegration-3")

**Copy the integration token and save it somewhere.**   
#### b. Duplicate template and share it to integration
**Duplicate [ books template](https://quark-plane-15f.notion.site/4bd2a74b373a48f488a6ba86eb2b309c?v=4d15601367de4de3a72c8ac903cce14a " books template") and `share -> invite -> integration_name -> invite`**  

![NotionDatabaseShare1](/repository/assets/NotionDatabaseShare1.png "NotionDatabaseShare1")
![NotionDatabaseShare2](/repository/assets/NotionDatabaseShare2.png "NotionDatabaseShare2")
![NotionDatabaseShare3](/repository/assets/NotionDatabaseShare3.png "NotionDatabaseShare3")

### 4. Authorize Google APIs
#### a. Create Google cloud project
**Goto https://console.cloud.google.com/ then click 'select a project'**   
![GCloudProject1](/repository/assets/GCloudProject1.png "GCloudProject1")
![GCloudProject2](/repository/assets/GCloudProject2.png "GCloudProject2")

**Give the project a name and click create**        
![GCloudProject3](/repository/assets/GCloudProject3.png "GCloudProject3")
 
#### b. Create OAuth consent screen    
![GCloudConsent1](/repository/assets/GCloudConsent1.png "GCloudConsent1")
![GCloudConsent2](/repository/assets/GCloudConsent2.png "GCloudConsent2")

**Fill 'name' and 'user support email'**    
![GCloudConsent3](/repository/assets/GCloudConsent3.png "GCloudConsent3")

**Scroll down fill in 'Email address' and continue**     
![GCloudConsent4](/repository/assets/GCloudConsent4.png "GCloudConsent4")
**click 'save and continue' for 'Scopes' and 'Test Users', don't have to fill in anything**        
![GCloudConsent5](/repository/assets/GCloudConsent5.png "GCloudConsent5")
**Publish app**     
![GCloudConsent6](/repository/assets/GCloudConsent6.png "GCloudConsent6")
![GCloudConsent7](/repository/assets/GCloudConsent7.png "GCloudConsent7")
#### c. Create OAuth2.0 credentials    
![GCloudCreds1](/repository/assets/GCloudCreds1.png "GCloudCreds1")
![GCloudCreds2](/repository/assets/GCloudCreds2.png "GCloudCreds2")
![GCloudCreds3](/repository/assets/GCloudCreds3.png "GCloudCreds3")
![GCloudCreds4](/repository/assets/GCloudCreds4.png "GCloudCreds4")
![GCloudCreds5](/repository/assets/GCloudCreds5.png "GCloudCreds5")
   
**Download file and save it as `credentials.json` in the `books2notion` folder we created in [1. Clone Git repo](#1-clone-git-repo)**      

#### d. Enable APIs  
**We have to enable `Books API`, `Google Drive API` and `Google Docs API`**    
![EnableGAPI1](/repository/assets/EnableGAPI1.png "EnableGAPI1")
![EnableGAPI2](/repository/assets/EnableGAPI2.png "EnableGAPI2")

**Search for each APIs mentioned above and enable it**   
![EnableGAPI3](/repository/assets/EnableGAPI3.png "EnableGAPI3")
![EnableGAPI4](/repository/assets/EnableGAPI4.png "EnableGAPI4")
![EnableGAPI5](/repository/assets/EnableGAPI5.png "EnableGAPI5")

### 5. Get env variables
#### a. Get NOTION_DATABASE_ID
**Copy the url of database we duplicated in [Duplicate Notion template](#b-duplicate-template-and-share-it-to-integration)**   
**It should look like this**    
```
https://www.notion.so/lucas-gen/e32a031992f348aeae115fe6dee8353?v=1e75f5e2b07349f4b331e88c4ca3beac
```
![](https://maze-condor-4f8.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0a48c753-25ef-4b24-8a2b-ac05c1dbfc1c%2FUntitled.png?table=block&id=4ccddee5-b2f2-44e4-8c4f-6638ab9a88fc&spaceId=41d27f42-f75a-496d-bb9b-d18fbe56e71b&width=2000&userId=&cache=v2)    
**Save the databse id somewhere**   
#### b. Get DRIVE_FOLDER_ID  
**Select any book from your Play Books library and open to read, you will find an option to save annotations to google drive and enable it (only have to doit once,then it will be default for everybook).**      

![AllowFolderSameGBooks](/repository/assets/AllowFolderSameGBooks.png "AllowFolderSameGBooks")

**Go to google drive and navigate to folder you selected for saving annotations and copy it's url.**    
**it should look like this - `https://drive.google.com/drive/folders/<folder id>`**    
**copy the folder id and save it somewhere**     
#### c. Get IMAGE_HOST_KEY
**goto https://freeimage.host/page/api?lang=en and save the api key somewhere**       
[![](/repository/assets/APIBookImage.png)](/repository/assets/APIBookImage.png)     
#### d. Get TIME_OFFSET
goto https://www.timeanddate.com/time/zone/ and find time offset from UTC    
Example `+05:30`,`-04:00`

### 6. Authorize script  
**Run the script for the first time `python3 schedule_sync.py` in the terminal, you will prompted to give access to application via browser. This script will create a `.env` file in `books2notion` folder.
You don't have to authorize everytime just first time only.**      
The `.env`  file generated by running above script should look something like this    
```
API_TOKEN=<google API token, filled by script>
REFRESH_TOKEN=<google refresh token, filled by script>
TOKEN_URI=<token URI, filled by script>
CLIENT_ID=<client id, filled by script>
CLIENT_SECRET=<client id, filled by script>
EXPIRY=<token expiry, filled by script>
NOTION_KEY=
NOTION_DATABASE_ID=
DRIVE_FOLDER_ID=
IMAGE_HOST_KEY=
TIME_OFFSET=
  ```
### 7. Schedule script  
#### Heroku
##### a. Create Heroku Account
goto https://heroku.com/ and make a free account       
##### b. Install Heroku cli
https://devcenter.heroku.com/articles/heroku-cli#install-the-heroku-cli
##### c. Heroku login
```
heroku login
```
##### d. Create  app
```
heroku create
```
##### e. Fill env variables
goto https://dashboard.heroku.com/apps and you find your app, it might not be named `books2notion`      
![HerokuDeploy1](/repository/assets/HerokuDeploy1.png "HerokuDeploy1")
**goto settings**      
![HerokuDeploy2](/repository/assets/HerokuDeploy2.png "HerokuDeploy2")
**Reveal config vars**    
![HerokuDeploy3](/repository/assets/HerokuDeploy3.png "HerokuDeploy3")
**Fill in env variables we saved from [3. Get Notion Integration Token](#a-get-notion-integration-token), [5. Get env variables](#5-get-env-variables), [6. Authorize script](#6-authorize-script)**     
![HerokuDeploy4](/repository/assets/HerokuDeploy4.png "HerokuDeploy4")
##### f. add buildback   
**croll down and you will find option to add buildpack**   
![HerokuDeploy5](/repository/assets/HerokuDeploy5.png "HerokuDeploy5")
![HerokuDeploy6](/repository/assets/HerokuDeploy6.png "HerokuDeploy6")
##### g. deploy application
```
git push heroku deployment:main
```
##### h. start dyno
**Go to resources and toggle on dyno**   
![HerokuDeploy7](/repository/assets/HerokuDeploy7.png "HerokuDeploy7")

**Now that's it, we have successfully deployed application.**   

#### Local
**Sheduling locally in Mac and Linux (cron)**   
Follow this [article](https://towardsdatascience.com/how-to-schedule-python-scripts-with-cron-the-only-guide-youll-ever-need-deea2df63b4e "article")   
**Sheduling locally in Windows (windows task scheduler)**     
Follow this [article](https://www.jcchouinard.com/python-automation-using-task-scheduler/ "article")   
