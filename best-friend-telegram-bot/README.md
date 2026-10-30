# Best Friend Telegram Bot

An n8n workflow that turns a Telegram bot into "Buddy", a warm, funny best-friend chatbot powered by Groq. Each Telegram chat gets its own memory (last 10 exchanges), and Buddy replies in whatever language the user writes in, including Hinglish.

## How it works

1. **Telegram Trigger** receives incoming messages.
2. **Has text?** checks that the message contains text.
   - No text (sticker, photo, etc.): sends a friendly "text only" reply.
   - Text: passes the message to the AI agent.
3. **Buddy Agent** (n8n AI Agent) replies using:
   - **Groq Chat Model**: `openai/gpt-oss-120b`, temperature 0.8
   - **Simple Memory**: window of 10, keyed by Telegram chat ID
   - Retry on fail: 3 tries, 2000 ms wait
4. **Send Reply** sends the answer back as plain text (no parse mode, no n8n attribution).

## Setup

1. In n8n, go to **Workflows > Import from File** and choose `workflow/best-friend-telegram-bot.json`.
2. Create credentials (credentials are not included in this repo):
   - **Telegram API**: bot token from [@BotFather](https://t.me/BotFather)
   - **Groq API**: key from https://console.groq.com/keys
3. Open these nodes and select your credentials:
   - Telegram Trigger, Send Reply, Send Text Only Notice: Telegram credential
   - Groq Chat Model: Groq credential
4. Toggle the workflow to **Active**. Telegram webhooks need n8n to be reachable on a public HTTPS URL (n8n Cloud already is).

## Testing

Open your bot in Telegram, then:

1. Send `/start`, then `hi`. Expect a short friendly reply.
2. Send a sticker. Expect: "I can only read text messages for now, bro".

## Notes

- Groq retired `llama-3.3-70b-versatile` on 2026-08-16 for free and developer accounts, so this workflow uses `openai/gpt-oss-120b`. Check https://console.groq.com/docs/deprecations if the model stops working and swap it in the Groq Chat Model node.
- No API keys or secrets are stored in this repository.
