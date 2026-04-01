import requests
import json
import random
from flask import Flask, request, jsonify

app = Flask(__name__)

TOKEN = '8585932070:AAEZFbSGB3h4Nw_ngDqpzRWyCkGMvbSv0uI'
URL = f"https://api.telegram.org/bot{TOKEN}"

# Счетчики
global_count = 0
user_counts = {}

def send_message(chat_id, text, keyboard=None):
    try:
        data = {'chat_id': chat_id, 'text': text, 'parse_mode': 'Markdown'}
        if keyboard:
            data['reply_markup'] = json.dumps(keyboard)
        requests.post(f"{URL}/sendMessage", data=data)
    except Exception as e:
        print(f"Ошибка: {e}")

def get_keyboard():
    return {'inline_keyboard': [[{'text': '👊 УДАРИТЬ 👊', 'callback_data': 'hit'}]]}

@app.route(f'/{TOKEN}', methods=['POST'])
def webhook():
    global global_count
    update = request.get_json()
    
    if 'message' in update and 'text' in update['message']:
        chat_id = update['message']['chat']['id']
        user_id = update['message']['from']['id']
        username = update['message']['from'].get('first_name', 'Пользователь')
        text = update['message']['text']
        
        if text == '/start':
            user_count = user_counts.get(user_id, 0)
            msg = (f"👴 *Привет, {username}!*\n\n"
                   f"📊 Всего ударов: *{global_count}*\n"
                   f"🔨 Твоих ударов: *{user_count}*\n\n"
                   "Жми на кнопку!")
            send_message(chat_id, msg, get_keyboard())
    
    elif 'callback_query' in update:
        callback = update['callback_query']
        chat_id = callback['message']['chat']['id']
        user_id = callback['from']['id']
        callback_id = callback['id']
        username = callback['from'].get('first_name', 'Пользователь')
        
        if callback['data'] == 'hit':
            global_count += 1
            user_counts[user_id] = user_counts.get(user_id, 0) + 1
            
            replies = ["Ой! Больно! 🔥", "Крякнул! 💪", f"Ну ты силен, {username}! 😤", "Ай! 😭"]
            reply_text = random.choice(replies)
            
            msg = (f"{reply_text}\n\n"
                   f"📊 Всего ударов: *{global_count}*\n"
                   f"🔨 Твоих ударов: *{user_counts[user_id]}*")
            
            requests.post(f"{URL}/answerCallbackQuery", data={'callback_query_id': callback_id})
            send_message(chat_id, msg, get_keyboard())
    
    return jsonify({'status': 'ok'})

@app.route('/')
def index():
    return 'Бот "Ударь Деда" работает!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=10000)
