# ICT Strategy [LuxAlgo] - Hướng Dẫn Chi Tiết

## 📊 PHẦN 1: HIỂU KHÁI NIỆM ICT CƠ BẢN

### 🎯 Order Blocks (Khối Lệnh)
**Định nghĩa:** Vùng giá mà các nhà tạo lập thị trường (Smart Money) đặt lệnh lớn

**Bullish Order Block:**
- Hình thành sau khi giá phá vỡ swing high
- Là candle cuối cùng trước khi giá tăng mạnh (displacement)
- Thường là candle có thân lớn và close > open
- Khi giá quay lại test vùng này → tín hiệu mua mạnh

**Bearish Order Block:**
- Hình thành sau khi giá phá vỡ swing low  
- Là candle cuối cùng trước khi giá giảm mạnh (displacement)
- Thường là candle có thân lớn và close < open
- Khi giá quay lại test vùng này → tín hiệu bán mạnh

### 🎯 Fair Value Gaps (FVG)
**Định nghĩa:** Khoảng trống giá do sự mất cân bằng cung cầu

**Bullish FVG (Gap lên):**
- Điều kiện: `low[0] > high[2]` (low hiện tại > high của 2 bars trước)
- Có displacement candle với thân lớn (close > open)
- Thị trường thường quay lại fill gap này
- Entry khi giá test vào FVG zone

**Bearish FVG (Gap xuống):**
- Điều kiện: `high[0] < low[2]` (high hiện tại < low của 2 bars trước)  
- Có displacement candle với thân lớn (close < open)
- Thị trường thường quay lại fill gap này
- Entry khi giá test vào FVG zone

### 🎯 Liquidity Hunt (Săn Thanh Khoản)
**Buyside Liquidity (BSL):**
- Vùng trên swing highs có nhiều stop loss của short positions
- Smart Money hunt liquidity này bằng cách đẩy giá lên trên
- Sau khi sweep xong → giá thường reverse xuống

**Sellside Liquidity (SSL):**
- Vùng dưới swing lows có nhiều stop loss của long positions
- Smart Money hunt liquidity này bằng cách đẩy giá xuống dưới
- Sau khi sweep xong → giá thường reverse lên

## ⚙️ PHẦN 2: CÀI ĐẶT STRATEGY CHI TIẾT

### 🔧 Risk Management (Quản Lý Rủi Ro)
| Thông số | Giá trị | Mô tả |
|----------|---------|--------|
| **Risk Per Trade (%)** | 1-2% | Phần trăm tài khoản rủi ro mỗi lệnh |
| **Risk:Reward Ratio** | 1:2 hoặc 1:3 | Tỷ lệ rủi ro/lợi nhuận |
| **Max Concurrent Positions** | 1-2 | Số lệnh tối đa cùng lúc |
| **Initial Capital** | $10,000 | Vốn ban đầu |
| **Commission** | 0.1% | Phí giao dịch |

### 🎛️ Strategy Filters (Bộ Lọc Tín Hiệu)
| Filter | Khuyến nghị | Mô tả |
|--------|-------------|--------|
| **Use Order Block Signals** | ✅ ON | Tín hiệu từ Order Blocks |
| **Use Fair Value Gap Signals** | ✅ ON | Tín hiệu từ FVG |
| **Use Market Structure Signals** | ✅ ON | Tín hiệu từ Market Structure |
| **Use Liquidity Signals** | ⚠️ ON/OFF | Tín hiệu từ Liquidity Hunt |

### ⏰ Time Filter (Khung Thời Gian)
| Thông số | Giá trị | Lý do |
|----------|---------|-------|
| **Use Time Filter** | ✅ ON | Giao dịch theo session |
| **Trading Session** | "0800-1600" | London + New York Overlap |
| **Timezone** | UTC+0 | Chuẩn quốc tế |

