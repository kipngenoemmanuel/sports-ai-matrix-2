<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>AI Sports Forecaster</title>
    <!-- Add Apple and Android Mobile App Capable Tags -->
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <link rel="apple-touch-icon" href="https://flaticon.com">
    
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; background-color: #0b0f19; margin: 0; padding: 20px; color: #e2e8f0; -webkit-tap-highlight-color: transparent; }
        .app-card { background: #111827; padding: 25px; border-radius: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); border: 1px solid #1f2937; margin-top: 10px; }
        h1 { font-size: 22px; color: #38bdf8; text-align: center; margin-top: 0; font-weight: 800; }
        .status-msg { text-align: center; color: #94a3b8; font-size: 14px; margin-bottom: 25px; }
        
        /* Native Mobile Camera Button Styling */
        .camera-btn { display: flex; align-items: center; justify-content: center; background: linear-gradient(135deg, #0284c7, #0369a1); color: white; padding: 18px; border-radius: 14px; font-size: 16px; font-weight: 700; cursor: pointer; text-align: center; margin-bottom: 20px; box-shadow: 0 4px 15px rgba(2, 132, 199, 0.4); border: none; width: 100%; box-sizing: border-box; }
        .camera-btn:active { transform: scale(0.98); background: #0369a1; }
        input[type="file"] { display: none; }
        
        #result-box { margin-top: 25px; display: none; }
        .card { background: rgba(0,0,0,0.4); padding: 15px; border-radius: 12px; margin-top: 12px; border: 1px solid rgba(255,255,255,0.05); }
        .card-title { font-size: 12px; font-weight: 700; color: #38bdf8; text-transform: uppercase; letter-spacing: 0.8px; margin-bottom: 8px; }
        .data-row { display: flex; justify-content: space-between; padding: 6px 0; font-size: 15px; }
        .label { color: #94a3b8; }
        .val { font-family: monospace; font-weight: 700; color: #f1f5f9; }
        .win { color: #4ade80; }
        .loader { text-align: center; padding: 20px; display: none; color: #38bdf8; font-weight: 600; }
    </style>
</head>
<body>

    <div class="app-card">
        <h1>⚽ Universal Match AI</h1>
        <div class="status-msg">Snap or upload a fixture screenshot to analyze markets</div>
        
        <!-- Native Camera Input Activator -->
        <button class="camera-btn" onclick="document.getElementById('mobile-camera').click()">
            📸 TAKE PHOTO / UPLOAD
        </button>
        <!-- The capture="camera" attribute triggers the phone's native camera app instantly on mobile -->
        <input type="file" id="mobile-camera" accept="image/*" capture="camera" onchange="runMobileAnalysis(this)">

        <div id="app-loader" class="loader">Parsing Odds & Processing Matrix...</div>

        <div id="result-box"></div>
    </div>

    <script>
        // BACKEND ENDPOINT SPECIFICATION
        // Replace this with your actual Render/Railway live cloud app URL
        const BACKEND_API_URL = "https://YOUR_RENDER_URL_://onrender.com";

        async function runMobileAnalysis(input) {
            if (input.files.length === 0) return;
            
            const loader = document.getElementById('app-loader');
            const resultBox = document.getElementById('result-box');
            
            loader.style.display = "block";
            resultBox.style.display = "none";

            const formData = new FormData();
            formData.append('file', input.files[0]);

            try {
                const response = await fetch(BACKEND_API_URL, { method: 'POST', body: formData });
                const data = await response.json();

                if (!response.ok) {
                    resultBox.innerHTML = `<div class="card" style="border-color:#dc2626; color:#fca5a5;"><b>Error:</b> ${data.error}</div>`;
                } else {
                    resultBox.innerHTML = `
                        <div class="card" style="border-color: #059669;">
                            <div class="card-title" style="color:#4ade80;">Detected Odds</div>
                            <div class="data-row"><span class="label">Home / Draw / Away:</span><span class="val">[${data.parsed_odds.home} | ${data.parsed_odds.draw} | ${data.parsed_odds.away}]</span></div>
                        </div>
                        <div class="card">
                            <div class="card-title">Match Outcome (1X2)</div>
                            <div class="data-row"><span class="label">🏠 Home Win:</span><span class="val">${data.results.1x2.home_win}%</span></div>
                            <div class="data-row"><span class="label">🤝 Draw Margin:</span><span class="val">${data.results.1x2.draw}%</span></div>
                            <div class="data-row"><span class="label">✈️ Away Win:</span><span class="val">${data.results.1x2.away_win}%</span></div>
                        </div>
                        <div class="card">
                            <div class="card-title">Totals (2.5)</div>
                            <div class="data-row"><span class="label">📈 Over 2.5:</span><span class="val win">${data.results.totals.over_25}%</span></div>
                            <div class="data-row"><span class="label">📉 Under 2.5:</span><span class="val">${data.results.totals.under_25}%</span></div>
                        </div>
                        <div class="card">
                            <div class="card-title">Both Teams to Score</div>
                            <div class="data-row"><span class="label">⚽ BTTS - YES:</span><span class="val">${data.results.btts.yes}%</span></div>
                            <div class="data-row"><span class="label">🚫 BTTS - NO:</span><span class="val">${data.results.btts.no}%</span></div>
                        </div>
                    `;
                }
            } catch (err) {
                resultBox.innerHTML = `<div class="card" style="border-color:#dc2626; color:#fca5a5;"><b>Connection Failure:</b> Cannot reach cloud prediction script server.</div>`;
            } finally {
                loader.style.display = "none";
                resultBox.style.display = "block";
            }
        }
    </script>
</body>
</html>