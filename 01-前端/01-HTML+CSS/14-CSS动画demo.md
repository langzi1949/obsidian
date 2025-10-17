```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CSS3 动画效果演示</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
        }

        h1 {
            text-align: center;
            color: white;
            margin-bottom: 40px;
            font-size: 2.5rem;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }

        .demo-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
            gap: 30px;
        }

        .demo-card {
            background: rgba(255, 255, 255, 0.95);
            border-radius: 15px;
            padding: 30px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.2);
            backdrop-filter: blur(10px);
            transition: transform 0.3s ease;
        }

        .demo-card:hover {
            transform: translateY(-5px);
        }

        .demo-title {
            font-size: 1.4rem;
            margin-bottom: 20px;
            color: #333;
            text-align: center;
            font-weight: bold;
        }

        .demo-area {
            height: 120px;
            display: flex;
            align-items: center;
            justify-content: center;
            background: #f8f9fa;
            border-radius: 10px;
            margin-bottom: 20px;
            overflow: hidden;
            position: relative;
        }

        /* 1. 淡入淡出效果 */
        .fade-element {
            width: 80px;
            height: 80px;
            background: linear-gradient(45deg, #ff6b6b, #ee5a24);
            border-radius: 50%;
            animation: fadeInOut 2s ease-in-out infinite;
        }

        @keyframes fadeInOut {
            0%, 100% { opacity: 0; }
            50% { opacity: 1; }
        }

        /* 2. 旋转效果 */
        .rotate-element {
            width: 80px;
            height: 80px;
            background: linear-gradient(45deg, #4834d4, #686de0);
            border-radius: 10px;
            animation: rotate 2s linear infinite;
        }

        @keyframes rotate {
            from { transform: rotate(0deg); }
            to { transform: rotate(360deg); }
        }

        /* 3. 缩放效果 */
        .scale-element {
            width: 60px;
            height: 60px;
            background: linear-gradient(45deg, #00d2d3, #54a0ff);
            border-radius: 50%;
            animation: scaleUpDown 1.5s ease-in-out infinite;
        }

        @keyframes scaleUpDown {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.5); }
        }

        /* 4. 平移效果 */
        .translate-element {
            width: 80px;
            height: 80px;
            background: linear-gradient(45deg, #feca57, #ff9ff3);
            border-radius: 15px;
            animation: slideLeftRight 2s ease-in-out infinite;
        }

        @keyframes slideLeftRight {
            0%, 100% { transform: translateX(-30px); }
            50% { transform: translateX(30px); }
        }

        /* 5. 弹跳效果 */
        .bounce-element {
            width: 70px;
            height: 70px;
            background: linear-gradient(45deg, #5f27cd, #a55eea);
            border-radius: 50%;
            animation: bounce 1s ease-in-out infinite;
        }

        @keyframes bounce {
            0%, 20%, 50%, 80%, 100% {
                transform: translateY(0);
            }
            40% {
                transform: translateY(-30px);
            }
            60% {
                transform: translateY(-15px);
            }
        }

        .code-section {
            background: #2d3748;
            border-radius: 8px;
            padding: 15px;
            color: #e2e8f0;
            font-family: 'Courier New', monospace;
            font-size: 12px;
            line-height: 1.4;
            overflow-x: auto;
        }

        .code-title {
            color: #68d391;
            font-weight: bold;
            margin-bottom: 10px;
        }

        /* 控制按钮 */
        .control-btn {
            background: linear-gradient(45deg, #667eea, #764ba2);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 25px;
            cursor: pointer;
            font-size: 14px;
            transition: all 0.3s ease;
            margin: 10px auto;
            display: block;
        }

        .control-btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(0,0,0,0.2);
        }

        .paused {
            animation-play-state: paused !important;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>CSS3 常见动画效果演示</h1>
        
        <div class="demo-grid">
            <!-- 1. 淡入淡出 -->
            <div class="demo-card">
                <h3 class="demo-title">1. 淡入淡出 (Fade In/Out)</h3>
                <div class="demo-area">
                    <div class="fade-element" id="fade"></div>
                </div>
                <div class="code-section">
                    <div class="code-title">CSS代码:</div>
                    <div>@keyframes fadeInOut {<br>
                    &nbsp;&nbsp;0%, 100% { opacity: 0; }<br>
                    &nbsp;&nbsp;50% { opacity: 1; }<br>
                    }<br><br>
                    .fade-element {<br>
                    &nbsp;&nbsp;animation: fadeInOut 2s ease-in-out infinite;<br>
                    }</div>
                </div>
                <button class="control-btn" onclick="toggleAnimation('fade')">暂停/播放</button>
            </div>

            <!-- 2. 旋转 -->
            <div class="demo-card">
                <h3 class="demo-title">2. 旋转 (Rotate)</h3>
                <div class="demo-area">
                    <div class="rotate-element" id="rotate"></div>
                </div>
                <div class="code-section">
                    <div class="code-title">CSS代码:</div>
                    <div>@keyframes rotate {<br>
                    &nbsp;&nbsp;from { transform: rotate(0deg); }<br>
                    &nbsp;&nbsp;to { transform: rotate(360deg); }<br>
                    }<br><br>
                    .rotate-element {<br>
                    &nbsp;&nbsp;animation: rotate 2s linear infinite;<br>
                    }</div>
                </div>
                <button class="control-btn" onclick="toggleAnimation('rotate')">暂停/播放</button>
            </div>

            <!-- 3. 缩放 -->
            <div class="demo-card">
                <h3 class="demo-title">3. 缩放 (Scale)</h3>
                <div class="demo-area">
                    <div class="scale-element" id="scale"></div>
                </div>
                <div class="code-section">
                    <div class="code-title">CSS代码:</div>
                    <div>@keyframes scaleUpDown {<br>
                    &nbsp;&nbsp;0%, 100% { transform: scale(1); }<br>
                    &nbsp;&nbsp;50% { transform: scale(1.5); }<br>
                    }<br><br>
                    .scale-element {<br>
                    &nbsp;&nbsp;animation: scaleUpDown 1.5s ease-in-out infinite;<br>
                    }</div>
                </div>
                <button class="control-btn" onclick="toggleAnimation('scale')">暂停/播放</button>
            </div>

            <!-- 4. 平移 -->
            <div class="demo-card">
                <h3 class="demo-title">4. 平移 (Translate)</h3>
                <div class="demo-area">
                    <div class="translate-element" id="translate"></div>
                </div>
                <div class="code-section">
                    <div class="code-title">CSS代码:</div>
                    <div>@keyframes slideLeftRight {<br>
                    &nbsp;&nbsp;0%, 100% { transform: translateX(-30px); }<br>
                    &nbsp;&nbsp;50% { transform: translateX(30px); }<br>
                    }<br><br>
                    .translate-element {<br>
                    &nbsp;&nbsp;animation: slideLeftRight 2s ease-in-out infinite;<br>
                    }</div>
                </div>
                <button class="control-btn" onclick="toggleAnimation('translate')">暂停/播放</button>
            </div>

            <!-- 5. 弹跳 -->
            <div class="demo-card">
                <h3 class="demo-title">5. 弹跳 (Bounce)</h3>
                <div class="demo-area">
                    <div class="bounce-element" id="bounce"></div>
                </div>
                <div class="code-section">
                    <div class="code-title">CSS代码:</div>
                    <div>@keyframes bounce {<br>
                    &nbsp;&nbsp;0%, 20%, 50%, 80%, 100% {<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;transform: translateY(0);<br>
                    &nbsp;&nbsp;}<br>
                    &nbsp;&nbsp;40% { transform: translateY(-30px); }<br>
                    &nbsp;&nbsp;60% { transform: translateY(-15px); }<br>
                    }<br><br>
                    .bounce-element {<br>
                    &nbsp;&nbsp;animation: bounce 1s ease-in-out infinite;<br>
                    }</div>
                </div>
                <button class="control-btn" onclick="toggleAnimation('bounce')">暂停/播放</button>
            </div>
        </div>
    </div>

    <script>
        function toggleAnimation(elementId) {
            const element = document.getElementById(elementId);
            element.classList.toggle('paused');
        }

        // 添加一些交互效果
        document.addEventListener('DOMContentLoaded', function() {
            const cards = document.querySelectorAll('.demo-card');
            
            cards.forEach(card => {
                card.addEventListener('mouseenter', function() {
                    this.style.transform = 'translateY(-10px) scale(1.02)';
                });
                
                card.addEventListener('mouseleave', function() {
                    this.style.transform = 'translateY(0) scale(1)';
                });
            });
        });
    </script>
</body>
</html>
```