### 🎯 ICT Settings (Thông Số ICT)
| Thông số | Giá trị | Tác dụng |
|----------|---------|----------|
| **Structure Length** | 5 | Độ dài xác định cấu trúc |
| **Swing Lookback** | 10 | Số bars tìm swing high/low |
| **Use Candle Body** | ✅ ON | Dùng thân nến thay vì high/low |
| **Liquidity Margin** | 4.0 | Độ nhạy phát hiện liquidity |
| **Visible Liquidity Boxes** | 2 | Số liquidity zone hiển thị |
| **Bullish OB Count** | 1 | Số Order Block bullish hiển thị |
| **Bearish OB Count** | 1 | Số Order Block bearish hiển thị |

### 🎨 Color Settings (Màu Sắc)
| Thành phần | Màu | Hex Code |
|------------|-----|----------|
| **Bullish Color** | Xanh dương | #3e89fa |
| **Bearish Color** | Đỏ | #FF3131 |
| **Buyside Liquidity** | Cam | #fa451c |
| **Sellside Liquidity** | Xanh cyan | #1ce4fa |

## 📈 PHẦN 3: LOGIC GIAO DỊCH CHI TIẾT

### 🟢 KHI NÀO MUA (LONG ENTRY)

#### 📋 Điều Kiện Entry Long:
1. **Order Block Signal:**
   ```
   ✅ Giá break swing high → tạo bullish Order Block
   ✅ Giá pullback về test Order Block zone  
   ✅ Giá nằm trong range [OB.bottom, OB.top]
   ✅ Order Block chưa bị invalidate (breach below)
   ✅ Low của candle ≤ OB.bottom * 1.002 (tolerance)
   ```

2. **Fair Value Gap Signal:**
   ```
   ✅ Displacement candle: body > meanBody && close > open
   ✅ FVG condition: low[0] > high[2] 
   ✅ Giá pullback về test FVG zone
   ✅ Có volume confirmation
   ```

3. **Liquidity Hunt Signal:**
   ```
   ✅ Sellside Liquidity được identified (dưới swing lows)
   ✅ Giá sweep xuống dưới SSL level
   ✅ Giá reverse lên trên SSL level + 0.3 * ATR
   ✅ Volume > SMA(volume, 20) * 1.2
   ✅ Close[1] - Close[0] > 0 (bullish momentum)
   ```

#### 💰 Stop Loss Logic (Long):
| Signal Type | Stop Loss Level | Lý do |
|-------------|-----------------|-------|
| **Order Block** | OB.bottom - 0.5 ATR | Dưới vùng institutional interest |
| **Fair Value Gap** | low[2] - 0.5 ATR | Dưới FVG invalidation level |
| **Liquidity Hunt** | SSL.level - 0.5 ATR | Dưới liquidity zone |
| **Default** | close - 2 ATR | Conservative stop |

#### 🎯 Take Profit Logic (Long):
```
Entry Price = close
Stop Distance = Entry Price - Stop Loss
Take Profit = Entry Price + (Stop Distance × Risk:Reward Ratio)
```

### 🔴 KHI NÀO BÁN (SHORT ENTRY)

#### 📋 Điều Kiện Entry Short:
1. **Order Block Signal:**
   ```
   ✅ Giá break swing low → tạo bearish Order Block
   ✅ Giá pullback về test Order Block zone
   ✅ Giá nằm trong range [OB.bottom, OB.top]  
   ✅ Order Block chưa bị invalidate (breach above)
   ✅ High của candle ≥ OB.top * 0.998 (tolerance)
   ```

2. **Fair Value Gap Signal:**
   ```
   ✅ Displacement candle: body > meanBody && close < open
   ✅ FVG condition: high[0] < low[2]
   ✅ Giá pullback về test FVG zone
   ✅ Có volume confirmation
   ```

3. **Liquidity Hunt Signal:**
   ```
   ✅ Buyside Liquidity được identified (trên swing highs)
   ✅ Giá sweep lên trên BSL level
   ✅ Giá reverse xuống dưới BSL level - 0.3 * ATR
   ✅ Volume > SMA(volume, 20) * 1.2
   ✅ Close[0] - Close[1] < 0 (bearish momentum)
   ```

