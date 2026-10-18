import pandas as pd

# 1. Trading Data Example (Aap ise CSV file se bhi load kar sakte hain)
trades_data = [
    {"Date": "2026-10-01", "Symbol": "BTCUSDT", "Type": "BUY", "Entry": 62000, "Exit": 63500, "Qty": 0.5},
    {"Date": "2026-10-02", "Symbol": "ETHUSDT", "Type": "BUY", "Entry": 2400, "Exit": 2350, "Qty": 2.0},
    {"Date": "2026-10-03", "Symbol": "NIFTY", "Type": "SELL", "Entry": 25000, "Exit": 24800, "Qty": 50},
    {"Date": "2026-10-04", "Symbol": "BTCUSDT", "Type": "BUY", "Entry": 63000, "Exit": 62500, "Qty": 0.5},
]

# DataFrame banana
df = pd.DataFrame(trades_data)

# 2. P&L Calculate karne ka Logic
def calculate_pnl(row):
    if row['Type'].upper() == 'BUY':
        return (row['Exit'] - row['Entry']) * row['Qty']
    elif row['Type'].upper() == 'SELL':
        return (row['Entry'] - row['Exit']) * row['Qty']
    return 0

df['PnL'] = df.apply(calculate_pnl, axis=1)

# 3. Status (WIN/LOSS) Define karna
df['Status'] = df['PnL'].apply(lambda x: 'WIN' if x > 0 else ('LOSS' if x < 0 else 'BREAKEVEN'))

# 4. Metrics Calculation (Win Rate, Total Profit/Loss, etc.)
total_trades = len(df)
total_wins = (df['Status'] == 'WIN').sum()
total_losses = (df['Status'] == 'LOSS').sum()
win_rate = (total_wins / total_trades) * 100 if total_trades > 0 else 0
net_pnl = df['PnL'].sum()
avg_win = df[df['Status'] == 'WIN']['PnL'].mean() if total_wins > 0 else 0
avg_loss = df[df['Status'] == 'LOSS']['PnL'].mean() if total_losses > 0 else 0

# 5. Results Display
print("=" * 40)
print("        TRADING JOURNAL SUMMARY        ")
print("=" * 40)
print(f"Total Trades   : {total_trades}")
print(f"Wins           : {total_wins}")
print(f"Losses         : {total_losses}")
print(f"Win Rate       : {win_rate:.2f}%")
print(f"Net P&L        : {net_pnl:.2f}")
print(f"Average Win    : {avg_win:.2f}")
print(f"Average Loss   : {avg_loss:.2f}")
print("=" * 40)

# Trade details View
print("\nDetailed Log:")
print(df[['Date', 'Symbol', 'Type', 'PnL', 'Status']])
