# RT-1
<!DOCTYPE html>

<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Rooster Scores">
    <link rel="apple-touch-icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'><rect fill='%232563eb' width='100' height='100'/><text y='70' x='50' text-anchor='middle' font-size='60' fill='white'>🐓</text></svg>">
    <link rel="manifest" href="data:application/json,{%22name%22:%22Rooster%20Score%20Tracker%22,%22short_name%22:%22Rooster%22,%22display%22:%22standalone%22,%22start_url%22:%22.%22,%22theme_color%22:%22%232563eb%22,%22background_color%22:%22%23ffffff%22,%22icons%22:[{%22src%22:%22data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 100 100'%3E%3Crect fill='%232563eb' width='100' height='100'/%3E%3Ctext y='70' x='50' text-anchor='middle' font-size='60' fill='white'%3E🐓%3C/text%3E%3C/svg%3E%22,%22sizes%22:%22512x512%22,%22type%22:%22image/svg+xml%22}]}">
    <title>Rooster Score Tracker</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Arial, sans-serif;
            background: #f9fafb;
            padding: 16px;
            min-height: 100vh;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
        }
        .header {
            background: white;
            border-radius: 12px;
            padding: 24px;
            margin-bottom: 24px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }
        h1 {
            font-size: 28px;
            color: #1f2937;
            margin-bottom: 20px;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        .add-barn {
            display: flex;
            gap: 8px;
        }
        .add-barn input {
            flex: 1;
            padding: 12px 16px;
            border: 1px solid #d1d5db;
            border-radius: 8px;
            font-size: 16px;
        }
        .add-barn input:focus {
            outline: none;
            border-color: #2563eb;
            box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
        }
        .btn {
            padding: 12px 24px;
            border: none;
            border-radius: 8px;
            font-size: 16px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.2s;
        }
        .btn-primary {
            background: #2563eb;
            color: white;
        }
        .btn-primary:active {
            background: #1d4ed8;
            transform: scale(0.98);
        }
        .barn-card {
            background: white;
            border-radius: 12px;
            padding: 24px;
            margin-bottom: 24px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }
        .barn-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }
        .barn-header h2 {
            font-size: 24px;
            color: #1f2937;
        }
        .btn-delete {
            background: #fee;
            color: #dc2626;
            padding: 8px 16px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
        }
        .btn-delete:active {
            background: #fcc;
        }
        .score-buttons {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 12px;
            margin-bottom: 24px;
        }
        .score-btn {
            background: linear-gradient(135deg, #2563eb 0%, #1d4ed8 100%);
            color: white;
            border: none;
            border-radius: 12px;
            padding: 24px;
            cursor: pointer;
            box-shadow: 0 4px 6px rgba(37, 99, 235, 0.2);
            transition: all 0.2s;
        }
        .score-btn:active {
            transform: scale(0.95);
            box-shadow: 0 2px 4px rgba(37, 99, 235, 0.3);
        }
        .score-btn-label {
            font-size: 36px;
            font-weight: bold;
            margin-bottom: 8px;
        }
        .score-btn-value {
            font-size: 12px;
            opacity: 0.9;
            margin-bottom: 8px;
        }
        .score-btn-count {
            font-size: 24px;
            font-weight: 600;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            border: 2px solid #d1d5db;
        }
        td {
            border: 1px solid #d1d5db;
            padding: 12px;
            text-align: center;
        }
        .row-sums {
            background: #dbeafe;
            font-weight: 600;
        }
        .row-labels {
            background: #f3f4f6;
            font-weight: bold;
            font-size: 18px;
        }
        .row-percentages {
            background: #fae8ff;
        }
        .cell-total {
            background: #fef3c7;
            font-weight: bold;
        }
        .cell-avg {
            background: #d1fae5;
            font-weight: bold;
        }
        .cell-empty {
            background: #f9fafb;
        }
        .empty-state {
            background: white;
            border-radius: 12px;
            padding: 64px 24px;
            text-align: center;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }
        .empty-state p {
            color: #6b7280;
            font-size: 18px;
        }
        @media (max-width: 640px) {
            .score-buttons {
                grid-template-columns: repeat(2, 1fr);
            }
            table {
                font-size: 14px;
            }
            td {
                padding: 8px 4px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🐓 Rooster Score Tracker</h1>
            <div class="add-barn">
                <input type="text" id="barnNameInput" placeholder="Enter barn name..." />
                <button class="btn btn-primary" onclick="addBarn()">Add Barn</button>
            </div>
        </div>
        <div id="barnsContainer"></div>
    </div>

```
<script>
    const SCORE_VALUES = {
        '-': 3.0,
        '3': 3.0,
        '+': 3.5,
        '4': 4.0
    };

    let barns = [];

    // Load barns from localStorage
    function loadBarns() {
        const stored = localStorage.getItem('roosterBarns');
        if (stored) {
            barns = JSON.parse(stored);
            barns.sort((a, b) => b.timestamp - a.timestamp);
        }
        renderBarns();
    }

    // Save barns to localStorage
    function saveBarns() {
        localStorage.setItem('roosterBarns', JSON.stringify(barns));
    }

    // Add a new barn
    function addBarn() {
        const input = document.getElementById('barnNameInput');
        const name = input.value.trim();
        
        if (!name) return;
        
        const newBarn = {
            id: Date.now().toString(),
            name: name,
            scores: { '-': 0, '3': 0, '+': 0, '4': 0 },
            timestamp: Date.now()
        };
        
        barns.unshift(newBarn);
        saveBarns();
        input.value = '';
        renderBarns();
    }

    // Update score for a barn
    function updateScore(barnId, scoreType) {
        const barn = barns.find(b => b.id === barnId);
        if (barn) {
            barn.scores[scoreType]++;
            barn.timestamp = Date.now();
            barns.sort((a, b) => b.timestamp - a.timestamp);
            saveBarns();
            renderBarns();
        }
    }

    // Delete a barn
    function deleteBarn(barnId) {
        if (confirm('Are you sure you want to delete this barn?')) {
            barns = barns.filter(b => b.id !== barnId);
            saveBarns();
            renderBarns();
        }
    }

    // Calculate statistics
    function calculateStats(scores) {
        const counts = scores;
        const sums = {
            '-': counts['-'] * SCORE_VALUES['-'],
            '3': counts['3'] * SCORE_VALUES['3'],
            '+': counts['+'] * SCORE_VALUES['+'],
            '4': counts['4'] * SCORE_VALUES['4']
        };
        
        const totalRoosters = Object.values(counts).reduce((a, b) => a + b, 0);
        const totalScore = Object.values(sums).reduce((a, b) => a + b, 0);
        const average = totalRoosters > 0 ? (totalScore / totalRoosters).toFixed(2) : '0.00';
        
        const percentages = {
            '-': totalRoosters > 0 ? ((counts['-'] / totalRoosters) * 100).toFixed(2) : '0.00',
            '3': totalRoosters > 0 ? ((counts['3'] / totalRoosters) * 100).toFixed(2) : '0.00',
            '+': totalRoosters > 0 ? ((counts['+'] / totalRoosters) * 100).toFixed(2) : '0.00',
            '4': totalRoosters > 0 ? ((counts['4'] / totalRoosters) * 100).toFixed(2) : '0.00'
        };
        
        return { counts, sums, totalRoosters, totalScore, average, percentages };
    }

    // Render all barns
    function renderBarns() {
        const container = document.getElementById('barnsContainer');
        
        if (barns.length === 0) {
            container.innerHTML = `
                <div class="empty-state">
                    <p>No barns yet. Add your first barn to get started!</p>
                </div>
            `;
            return;
        }
        
        container.innerHTML = barns.map(barn => {
            const stats = calculateStats(barn.scores);
            
            return `
                <div class="barn-card">
                    <div class="barn-header">
                        <h2>${barn.name}</h2>
                        <button class="btn-delete" onclick="deleteBarn('${barn.id}')">Delete</button>
                    </div>
                    
                    <div class="score-buttons">
                        ${Object.keys(SCORE_VALUES).map(scoreType => `
                            <button class="score-btn" onclick="updateScore('${barn.id}', '${scoreType}')">
                                <div class="score-btn-label">${scoreType}</div>
                                <div class="score-btn-value">Score: ${SCORE_VALUES[scoreType]}</div>
                                <div class="score-btn-count">${barn.scores[scoreType]}</div>
                            </button>
                        `).join('')}
                    </div>
                    
                    <table>
                        <tr class="row-sums">
                            <td>${stats.sums['-'].toFixed(1)}</td>
                            <td>${stats.sums['3'].toFixed(1)}</td>
                            <td>${stats.sums['+'].toFixed(1)}</td>
                            <td>${stats.sums['4'].toFixed(1)}</td>
                            <td class="cell-total">${stats.totalScore.toFixed(1)}</td>
                            <td class="cell-avg">${stats.average}</td>
                        </tr>
                        <tr class="row-labels">
                            <td>-</td>
                            <td>3</td>
                            <td>+</td>
                            <td>4</td>
                            <td class="cell-total">Total</td>
                            <td class="cell-avg">Avg</td>
                        </tr>
                        <tr>
                            <td>${stats.counts['-']}</td>
                            <td>${stats.counts['3']}</td>
                            <td>${stats.counts['+']}</td>
                            <td>${stats.counts['4']}</td>
                            <td class="cell-total">${stats.totalRoosters}</td>
                            <td class="cell-empty"></td>
                        </tr>
                        <tr class="row-percentages">
                            <td>${stats.percentages['-']}%</td>
                            <td>${stats.percentages['3']}%</td>
                            <td>${stats.percentages['+']}%</td>
                            <td>${stats.percentages['4']}%</td>
                            <td class="cell-total">100%</td>
                            <td class="cell-empty"></td>
                        </tr>
                    </table>
                </div>
            `;
        }).join('');
    }

    // Allow Enter key to add barn
    document.getElementById('barnNameInput').addEventListener('keypress', function(e) {
        if (e.key === 'Enter') addBarn();
    });

    // Register service worker for offline capability
    if ('serviceWorker' in navigator) {
        window.addEventListener('load', () => {
            const swCode = `
                self.addEventListener('install', (e) => {
                    self.skipWaiting();
                });
                self.addEventListener('fetch', (e) => {
                    e.respondWith(
                        caches.match(e.request).then((response) => {
                            return response || fetch(e.request);
                        })
                    );
                });
            `;
            const blob = new Blob([swCode], { type: 'application/javascript' });
            const swUrl = URL.createObjectURL(blob);
            navigator.serviceWorker.register(swUrl);
        });
    }

    // Load barns on page load
    loadBarns();
</script>
```

</body>
</html>