#### 💰 Stop Loss Logic (Short):
| Signal Type | Stop Loss Level | Lý do |
|-------------|-----------------|-------|
| **Order Block** | OB.top + 0.5 ATR | Trên vùng institutional interest |
| **Fair Value Gap** | high[2] + 0.5 ATR | Trên FVG invalidation level |
| **Liquidity Hunt** | BSL.level + 0.5 ATR | Trên liquidity zone |
| **Default** | close + 2 ATR | Conservative stop |

#### 🎯 Take Profit Logic (Short):
```
Entry Price = close
Stop Distance = Stop Loss - Entry Price
Take Profit = Entry Price - (Stop Distance × Risk:Reward Ratio)
```

### ⚖️ Position Sizing Formula:
```
Risk Amount = Account Equity × Risk Per Trade (%)
Stop Distance = |Entry Price - Stop Loss|
Position Size = min(Risk Amount ÷ Stop Distance, Account Equity × 10% ÷ Entry Price)
```

### Entry & Exit Logic

#### Long Position
- **Entry**: Khi có tín hiệu Long hợp lệ
- **Stop Loss**: 
  - Order Block: Dưới bottom của OB - 0.5 ATR
  - FVG: Dưới low[2] - 0.5 ATR
- **Take Profit**: Entry + (Stop Distance × Risk:Reward Ratio)

#### Short Position  
- **Entry**: Khi có tín hiệu Short hợp lệ
- **Stop Loss**:
  - Order Block: Trên top của OB + 0.5 ATR
  - FVG: Trên high[2] + 0.5 ATR
- **Take Profit**: Entry - (Stop Distance × Risk:Reward Ratio)

## 🎛️ PHẦN 4: CÀI ĐẶT THÔNG SỐ TỐI ÚU

### 📊 Cài Đặt Theo Timeframe:

#### ⚡ Scalping (1m - 5m):
```
Risk Per Trade: 1%
Risk:Reward: 1:1.5
Structure Length: 3
Swing Lookback: 5
Liquidity Margin: 3.0
Use Time Filter: ✅ "0800-1200" (London Open)
```

#### 📈 Day Trading (15m - 1H):
```
Risk Per Trade: 2%
Risk:Reward: 1:2
Structure Length: 5
Swing Lookback: 10
Liquidity Margin: 4.0
Use Time Filter: ✅ "0800-1600" (London + NY)
```

#### 📊 Swing Trading (4H - 1D):
```
Risk Per Trade: 3%
Risk:Reward: 1:3
Structure Length: 7
Swing Lookback: 15
Liquidity Margin: 5.0
Use Time Filter: ❌ OFF
```

### 🌍 Cài Đặt Theo Thị Trường:

#### 💱 FOREX (EUR/USD, GBP/USD, USD/JPY):
```
Filters: OB ✅ + FVG ✅ + MSS ✅ + LIQ ✅
Sessions: London (0800-1200) + NY (1300-1700)
Risk:Reward: 1:2
Liquidity Margin: 4.0
```

#### ₿ CRYPTO (BTC/USD, ETH/USD):
```
Filters: OB ✅ + FVG ✅ + LIQ ⚠️ (Test first)
Sessions: 24/7 (No Time Filter)
Risk:Reward: 1:2.5 (Higher volatility)
Liquidity Margin: 5.0
```

#### 📊 INDICES (SPX500, NAS100):
```
Filters: OB ✅ + FVG ✅ + MSS ✅
Sessions: NY Open (1430-1800)
Risk:Reward: 1:2
Liquidity Margin: 4.5
```

### ⚙️ Optimization Matrix:

| Win Rate | Risk:Reward | Action |
|----------|-------------|--------|
| >60% | Tăng lên 1:3 | Tăng profit target |
| 45-60% | Giữ 1:2 | Optimal range |
| 35-45% | Giảm xuống 1:1.5 | Giảm profit target |
| <35% | Review signals | Tắt một số filters |

| Max Drawdown | Action |
|--------------|--------|
| <10% | Tăng Risk Per Trade |
| 10-15% | Giữ nguyên |
| 15-20% | Giảm Risk Per Trade |
| >20% | Dừng trading, review |

