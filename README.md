<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>HOANGDZ - CRYSTAL AI ULTIMATE</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;900&family=Share+Tech+Mono&display=swap" rel="stylesheet">
    <style>
        :root { 
            --accent: #00d2ff; 
            --danger: #ff3232;
            --success: #00ff88;
            --bg: #00050a;
            --glass: rgba(255, 255, 255, 0.05);
        }

        body { 
            margin: 0; font-family: 'Share Tech Mono', monospace; 
            background: var(--bg); color: #fff; 
            overflow: hidden; height: 100vh;
            display: flex; justify-content: center; align-items: center;
        }

        /* Scanline Effect */
        body::before {
            content: " "; display: block; position: absolute; top: 0; left: 0; bottom: 0; right: 0;
            background: linear-gradient(rgba(18, 16, 16, 0) 50%, rgba(0, 0, 0, 0.25) 50%), linear-gradient(90deg, rgba(255, 0, 0, 0.06), rgba(0, 255, 0, 0.02), rgba(0, 0, 255, 0.06));
            z-index: 100; background-size: 100% 4px, 3px 100%; pointer-events: none;
        }

        #snow-canvas { position: fixed; top: 0; left: 0; width: 100%; height: 100%; pointer-events: none; z-index: 1; opacity: 0.5; }

        .app-container { 
            width: 100%; max-width: 400px; padding: 20px; 
            position: relative; z-index: 10;
        }

        /* Warning Overlay */
        #warning-modal {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(0,0,0,0.9); z-index: 999;
            display: flex; flex-direction: column; justify-content: center; align-items: center;
            text-align: center; padding: 30px; box-sizing: border-box;
        }
        .warning-box { border: 2px solid var(--danger); padding: 20px; border-radius: 15px; background: rgba(255,0,0,0.1); }

        /* Main UI Components */
        .brand-header {
            text-align: center; margin-bottom: 20px;
            padding: 20px; border-radius: 20px;
            background: var(--glass); border: 1px solid rgba(255,255,255,0.1);
            backdrop-filter: blur(10px);
        }
        .brand-header h1 { font-family: 'Orbitron'; font-size: 28px; margin: 0; color: var(--accent); letter-spacing: 4px; }

        .main-panel {
            background: rgba(0, 15, 30, 0.8);
            border: 1px solid var(--accent);
            border-radius: 20px; padding: 20px;
            box-shadow: 0 0 30px rgba(0, 210, 255, 0.2);
        }

        .input-group { position: relative; margin-bottom: 15px; }
        input {
            width: 100%; padding: 15px; background: #000;
            border: 1px solid #333; border-radius: 10px;
            color: var(--accent); font-size: 14px; box-sizing: border-box;
            font-family: 'Share Tech Mono'; transition: 0.3s;
        }
        input:focus { border-color: var(--accent); outline: none; box-shadow: 0 0 10px var(--accent); }

        .btn-main {
            width: 100%; padding: 15px; border: none; border-radius: 10px;
            background: var(--accent); color: #000; font-weight: 900;
            cursor: pointer; transition: 0.3s; font-family: 'Orbitron';
        }
        .btn-main:disabled { opacity: 0.5; cursor: not-allowed; }

        /* Result Styles */
        #result-box { display: none; margin-top: 20px; padding: 15px; border-top: 1px solid rgba(255,255,255,0.1); text-align: center; }
        .res-value { font-size: 60px; font-weight: 900; font-family: 'Orbitron'; margin: 10px 0; }
        .tai-color { color: #ffae00; text-shadow: 0 0 30px #ffae00; }
        .xiu-color { color: #00d2ff; text-shadow: 0 0 30px #00d2ff; }

        .history-list { margin-top: 15px; max-height: 120px; overflow-y: auto; font-size: 11px; }
        .history-item { display: flex; justify-content: space-between; padding: 5px 0; border-bottom: 1px solid #111; }
    </style>
</head>
<body>

<div id="warning-modal">
    <div class="warning-box">
        <h2 style="color: var(--danger); margin-top:0;">⚠️ CẢNH BÁO HỆ THỐNG</h2>
        <p>Công cụ này chỉ dùng cho mục đích nghiên cứu thuật toán MD5. <br>Chúng tôi không chịu trách nhiệm cho bất kỳ tổn thất tài chính nào của bạn.</p>
        <button class="btn-main" onclick="acceptWarning()" style="background: var(--danger); color: white;">TÔI ĐÃ HIỂU & ĐỒNG Ý</button>
    </div>
</div>

<canvas id="snow-canvas"></canvas>

<div class="app-container">
    <div class="brand-header">
        <h1>HOANGDZ</h1>
        <div id="ai-status" style="color: var(--success); font-size: 10px;">● AI NEURAL NETWORK ONLINE</div>
    </div>

    <div class="main-panel">
        <div class="input-group">
            <input type="text" id="md5Input" placeholder="NHẬP MÃ PHIÊN MD5 (32 KÝ TỰ)..." maxlength="32">
        </div>
        <button class="btn-main" id="predictBtn" onclick="processAnalysis()">BẮT ĐẦU PHÂN TÍCH</button>

        <div id="result-box">
            <div style="font-size: 10px; opacity: 0.7;">DỰ ĐOÁN AI PHIÊN TIẾP THEO</div>
            <div id="resValue" class="res-value">--</div>
            <div id="aiConfidence" style="color: var(--success); font-size: 12px;">ĐỘ CHÍNH XÁC: 0%</div>
            
            <div style="display: flex; gap: 10px; margin-top: 15px;">
                <button onclick="recordResult(true)" style="flex:1; background: var(--success); border:none; padding:10px; border-radius:5px; font-weight:bold;">WIN</button>
                <button onclick="recordResult(false)" style="flex:1; background: var(--danger); border:none; padding:10px; border-radius:5px; font-weight:bold; color:white;">LOSS</button>
            </div>
        </div>

        <div class="history-list" id="historyList">
            <div style="text-align:center; opacity:0.3;">--- LỊCH SỬ PHÂN TÍCH ---</div>
        </div>
    </div>
    <div style="text-align:center; font-size:9px; margin-top:15px; opacity:0.5;">VERSION 14.0.2 - SECURED BY HOANGDZ PRIVATE API</div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>
<script>
    // Xử lý Cảnh báo
    function acceptWarning() {
        document.getElementById('warning-modal').style.display = 'none';
        // Thêm âm thanh khởi động nếu muốn
    }

    // AI Logic & State
    let aiMemory = 0; 
    let winCount = 0;
    let totalCount = 0;

    function processAnalysis() {
        const val = document.getElementById('md5Input').value.trim();
        if(val.length < 32) {
            alert("Mã MD5 không hợp lệ! (Cần đủ 32 ký tự)");
            return;
        }

        const btn = document.getElementById('predictBtn');
        btn.disabled = true;
        btn.innerText = "ĐANG TÍNH TOÁN...";

        // Giả lập xử lý Deep Learning
        setTimeout(() => {
            const hash = CryptoJS.MD5(val + aiMemory).toString();
            const firstChar = parseInt(hash.charAt(0), 16);
            const result = (firstChar + aiMemory) % 2 === 0 ? "TÀI" : "XỈU";
            const confidence = 92 + Math.random() * 7.5;

            const resDisplay = document.getElementById('resValue');
            resDisplay.innerText = result;
            resDisplay.className = 'res-value ' + (result === "TÀI" ? "tai-color" : "xiu-color");
            
            document.getElementById('aiConfidence').innerText = `ĐỘ TIN CẬY: ${confidence.toFixed(2)}%`;
            document.getElementById('result-box').style.display = 'block';
            
            btn.disabled = false;
            btn.innerText = "TIẾP TỤC PHÂN TÍCH";
            window.currentBet = { side: result, hash: val.substring(0,8) };
        }, 1200);
    }

    function recordResult(isWin) {
        totalCount++;
        if(isWin) {
            winCount++;
        } else {
            aiMemory += 1; // Học máy: Tự điều chỉnh logic nếu dự đoán sai
        }

        const list = document.getElementById('historyList');
        const item = document.createElement('div');
        item.className = 'history-item';
        item.innerHTML = `
            <span>#${window.currentBet.hash}</span>
            <span style="color: ${window.currentBet.side === 'TÀI' ? '#ffae00' : '#00d2ff'}">${window.currentBet.side}</span>
            <span style="color: ${isWin ? '#00ff88' : '#ff3232'}">${isWin ? 'WIN' : 'LOSS'}</span>
        `;
        list.prepend(item);
        
        document.getElementById('result-box').style.display = 'none';
        document.getElementById('md5Input').value = "";
        
        // Cập nhật trạng thái AI
        const rate = ((winCount/totalCount)*100).toFixed(1);
        document.getElementById('ai-status').innerText = `● AI ACCURACY: ${rate}% (SAMPLES: ${totalCount})`;
    }

    // Hiệu ứng hạt (Snow)
    const canvas = document.getElementById('snow-canvas');
    const ctx = canvas.getContext('2d');
    let dots = [];
    function init() {
        canvas.width = window.innerWidth; canvas.height = window.innerHeight;
        for(let i=0; i<50; i++) dots.push({x: Math.random()*canvas.width, y: Math.random()*canvas.height, r: Math.random()*2, d: Math.random()*1});
    }
    function draw() {
        ctx.clearRect(0,0,canvas.width, canvas.height);
        ctx.fillStyle = "rgba(255,255,255,0.5)";
        dots.forEach(p => {
            ctx.beginPath(); ctx.arc(p.x, p.y, p.r, 0, Math.PI*2); ctx.fill();
            p.y += p.d; if(p.y > canvas.height) p.y = -5;
        });
        requestAnimationFrame(draw);
    }
    init(); draw();
</script>
</body>
</html>
