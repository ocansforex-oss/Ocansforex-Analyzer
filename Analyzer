<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ICT Analyzer - Simple</title>
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
    </style>
</head>
<body class="bg-slate-950 text-slate-200 min-h-screen">
    
    <!-- Header -->
    <header class="bg-slate-900 border-b border-slate-800 p-4">
        <div class="flex flex-col md:flex-row gap-4 items-center justify-between max-w-6xl mx-auto">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 bg-blue-600 rounded-lg flex items-center justify-center font-bold">ICT</div>
                <div>
                    <h1 class="font-bold text-lg">Market Analyzer</h1>
                    <p class="text-xs text-slate-400">Smart Money Concepts</p>
                </div>
            </div>
            
            <div class="flex gap-2 w-full md:w-auto">
                <select id="pairSelect" class="bg-slate-800 border border-slate-700 rounded-lg px-3 py-2 text-sm flex-1 md:w-40">
                    <option value="EURUSD">EUR/USD</option>
                    <option value="GBPUSD">GBP/USD</option>
                    <option value="USDJPY">USD/JPY</option>
                    <option value="XAUUSD">Gold</option>
                    <option value="BTCUSD">Bitcoin</option>
                    <option value="US30">US30</option>
                </select>
                
                <select id="timeframeSelect" class="bg-slate-800 border border-slate-700 rounded-lg px-3 py-2 text-sm w-24">
                    <option value="15m">15M</option>
                    <option value="1h">1H</option>
                    <option value="4h">4H</option>
                    <option value="1d">1D</option>
                </select>
                
                <button onclick="analyze()" class="bg-blue-600 hover:bg-blue-700 px-4 py-2 rounded-lg text-sm font-medium transition-colors">
                    Analyze
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content -->
    <main class="max-w-6xl mx-auto p-4 grid grid-cols-1 lg:grid-cols-3 gap-4">
        
        <!-- Chart Area -->
        <div class="lg:col-span-2 space-y-4">
            <div id="chart" class="h-[400px] bg-slate-900 rounded-xl border border-slate-800"></div>
            
            <!-- Quick Stats -->
            <div class="grid grid-cols-3 gap-3">
                <div class="glass rounded-lg p-3 text-center">
                    <div class="text-xs text-slate-500 mb-1">Trend</div>
                    <div id="trendDisplay" class="font-bold text-lg text-slate-400">--</div>
                </div>
                <div class="glass rounded-lg p-3 text-center">
                    <div class="text-xs text-slate-500 mb-1">Structure</div>
                    <div id="structureDisplay" class="font-bold text-lg text-slate-400">--</div>
                </div>
                <div class="glass rounded-lg p-3 text-center">
                    <div class="text-xs text-slate-500 mb-1">Session</div>
                    <div id="sessionDisplay" class="font-bold text-lg text-blue-400">--</div>
                </div>
            </div>
        </div>

        <!-- Signal Panel -->
        <div class="space-y-4">
            
            <!-- Main Signal -->
            <div id="signalCard" class="glass rounded-xl p-6 text-center">
                <div class="text-sm text-slate-400 mb-2">AI Recommendation</div>
                <div id="signalText" class="text-4xl font-black text-slate-500 mb-2">WAIT</div>
                <div class="text-xs text-slate-500">Upload chart or click Analyze</div>
            </div>

            <!-- Pattern List -->
            <div class="glass rounded-xl p-4">
                <h3 class="font-bold text-sm mb-3 flex items-center gap-2">
                    <span class="w-2 h-2 bg-purple-500 rounded-full"></span>
                    Detected Patterns
                </h3>
                <div id="patternList" class="space-y-2 text-sm">
                    <div class="text-slate-500 text-center py-4">No patterns detected</div>
                </div>
            </div>

            <!-- Trade Setup -->
            <div class="glass rounded-xl p-4">
                <h3 class="font-bold text-sm mb-3 text-emerald-400">Trade Levels</h3>
                <div class="space-y-2 text-sm">
                    <div class="flex justify-between">
                        <span class="text-slate-400">Entry</span>
                        <span id="entryLevel" class="mono font-bold">--</span>
                    </div>
                    <div class="flex justify-between">
                        <span class="text-slate-400">Stop Loss</span>
                        <span id="slLevel" class="mono font-bold text-red-400">--</span>
                    </div>
                    <div class="flex justify-between">
                        <span class="text-slate-400">Take Profit</span>
                        <span id="tpLevel" class="mono font-bold text-emerald-400">--</span>
                    </div>
                    <div class="flex justify-between border-t border-slate-700 pt-2 mt-2">
                        <span class="text-slate-400">Risk:Reward</span>
                        <span id="rrLevel" class="font-bold text-blue-400">--</span>
                    </div>
                </div>
            </div>

            <!-- Risk Calculator -->
            <div class="glass rounded-xl p-4">
                <h3 class="font-bold text-sm mb-3">Position Size</h3>
                <div class="space-y-2">
                    <input type="number" id="balance" placeholder="Account ($)" value="10000" class="w-full bg-slate-800 border border-slate-700 rounded px-3 py-2 text-sm">
                    <input type="number" id="riskPercent" placeholder="Risk %" value="1" class="w-full bg-slate-800 border border-slate-700 rounded px-3 py-2 text-sm">
                    <div class="flex justify-between items-center pt-2">
                        <span class="text-xs text-slate-400">Lots:</span>
                        <span id="lotSize" class="font-bold text-lg mono">0.00</span>
                    </div>
                </div>
            </div>
        </div>
    </main>

    <script>
        let chart, candleSeries;
        let currentData = [];

        // Initialize
        document.addEventListener('DOMContentLoaded', () => {
            initChart();
            updateSession();
            setInterval(updateSession, 60000); // Update every minute
            
            // Auto-calculate lots
            ['balance', 'riskPercent'].forEach(id => {
                document.getElementById(id).addEventListener('input', calculateLots);
            });
        });

        function initChart() {
            chart = LightweightCharts.createChart(document.getElementById('chart'), {
                layout: { background: { color: '#0f172a' }, textColor: '#94a3b8' },
                grid: { vertLines: { color: '#1e293b' }, horzLines: { color: '#1e293b' } },
                rightPriceScale: { borderColor: '#1e293b' },
                timeScale: { borderColor: '#1e293b', timeVisible: true }
            });
            
            candleSeries = chart.addCandlestickSeries({
                upColor: '#10b981', downColor: '#ef4444',
                borderUpColor: '#10b981', borderDownColor: '#ef4444',
                wickUpColor: '#10b981', wickDownColor: '#ef4444'
            });
            
            generateData();
        }

        function generateData() {
            const pair = document.getElementById('pairSelect').value;
            const basePrices = { EURUSD: 1.085, GBPUSD: 1.265, USDJPY: 149.2, XAUUSD: 2035, BTCUSD: 67500, US30: 38500 };
            let price = basePrices[pair] || 100;
            const data = [];
            const now = new Date();
            
            for (let i = 100; i > 0; i--) {
                const time = new Date(now - i * 15 * 60000);
                const change = (Math.random() - 0.5) * price * 0.002;
                const open = price;
                const close = price + change;
                const high = Math.max(open, close) + Math.random() * Math.abs(change);
                const low = Math.min(open, close) - Math.random() * Math.abs(change);
                
                data.push({
                    time: time.getTime() / 1000,
                    open, high, low, close
                });
                price = close;
            }
            
            currentData = data;
            candleSeries.setData(data);
            chart.timeScale().fitContent();
        }

        function analyze() {
            generateData();
            
            // Simulate analysis
            const trends = ['BULLISH', 'BEARISH', 'NEUTRAL'];
            const trend = trends[Math.floor(Math.random() * trends.length)];
            const confidence = Math.floor(70 + Math.random() * 25);
            
            // Update displays
            document.getElementById('trendDisplay').textContent = trend;
            document.getElementById('trendDisplay').className = 'font-bold text-lg ' + 
                (trend === 'BULLISH' ? 'text-emerald-400' : trend === 'BEARISH' ? 'text-red-400' : 'text-yellow-400');
            
            document.getElementById('structureDisplay').textContent = trend === 'NEUTRAL' ? 'RANGE' : 'BOS';
            
            // Signal card
            const card = document.getElementById('signalCard');
            const signalText = document.getElementById('signalText');
            
            card.className = 'glass rounded-xl p-6 text-center ';
            if (trend === 'BULLISH') {
                card.classList.add('signal-buy');
                signalText.textContent = 'BUY';
            } else if (trend === 'BEARISH') {
                card.classList.add('signal-sell');
                signalText.textContent = 'SELL';
            } else {
                card.classList.add('signal-neutral');
                signalText.textContent = 'WAIT';
            }
            
            // Patterns
            const patterns = trend === 'BULLISH' ? [
                { name: 'Bullish Order Block', type: 'ob' },
                { name: 'Fair Value Gap', type: 'fvg' },
                { name: 'Liquidity Sweep', type: 'liq' }
            ] : trend === 'BEARISH' ? [
                { name: 'Bearish Order Block', type: 'ob' },
                { name: 'Break of Structure', type: 'bos' }
            ] : [{ name: 'No clear pattern', type: 'none' }];
            
            document.getElementById('patternList').innerHTML = patterns.map(p => `
                <div class="flex items-center gap-2 p-2 bg-slate-800/50 rounded">
                    <span class="w-2 h-2 rounded-full ${p.type === 'ob' ? 'bg-purple-500' : p.type === 'fvg' ? 'bg-yellow-500' : p.type === 'liq' ? 'bg-red-500' : 'bg-blue-500'}"></span>
                    <span>${p.name}</span>
                </div>
            `).join('');
            
            // Trade levels
            const lastClose = currentData[currentData.length - 1].close;
            const pip = pair === 'XAUUSD' ? 0.1 : pair === 'BTCUSD' ? 100 : pair === 'US30' ? 10 : 0.0001;
            
            if (trend !== 'NEUTRAL') {
                const direction = trend === 'BULLISH' ? 1 : -1;
                document.getElementById('entryLevel').textContent = lastClose.toFixed(getDecimals());
                document.getElementById('slLevel').textContent = (lastClose - (20 * pip * direction)).toFixed(getDecimals());
                document.getElementById('tpLevel').textContent = (lastClose + (50 * pip * direction)).toFixed(getDecimals());
                document.getElementById('rrLevel').textContent = '1:2.5';
                
                calculateLots();
            } else {
                ['entryLevel', 'slLevel', 'tpLevel', 'rrLevel'].forEach(id => {
                    document.getElementById(id).textContent = '--';
                });
            }
        }

        function calculateLots() {
            const balance = parseFloat(document.getElementById('balance').value) || 0;
            const risk = parseFloat(document.getElementById('riskPercent').value) || 0;
            const riskAmount = balance * (risk / 100);
            
            // Assume 20 pip stop, $10 per pip per lot
            const lots = riskAmount / (20 * 10);
            document.getElementById('lotSize').textContent = lots.toFixed(2);
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
                session = 'London/NY';
                color = 'text-purple-400 font-bold';
            }
            
            document.getElementById('sessionDisplay').textContent = session;
            document.getElementById('sessionDisplay').className = 'font-bold text-lg ' + color;
        }

        function getDecimals() {
            const pair = document.getElementById('pairSelect').value;
            if (pair === 'XAUUSD') return 2;
            if (pair === 'BTCUSD') return 2;
            if (pair === 'US30') return 1;
            return 5;
        }

        // Event listeners
        document.getElementById('pairSelect').addEventListener('change', generateData);
        document.getElementById('timeframeSelect').addEventListener('change', generateData);
    </script>
</body>
</html>