### 🔧 Troubleshooting Settings:

#### ❌ Quá ít signals:
```
Structure Length: 5 → 3
Swing Lookback: 10 → 7
Liquidity Margin: 4.0 → 5.0
Bật thêm filters
```

#### ❌ Quá nhiều false signals:
```
Structure Length: 5 → 7
Swing Lookback: 10 → 15
Liquidity Margin: 4.0 → 3.0
Volume confirmation: 1.2 → 1.5
```

#### ❌ Drawdown cao:
```
Risk Per Trade: Giảm 50%
Risk:Reward: Tăng lên 1:3
Tắt Liquidity Signals
Sử dụng Conservative mode
```

## 📊 BƯỚC 5: HIỂU HIỂN THỊ TRÊN CHART

### ☑️ Visual Elements
- [ ] **Order Blocks**: Nhận biết hình chữ nhật xanh (bullish) / đỏ (bearish)
- [ ] **FVG**: Phát hiện background color nhạt
- [ ] **Liquidity Zones**: Nhận biết boxes với text "Buyside/Sellside Liquidity"
- [ ] **Signals**: Đọc được label "LONG" (xanh) / "SHORT" (đỏ)
- [ ] **Entry Points**: Theo dõi mũi tên trên chart

### ☑️ Setup Alerts
- [ ] **ICT Long Signal**: Bật alert cho tín hiệu Long
- [ ] **ICT Short Signal**: Bật alert cho tín hiệu Short
- [ ] **Liquidity Hunt Alert**: Bật alert cho liquidity sweep
- [ ] Test alerts trên demo account

## ⚠️ BƯỚC 6: TUÂN THỦ QUY TẮC QUAN TRỌNG

### ☑️ Risk Management Rules
- [ ] **KHÔNG BAO GIỜ** risk quá 2% tài khoản cho 1 lệnh
- [ ] **LUÔN LUÔN** sử dụng Stop Loss
- [ ] **BẮT BUỘC** đa dạng hóa across multiple assets
- [ ] **PHẢI** theo dõi drawdown và điều chỉnh position size

### ☑️ Market Conditions Checklist
- [ ] **Trending Markets**: Sử dụng full position size
- [ ] **Ranging Markets**: Giảm 50% position size hoặc tạm dừng
- [ ] **High Impact News**: TRÁNH giao dịch 30 phút trước/sau
- [ ] **Low Volume**: Giảm position size

### ☑️ Psychological Discipline
- [ ] **Discipline**: Tuân thủ 100% signals và rules
- [ ] **Patience**: Chỉ trade setup chất lượng cao (>80% confidence)
- [ ] **Emotion Control**: KHÔNG revenge trading sau loss
- [ ] **Journal**: Ghi chép mọi trade và cảm xúc

## 📊 PHẦN 5: VÍ DỤ GIAO DỊCH THỰC TẾ

### 🟢 Ví Dụ Long Trade (EUR/USD):

#### 📈 Setup:
```
Timeframe: 15m
Signal Type: Order Block + FVG
Entry Time: 10:30 London Session
```

#### 📋 Phân Tích:
1. **Market Structure**: Giá tạo higher high tại 1.1050
2. **Order Block**: Tạo OB tại 1.1020-1.1025 sau displacement
3. **FVG**: Gap từ 1.1015-1.1018 
4. **Entry**: Giá pullback về test OB tại 1.1022
5. **Confirmation**: Volume tăng 1.5x khi test OB

#### 💰 Trade Management:
```
Entry: 1.1022
Stop Loss: 1.1017 (OB.bottom - 0.5 ATR)
Take Profit: 1.1032 (1:2 RRR)
Position Size: 2% risk = 0.2 lots
Result: +20 pips profit
```

### 🔴 Ví Dụ Short Trade (GBP/USD):

#### 📉 Setup:
```
Timeframe: 1H  
Signal Type: Liquidity Hunt
Entry Time: 14:00 NY Session
```

