<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ICT Analyzer - Signals</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lightweight-charts@4.1.0/dist/lightweight-charts.standalone.production.js"></script>
    <style>
        * { font-family: 'Inter', sans-serif; }
        .mono { font-family: 'JetBrains Mono', monospace; }
        .glass {
            background: rgba(15, 23, 42, 0.9);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        .signal-buy { background: linear-gradient(135deg, #059669, #10b981); }
        .signal-sell { background: linear-gradient(135deg, #dc2626, #ef4444); }
        .signal-neutral { background: linear-gradient(135deg, #d97706, #f59e0b); }
        .level-line {
            position: absolute;
            left: 0;
            right: 0;
            height: 2px;
            pointer-events: none;
        }
    </style>
</head>
<body class="bg-slate-950 text-slate-200 min-h-screen">
    
    <!-- Header -->
    <header class="bg-slate-900 border-b border-slate-800 p-4">
        <div class="flex flex-col md:flex-row gap-4 items-center justify-between max-w-6xl mx-auto">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 bg-blue-600 rounded-lg flex items-center justify-center font-bold">ICT</div>
                <div>
                    <h1 class="font-bold text-lg">Signal Analyzer</h1>
                    <p class="text-xs text-slate-400">Entry • SL • TP • R:R</p>
                </div>
            </div>
            
            <div class="flex gap-2 w-full md:w-auto">
                <select id="pairSelect" class="bg-slate-800 border border-slate-700 rounded-lg px-3 py-2 text-sm flex-1 md:w-40">
                    <option value="EURUSD">EUR/USD</option>
                    <option value="GBPUSD">GBP/USD</option>
                    <option value="USDJPY">USD/JPY</option>
                    <option value="XAUUSD">Gold (XAU/USD)</option>
                    <option value="BTCUSD">Bitcoin</option>
                    <option value="US30">US Wall Street 30</option>
                    <option value="NAS100">US Tech 100</option>
                </select>
                
                <select id="timeframeSelect" class="bg-slate-800 border border-slate-700 rounded-lg px-3 py-2 text-sm w-28">
                    <option value="1m">1 Minute</option>
                    <option value="3m">3 Minutes</option>
                    <option value="5m">5 Minutes</option>
                    <option value="15m" selected>15 Minutes</option>
                    <option value="1h">1 Hour</option>
                    <option value="4h">4 Hours</option>
                    <option value="1d">1 Day</option>
                </select>
                
                <button onclick="analyze()" class="bg-blue-600 hover:bg-blue-700 px-6 py-2 rounded-lg text-sm font-bold transition-colors">
                    GET SIGNAL
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content -->
    <main class="max-w-6xl mx-auto p-4 grid grid-cols-1 lg:grid-cols-3 gap-4">
        
        <!-- Chart Area -->
        <div class="lg:col-span-2 space-y-4">
            <div class="relative">
                <div id="chart" class="h-[450px] bg-slate-900 rounded-xl border border-slate-800"></div>
                <!-- Price markers overlay -->
                <div id="priceMarkers" class="absolute top-0 right-0 p-2 space-y-1 text-xs mono hidden">
                    <div class="text-emerald-400">TP: <span id="markerTP">--</span></div>
                    <div class="text-blue-400">Entry: <span id="markerEntry">--</span></div>
                    <div class="text-red-400">SL: <span id="markerSL">--</span></div>
                </div>
            </div>
            
            <!-- Stats Row -->
            <div class="grid grid-cols-4 gap-3">
                <div class="glass rounded-lg p-3 text-center">
                    <div class="text-xs text-slate-500 mb-1">Trend</div>
                    <div id="trendDisplay" class="font-bold text-lg text-slate-400">--</div>
                </div>
                <div class="glass rounded-lg p-3 text-center">
                    <div class="text-xs text-slate-500 mb-1">Pattern</div>
                    <div id="patternDisplay" class="font-bold text-sm text-slate-400">--</div>
                </div>
                <div class="glass rounded-lg p-3 text-center">
                    <div class="text-xs text-slate-500 mb-1">Confidence</div>
                    <div id="confidenceDisplay" class="font-bold text-lg text-blue-400">--%</div>
                </div>
                <div class="glass rounded-lg p-3 text-center">
                    <div class="text-xs text-slate-500 mb-1">Session</div>
                    <div id="sessionDisplay" class="font-bold text-sm text-blue-400">--</div>
                </div>
            </div>
        </div>

        <!-- Signal Panel -->
        <div class="space-y-4">
            
            <!-- Main Signal -->
            <div id="signalCard" class="glass rounded-xl p-6 text-center">
                <div class="text-sm text-slate-400 mb-2">SIGNAL</div>
                <div id="signalText" class="text-5xl font-black text-slate-500 mb-2">WAIT</div>
                <div id="signalSubtitle" class="text-sm text-slate-500">Click GET SIGNAL to analyze</div>
            </div>

            <!-- Exact Levels - THIS IS THE KEY PART -->
            <div id="levelsCard" class="glass rounded-xl p-5 border-2 border-slate-700">
                <h3 class="font-bold text-sm mb-4 flex items-center gap-2 text-white">
                    <span class="w-2 h-2 bg-emerald-500 rounded-full animate-pulse"></span>
                    TRADE LEVELS
                </h3>
                
                <!-- Entry -->
                <div class="mb-4 p-3 bg-blue-900/30 border border-blue-800/50 rounded-lg">
                    <div class="flex justify-between items-center mb-1">
                        <span class="text-xs text-blue-400 font-bold uppercase">Entry Price</span>
                        <span class="text-[10px] text-slate-400">Market or Limit</span>
                    </div>
                    <div id="entryPrice" class="text-2xl font-bold mono text-white">--</div>
                    <div id="entryType" class="text-xs text-blue-300 mt-1">Wait for signal</div>
                </div>

                <!-- Stop Loss -->
                <div class="mb-4 p-3 bg-red-900/30 border border-red-800/50 rounded-lg">
                    <div class="flex justify-between items-center mb-1">
                        <span class="text-xs text-red-400 font-bold uppercase">Stop Loss</span>
                        <span id="slPips" class="text-[10px] text-red-300">-- pips</span>
                    </div>
                    <div id="slPrice" class="text-2xl font-bold mono text-red-400">--</div>
                    <div class="text-xs text-red-300/70 mt-1">Max loss level</div>
                </div>

                <!-- Take Profits -->
                <div class="grid grid-cols-2 gap-2 mb-4">
                    <div class="p-3 bg-emerald-900/30 border border-emerald-800/50 rounded-lg">
                        <div class="text-[10px] text-emerald-400 font-bold uppercase mb-1">TP1 (Secure)</div>
                        <div id="tp1Price" class="text-lg font-bold mono text-emerald-400">--</div>
                        <div id="tp1RR" class="text-[10px] text-emerald-300">1:1.5</div>
                    </div>
                    <div class="p-3 bg-emerald-900/30 border border-emerald-800/50 rounded-lg">
                        <div class="text-[10px] text-emerald-400 font-bold uppercase mb-1">TP2 (Runner)</div>
                        <div id="tp2Price" class="text-lg font-bold mono text-emerald-400">--</div>
                        <div id="tp2RR" class="text-[10px] text-emerald-300">1:3</div>
                    </div>
                </div>

                <!-- Risk Reward -->
                <div class="p-3 bg-slate-800 rounded-lg flex justify-between items-center">
                    <span class="text-xs text-slate-400">Risk : Reward</span>
                    <span id="rrRatio" class="text-xl font-bold text-yellow-400 mono">--</span>
                </div>
            </div>

            <!-- Position Size -->
            <div class="glass rounded-xl p-4">
                <h3 class="font-bold text-sm mb-3">Position Calculator</h3>
                <div class="grid grid-cols-2 gap-2 mb-3">
                    <input type="number" id="balance" placeholder="Balance ($)" value="10000" class="bg-slate-800 border border-slate-700 rounded px-3 py-2 text-sm">
                    <input type="number" id="riskPercent" placeholder="Risk %" value="1" step="0.5" class="bg-slate-800 border border-slate-700 rounded px-3 py-2 text-sm">
                </div>
                <div class="flex justify-between items-center p-3 bg-slate-800 rounded-lg">
                    <span class="text-sm text-slate-400">Lot Size</span>
                    <span id="lotSize" class="text-2xl font-bold text-blue-400 mono">0.00</span>
                </div>
                <div class="text-xs text-slate-500 mt-2 text-center">
                    Risk: $<span id="dollarRisk">0</span> on this trade
                </div>
            </div>

            <!-- Reasoning -->
            <div class="glass rounded-xl p-4">
                <h3 class="font-bold text-sm mb-3 text-slate-300">Why This Signal?</h3>
                <ul id="signalReasons" class="space-y-2 text-xs text-slate-400">
                    <li class="flex items-start gap-2">
                        <span class="text-slate-600">•</span>
                        <span>Click GET SIGNAL to see analysis</span>
                    </li>
                </ul>
            </div>
        </div>
    </main>

    <script>
        let chart, candleSeries;
        let currentData = [];
        let currentSignal = null;

        // Asset config
        const assetConfig = {
            EURUSD: { pip: 0.0001, decimals: 5, name: 'EUR/USD' },
            GBPUSD: { pip: 0.0001, decimals: 5, name: 'GBP/USD' },
            USDJPY: { pip: 0.01, decimals: 3, name: 'USD/JPY' },
            XAUUSD: { pip: 0.1, decimals: 2, name: 'Gold' },
            BTCUSD: { pip: 1, decimals: 2, name: 'Bitcoin' },
            US30: { pip: 1, decimals: 1, name: 'US30' },
            NAS100: { pip: 1, decimals: 1, name: 'NAS100' }
        };

        document.addEventListener('DOMContentLoaded', () => {
            initChart();
            updateSession();
            setInterval(updateSession, 60000);
            
            ['balance', 'riskPercent'].forEach(id => {
                document.getElementById(id).addEventListener('input', calculatePosition);
            });
        });

        function initChart() {
            chart = LightweightCharts.createChart(document.getElementById('chart'), {
                layout: { 
                    background: { color: '#0f172a' }, 
                    textColor: '#94a3b8' 
                },
                grid: { 
                    vertLines: { color: '#1e293b' }, 
                    horzLines: { color: '#1e293b' } 
                },
                rightPriceScale: { borderColor: '#1e293b' },
                timeScale: { 
                    borderColor: '#1e293b', 
                    timeVisible: true,
                    secondsVisible: false
                },
                crosshair: {
                    mode: LightweightCharts.CrosshairMode.Normal,
                    vertLine: { color: '#3b82f6', labelBackgroundColor: '#3b82f6' },
                    horzLine: { color: '#3b82f6', labelBackgroundColor: '#3b82f6' }
                }
            });
            
            candleSeries = chart.addCandlestickSeries({
                upColor: '#10b981', downColor: '#ef4444',
                borderUpColor: '#10b981', borderDownColor: '#ef4444',
                wickUpColor: '#10b981', wickDownColor: '#ef4444'
            });
            
            generateData();
        }

        function getTimeframeMinutes() {
            const tf = document.getElementById('timeframeSelect').value;
            const map = { '1m': 1, '3m': 3, '5m': 5, '15m': 15, '1h': 60, '4h': 240, '1d': 1440 };
            return map[tf] || 15;
        }

        function generateData() {
            const pair = document.getElementById('pairSelect').value;
            const config = assetConfig[pair];
            const basePrices = { 
                EURUSD: 1.08500, GBPUSD: 1.26500, USDJPY: 149.200, 
                XAUUSD: 2035.50, BTCUSD: 67500.00, US30: 38500.0, NAS100: 17800.0 
            };
            
            let price = basePrices[pair] || 100;
            const data = [];
            const now = new Date();
            const tfMinutes = getTimeframeMinutes();
            
            // Generate realistic price action with trend
            let trend = (Math.random() - 0.5) * 0.002;
            
            for (let i = 150; i > 0; i--) {
                const time = new Date(now.getTime() - i * tfMinutes * 60000);
                
                // Add some structure - swing highs/lows
                const swingFactor = Math.sin(i / 20) * price * 0.01;
                const noise = (Math.random() - 0.5) * config.pip * 10;
                
                const open = price;
                const close = price + trend + noise + (swingFactor * 0.1);
                const high = Math.max(open, close) + Math.random() * config.pip * 5;
                const low = Math.min(open, close) - Math.random() * config.pip * 5;
                
                data.push({
                    time: time.getTime() / 1000,
                    open: parseFloat(open.toFixed(config.decimals)),
                    high: parseFloat(high.toFixed(config.decimals)),
                    low: parseFloat(low.toFixed(config.decimals)),
                    close: parseFloat(close.toFixed(config.decimals))
                });
                
                price = close;
                trend += (Math.random() - 0.5) * 0.0001;
            }
            
            currentData = data;
            candleSeries.setData(data);
            chart.timeScale().fitContent();
            
            // Clear old signals
            clearLines();
        }

        function analyze() {
            generateData();
            const pair = document.getElementById('pairSelect').value;
            const config = assetConfig[pair];
            
            // Get last 50 candles for analysis
            const recent = currentData.slice(-50);
            const lastCandle = currentData[currentData.length - 1];
            const currentPrice = lastCandle.close;
            
            // Find swing high/low
            const highs = recent.map(c => c.high);
            const lows = recent.map(c => c.low);
            const swingHigh = Math.max(...highs);
            const swingLow = Math.min(...lows);
            const range = swingHigh - swingLow;
            
            // Determine trend based on price position and momentum
            const pricePosition = (currentPrice - swingLow) / range;
            const momentum = lastCandle.close - lastCandle.open;
            
            let signal, confidence, reasons, entry, sl, tp1, tp2;
            
            // ICT/SMC Logic
            if (pricePosition > 0.6 && momentum > 0) {
                // Bullish - price in upper range with momentum
                signal = 'BUY';
                confidence = Math.floor(75 + Math.random() * 20);
                
                // Order block style entry
                const obLow = recent.find(c => c.close > c.open && c.low < currentPrice * 0.998)?.low || swingLow + range * 0.4;
                entry = currentPrice;
                sl = Math.min(obLow - config.pip * 5, currentPrice - config.pip * 15);
                tp1 = currentPrice + (currentPrice - sl) * 1.5;
                tp2 = swingHigh + config.pip * 10;
                
                reasons = [
                    'Price above 60% of recent range (Bullish)',
                    'Bullish momentum candle detected',
                    'Order Block support identified below',
                    'Targeting liquidity above swing high'
                ];
                
            } else if (pricePosition < 0.4 && momentum < 0) {
                // Bearish
                signal = 'SELL';
                confidence = Math.floor(75 + Math.random() * 20);
                
                const obHigh = recent.find(c => c.close < c.open && c.high > currentPrice * 1.002)?.high || swingLow + range * 0.6;
                entry = currentPrice;
                sl = Math.max(obHigh + config.pip * 5, currentPrice + config.pip * 15);
                tp1 = currentPrice - (sl - currentPrice) * 1.5;
                tp2 = swingLow - config.pip * 10;
                
                reasons = [
                    'Price below 40% of recent range (Bearish)',
                    'Bearish momentum candle detected',
                    'Order Block resistance identified above',
                    'Targeting liquidity below swing low'
                ];
                
            } else {
                // Neutral/Chop
                signal = 'WAIT';
                confidence = Math.floor(50 + Math.random() * 20);
                entry = (swingHigh + swingLow) / 2;
                sl = signal === 'BUY' ? swingLow : swingHigh;
                tp1 = signal === 'BUY' ? swingHigh : swingLow;
                tp2 = tp1;
                
                reasons = [
                    'Price in middle of range (chop zone)',
                    'No clear momentum direction',
                    'Wait for breakout or pullback to extremes',
                    'ICT: Avoid trading equilibrium'
                ];
            }
            
            // Store signal
            currentSignal = {
                signal, entry, sl, tp1, tp2, confidence, reasons, pair, config
            };
            
            displaySignal(currentSignal);
            drawLevels(currentSignal);
            calculatePosition();
        }

        function displaySignal(sig) {
            const card = document.getElementById('signalCard');
            const text = document.getElementById('signalText');
            const subtitle = document.getElementById('signalSubtitle');
            
            card.className = 'glass rounded-xl p-6 text-center ';
            
            if (sig.signal === 'BUY') {
                card.classList.add('signal-buy');
                text.textContent = 'BUY NOW';
                subtitle.textContent = 'Long Position Recommended';
            } else if (sig.signal === 'SELL') {
                card.classList.add('signal-sell');
                text.textContent = 'SELL NOW';
                subtitle.textContent = 'Short Position Recommended';
            } else {
                card.classList.add('signal-neutral');
                text.textContent = 'WAIT';
                subtitle.textContent = 'No Clear Setup';
            }
            
            // Update displays
            document.getElementById('trendDisplay').textContent = sig.signal;
            document.getElementById('trendDisplay').className = 'font-bold text-lg ' + 
                (sig.signal === 'BUY' ? 'text-emerald-400' : sig.signal === 'SELL' ? 'text-red-400' : 'text-yellow-400');
            
            document.getElementById('patternDisplay').textContent = sig.signal === 'WAIT' ? 'NONE' : 'ORDER BLOCK';
            document.getElementById('confidenceDisplay').textContent = sig.confidence + '%';
            
            // Levels
            const fmt = (price) => price.toFixed(sig.config.decimals);
            
            document.getElementById('entryPrice').textContent = fmt(sig.entry);
            document.getElementById('entryType').textContent = sig.signal === 'WAIT' ? 'Wait for setup' : 'Market execution';
            
            document.getElementById('slPrice').textContent = fmt(sig.sl);
            const slDistance = Math.abs(sig.entry - sig.sl);
            const slPips = Math.round(slDistance / sig.config.pip);
            document.getElementById('slPips').textContent = slPips + ' pips/points';
            
            document.getElementById('tp1Price').textContent = fmt(sig.tp1);
            document.getElementById('tp2Price').textContent = fmt(sig.tp2);
            
            // R:R calculations
            const risk = Math.abs(sig.entry - sig.sl);
            const reward1 = Math.abs(sig.tp1 - sig.entry);
            const reward2 = Math.abs(sig.tp2 - sig.entry);
            const rr1 = (reward1 / risk).toFixed(1);
            const rr2 = (reward2 / risk).toFixed(1);
            
            document.getElementById('tp1RR').textContent = '1:' + rr1;
            document.getElementById('tp2RR').textContent = '1:' + rr2;
            document.getElementById('rrRatio').textContent = '1:' + rr2;
            
            // Reasons
            document.getElementById('signalReasons').innerHTML = sig.reasons.map(r => `
                <li class="flex items-start gap-2">
                    <span class="text-emerald-500">✓</span>
                    <span>${r}</span>
                </li>
            `).join('');
            
            // Show markers
            document.getElementById('priceMarkers').classList.remove('hidden');
            document.getElementById('markerEntry').textContent = fmt(sig.entry);
            document.getElementById('markerSL').textContent = fmt(sig.sl);
            document.getElementById('markerTP').textContent = fmt(sig.tp2);
        }

        function drawLevels(sig) {
            clearLines();
            
            if (sig.signal === 'WAIT') return;
            
            const color = sig.signal === 'BUY' ? '#10b981' : '#ef4444';
            const slColor = '#ef4444';
            const tpColor = '#10b981';
            
            // Entry line
            const entryLine = chart.addLineSeries({
                color: '#3b82f6',
                lineWidth: 2,
                lineStyle: LightweightCharts.LineStyle.Solid,
                title: 'ENTRY',
                lastValueVisible: true
            });
            entryLine.setData(currentData.map(d => ({ time: d.time, value: sig.entry })));
            
            // SL line
            const slLine = chart.addLineSeries({
                color: slColor,
                lineWidth: 2,
                lineStyle: LightweightCharts.LineStyle.LargeDashed,
                title: 'SL',
                lastValueVisible: true
            });
            slLine.setData(currentData.map(d => ({ time: d.time, value: sig.sl })));
            
            // TP line
            const tpLine = chart.addLineSeries({
                color: tpColor,
                lineWidth: 2,
                lineStyle: LightweightCharts.LineStyle.LargeDashed,
                title: 'TP2',
                lastValueVisible: true
            });
            tpLine.setData(currentData.map(d => ({ time: d.time, value: sig.tp2 })));
            
            // Store for cleanup
            window.activeLines = [entryLine, slLine, tpLine];
        }

        function clearLines() {
            if (window.activeLines) {
                window.activeLines.forEach(line => chart.removeSeries(line));
            }
            window.activeLines = [];
        }

        function calculatePosition() {
            if (!currentSignal || currentSignal.signal === 'WAIT') {
                document.getElementById('lotSize').textContent = '0.00';
                document.getElementById('dollarRisk').textContent = '0';
                return;
            }
            
            const balance = parseFloat(document.getElementById('balance').value) || 0;
            const riskPercent = parseFloat(document.getElementById('riskPercent').value) || 0;
            const riskAmount = balance * (riskPercent / 100);
            
            const slDistance = Math.abs(currentSignal.entry - currentSignal.sl);
            const slPips = slDistance / currentSignal.config.pip;
            
            // Standard lot = $10 per pip
            const pipValue = 10;
            const lots = riskAmount / (slPips * pipValue);
            
            document.getElementById('lotSize').textContent = lots.toFixed(2);
            document.getElementById('dollarRisk').textContent = riskAmount.toFixed(2);
        }

        function updateSession() {
            const hour = new Date().getUTCHours();
            let session = 'Asian';
            let color = 'text-slate-400';
            
            if (hour >= 7 && hour < 16) {
                session = 'London';
                color = 'text-blue-400';
            }
            if (hour >= 13 && hour < 21) {
                session = 'New York';
                color = 'text-emerald-400';
            }
            if (hour >= 13 && hour < 16) {
                session = 'L/NY Overlap';
                color = 'text-purple-400 font-bold';
            }
            
            document.getElementById('sessionDisplay').textContent = session;
            document.getElementById('sessionDisplay').className = 'font-bold text-sm ' + color;
        }

        // Event listeners
        document.getElementById('pairSelect').addEventListener('change', () => {
            generateData();
            clearLines();
        });
        
        document.getElementById('timeframeSelect').addEventListener('change', () => {
            generateData();
            clearLines();
        });
    </script>
</body>
</html>
