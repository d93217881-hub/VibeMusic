<!DOCTYPE html>
<html>
<head>
    <title>⚡ Мгновенный плеер</title>
    <style>
        *{margin:0;padding:0;box-sizing:border-box;font-family:system-ui,sans-serif}
        body{background:#0a0c1a;display:flex;align-items:center;justify-content:center;min-height:100vh;padding:16px}
        .player{max-width:400px;width:100%;background:#14182e;border-radius:40px;padding:24px;box-shadow:0 20px 40px #000}
        .btn{background:#4169ff;color:white;border:none;border-radius:60px;padding:18px;font-size:18px;font-weight:600;width:100%;cursor:pointer;margin:10px 0}
        .track-list{list-style:none;max-height:300px;overflow-y:auto;margin:20px 0;background:#0f1325;border-radius:30px;padding:10px}
        .track-list li{padding:14px;color:#e0e6ff;border-bottom:1px solid #2a3150;cursor:pointer}
        .now-playing{color:white;font-size:20px;margin:15px 0;text-align:center}
        .hidden{display:none}
    </style>
</head>
<body>
    <div class="player">
        <h1 style="color:white;text-align:center;margin-bottom:20px">⚡ МОЙ ПЛЕЕР</h1>
        
        <!-- КНОПКА - ОДИН КЛИК -->
        <button class="btn" id="scanBtn">📁 СКАНИРОВАТЬ МОИ ФАЙЛЫ</button>
        
        <!-- Плеер появляется после сканирования -->
        <div id="playerContent" style="display:none">
            <div class="now-playing" id="nowPlaying">🎵 Выбери трек</div>
            
            <audio id="audio" controls style="width:100%;margin:10px 0;border-radius:30px"></audio>
            
            <div style="display:flex;gap:10px;margin:15px 0">
                <button class="btn" id="prevBtn" style="padding:12px">⏮</button>
                <button class="btn" id="playBtn" style="padding:12px">▶</button>
                <button class="btn" id="nextBtn" style="padding:12px">⏭</button>
            </div>
            
            <h3 style="color:white;margin:15px 0">📋 Найдено треков: <span id="trackCount">0</span></h3>
            <ul class="track-list" id="trackList"></ul>
        </div>
    </div>

    <script>
        // Это имитация сканирования - в браузере нельзя получить файлы без разрешения
        // Но мы сделаем так, чтобы пользователь сам выбрал папку ОДНИМ КЛИКОМ
        
        const scanBtn = document.getElementById('scanBtn');
        const playerContent = document.getElementById('playerContent');
        const trackList = document.getElementById('trackList');
        const trackCount = document.getElementById('trackCount');
        const audio = document.getElementById('audio');
        const nowPlaying = document.getElementById('nowPlaying');
        const playBtn = document.getElementById('playBtn');
        const prevBtn = document.getElementById('prevBtn');
        const nextBtn = document.getElementById('nextBtn');
        
        let tracks = [];
        let currentIndex = 0;
        
        // При нажатии на кнопку - открываем выбор файлов (это единственный способ)
        scanBtn.addEventListener('click', () => {
            const input = document.createElement('input');
            input.type = 'file';
            input.multiple = true;
            input.accept = 'audio/*';
            
            input.onchange = (e) => {
                const files = Array.from(e.target.files);
                
                if(files.length > 0) {
                    tracks = files.map((file, i) => ({
                        name: file.name,
                        url: URL.createObjectURL(file)
                    }));
                    
                    // Показываем плеер
                    playerContent.style.display = 'block';
                    scanBtn.style.display = 'none';
                    
                    // Обновляем список
                    renderTrackList();
                    trackCount.textContent = tracks.length;
                    
                    // Автоматически добавляем все треки
                    console.log(`✅ Добавлено ${tracks.length} треков`);
                }
            };
            
            input.click(); // ОДИН КЛИК - ВЫБИРАЕШЬ ПАПКУ И ВСЕ ФАЙЛЫ
        });
        
        function renderTrackList() {
            trackList.innerHTML = '';
            tracks.forEach((track, index) => {
                const li = document.createElement('li');
                li.textContent = track.name;
                li.onclick = () => playTrack(index);
                trackList.appendChild(li);
            });
        }
        
        function playTrack(index) {
            if(index >= 0 && index < tracks.length) {
                currentIndex = index;
                audio.src = tracks[index].url;
                audio.play();
                nowPlaying.textContent = `🎵 ${tracks[index].name}`;
                playBtn.textContent = '⏸';
            }
        }
        
        playBtn.onclick = () => {
            if(audio.paused) {
                audio.play();
                playBtn.textContent = '⏸';
            } else {
                audio.pause();
                playBtn.textContent = '▶';
            }
        };
        
        prevBtn.onclick = () => {
            if(currentIndex > 0) playTrack(currentIndex - 1);
        };
        
        nextBtn.onclick = () => {
            if(currentIndex < tracks.length - 1) playTrack(currentIndex + 1);
        };
        
        audio.onended = () => {
            nextBtn.click();
        };
    </script>
</body>
</html>