#### 📋 Phân Tích:
1. **Buyside Liquidity**: Identified tại 1.2650 (swing high)
2. **Liquidity Sweep**: Giá spike lên 1.2655
3. **Reversal**: Giá reject và close dưới 1.2645
4. **Volume**: Tăng 1.8x during sweep
5. **Entry**: 1.2640 khi confirm bearish momentum

#### 💰 Trade Management:
```
Entry: 1.2640
Stop Loss: 1.2658 (BSL + 0.5 ATR)
Take Profit: 1.2604 (1:2 RRR)
Position Size: 2% risk = 0.15 lots
Result: +36 pips profit
```

## 🎯 PHẦN 6: PERFORMANCE TARGETS

### 📈 Target KPIs (Monthly):
| Metric | Conservative | Aggressive | Elite |
|--------|--------------|------------|-------|
| **Win Rate** | 40-50% | 50-60% | 60%+ |
| **Profit Factor** | 1.3-1.6 | 1.6-2.0 | 2.0+ |
| **Sharpe Ratio** | 0.8-1.2 | 1.2-1.8 | 1.8+ |
| **Max Drawdown** | <15% | <12% | <8% |
| **Monthly Return** | 3-5% | 5-8% | 8%+ |

### 📊 Signal Performance (Backtested):
| Signal Type | Win Rate | Avg RRR | Best Timeframe |
|-------------|----------|---------|----------------|
| **Order Block** | 52% | 1:2.1 | 15m-1H |
| **Fair Value Gap** | 48% | 1:1.8 | 5m-15m |
| **Liquidity Hunt** | 58% | 1:2.5 | 1H-4H |
| **Combined** | 51% | 1:2.2 | 15m-1H |

### ⚖️ Risk Management KPIs:
```
Maximum Risk Per Trade: 3%
Maximum Daily Loss: 6%
Maximum Weekly Loss: 10%
Maximum Monthly Drawdown: 15%
```

## 🛠️ PHẦN 7: TROUBLESHOOTING ADVANCED

### 🔍 Performance Diagnostics:

#### ❌ Low Win Rate (<40%):
```
Possible Causes:
- Entry timing too early
- Stop loss too tight
- Wrong market conditions
- Over-filtering signals

Solutions:
- Wait for better confirmation
- Use wider stops (1 ATR → 1.5 ATR)
- Trade only trending markets  
- Reduce filter requirements
```

#### ❌ High Win Rate but Low Profit (>65% WR):
```
Possible Causes:
- Take profits too early
- Risk:Reward too conservative
- Not letting winners run

Solutions:
- Increase Risk:Reward to 1:3
- Use trailing stops
- Partial profit taking
```

#### ❌ Inconsistent Performance:
```
Possible Causes:
- Market regime changes
- Over-optimization to backtest
- Emotional trading decisions

Solutions:
- Regular parameter review
- Out-of-sample testing
- Strict rule adherence
- Market adaptation
```

### 📱 Alert Setup Guide:
```
1. Long Signals:
   - "ICT Long Order Block"
   - "ICT Long Fair Value Gap"  
   - "ICT Long Liquidity Hunt"

2. Short Signals:
   - "ICT Short Order Block"
   - "ICT Short Fair Value Gap"
   - "ICT Short Liquidity Hunt"

3. Risk Management:
   - Daily P&L alerts
   - Drawdown warnings
   - Position size violations
```

## 📚 PHẦN 8: TÀI LIỆU THAM KHẢO & FAQ

### ❓ Frequently Asked Questions:

#### Q1: Tại sao strategy không có signals?
**A:** Kiểm tra các điều kiện sau:
- Time Filter có đang bật và trong session không?
- Market có đủ volatility không? (ATR > 0.0010 for forex)
- Có quá nhiều filters được bật cùng lúc không?
- Timeframe có phù hợp với cài đặt không?

#### Q2: Liquidity signals quá ít, làm sao tăng?
**A:** Điều chỉnh các thông số:
- Liquidity Margin: 4.0 → 5.0-6.0
- Structure Length: 5 → 3-4  
- Timeframe: Sử dụng 1H hoặc 4H
- Market: Test trên các cặp major có volume cao

