----------Install requirements For Debian based distros

1. sudo apt install python3
2. sudo snap install docker 

----------For Arch and it's derivatives:

1. sudo pacman -S docker python

----------Setting up config file

1. cp config_sample.env config.env

----------Remove the first line saying:

_____REMOVE_THIS_LINE_____=True

----------Fill up rest of the fields. Meaning of each fields are discussed below:

BOT_TOKEN : The telegram bot token that you get from @BotFather
GDRIVE_FOLDER_ID : This is the folder ID of the Google Drive Folder to which you want to upload all the mirrors.
DOWNLOAD_DIR : The path to the local folder where the downloads should be downloaded to
DOWNLOAD_STATUS_UPDATE_INTERVAL : A short interval of time in seconds after which the Mirror progress message is updated. (I recommend to keep it 5 seconds at least)
OWNER_ID : The Telegram user ID (not username) of the owner of the bot
AUTO_DELETE_MESSAGE_DURATION : Interval of time (in seconds), after which the bot deletes it's message (and command message) which is expected to be viewed instantly. Note: Set to -1 to never automatically delete messages
IS_TEAM_DRIVE : (Optional field) Set to "True" if GDRIVE_FOLDER_ID is from a Team Drive else False or Leave it empty.
INDEX_URL : (Optional field) Refer to https://github.com/maple3142/GDIndex/ The URL should not have any trailing '/'
Note: You can limit maximum concurrent downloads by changing the value of MAX_CONCURRENT_DOWNLOADS in aria.sh. By default, it's set to 2

----------Getting Google OAuth API credential file

1. Visit the Google Cloud Console
2. Go to the OAuth Consent tab, fill it, and save.
3. Go to the Credentials tab and click Create Credentials -> OAuth Client ID
4. Choose Other and Create.
5. Use the download button to download your credentials.
6. Move that file to the root of mirror-bot, and rename it to credentials.json
Note: Enable google drive API
7. Finally, run the script to generate token file (token.pickle) for Google Drive:

pip install google-api-python-client google-auth-httplib2 google-auth-oauthlib

python3 generate_drive_token.py

----------Deploying
1. Start docker daemon (skip if already running): sudo dockerd
2. Build Docker image: sudo docker build . -t mirror-bot
3. Run the image: sudo docker run mirror-bot

(Important: Till here you have used Ubuntu/Linux, Now you have to use widnows. Upload all the files to github your account and then 
use github desktop to clone the repository on your windows machine and then try to deploy on heroku)_If you do not do this, you'll face issues while deploying. Remember future me.

----------Deploying on Heroku
1. Run the script to generate token file(token.pickle) for Google Drive: python3 generate_drive_token.py
2. Install Heroku cli (If not installed already)
3. Login into your heroku account with command: heroku login
4. Create a new heroku app: heroku create appname	
5. Select This App in your Heroku-cli: heroku git:remote -a appname
6. Change Dyno Stack to a Docker Container:heroku stack:set container
7. Add Private Credentials and Config Stuff: git add -f credentials.json token.pickle config.env
8. Commit new changes: git commit -m "Added Creds."
9. Push Code to Heroku: git push heroku master --force
10. Restart Worker by these commands:
heroku ps:scale worker=0
heroku ps:scale worker=1	 	


Heroku-Note: Doing authorizations ( /authorize command ) through telegram wont be permanent as heroku uses ephemeral filesystem. They will be reset on each dyno boot. As a workaround you can:

Make a file authorized_chats.txt and write the user names and chat_id of you want to authorize, each separated by new line
Then force add authorized_chats.txt to git and push it to heroku
git add authorized_chats.txt -f
git commit -asm "Added hardcoded authorized_chats.txt"
git push heroku heroku:master