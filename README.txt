diyzu Bot — Fixed Version
==========================

SETUP
-----
1. Install dependencies:
   pip install -r requirements.txt

2. Set your bot token — TWO options:
   a) Edit bot.py line:  BOT_TOKEN = "YOUR_BOT_TOKEN_HERE"
   b) Environment var:   export BOT_TOKEN=your_token

3. Set your Admin Telegram User ID — TWO options:
   a) Edit bot.py line:  ADMIN_IDS = [123456789]
   b) Environment var:   export ADMIN_IDS=123456789

4. Run the bot:
   python bot.py

CUSTOM EMOJI (BINANCE LOGO)
----------------------------
The bot uses a 🔶 fallback emoji by default. To use the real Binance logo
as a custom Telegram emoji:

1. Upload binance_logo.jpg as a sticker to your bot's sticker set using
   the @Stickers bot on Telegram.
2. Get the custom_emoji_id of that sticker.
3. Set it:
   a) In bot.py: BINANCE_EMOJI_ID = "your_custom_emoji_id"
   b) Or:        export BINANCE_EMOJI_ID=your_custom_emoji_id

Until set, the bot falls back to 🔶 automatically — no errors.

FIXES APPLIED
-------------
1. CRITICAL BUG: res_add_keys_item state parser was split("|",3) checking
   len==4 but state only has 3 parts — admin could NEVER add reseller keys.
   FIXED: state is now correctly split into exactly 4 parts.

2. Binance Pay now shows a clickable "Open Binance App" deep-link button
   (https://app.binance.com/qr/orf/pay?userId=YOUR_ID) so buyers can tap
   to pay directly from Telegram — no manual app-switching needed.

3. Users stuck in "awaiting screenshot" state who send a text message now
   get a clear reminder: "Please send your Binance/UPI screenshot as a
   photo." Previously the bot was silently ignoring their text.

4. /start now clears any stuck state automatically. If a user is stuck
   just tell them to type /start.

5. Bot startup clears ALL stale _state entries from the data file so
   nobody is stuck on restart.

6. Old/orphaned pending orders are cancelled when the same user starts a
   new payment — no more piling up of ghost orders.

7. When wallet balance is low but Binance/UPI are available, the message
   now says "Balance low — pay via Binance/UPI below" instead of just
   "Insufficient balance" with no explanation.

8. New "Clear Pending Orders" button in Admin Panel to wipe all pending
   orders at any time.

9. All dur (duration) values are now encoded/decoded safely (spaces → ~)
   in callback_data for consistency.

ADMIN COMMANDS
--------------
- /start               — Main menu (also clears your stuck state)
- Admin Panel          — Full admin dashboard
  → Add Keys           — Add keys for a plan (one key per line)
  → List Keys          — See unused/used key counts
  → Add Reseller       — Promote a user to reseller by their Telegram ID
  → List Resellers     — View all resellers and balances
  → Pending Orders     — View orders awaiting approval
  → Clear Pending      — Wipe all ghost pending orders (FIX #8)
  → Settings           — Set Binance ID and UPI ID

PAYMENT FLOW
------------
User → Buy Key → Select Plan → Payment screen with:
  [Open Binance App]  ← deep-link button (taps straight into Binance)
  [Paid via Binance — Upload Screenshot]
  [Paid via UPI — Upload Screenshot]
  [Cancel]

After screenshot upload → Admin gets notified with photo + [Approve] [Reject]
On approve → key is auto-assigned and sent to buyer.

FILES
-----
- bot.py          — Main bot code (all fixes applied)
- bot_data.json   — Clean data file (no stale states or pending orders)
- requirements.txt
- README.txt      — This file

NOTE: bot_data.json starts clean — no stale pending orders, no stuck states.
