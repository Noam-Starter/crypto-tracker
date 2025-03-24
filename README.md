import requests
import pandas as pd
import matplotlib.pyplot as plt

def get_crypto_price(coin_id="bitcoin"):
    url = f"https://api.coingecko.com/api/v3/simple/price?ids={coin_id}&vs_currencies=usd"
    response = requests.get(url)
    data = response.json()
    return data.get(coin_id, {}).get("usd", None)

def calculate_profit(coin_id, buy_price, amount):
    current_price = get_crypto_price(coin_id)
    if current_price is not None:
        profit = (current_price - buy_price) * amount
        return profit
    else:
        return None

def plot_price_history(coin_id="bitcoin"):
    url = f"https://api.coingecko.com/api/v3/coins/{coin_id}/market_chart?vs_currency=usd&days=30"
    response = requests.get(url)
    data = response.json()
    
    prices = data['prices']
    dates = [pd.to_datetime(price[0], unit='ms') for price in prices]
    values = [price[1] for price in prices]
    
    plt.figure(figsize=(10, 5))
    plt.plot(dates, values, label=coin_id.capitalize())
    plt.xlabel('תאריך')
    plt.ylabel('מחיר (USD)')
    plt.title(f'היסטוריית מחירים של {coin_id.capitalize()} ב-30 הימים האחרונים')
    plt.legend()
    plt.grid(True)
    plt.show()

# דוגמה לשימוש
portfolio = [
    {"coin": "bitcoin", "buy_price": 30000, "amount": 0.1},
    {"coin": "ethereum", "buy_price": 2000, "amount": 0.5},
]

for asset in portfolio:
    profit = calculate_profit(asset["coin"], asset["buy_price"], asset["amount"])
    if profit is not None:
        print(f"{asset['coin'].capitalize()} - רווח/הפסד: {profit:.2f} USD")
    else:
        print(f"לא ניתן לקבל מחיר נוכחי עבור {asset['coin']}")

# הצגת גרף היסטוריית מחירים
plot_price_history('bitcoin')
