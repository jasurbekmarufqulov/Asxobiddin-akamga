<!DOCTYPE html>
<html lang="uz">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Matematika Ustoz</title>
    <style>
        body {
            /* Jigar rangdagi gradient */
            background: linear-gradient(135deg, #3d2616 0%, #a67c52 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Segoe UI', sans-serif;
            margin: 0;
            overflow-y: auto;
            padding: 20px 0;
        }

        /* Harakatlanuvchi raqamlar */
        .moving-numbers { position: absolute; top: 0; left: 0; width: 100%; height: 100%; pointer-events: none; z-index: 1; overflow: hidden; }
        .num { position: absolute; color: rgba(255, 255, 255, 0.08); font-size: 2rem; font-weight: bold; animation: float 10s infinite linear; }
        @keyframes float { 0% { transform: translateY(110vh); opacity: 0; } 50% { opacity: 0.5; } 100% { transform: translateY(-10vh); opacity: 0; } }

        /* Rasmni to'g'rilash */
        .ustoz-photo {
            width: 80px; height: 80px;
            border-radius: 50%; border: 3px solid rgba(255, 255, 255, 0.3);
            box-shadow: 0 4px 15px rgba(0,0,0,0.3);
            object-fit: cover;
            margin: 0 auto 15px auto;
            display: block;
        }

        .menu-btn { position: absolute; top: 30px; right: 20px; cursor: pointer; z-index: 100; }
        .menu-btn span { display: block; width: 30px; height: 3px; background: white; margin: 6px 0; }

        .nav-menu { position: fixed; top: 0; right: -300px; width: 250px; height: 100%; background: rgba(0, 0, 0, 0.85); backdrop-filter: blur(15px); display: flex; flex-direction: column; justify-content: center; align-items: center; transition: 0.5s; z-index: 50; }
        .nav-menu.active { right: 0; }
        .nav-menu a { color: white; text-decoration: none; font-size: 20px; margin: 15px 0; padding: 12px 25px; border: 1px solid rgba(255,255,255,0.3); border-radius: 12px; width: 160px; text-align: center; }

        .glass-card {
            background: rgba(255, 255, 255, 0.1); backdrop-filter: blur(20px);
            padding: 30px; border-radius: 30px; box-shadow: 0 15px 35px rgba(0, 0, 0, 0.2);
            width: 90%; max-width: 400px; color: white; text-align: center;
            z-index: 10; position: relative; border: 1px solid rgba(255, 255, 255, 0.1);
        }

        #balanceDisplay { font-size: 14px; font-weight: 600; background: rgba(255, 215, 0, 0.2); padding: 6px 15px; border-radius: 10px; display: inline-block; margin-bottom: 10px; color: #ffd700; border: 1px solid rgba(255, 215, 0, 0.3); }

        .option-btn { width: 100%; padding: 15px; margin: 10px 0; background: rgba(255, 255, 255, 0.05); border: 1px solid rgba(255, 255, 255, 0.1); border-radius: 15px; color: white; font-size: 16px; cursor: pointer; transition: 0.3s; }
        .option-btn:active { transform: scale(0.98); }
        .correct { background-color: #8b4513 !important; border-color: #a0522d !important; box-shadow: 0 0 10px #a0522d; }
        .wrong { background-color: #5d4037 !important; border-color: #4e342e !important; }
    </style>
</head>
<body>

    <div class="moving-numbers">
        <span class="num" style="left:10%; animation-delay:0s">1</span>
        <span class="num" style="left:25%; animation-delay:2s">π</span>
        <span class="num" style="left:40%; animation-delay:4s">5</span>
        <span class="num" style="left:60%; animation-delay:1s">+</span>
        <span class="num" style="left:80%; animation-delay:3s">9</span>
        <span class="num" style="left:90%; animation-delay:5s">x</span>
    </div>

    <div class="menu-btn" onclick="toggleMenu()">
        <span></span><span></span><span></span>
    </div>

    <div class="nav-menu" id="navMenu">
        <a href="https://youtube.com/@matematikakamoliddinov8443?feature=shared">YouTube</a>
        <a href="https://www.instagram.com/asxobiddin_kamoliddinov_math?igsh=eG9oczJiNzNrZXM0">Instagram</a>
        <a href="https://t.me/asxobiddin_matematikaustoz" target="_blank">Telegram</a>
    </div>

    <div class="glass-card">
        <img src="html.jpg" alt="Ustoz" class="ustoz-photo" onerror="this.style.display='none'">
        
        <div id="balanceDisplay">Balans: 0 so'm</div>
        <h1 id="title">Matematika Ustoz</h1>
        <p id="savol">Yuklanmoqda...</p>
        <div id="options"></div>
    </div>

    <script>
        const quizData = [
            { "savol": "Pifagor teoremasi qaysi formulaga asoslangan?", "variantlar": ["a + b = c", "a² + b² = c²", "a * b = c", "a / b = c"], "javob": "a² + b² = c²" },
            { "savol": "2 + 2 * 2 = ?", "variantlar": ["4", "6", "8", "10"], "javob": "6" },
            { "savol": "Doira yuzi qaysi formula bilan topiladi?", "variantlar": ["2πr", "πr²", "πd", "r²"], "javob": "πr²" },
            { "savol": "12 ning 25% qismi nechaga teng?", "variantlar": ["2", "3", "4", "5"], "javob": "3" },
            { "savol": "1 dan 100 gacha bo'lgan tub sonlar soni nechta?", "variantlar": ["20", "25", "30", "35"], "javob": "25" },
            { "savol": "Teng yonli uchburchakning asosidagi burchaklar qanday?", "variantlar": ["Har xil", "To'g'ri", "Teng", "O'tmas"], "javob": "Teng" },
            { "savol": "x + 5 = 12 bo'lsa, x nechaga teng?", "variantlar": ["5", "6", "7", "8"], "javob": "7" },
            { "savol": "Kvadratning tomoni 4 sm bo'lsa, yuzi nechaga teng?", "variantlar": ["8", "12", "16", "20"], "javob": "16" },
            { "savol": "1 soatda necha soniya bor?", "variantlar": ["600", "3600", "1200", "60"], "javob": "3600" },
            { "savol": "5 ning kvadrati nechaga teng?", "variantlar": ["10", "15", "25", "50"], "javob": "25" },
            { "savol": "Eng kichik natural son qaysi?", "variantlar": ["0", "1", "2", "-1"], "javob": "1" },
            { "savol": "Uchburchak burchaklari yig'indisi nechaga teng?", "variantlar": ["90", "180", "270", "360"], "javob": "180" },
            { "savol": "3/4 kasrning o'nli ko'rinishi qanday?", "variantlar": ["0.25", "0.5", "0.75", "0.8"], "javob": "0.75" },
            { "savol": "To'g'ri burchak necha gradus?", "variantlar": ["45", "90", "180", "360"], "javob": "90" },
            { "savol": "5 * 5 + 5 = ?", "variantlar": ["30", "50", "25", "35"], "javob": "30" },
            { "savol": "Doira diametri radiusidan necha marta katta?", "variantlar": ["1", "2", "3", "4"], "javob": "2" },
            { "savol": "100 ning kvadrat ildizi nechaga teng?", "variantlar": ["5", "10", "20", "50"], "javob": "10" },
            { "savol": "Tub son nima?", "variantlar": ["Faqat 1ga bo'linadi", "Faqat o'ziga bo'linadi", "1 va o'ziga bo'linadi", "Hammasiga"], "javob": "1 va o'ziga bo'linadi" },
            { "savol": "Parallel chiziqlar kesishadimi?", "variantlar": ["Ha", "Yo'q", "Faqat burchak ostida", "Faqat cheksizlikda"], "javob": "Yo'q" },
            { "savol": "1000 gramm necha kilogramm?", "variantlar": ["0.1", "1", "10", "100"], "javob": "1" },
            { "savol": "Sanoq sistemasi asosida nima yotadi?", "variantlar": ["Raqam", "Son", "Belgi", "Amal"], "javob": "Son" },
            { "savol": "Rombning diagonallari qanday burchak ostida kesishadi?", "variantlar": ["45", "60", "90", "120"], "javob": "90" },
            { "savol": "Logarifm asosida nima turadi?", "variantlar": ["Qo'shish", "Ayirish", "Ko'paytirish", "Daraja"], "javob": "Daraja" },
            { "savol": "Pifagor teoremasi qaysi uchburchaklar uchun?", "variantlar": ["Ixtiyoriy", "To'g'ri", "O'tmas", "O'tkir"], "javob": "To'g'ri" },
            { "savol": "Parallelogramm qarama-qarshi tomonlari qanday?", "variantlar": ["Teng", "Har xil", "Kesishadi", "Perpendikulyar"], "javob": "Teng" },
            { "savol": "Tenglama ildizi nima?", "variantlar": ["Noma'lum", "Son", "O'zgaruvchi", "Javob"], "javob": "Son" },
            { "savol": "Sfera yuzi formulasi?", "variantlar": ["4πr²", "2πr", "πr²", "r³"], "javob": "4πr²" },
            { "savol": "Natural sonlar qaysilar?", "variantlar": ["Manfiy", "Sanash uchun", "Nol", "Kasr"], "javob": "Sanash uchun" },
            { "savol": "Kvadrat tenglama nechta ildizga ega bo'lishi mumkin?", "variantlar": ["1", "2", "3", "Cheksiz"], "javob": "2" },
            { "savol": "Matematika fanining asosiy tili nima?", "variantlar": ["Raqamlar", "Mantiq", "Geometriya", "Algoritm"], "javob": "Mantiq" }
        ];

        let currentIdx = 0;
        let balance = 0;

        function toggleMenu() { document.getElementById('navMenu').classList.toggle('active'); }

        function loadQuestion() {
            const data = quizData[currentIdx];
            document.getElementById('savol').innerText = data.savol;
            const optionsDiv = document.getElementById('options');
            optionsDiv.innerHTML = ''; 

            data.variantlar.forEach(opt => {
                const btn = document.createElement('button');
                btn.className = 'option-btn';
                btn.innerText = opt;
                btn.onclick = () => checkAnswer(btn, opt);
                optionsDiv.appendChild(btn);
            });
        }

        function checkAnswer(btn, selected) {
            const data = quizData[currentIdx];
            const allButtons = document.querySelectorAll('.option-btn');
            allButtons.forEach(b => b.disabled = true);

            if (selected === data.javob) {
                btn.classList.add('correct');
                balance += 1000;
                document.getElementById('balanceDisplay').innerText = "Balans: " + balance + " so'm";
            } else {
                btn.classList.add('wrong');
                allButtons.forEach(b => { if (b.innerText === data.javob) b.classList.add('correct'); });
            }

            setTimeout(() => {
                currentIdx++;
                if (currentIdx < quizData.length) {
                    loadQuestion();
                } else {
                    document.querySelector('.glass-card').innerHTML = 
                        `<h1 style="font-size:24px">Test yakunlandi!</h1><p>Siz ishlab topgan pul: <b>${balance} so'm</b></p>
                        <button class="option-btn" onclick="location.reload()">Qaytadan boshlash</button>`;
                }
            }, 1500);
        }

        loadQuestion();
    </script>
</body>
</html>
