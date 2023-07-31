import logging
from telegram import Update
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters, CallbackContext

# Replace 'YOUR_TELEGRAM_BOT_TOKEN' with your actual Telegram bot token
TELEGRAM_BOT_TOKEN = '6040598338:AAFCBaJVTF8hSajKr7G43pfvW1UY6BPHFN8'

# Replace 'TERRABOX_CONVERTER_API_URL' with the URL of the Terrabox link converter API or service
TERRABOX_CONVERTER_API_URL = '5762616457'

# Replace 'LIST_OF_SOURCE_CHANNELS' with the list of chat IDs from where you want to pick up the posts
LIST_OF_SOURCE_CHANNELS = [-1001792385566, -1001815531453, -1001751162842]

# Replace 'LIST_OF_DESTINATION_CHANNELS' with the list of chat IDs where you want to post the converted links
LIST_OF_DESTINATION_CHANNELS = [-1001850651458, -1001348030409, -1001793239867,  -1001864806401]

# Setup logging
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)
logger = logging.getLogger(__name__)

def start(update: Update, _: CallbackContext):
    update.message.reply_text('Hi! I am your Terrabox link converter bot.')

def convert_link(link):
    # Implement the logic to convert the link using Terrabox link converter API here
    # Make a POST request to TERRABOX_CONVERTER_API_URL with the link and get the converted link
    # Replace the below line with the actual logic to handle the API call
    # For example:
    # response = requests.post(TERRABOX_CONVERTER_API_URL, data={'link': link})
    # converted_link = response.json().get('converted_link')
    # return converted_link

    # For the purpose of this example, we'll return a dummy converted link
    return f'https://terrabox-converter.com/{link}'

def process_post(update: Update, _: CallbackContext):
    chat_id = update.effective_chat.id

    if chat_id in LIST_OF_SOURCE_CHANNELS:
        message = update.message
        converted_link = convert_link(message.text)
        
        if converted_link:
            # Post the converted link to the destination channels
            for destination_chat_id in LIST_OF_DESTINATION_CHANNELS:
                update.message.bot.send_message(destination_chat_id, f'{message.from_user.mention_html()}: {converted_link}', parse_mode='HTML')

def main():
    updater = Updater(TELEGRAM_BOT_TOKEN)
    dispatcher = updater.dispatcher

    # Add command handler for the '/start' command
    dispatcher.add_handler(CommandHandler('start', start))

    # Add message handler to process incoming messages
    dispatcher.add_handler(MessageHandler(Filters.text & ~Filters.command, process_post))

    # Start the Bot
    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
