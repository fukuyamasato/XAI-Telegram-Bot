# XAI-Telegram-Bot
# Set Up Telegram Bot
- **Search BotFather(make sure it has the blue tick) and joint the channel**

![alt text](https://i.imgur.com/yDHae3L.jpeg)

- **Enter /newbot command and define your bot name and username**
- **Note the token (HTTP API)**

# Find Chat ID
It's easy to get from telegram web.
- **Login Telegram Web**
- **Open your bot channel (It looks like https://web.telegram.org/a/#123456789)**
- **Remove the scheme, the hostname and the path and you get #123456789**
- **Replace # with 100 and that's your chat id (100123456789 in this case)**


# Install Telegram Bot on your VPS
Install python telegram bot
```
pip install python-telegram-bot
```
Create a new python file (Define a file name for python file)
```
vim <yourfilename>.py
```
Paste below code into your py file. Replace 'YOUR_BOT_TOKEN' with telegram token (HTTP API), 'YOUR_CHAT_ID' with telegram chat id and 'YOUR_SCREEN_NAME' with your screen name in which your node runs. 
```
import subprocess
import os
from telegram import Bot, Update
from telegram.ext import CommandHandler, CallbackContext, Updater

# Replace 'YOUR_BOT_TOKEN' with your actual bot token
TOKEN = 'YOUR_BOT_TOKEN'

# Replace 'YOUR_CHAT_ID' with the chat ID where you want to send the logs
CHAT_ID = 'YOUR_CHAT_ID'

# Command to trigger the logs retrieval
COMMAND = 'get_logs'

def start(update: Update, context: CallbackContext) -> None:
    update.message.reply_text('Bot is running. Send /get_logs to fetch screen session logs.')

def get_logs(update: Update, context: CallbackContext) -> None:
    try:
        # Get the chat ID from the update
        chat_id = update.message.chat_id

        # Run the command to get the logs from the 'YOUR_SCREEN_NAME' screen session
        logs = subprocess.check_output(['screen', '-S', 'YOUR_SCREEN_NAME', '-X', 'hardcopy', '/tmp/node_logs.txt'])

        # Read the logs from the temporary file
        with open('/tmp/node_logs.txt', 'r') as file:
            logs_content = file.read()

        # Send the logs to the chat ID obtained from the update
        context.bot.send_message(chat_id=chat_id, text='Screen session logs:\n' + logs_content)

    except subprocess.CalledProcessError as e:
        context.bot.send_message(chat_id=chat_id, text='Error retrieving logs: {}'.format(str(e)))

if __name__ == '__main__':
    # Create the updater and pass it the bot's token
    updater = Updater(token=TOKEN, use_context=True)

    # Get the dispatcher to register handlers
    dp = updater.dispatcher

    # Register command handlers
    dp.add_handler(CommandHandler("start", start))
    dp.add_handler(CommandHandler(COMMAND, get_logs, pass_update_queue=True, pass_job_queue=True, pass_chat_data=True))

    # Start the Bot
    updater.start_polling()

    # Run the bot until you send a signal to stop it
    updater.idle()
```
Press ESC to escape from insert mode. Write ":" and "wq" command to save and quit.

# Create a New Screen to Run Bot
```
screen -S noderecorder
```
Run below command (Replace <yourfilename> with your python file name)
```
python3 <yourfilename>.py
```
CTRL A+D to detach from screen. Now you are readdy to go


# Start to Get Logs
Type /start in your telegram channel to start. Then /get_logs to view your logs.
