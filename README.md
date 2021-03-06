# TON Decentralized Exchange

This is implementation of fully decentralized exchange with the support of the exchange of grams, extra currencies and [TRC20 tokens](https://github.com/cod1ng-studio/TRC20).

### Compilation

Contract: `func -P -o dex.fif stdlib.fc msg_hex_comment.fc dex.fc`

Bot: `npm install` then `node ExchangeBot.js`

### Internal messages:

Following the [TON smartcontracts guidelines](https://test.ton.org/smguidelines.txt) all internal messages starts with 32 uint operation_id and 64 uint query_id.

If the query is invalid, the smart contract throws an exception.

---

insert_order(from_value, from_currency_type, from_currency, to_value, to_currency_type, to_currency) - creates a new exchange order.

currency_type has 2 values:

0 - if currency is Gram or build-in TON extra-currency. In this case currency value is 32 signed int (TON currency ID, Gram is 0).

1 - if currency is TRC20 token. In this case currency value is TRC20 token smart contract address (8bit wc + 256bit addr).

if from_currency is gram an appropriate amount PLUS one additional Gram must be attached to the message. 1 Gram will be returned back.

if from_currency is TON extra currency an appropriate amount must be attached to the message.

After someone else creates an order directly opposite to yours, an exchange is made.

Order lifetime is 1 day. If the order is not closed during the day, anyone can cancel this order (funds will be returned back).

The funds will automatically go to the wallets from which you created the order.

There is currently no partial-exchange of orders.

`fift insert-order.fif 0.33 1 DOGE.addr 0.1 0 0` - creates order 0.33 DOGE -> 0.1 Gram

---

cancel_order(order_id) - cancel exchange order which you created (or any order whose life time has expired).

The funds will automatically go to the wallets from which you created the order.

`fift cancel-order.fif 1` - cancel order with ID 1

---

### Get methods:

_ orders(slice sender, slice from_currency, slice to_currency) - list of pending orders.

If parameter is empty slice it will be ignored, else orders will be filtering by specified parameter(-s).

`orders "" "" ""` - show all pending orders

### Bot

For convenience, you can create queries by sending simple messages with text comment to token smart contract.
In this case, the comment text should contain the hex of the message body (produced by 'csr.' fift command).

Thanks to this, it was possible to create a user friendly interface - [@dexton_bot](https://t.me/dexton_bot) in Telegram.

Using the bot, users can exchange currencies without using a console.

The bot generates ton:// links that you need to open with your TON wallet and send.