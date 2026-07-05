<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Zaysistan Tiers</title>
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background-color: #0f1115; color: #e1e7ed; padding: 20px; }
        
        /* Navbar */
        nav { display: flex; justify-content: space-between; align-items: center; padding: 15px 40px; background: #161920; border-radius: 12px; margin-bottom: 30px; box-shadow: 0 4px 15px rgba(0,0,0,0.3); }
        .logo { font-size: 24px; font-weight: bold; color: #ff4757; letter-spacing: 1px; }
        .auth-buttons button { background: none; border: none; color: #fff; font-size: 16px; cursor: pointer; margin-left: 15px; padding: 8px 16px; border-radius: 6px; transition: 0.3s; font-weight: 500; }
        .auth-buttons .admin-btn { border: 1px solid #ffa502; color: #ffa502; }
        .auth-buttons .admin-btn:hover { background: #ffa502; color: #111; }
        .auth-buttons .login-btn { border: 1px solid #ff4757; color: #ff4757; }
        .auth-buttons .login-btn:hover { background: #ff4757; color: #fff; }
        .auth-buttons .register-btn { background: #ff4757; }
        .auth-buttons .register-btn:hover { background: #ff2e44; }

        /* Container */
        .container { max-width: 1200px; margin: 0 auto; }
        
        /* Arama ve Liste */
        .search-box { width: 100%; padding: 15px; background: #161920; border: 1px solid #232836; border-radius: 8px; color: #fff; font-size: 16px; margin-bottom: 20px; outline: none; }
        .tier-table { width: 100%; border-collapse: collapse; background: #161920; border-radius: 12px; overflow: hidden; box-shadow: 0 4px 15px rgba(0,0,0,0.2); }
        .tier-table th, .tier-table td { padding: 15px 20px; text-align: left; border-bottom: 1px solid #232836; }
        .tier-table th { background: #1c2029; color: #8b949e; font-weight: 600; }
        .player-row { cursor: pointer; transition: 0.2s; }
        .player-row:hover { background: #1c2029; }
        .player-cell { display: flex; align-items: center; gap: 12px; }
        .player-avatar-mini { width: 28px; height: 28px; border-radius: 4px; }
        .empty-message { text-align: center; padding: 40px; color: #8b949e; font-style: italic; }

        /* Modallar */
        .modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.7); justify-content: center; align-items: center; z-index: 100; }
        .modal-content { background: #161920; padding: 30px; border-radius: 12px; width: 100%; max-width: 400px; border: 1px solid #232836; position: relative; }
        .modal-content h3 { margin-bottom: 20px; color: #fff; font-size: 22px; }
        .modal-content input, .modal-content select { width: 100%; padding: 12px; background: #0f1115; border: 1px solid #232836; border-radius: 6px; color: #fff; margin-bottom: 15px; outline: none; }
        .modal-content button { width: 100%; padding: 12px; background: #ff4757; border: none; border-radius: 6px; color: #fff; font-size: 16px; cursor: pointer; font-weight: bold; }
        .close-btn { position: absolute; top: 15px; right: 15px; color: #8b949e; cursor: pointer; font-size: 20px; }

        /* Profil Sayfası */
        .profile-card { display: none; background: #161920; border-radius: 12px; padding: 30px; margin-top: 20px; border: 1px solid #232836; position: relative; }
        .profile-header { display: flex; align-items: center; margin-bottom: 25px; }
        .profile-avatar { width: 64px; height: 64px; border-radius: 8px; margin-right: 20px; background: #232836; }
        .profile-name { font-size: 24px; font-weight: bold; color: #fff; }
        .badge-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); gap: 15px; }
        
        .delete-account-btn { position: absolute; top: 30px; right: 30px; background: rgba(255, 71, 87, 0.1); border: 1px solid #ff4757; color: #ff4757; padding: 8px 16px; border-radius: 6px; cursor: pointer; font-weight: bold; transition: 0.3s; }
        .delete-account-btn:hover { background: #ff4757; color: #fff; }
        .admin-badge-indicator { background: #ffa502; color: #111; padding: 4px 8px; font-size: 12px; font-weight: bold; border-radius: 4px; margin-left: 15px; }

        /* Kit Kutucukları */
        .kit-box { background: #1c2029; border: 1px solid #232836; padding: 12px; border-radius: 8px; display: flex; justify-content: space-between; align-items: center; }
        .kit-box.admin-editable { border: 1px dashed #ffa502; }
        .kit-name { font-weight: 600; text-transform: uppercase; font-size: 14px; color: #8b949e; }
        .kit-badge { padding: 4px 8px; border-radius: 4px; font-weight: bold; font-size: 12px; text-transform: uppercase; }
        
        /* Tier Seçim Select Kutusu */
        .admin-select { background: #0f1115; color: #fff; border: 1px solid #ffa502; padding: 2px 5px; border-radius: 4px; font-weight: bold; outline: none; }

        /* Tier Renkleri */
        .tier-ht { background: rgba(255, 71, 87, 0.2); color: #ff4757; border: 1px solid #ff4757; }
        .tier-lt { background: rgba(255, 165, 2, 0.2); color: #ffa502; border: 1px solid #ffa502; }
        .tier-unranked { background: rgba(116, 125, 140, 0.2); color: #747d8c; border: 1px solid #747d8c; }
    </style>
</head>
<body>

    <nav>
        <div class="logo" onclick="goHome()" style="cursor:pointer">ZAYSISTAN TIERS</div>
        <div class="auth-buttons" id="authButtons">
            <span id="adminIndicator" style="display: none;" class="admin-badge-indicator">ADMIN MODU AKTİF</span>
            <button class="admin-btn" id="adminMenuBtn" onclick="openModal('adminModal')">Admin Paneli</button>
            <button class="login-btn" id="navLoginBtn" onclick="openModal('loginModal')">Giriş Yap</button>
            <button class="register-btn" id="navRegBtn" onclick="openModal('registerModal')">Kayıt Ol</button>
            <button class="login-btn" id="navMyProfileBtn" style="display:none;" onclick="showMyProfile()">Profilim</button>
        </div>
    </nav>

    <div class="container">
        <!-- Ana Liste Ekranı -->
        <div id="mainPage">
            <input type="text" class="search-box" id="searchBar" onkeyup="filterPlayers()" placeholder="Oyuncu adı ara...">
            <table class="tier-table">
                <thead>
                    <tr>
                        <th style="width: 80px;">Sıra</th>
                        <th>Oyuncu</th>
                        <th>En İyi Derece</th>
                        <th>En Kötü Derece</th>
                    </tr>
                </thead>
                <tbody id="leaderboardBody">
                    <!-- Dinamik Sıralama -->
                </tbody>
            </table>
        </div>

        <!-- Oyuncu Profil Sayfası / Düzenleme Alanı -->
        <div id="profilePage" class="profile-card">
            <button class="delete-account-btn" id="delAccBtn" onclick="openModal('deleteModal')">Hesabı Sil</button>
            <div class="profile-header">
                <img id="pAvatar" class="profile-avatar" src="" alt="">
                <div id="pName" class="profile-name">Minecraft_Nick</div>
            </div>
            <h4 style="margin-bottom: 20px; color: #ff4757; letter-spacing: 0.5px;" id="profileTitle">KİT TIER DURUMLARI</h4>
            <p id="adminTip" style="display:none; color: #ffa502; font-size: 14px; margin-bottom: 15px; font-style: italic;">💡 Admin İpucu: Tier derecelerini değiştirmek için sağdaki kutulardan yeni tier seçebilirsin kanka!</p>
            <div class="badge-grid" id="pBadges">
                <!-- Kitler -->
            </div>
            <button onclick="logout()" style="margin-top: 30px; background: #232836; border: 1px solid #30364d; color: #fff; width: auto; padding: 10px 20px; cursor: pointer; border-radius: 6px;">Geri Dön / Çıkış Yap</button>
        </div>
    </div>

    <!-- Admin Giriş Modalı -->
    <div id="adminModal" class="modal">
        <div class="modal-content">
            <span class="close-btn" onclick="closeModal('adminModal')">&times;</span>
            <h3 style="color: #ffa502;">Admin Girişi</h3>
            <input type="password" id="adminPassInput" placeholder="Admin Şifresini Gir">
            <button onclick="handleAdminLogin()" style="background: #ffa502; color: #111;">Giriş Yap</button>
        </div>
    </div>

    <!-- Giriş Modalı -->
    <div id="loginModal" class="modal">
        <div class="modal-content">
            <span class="close-btn" onclick="closeModal('loginModal')">&times;</span>
            <h3>Giriş Yap</h3>
            <input type="text" id="loginUser" placeholder="Minecraft Adın">
            <input type="password" id="loginPass" placeholder="Şifren">
            <button onclick="handleLogin()">Giriş</button>
        </div>
    </div>

    <!-- Kayıt Modalı -->
    <div id="registerModal" class="modal">
        <div class="modal-content">
            <span class="close-btn" onclick="closeModal('registerModal')">&times;</span>
            <h3>Kayıt Ol</h3>
            <input type="text" id="regUser" placeholder="Minecraft Adın">
            <input type="password" id="regPass" placeholder="Şifren">
            <button onclick="handleRegister()">Hesap Oluştur</button>
        </div>
    </div>

    <!-- Hesabı Sil Modalı -->
    <div id="deleteModal" class="modal">
        <div class="modal-content">
            <span class="close-btn" onclick="closeModal('deleteModal')">&times;</span>
            <h3 style="color: #ff4757;">Hesabını Sil</h3>
            <p style="color: #8b949e; font-size: 14px; margin-bottom: 15px;">Bu işlem geri alınamaz.</p>
            <input type="text" id="delUser" placeholder="Minecraft Adını Doğrula">
            <input type="password" id="delPass" placeholder="Şifreni Doğrula">
            <button onclick="handleDeleteAccount()" style="background: #ff4757;">Hesabı Kalıcı Olarak Sil</button>
        </div>
    </div>

    <script>
        if (!localStorage.getItem('players')) {
            localStorage.setItem('players', JSON.stringify([]));
        }

        const kitList = ["sword", "netpoth", "mace", "cpvp", "axe", "smp", "uhc", "cart", "diapot", "pot"];
        const tierListOptions = ["UNRANKED", "HT1", "LT1", "HT2", "LT2", "HT3", "LT3", "HT4", "LT4", "HT5", "LT5"];

        const tierWeights = {
            "HT1": 11, "LT1": 10, "HT2": 9, "LT2": 8, "HT3": 7, 
            "LT3": 6, "HT4": 5, "LT4": 4, "HT5": 3, "LT5": 2, "UNRANKED": 0
        };

        let currentLoggedInUser = null;
        let isAdminMode = false;

        function openModal(id) { document.getElementById(id).style.display = 'flex'; }
        function closeModal(id) { document.getElementById(id).style.display = 'none'; }
        function goHome() { logout(); }

        function handleAdminLogin() {
            let pass = document.getElementById('adminPassInput').value;
            if(pass === "carlbabacangod") {
                isAdminMode = true;
                document.getElementById('adminIndicator').style.display = 'inline-block';
                document.getElementById('adminMenuBtn').innerText = "Admin Modundan Çık";
                document.getElementById('adminMenuBtn').setAttribute("onclick", "exitAdminMode()");
                alert("Admin modu aktif! Artık listeden istediğin oyuncuya tıklayıp tier verebilirsin kanka.");
                closeModal('adminModal');
                updateLeaderboard();
            } else {
                alert("Yanlış şifre kanka!");
            }
            document.getElementById('adminPassInput').value = "";
        }

        function exitAdminMode() {
            isAdminMode = false;
            document.getElementById('adminIndicator').style.display = 'none';
            document.getElementById('adminMenuBtn').innerText = "Admin Paneli";
            document.getElementById('adminMenuBtn').setAttribute("onclick", "openModal('adminModal')");
            alert("Admin modundan çıkıldı.");
            logout();
        }

        function handleRegister() {
            let nick = document.getElementById('regUser').value.trim();
            let pass = document.getElementById('regPass').value;
            if(!nick || !pass) return alert("Alanları doldur kanka!");

            let players = JSON.parse(localStorage.getItem('players'));
            if(players.some(p => p.name.toLowerCase() === nick.toLowerCase())) {
                return alert("Bu oyuncu zaten kayıtlı kanka!");
            }

            let initialKits = {};
            kitList.forEach(kit => initialKits[kit] = "UNRANKED");

            players.push({ name: nick, password: pass, kits: initialKits });
            localStorage.setItem('players', JSON.stringify(players));

            alert("Kayıt başarılı!");
            closeModal('registerModal');
            updateLeaderboard();
            openModal('loginModal');
        }

        function handleLogin() {
            let nick = document.getElementById('loginUser').value.trim();
            let pass = document.getElementById('loginPass').value;
            
            let players = JSON.parse(localStorage.getItem('players'));
            let player = players.find(p => p.name.toLowerCase() === nick.toLowerCase() && p.password === pass);

            if(!player) return alert("Hatalı kullanıcı adı veya şifre!");

            currentLoggedInUser = player.name;
            closeModal('loginModal');
            
            // Üst menüyü güncelle (Kayıt ol/Giriş yap yerine Profilim gelsin)
            document.getElementById('navLoginBtn').style.display = 'none';
            document.getElementById('navRegBtn').style.display = 'none';
            document.getElementById('navMyProfileBtn').style.display = 'inline-block';

            showProfile(player.name);
        }

        function showMyProfile() {
            if(currentLoggedInUser) showProfile(currentLoggedInUser);
        }

        // Profili Ekrana Getirme (Kritik Alan: Admin Değilse Sadece Okunabilir Olur)
        function showProfile(playerName) {
            let players = JSON.parse(localStorage.getItem('players'));
            let player = players.find(p => p.name.toLowerCase() === playerName.toLowerCase());

            if(!player) return;

            document.getElementById('mainPage').style.display = 'none';
            document.getElementById('profilePage').style.display = 'block';
            
            document.getElementById('pName').innerText = player.name;
            document.getElementById('pAvatar').src = `https://minotar.net/avatar/${player.name}/64.png`;

            // Görünüm Ayarları (Admin mi, Profil Sahibi mi, Ziyaretçi mi?)
            if(isAdminMode) {
                document.getElementById('profileTitle').innerText = `YÖNETİM: ${player.name} KİT AYARLARI`;
                document.getElementById('adminTip').style.display = 'block';
                document.getElementById('delAccBtn').style.display = 'none'; 
            } else {
                document.getElementById('profileTitle').innerText = `${player.name} OYUNCU PROFİLİ`;
                document.getElementById('adminTip').style.display = 'none';
                
                // Sadece kendi hesabına giriş yapmışsa "Hesabı Sil" butonu görünsün
                if(currentLoggedInUser && currentLoggedInUser.toLowerCase() === playerName.toLowerCase()) {
                    document.getElementById('delAccBtn').style.display = 'block';
                } else {
                    document.getElementById('delAccBtn').style.display = 'none';
                }
            }
            
            renderBadges(player);
        }

        function renderBadges(player) {
            let badgesHtml = "";
            kitList.forEach(kit => {
                let currentTier = player.kits[kit] || "UNRANKED";
                let badgeClass = currentTier.startsWith('HT') ? 'tier-ht' : (currentTier.startsWith('LT') ? 'tier-lt' : 'tier-unranked');
                
                if (isAdminMode) {
                    // SADECE ADMİNSE değiştirebilir select kutusu gelir
                    let selectOptions = "";
                    tierListOptions.forEach(opt => {
                        let selected = opt === currentTier ? "selected" : "";
                        selectOptions += `<option value="${opt}" ${selected}>${opt}</option>`;
                    });
                    
                    badgesHtml += `
                        <div class="kit-box admin-editable">
                            <span class="kit-name">${kit}</span>
                            <select class="admin-select" onchange="changePlayerTier('${player.name}', '${kit}', this.value)">
                                ${selectOptions}
                            </select>
                        </div>
                    `;
                } else {
                    // NORMAL OYUNCULARDA sadece metin olarak gözükür, müdahale edilemez
                    badgesHtml += `
                        <div class="kit-box">
                            <span class="kit-name">${kit}</span>
                            <span class="kit-badge ${badgeClass}">${currentTier}</span>
                        </div>
                    `;
                }
            });
            document.getElementById('pBadges').innerHTML = badgesHtml;
        }

        function changePlayerTier(playerName, kitName, newTier) {
            if(!isAdminMode) return; // Koruma tedbiri
            let players = JSON.parse(localStorage.getItem('players'));
            let player = players.find(p => p.name.toLowerCase() === playerName.toLowerCase());
            
            if(player) {
                player.kits[kitName] = newTier;
                localStorage.setItem('players', JSON.stringify(players));
                renderBadges(player);
            }
        }

        function handleDeleteAccount() {
            let nick = document.getElementById('delUser').value.trim();
            let pass = document.getElementById('delPass').value;

            if (nick.toLowerCase() !== currentLoggedInUser.toLowerCase()) {
                return alert("Giriş yaptığın kullanıcı adı ile uyuşmuyor kanka!");
            }

            let players = JSON.parse(localStorage.getItem('players'));
            let playerIndex = players.findIndex(p => p.name.toLowerCase() === nick.toLowerCase() && p.password === pass);

            if (playerIndex === -1) {
                return alert("Şifre hatalı kanka!");
            }

            players.splice(playerIndex, 1);
            localStorage.setItem('players', JSON.stringify(players));

            alert("Hesap başarıyla silindi.");
            closeModal('deleteModal');
            forceFullLogout();
        }

        function updateLeaderboard() {
            let players = JSON.parse(localStorage.getItem('players'));
            let tbody = document.getElementById('leaderboardBody');
            
            if(players.length === 0) {
                tbody.innerHTML = `<tr><td colspan="4" class="empty-message">Şu an listede kimse yok.</td></tr>`;
                return;
            }

            players.forEach(player => {
                let maxWeight = -1;
                let minWeight = 999;
                let highestTier = "UNRANKED";
                let lowestTier = "UNRANKED";
                let totalScore = 0;

                kitList.forEach(kit => {
                    let t = player.kits[kit] || "UNRANKED";
                    let w = tierWeights[t];
                    totalScore += w;

                    if(w > maxWeight) { maxWeight = w; highestTier = t; }
                    if(w < minWeight) { minWeight = w; lowestTier = t; }
                });
                player.highestTier = highestTier;
                player.lowestTier = lowestTier;
                player.totalScore = totalScore;
            });

            players.sort((a, b) => b.totalScore - a.totalScore);

            let html = "";
            players.forEach((player, index) => {
                let highBadgeClass = player.highestTier.startsWith('HT') ? 'tier-ht' : (player.highestTier.startsWith('LT') ? 'tier-lt' : 'tier-unranked');
                let lowBadgeClass = player.lowestTier.startsWith('HT') ? 'tier-ht' : (player.lowestTier.startsWith('LT') ? 'tier-lt' : 'tier-unranked');
                
                html += `
                    <tr class="player-row" onclick="handleRowClick('${player.name}')">
                        <td><strong>#${index + 1}</strong></td>
                        <td>
                            <div class="player-cell">
                                <img class="player-avatar-mini" src="https://minotar.net/avatar/${player.name}/32.png">
                                <span>${player.name}</span>
                            </div>
                        </td>
                        <td><span class="kit-badge ${highBadgeClass}">${player.highestTier}</span></td>
                        <td><span class="kit-badge ${lowBadgeClass}">${player.lowestTier}</span></td>
                    </tr>
                `;
            });
            tbody.innerHTML = html;
        }

        // TIKLAMA MOTORU: Artık herkes tıklayıp profilleri görebilir!
        function handleRowClick(playerName) {
            showProfile(playerName);
        }

        function filterPlayers() {
            let input = document.getElementById('searchBar').value.toLowerCase();
            let rows = document.getElementById('leaderboardBody').getElementsByTagName('tr');
            
            for (let i = 0; i < rows.length; i++) {
                let playerCell = rows[i].getElementsByTagName('td')[1];
                if (playerCell) {
                    let textValue = playerCell.textContent || playerCell.innerText;
                    if (textValue.toLowerCase().indexOf(input) > -1) {
                        rows[i].style.display = "";
                    } else {
                        rows[i].style.display = "none";
                    }
                }
            }
        }

        function logout() {
            document.getElementById('profilePage').style.display = 'none';
            document.getElementById('mainPage').style.display = 'block';
            updateLeaderboard();
        }

        function forceFullLogout() {
            currentLoggedInUser = null;
            document.getElementById('navLoginBtn').style.display = 'inline-block';
            document.getElementById('navRegBtn').style.display = 'inline-block';
            document.getElementById('navMyProfileBtn').style.display = 'none';
            logout();
        }

        updateLeaderboard();
    </script>
</body>
</html>
