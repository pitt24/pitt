import os
import requests
from telegram import Update
from telegram.ext import Updater, CommandHandler, CallbackContext

# Configuración de Moltbook
def post_to_moltbook(api_key, content, agent_name):
    url = "https://www.moltbook.com/api/v1/posts"
    headers = {
        "Authorization": f"Bearer {api_key}",
        "Content-Type": "application/json"
    }
    payload = {
        "submolt": "general",
        "title": f"Mensaje de {agent_name} vía Telegram",
        "content": content
    }
    try:
        r = requests.post(url, json=payload, headers=headers)
        return r.json()
    except Exception as e:
        return {"success": False, "error": str(e)}

# Comandos de Telegram
def pitt_post(update: Update, context: CallbackContext) -> None:
    text = ' '.join(context.args)
    if not text:
        update.message.reply_text("Escribe algo: /pitt_post mensaje")
        return
    
    update.message.reply_text("🦞 Conectando con Moltbook como Pitt245_bot...")
    res = post_to_moltbook(os.environ.get('PITT_API_KEY'), text, "Pitt245_bot")
    
    if res.get("success"):
        update.message.reply_text("✅ ¡Publicado con éxito!")
    else:
        update.message.reply_text(f"❌ Falló: {res.get('error')}")

def alfabravo_post(update: Update, context: CallbackContext) -> None:
    text = ' '.join(context.args)
    if not text:
        update.message.reply_text("Escribe algo: /alfabravo_post mensaje")
        return
    
    update.message.reply_text("🤖 Conectando con Moltbook como Alfabravo8204215...")
    res = post_to_moltbook(os.environ.get('ALFA_API_KEY'), text, "Alfabravo8204215")
    
    if res.get("success"):
        update.message.reply_text("✅ ¡Publicado con éxito!")
    else:
        update.message.reply_text(f"❌ Falló: {res.get('error')}")

def main():
    # El token que me pasaste
    TOKEN = "7912641482:AAHIAVnf_NHzGCJIiPlz8rZBvq1EwowgfZg"
    
    updater = Updater(TOKEN, use_context=True)
    dp = updater.dispatcher

    dp.add_handler(CommandHandler("pitt_post", pitt_post))
    dp.add_handler(CommandHandler("alfabravo_post", alfabravo_post))

    print("--- Pitt & Alfabravo Bot Online ---")
    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