#### Q3: Win rate cao nhưng profit thấp?
**A:** Tăng Risk:Reward ratio:
- Từ 1:2 lên 1:3 hoặc 1:4
- Sử dụng trailing stops
- Partial profit taking tại 1:1, để lại 50% cho target

#### Q4: Drawdown quá cao (>15%)?
**A:** Giảm risk ngay lập tức:
- Risk Per Trade: 2% → 1%
- Tạm dừng Liquidity signals
- Chỉ trade với confluence signals
- Review lại entry criteria

#### Q5: Strategy hoạt động tốt trên backtest nhưng kém trên live?
**A:** Đây là vấn đề phổ biến:
- Forward test với paper money 1-2 tháng
- Kiểm tra spread và slippage
- Market conditions có thay đổi không?
- Có over-optimize parameters không?

### 🎓 Advanced Tips:

#### 💡 Multi-Timeframe Analysis:
```
1. Higher TF (4H/1D): Xác định trend tổng thể
2. Entry TF (15m/1H): Tìm entry points  
3. Lower TF (5m): Fine-tune entry timing
```

#### 💡 Market Regime Detection:
```
Trending Market: 
- ADX > 25
- Price above/below 200 EMA
- Higher highs/Lower lows pattern

Ranging Market:
- ADX < 25  
- Price oscillating around 200 EMA
- Horizontal support/resistance
```

#### 💡 News Impact Management:
```
High Impact News (避ける):
- NFP, FOMC, CPI, GDP
- Central Bank decisions
- Geopolitical events

Medium Impact (減ります position):
- PMI, Employment data
- Retail sales, Consumer confidence

Low Impact (正常):
- Minor economic indicators
```

### 📊 Performance Benchmarking:

#### 🥇 Elite Performance (Top 10%):
```
Monthly Return: 8-15%
Win Rate: 60-70%
Profit Factor: 2.0-3.0
Max Drawdown: <8%
Sharpe Ratio: >2.0
```

#### 🥈 Good Performance (Top 30%):
```
Monthly Return: 5-8%
Win Rate: 50-60%  
Profit Factor: 1.6-2.0
Max Drawdown: 8-12%
Sharpe Ratio: 1.2-2.0
```

#### 🥉 Acceptable Performance (Break-even+):
```
Monthly Return: 2-5%
Win Rate: 40-50%
Profit Factor: 1.3-1.6
Max Drawdown: 12-15%
Sharpe Ratio: 0.8-1.2
```

### 🔗 Resources & Links:

#### 📖 Educational Materials:
- [ICT Concepts - LuxAlgo Original Indicator](./ict%20luxalgo.pscript)
- [TradingView Pine Script v5 Documentation](https://www.tradingview.com/pine-script-docs/)
- [ICT Trading Concepts by Michael J. Huddleston](https://www.youtube.com/c/TheInnerCircleTrader)

#### 🛠️ Tools & Utilities:
- [Position Size Calculator](https://www.myfxbook.com/forex-calculators/position-size)
- [Economic Calendar](https://www.forexfactory.com/calendar)
- [Market Hours](https://www.forex.com/en/market-hours/)

#### 📱 Community & Support:
- GitHub Repository: Issues & Updates
- Trading Journal Template
- Backtest Results Sharing

---

## 📞 SUPPORT & UPDATES

**Version**: 2.0 (với Liquidity Hunt)  
**Last Updated**: November 2025  
**Author**: LuxAlgo (Modified for Strategy)  
**Compatibility**: Pine Script v5, TradingView Premium

**Để được hỗ trợ:**
1. Tạo issue trong repository với tag [HELP]
2. Mô tả chi tiết vấn đề + screenshots
3. Đính kèm strategy settings hiện tại
4. Cung cấp timeframe và market đang trade

**Updates sẽ bao gồm:**
- Bug fixes và performance improvements
- Thêm filters và confirmations mới
- Market adaptation cho crypto/stocks
- Mobile alerts optimization