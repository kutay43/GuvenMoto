<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>GÜVEN MOTO | Profesyonel Servis</title>
    
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">

    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: { sans: ['Inter', 'sans-serif'] },
                    colors: { brand: { dark: '#111827', primary: '#ea580c' } }
                }
            }
        }
    </script>
    <style>
        body { background-color: #f3f4f6; }
        .fade-in { animation: fadeIn 0.3s ease-out forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }
        ::-webkit-scrollbar { width: 6px; } ::-webkit-scrollbar-thumb { background: #9ca3af; border-radius: 3px; }
        .kanban-col { min-height: 600px; background: #e5e7eb; border-radius: 8px; padding: 12px; }
    </style>
</head>
<body class="text-gray-800 antialiased h-screen flex flex-col overflow-hidden">

    <audio id="notification-sound" src="https://assets.mixkit.co/active_storage/sfx/2869/2869-preview.mp3" preload="auto"></audio>
    <div id="toast-container" class="fixed top-5 right-5 z-[200] space-y-2 pointer-events-none"></div>

    <div id="screen-landing" class="fixed inset-0 z-[100] bg-gray-900 flex flex-col items-center justify-center p-4">
        <div class="relative z-10 text-center space-y-8 max-w-5xl w-full fade-in">
            <div class="inline-flex items-center justify-center w-28 h-28 bg-brand-primary rounded-full shadow-2xl mb-2 border-4 border-gray-800">
                <i class="fa-solid fa-motorcycle text-white text-5xl"></i>
            </div>
            <div>
                <h1 class="text-4xl md:text-6xl font-black text-white tracking-tight mb-2">GÜVEN <span class="text-brand-primary">MOTO</span></h1>
                <p class="text-gray-400 text-lg uppercase tracking-widest font-semibold">Online Servis Sistemi</p>
                <p id="connection-status" class="text-xs text-yellow-500 mt-2 font-mono">Sunucu Bağlantısı Bekleniyor...</p>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 w-full max-w-4xl mx-auto mt-12">
                <button onclick="openCustomerPortal()" class="group bg-gray-800/80 hover:bg-brand-primary hover:text-white backdrop-blur-md border border-gray-700 p-6 rounded-xl transition-all hover:-translate-y-1 text-left shadow-lg">
                    <div class="flex items-center justify-between mb-2"><i class="fa-regular fa-calendar-check text-2xl text-gray-300 group-hover:text-white"></i></div>
                    <h3 class="text-xl font-bold text-white mb-1">Randevu Oluştur</h3>
                </button>
                <button onclick="openStatusCheck()" class="group bg-gray-800/80 hover:bg-green-600 hover:text-white backdrop-blur-md border border-gray-700 p-6 rounded-xl transition-all hover:-translate-y-1 text-left shadow-lg">
                    <div class="flex items-center justify-between mb-2"><i class="fa-solid fa-magnifying-glass text-2xl text-gray-300 group-hover:text-white"></i></div>
                    <h3 class="text-xl font-bold text-white mb-1">Durum Sorgula</h3>
                </button>
                <button onclick="checkAdminSession()" class="group bg-gray-800/80 hover:bg-blue-600 hover:text-white backdrop-blur-md border border-gray-700 p-6 rounded-xl transition-all hover:-translate-y-1 text-left shadow-lg">
                    <div class="flex items-center justify-between mb-2"><i class="fa-solid fa-user-shield text-2xl text-gray-300 group-hover:text-white"></i></div>
                    <h3 class="text-xl font-bold text-white mb-1">Personel Girişi</h3>
                </button>
            </div>
        </div>
    </div>

    <div id="screen-customer" class="fixed inset-0 z-[90] bg-gray-50 overflow-y-auto hidden">
        <div class="max-w-xl mx-auto min-h-screen flex flex-col p-4 justify-center">
            <button onclick="returnToLanding()" class="absolute top-6 left-6 text-gray-500 font-bold flex items-center gap-2"><i class="fa-solid fa-arrow-left"></i> Geri</button>
            <div class="bg-white rounded-2xl shadow-xl overflow-hidden border border-gray-200 fade-in mt-10">
                <div class="bg-brand-dark p-6 text-center"><h2 class="text-2xl font-bold text-white">Servis Kaydı</h2></div>
                <form onsubmit="submitCustomerRequest(event)" class="p-6 space-y-4">
                    <input id="c-plate" type="text" placeholder="PLAKA (34 AB 123)" class="w-full p-3 border rounded-lg font-bold uppercase" required>
                    <input id="c-model" type="text" placeholder="MARKA / MODEL" class="w-full p-3 border rounded-lg" required>
                    <div class="grid grid-cols-2 gap-4">
                        <input id="c-name" type="text" placeholder="AD SOYAD" class="w-full p-3 border rounded-lg" required>
                        <input id="c-phone" type="tel" placeholder="TELEFON" class="w-full p-3 border rounded-lg" required>
                    </div>
                    <textarea id="c-issue" rows="3" placeholder="ŞİKAYET / TALEP" class="w-full p-3 border rounded-lg" required></textarea>
                    <button type="submit" class="w-full bg-brand-primary hover:bg-orange-700 text-white font-bold py-4 rounded-lg shadow-lg">Kaydı Gönder</button>
                </form>
            </div>
        </div>
    </div>

    <div id="screen-status" class="fixed inset-0 z-[90] bg-gray-50 overflow-y-auto hidden">
        <div class="max-w-md mx-auto min-h-screen flex flex-col p-4 justify-center">
            <button onclick="returnToLanding()" class="absolute top-6 left-6 text-gray-500 font-bold flex items-center gap-2"><i class="fa-solid fa-arrow-left"></i> Geri</button>
            <div class="bg-white rounded-2xl shadow-xl border border-gray-200 fade-in text-center p-8">
                <h2 class="text-2xl font-bold text-gray-800 mb-4">Servis Durumu</h2>
                <div class="flex gap-2 mb-6">
                    <input type="text" id="status-plate" placeholder="PLAKA GİRİNİZ" class="w-full p-3 border rounded-lg font-bold uppercase text-center text-lg">
                    <button onclick="checkStatus()" class="bg-gray-800 text-white px-5 rounded-lg"><i class="fa-solid fa-arrow-right"></i></button>
                </div>
                <div id="status-result" class="hidden text-left bg-gray-50 p-5 rounded-xl border border-gray-200"></div>
            </div>
        </div>
    </div>

    <div id="screen-admin" class="fixed inset-0 z-[80] flex overflow-hidden hidden bg-gray-100">
        <aside class="w-20 lg:w-64 bg-brand-dark text-white flex flex-col z-20 shadow-2xl flex-shrink-0">
            <div class="h-20 flex items-center justify-center lg:justify-start lg:px-6 border-b border-gray-700/50 bg-gray-900">
                <i class="fa-solid fa-wrench text-brand-primary text-xl"></i>
                <div class="hidden lg:block ml-3 font-bold">GÜVEN MOTO</div>
            </div>
            <nav class="flex-1 py-6 px-3 space-y-2 overflow-y-auto">
                <button onclick="router('dashboard')" class="nav-btn w-full flex items-center p-3 rounded-lg hover:bg-white/10 text-gray-400 bg-brand-primary text-white"><i class="fa-solid fa-chart-pie text-lg w-8"></i><span class="hidden lg:block">Genel Bakış</span></button>
                <button onclick="router('kanban')" class="nav-btn w-full flex items-center p-3 rounded-lg hover:bg-white/10 text-gray-400"><i class="fa-solid fa-list-check text-lg w-8"></i><span class="hidden lg:block">İş Takip</span></button>
                <button onclick="router('requests')" class="nav-btn w-full flex items-center p-3 rounded-lg hover:bg-white/10 text-gray-400 relative">
                    <div class="relative w-8"><i class="fa-solid fa-bell text-lg"></i><span id="nav-badge" class="absolute top-0 right-1 w-2.5 h-2.5 bg-red-500 rounded-full hidden animate-pulse"></span></div>
                    <span class="hidden lg:block">Talepler</span>
                </button>
                <button onclick="router('archive')" class="nav-btn w-full flex items-center p-3 rounded-lg hover:bg-white/10 text-gray-400"><i class="fa-solid fa-box-archive text-lg w-8"></i><span class="hidden lg:block">Arşiv (Eski İşler)</span></button>
                
                <button onclick="router('inventory')" class="nav-btn w-full flex items-center p-3 rounded-lg hover:bg-white/10 text-gray-400"><i class="fa-solid fa-box-open text-lg w-8"></i><span class="hidden lg:block">Stok</span></button>
            </nav>
            <div class="p-4 bg-gray-900"><button onclick="logoutAdmin()" class="flex items-center gap-3 text-gray-400 hover:text-red-400 w-full p-2"><i class="fa-solid fa-right-from-bracket"></i> <span class="hidden lg:inline">Çıkış</span></button></div>
        </aside>
        <main class="flex-1 flex flex-col relative overflow-hidden">
            <header class="h-16 bg-white border-b border-gray-200 flex items-center justify-between px-4 lg:px-8 z-10 shadow-sm">
                <div class="flex items-center gap-4 flex-1">
                     <h2 id="page-title" class="text-xl font-bold text-gray-800 whitespace-nowrap hidden md:block">Panel</h2>
                     <div class="flex items-center bg-gray-100 rounded-lg p-1 ml-4 border focus-within:border-brand-primary w-full max-w-xs">
                        <input type="text" id="header-search-plate" placeholder="Geçmiş Ara (Plaka)" class="bg-transparent p-1 px-2 outline-none text-sm w-full font-bold uppercase">
                        <button onclick="searchHistoryFromHeader()" class="px-2 text-gray-500 hover:text-brand-primary"><i class="fa-solid fa-search"></i></button>
                     </div>
                </div>
                <div class="flex gap-2">
                    <button id="btn-toggle-notify" onclick="toggleNotifications()" class="text-xs bg-red-50 text-red-600 px-3 py-1 rounded-full border border-red-200 hover:bg-red-100 hidden md:flex items-center gap-1 font-bold">
                        <i class="fa-solid fa-bell-slash"></i> Bildirimleri Aç
                    </button>
                    <button onclick="newJobModal()" class="px-4 py-2 bg-brand-dark hover:bg-gray-800 text-white rounded-lg font-bold text-sm flex items-center gap-2"><i class="fa-solid fa-plus"></i> <span class="hidden md:inline">Yeni İş</span></button>
                </div>
            </header>
            <div id="content-area" class="flex-1 overflow-x-hidden overflow-y-auto p-4 md:p-6 space-y-6 bg-gray-100">
                <div id="view-dashboard" class="page-view"><div class="grid grid-cols-2 md:grid-cols-4 gap-4" id="dashboard-stats"></div><div class="bg-white p-6 rounded-xl border border-gray-200 shadow-sm mt-6 h-64 relative"><canvas id="revenueChart"></canvas></div></div>
                <div id="view-kanban" class="page-view hidden h-full"><div class="flex gap-4 overflow-x-auto pb-4 h-full snap-x" id="kanban-container"></div></div>
                <div id="view-requests" class="page-view hidden"><div id="requests-container" class="grid grid-cols-1 md:grid-cols-3 gap-4"></div></div>
                
                <div id="view-archive" class="page-view hidden">
                    <div class="bg-white rounded-xl border border-gray-200 p-6">
                        <h3 class="font-bold text-lg mb-4 text-brand-dark">Arşivlenmiş ve Biten İşler</h3>
                        <div class="overflow-x-auto">
                            <table class="w-full text-sm text-left">
                                <thead class="bg-gray-100 text-gray-600 uppercase">
                                    <tr><th class="p-3">Tarih</th><th class="p-3">Plaka</th><th class="p-3">Model</th><th class="p-3">Müşteri</th><th class="p-3 text-right">Tutar</th><th class="p-3"></th></tr>
                                </thead>
                                <tbody id="archive-table-body"></tbody>
                            </table>
                        </div>
                    </div>
                </div>

                <div id="view-inventory" class="page-view hidden"><div class="bg-white rounded-xl border border-gray-200 p-6"><div class="flex justify-between mb-4"><h3 class="font-bold">Stok</h3><button onclick="addStockItem()" class="bg-brand-dark text-white px-3 py-1 rounded text-sm">+ Ekle</button></div><table class="w-full text-sm text-left"><thead class="bg-gray-100"><tr><th class="p-3">Ürün</th><th class="p-3">Adet</th><th class="p-3">Fiyat</th><th class="p-3"></th></tr></thead><tbody id="inventory-table-body"></tbody></table></div></div>
            </div>
        </main>
    </div>

    <div id="modal-overlay" class="fixed inset-0 bg-gray-900/70 hidden z-[150] flex items-center justify-center p-4">
        <div class="bg-white w-full md:max-w-6xl md:max-h-[90vh] rounded-2xl shadow-2xl flex flex-col overflow-hidden">
            <div class="p-4 border-b flex justify-between bg-gray-50">
                <h3 class="font-bold text-lg">İş Emri Detay</h3>
                <button onclick="closeModal()" class="text-gray-500 hover:text-red-500"><i class="fa-solid fa-times text-xl"></i></button>
            </div>
            <div id="modal-content" class="p-6 overflow-y-auto flex-1 bg-white"></div>
        </div>
    </div>

    <div id="modal-history" class="fixed inset-0 bg-gray-900/80 hidden z-[160] flex items-center justify-center p-4">
        <div class="bg-white w-full md:max-w-3xl max-h-[80vh] rounded-2xl shadow-2xl flex flex-col overflow-hidden">
            <div class="p-4 border-b flex justify-between bg-brand-dark text-white">
                <h3 class="font-bold text-lg"><i class="fa-solid fa-clock-rotate-left"></i> Servis Geçmişi</h3>
                <button onclick="document.getElementById('modal-history').classList.add('hidden')" class="text-gray-300 hover:text-white"><i class="fa-solid fa-times text-xl"></i></button>
            </div>
            <div id="history-content" class="p-6 overflow-y-auto flex-1 bg-gray-50"></div>
        </div>
    </div>
    
    <div id="modal-login" class="fixed inset-0 bg-gray-900/90 hidden z-[200] flex items-center justify-center p-4">
        <div class="bg-white p-8 rounded-2xl w-full max-w-sm text-center">
            <h3 class="font-bold mb-4">Personel Girişi</h3>
            <p class="text-xs text-gray-500 mb-4">Yetkili Personel Girişi</p>
            <input type="password" id="admin-pass" class="w-full p-3 border rounded-lg mb-4 text-center" placeholder="Şifre">
            <button onclick="performLogin()" class="w-full bg-brand-dark text-white py-3 rounded-lg font-bold">Giriş Yap</button>
            <button onclick="document.getElementById('modal-login').classList.add('hidden')" class="w-full text-gray-400 text-sm mt-3">İptal</button>
        </div>
    </div>

    <script>
        // --- 1. FIREBASE CONFIG ---
        const firebaseConfig = {
          apiKey: "AIzaSyBfYblnqOz17bmINf97rDr4R0JcXAqjBvg",
          authDomain: "guvenmoto-f2ae6.firebaseapp.com",
          databaseURL: "https://guvenmoto-f2ae6-default-rtdb.firebaseio.com",
          projectId: "guvenmoto-f2ae6",
          storageBucket: "guvenmoto-f2ae6.firebasestorage.app",
          messagingSenderId: "22153270990",
          appId: "1:22153270990:web:d9fb5929a1897a4b5e229f",
          measurementId: "G-HK4EBEQNC9"
        };

        let db;
        let localData = { jobs: [], inventory: [] };
        let isAdmin = false;
        let lastRequestCount = 0;
        let tempJobParts = []; 
        let notificationEnabled = false;

        try {
            firebase.initializeApp(firebaseConfig);
            db = firebase.database();
            
            db.ref(".info/connected").on("value", (snap) => {
                const el = document.getElementById('connection-status');
                if (snap.val() === true) {
                    el.innerText = "Sistem: ONLINE";
                    el.className = "text-xs text-green-500 mt-2 font-bold font-mono";
                } else {
                    el.innerText = "Sistem: OFFLINE";
                    el.className = "text-xs text-red-500 mt-2 font-bold font-mono";
                }
            });
        } catch(e) { console.error(e); alert("Firebase Hatası!"); }

        window.addEventListener('DOMContentLoaded', () => {
            if(!db) return;
            const jobsRef = db.ref('jobs');
            const invRef = db.ref('inventory');

            jobsRef.on('value', (snapshot) => {
                const data = snapshot.val();
                localData.jobs = data ? Object.values(data) : [];
                checkNotifications();
                refreshUI();
            });

            invRef.on('value', (snapshot) => {
                const data = snapshot.val();
                localData.inventory = data ? Object.values(data) : [];
                if(isAdmin && !document.getElementById('view-inventory').classList.contains('hidden')) renderInventory();
            });

            if (Notification.permission === "granted") {
                notificationEnabled = true;
                updateNotificationButtonUI();
            }

            if(localStorage.getItem('isAdminLoggedIn') === 'true') showAdminPanel();
            else document.getElementById('screen-landing').classList.remove('hidden');
        });

        // --- AUTH ---
        function checkAdminSession() { document.getElementById('modal-login').classList.remove('hidden'); }
        function performLogin() {
            if(document.getElementById('admin-pass').value === '2009') {
                localStorage.setItem('isAdminLoggedIn', 'true');
                showAdminPanel();
            } else alert("Hatalı Şifre!");
        }
        function showAdminPanel() {
            isAdmin = true;
            document.getElementById('screen-landing').classList.add('hidden');
            document.getElementById('modal-login').classList.add('hidden');
            document.getElementById('screen-admin').classList.remove('hidden');
            lastRequestCount = localData.jobs.filter(j=>j.status==='request').length;
            router('dashboard');
        }
        function logoutAdmin() { localStorage.removeItem('isAdminLoggedIn'); location.reload(); }

        // --- NAV ---
        function openCustomerPortal() { document.getElementById('screen-landing').classList.add('hidden'); document.getElementById('screen-customer').classList.remove('hidden'); }
        function openStatusCheck() { document.getElementById('screen-landing').classList.add('hidden'); document.getElementById('screen-status').classList.remove('hidden'); }
        function returnToLanding() { document.getElementById('screen-customer').classList.add('hidden'); document.getElementById('screen-status').classList.add('hidden'); document.getElementById('screen-landing').classList.remove('hidden'); }

        function router(view) {
            document.querySelectorAll('.page-view').forEach(e => e.classList.add('hidden'));
            document.querySelectorAll('.nav-btn').forEach(e => {e.classList.remove('bg-brand-primary','text-white'); e.classList.add('text-gray-400')});
            document.getElementById('view-'+view).classList.remove('hidden');
            const activeBtn = document.querySelector(`button[onclick="router('${view}')"]`);
            if(activeBtn) activeBtn.classList.add('bg-brand-primary','text-white');
            refreshUI();
        }
        function refreshUI() {
            if(!isAdmin) return;
            if(!document.getElementById('view-dashboard').classList.contains('hidden')) renderDashboard();
            if(!document.getElementById('view-kanban').classList.contains('hidden')) renderKanban();
            if(!document.getElementById('view-requests').classList.contains('hidden')) renderRequests();
            if(!document.getElementById('view-archive').classList.contains('hidden')) renderArchive(); // ARŞİV EKLENDİ
        }

        // --- BİLDİRİM SİSTEMİ ---
        function updateNotificationButtonUI() {
            const btn = document.getElementById('btn-toggle-notify');
            if(notificationEnabled) {
                btn.innerHTML = '<i class="fa-solid fa-bell"></i> Bildirimleri Kapat';
                btn.classList.remove('bg-red-50', 'text-red-600', 'border-red-200');
                btn.classList.add('bg-green-50', 'text-green-600', 'border-green-200');
            } else {
                btn.innerHTML = '<i class="fa-solid fa-bell-slash"></i> Bildirimleri Aç';
                btn.classList.remove('bg-green-50', 'text-green-600', 'border-green-200');
                btn.classList.add('bg-red-50', 'text-red-600', 'border-red-200');
            }
        }

        function toggleNotifications() {
            if(notificationEnabled) {
                notificationEnabled = false;
                updateNotificationButtonUI();
                showToast("Bildirimler KAPATILDI.");
            } else {
                if (!("Notification" in window)) return alert("Tarayıcı desteklemiyor.");
                if (Notification.permission === "granted") {
                    notificationEnabled = true; playUnlockSound(); updateNotificationButtonUI(); showToast("Bildirimler AÇIK.");
                } else if (Notification.permission !== "denied") {
                    Notification.requestPermission().then(permission => {
                        if (permission === "granted") { notificationEnabled = true; playUnlockSound(); updateNotificationButtonUI(); showToast("Bildirimler AÇILDI!"); }
                    });
                } else { alert("Tarayıcı ayarlarından izin verin."); }
            }
        }

        function playUnlockSound() {
            const audio = document.getElementById('notification-sound');
            audio.volume = 0; 
            audio.play().then(() => { audio.pause(); audio.currentTime = 0; audio.volume = 1; }).catch(() => {});
        }

        function checkNotifications() {
            if(!isAdmin) return;
            const count = localData.jobs.filter(j => j.status === 'request').length;
            if(count > lastRequestCount && lastRequestCount !== 0) {
                if(notificationEnabled) {
                    const audio = document.getElementById('notification-sound');
                    audio.play().catch(e => console.log("Ses hatası:", e));
                    if (Notification.permission === "granted") new Notification("GÜVEN MOTO", { body: "Yeni Talep Var!" });
                }
                showToast("Yeni Müşteri Talebi!");
            }
            lastRequestCount = count;
            const b = document.getElementById('nav-badge');
            if(count>0) { b.classList.remove('hidden'); b.innerText = count;} else b.classList.add('hidden');
        }

        // --- ARŞİV LİSTESİ ---
        function renderArchive() {
            const list = localData.jobs.filter(j => j.status === 'archived' || j.status === 'completed').sort((a,b) => b.id - a.id);
            document.getElementById('archive-table-body').innerHTML = list.length ? list.map(j => {
                const total = (j.labor||0) + (j.parts?.reduce((a,b)=>a+Number(b.price),0)||0);
                const statusBadge = j.status === 'archived' 
                    ? '<span class="px-2 py-1 bg-gray-200 text-gray-600 text-xs rounded font-bold">Arşivde</span>' 
                    : '<span class="px-2 py-1 bg-green-100 text-green-700 text-xs rounded font-bold">Bitti (Ekranda)</span>';
                return `
                <tr class="border-b hover:bg-gray-50 transition">
                    <td class="p-3 text-sm text-gray-500">${j.date}</td>
                    <td class="p-3 font-bold">${j.plate}</td>
                    <td class="p-3">${j.model}</td>
                    <td class="p-3">${j.customer}</td>
                    <td class="p-3 text-right font-bold text-brand-primary">${total}₺</td>
                    <td class="p-3 text-right">
                        ${statusBadge}
                        <button onclick="openDetail(${j.id})" class="ml-2 text-blue-600 hover:text-blue-800"><i class="fa-solid fa-eye"></i></button>
                    </td>
                </tr>`;
            }).join('') : '<tr><td colspan="6" class="p-6 text-center text-gray-400">Arşivde kayıt yok.</td></tr>';
        }

        // --- GEÇMİŞ (HISTORY) & ARŞİV SİSTEMİ ---
        function searchHistoryFromHeader() {
            const plate = document.getElementById('header-search-plate').value;
            showHistory(plate);
        }

        function showHistory(plate) {
            if(!plate) return alert("Lütfen plaka giriniz.");
            const cleanPlate = plate.toUpperCase().replace(/\s/g, '');
            const historyList = localData.jobs.filter(j => 
                j.plate.toUpperCase().replace(/\s/g, '') === cleanPlate && 
                ['completed', 'archived'].includes(j.status)
            ).sort((a,b) => b.id - a.id);

            const modal = document.getElementById('modal-history');
            const content = document.getElementById('history-content');
            
            modal.classList.remove('hidden');

            if(historyList.length === 0) {
                content.innerHTML = `<div class="text-center text-gray-500 py-10"><i class="fa-solid fa-file-circle-xmark text-4xl mb-4"></i><br>Kayıt bulunamadı.</div>`;
                return;
            }

            content.innerHTML = `
                <h4 class="font-bold text-xl mb-4 text-brand-primary border-b pb-2">${plate.toUpperCase()} - Servis Geçmişi</h4>
                <div class="space-y-4">
                    ${historyList.map(h => {
                        const total = (h.labor||0) + (h.parts?.reduce((a,b)=>a+Number(b.price),0)||0);
                        const statusText = h.status === 'archived' ? '(Arşivlenmiş)' : '';
                        return `
                        <div class="bg-white p-4 rounded-lg shadow border-l-4 border-brand-primary">
                            <div class="flex justify-between items-start mb-2">
                                <div>
                                    <span class="text-xs text-gray-400 font-bold block">${h.date} ${statusText}</span>
                                    <span class="font-bold text-gray-800">${h.model}</span>
                                </div>
                                <span class="font-bold text-lg text-brand-primary">${total} ₺</span>
                            </div>
                            <div class="bg-gray-50 p-2 rounded text-sm text-gray-600 mb-2">
                                <span class="font-bold text-xs text-gray-400 block">ŞİKAYET:</span> ${h.issue}
                            </div>
                            <div class="text-sm">
                                <span class="font-bold text-xs text-gray-400 block">YAPILANLAR:</span>
                                <ul class="list-disc list-inside text-gray-700">
                                    ${(h.parts||[]).map(p=>`<li>${p.name} (${p.price}₺)</li>`).join('')}
                                    ${h.parts?.length===0 ? '<li>Parça değişimi yok</li>' : ''}
                                </ul>
                                <div class="mt-1 text-xs text-right text-gray-400">İşçilik: ${h.labor}₺</div>
                            </div>
                        </div>
                        `;
                    }).join('')}
                </div>
            `;
        }

        // --- DASHBOARD & KANBAN ---
        function renderDashboard() {
            const stats = document.getElementById('dashboard-stats');
            const total = localData.jobs.reduce((a,b)=>a+(b.labor||0)+(b.parts?.reduce((x,y)=>x+y.price,0)||0),0);
            stats.innerHTML = `
                <div class="bg-white p-5 rounded-xl border shadow-sm"><p class="text-xs text-gray-500 font-bold">CİRO</p><h3 class="text-2xl font-black">${total} ₺</h3></div>
                <div class="bg-white p-5 rounded-xl border shadow-sm"><p class="text-xs text-gray-500 font-bold">SERVİSTE</p><h3 class="text-2xl font-black text-blue-600">${localData.jobs.filter(j=>['pending','in-progress'].includes(j.status)).length}</h3></div>
                <div class="bg-white p-5 rounded-xl border shadow-sm"><p class="text-xs text-gray-500 font-bold">BEKLEYEN</p><h3 class="text-2xl font-black text-orange-600">${localData.jobs.filter(j=>j.status==='request').length}</h3></div>
                <div class="bg-white p-5 rounded-xl border shadow-sm"><p class="text-xs text-gray-500 font-bold">TESLİM</p><h3 class="text-2xl font-black text-green-600">${localData.jobs.filter(j=>j.status==='completed').length}</h3></div>
            `;
            const ctx = document.getElementById('revenueChart');
            if(window.myChart) window.myChart.destroy();
            window.myChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: ['Hizmet', 'Parça'],
                    datasets: [{
                        label: 'Gelir',
                        data: [ localData.jobs.reduce((a,b)=>a+(b.labor||0),0), localData.jobs.reduce((a,b)=>a+(b.parts?.reduce((x,y)=>x+y.price,0)||0),0) ],
                        backgroundColor: ['#ea580c', '#374151']
                    }]
                }, options: { responsive: true, maintainAspectRatio: false }
            });
        }

        function renderKanban() {
            const cols = ['pending', 'in-progress', 'ready', 'completed'];
            const names = {'pending':'SIRADA', 'in-progress':'İŞLEMDE', 'ready':'HAZIR', 'completed':'BİTTİ'};
            const colors = {'pending':'border-gray-400', 'in-progress':'border-blue-500', 'ready':'border-green-500', 'completed':'border-black'};
            
            document.getElementById('kanban-container').innerHTML = cols.map(s => `
                <div class="min-w-[280px] md:flex-1 kanban-col snap-center border-t-4 ${colors[s]}">
                    <h3 class="font-bold mb-4 uppercase text-xs tracking-wider">${names[s]} (${localData.jobs.filter(j=>j.status===s).length})</h3>
                    <div class="space-y-3">
                        ${localData.jobs.filter(j=>j.status===s).map(j => `
                            <div onclick="openDetail(${j.id})" class="bg-white p-4 rounded-xl shadow-sm cursor-pointer hover:shadow-md">
                                <div class="font-bold flex justify-between text-lg"><span>${j.plate}</span><span class="text-brand-primary font-black">${(j.labor||0)+(j.parts?.reduce((a,b)=>a+b.price,0)||0)}₺</span></div>
                                <div class="text-sm text-gray-700 font-semibold truncate">${j.model}</div>
                                <div class="text-xs text-gray-400 mt-2">${j.customer}</div>
                            </div>
                        `).join('')}
                    </div>
                </div>
            `).join('');
        }

        function renderRequests() {
            const list = localData.jobs.filter(j => j.status === 'request').sort((a,b) => b.id - a.id);
            document.getElementById('requests-container').innerHTML = list.length ? list.map(j => `
                <div class="bg-white p-5 rounded-xl border shadow-sm relative">
                    <div class="absolute top-3 right-3 bg-red-100 text-red-600 text-xs font-bold px-2 py-1 rounded animate-pulse">YENİ</div>
                    <div class="flex justify-between items-center mb-2">
                        <h4 class="font-bold text-lg">${j.plate}</h4>
                        <button onclick="showHistory('${j.plate}')" class="text-xs bg-blue-50 text-blue-600 px-2 py-1 rounded border border-blue-200 font-bold hover:bg-blue-100"><i class="fa-solid fa-clock-rotate-left"></i> Geçmiş</button>
                    </div>
                    <p class="text-gray-600 mb-2">${j.model}</p>
                    <p class="text-sm bg-gray-50 p-2 rounded mb-4 italic">"${j.issue}"</p>
                    <div class="grid grid-cols-2 gap-2">
                        <button onclick="deleteJob(${j.id})" class="border border-gray-300 rounded py-2 text-sm font-bold hover:bg-gray-50">Reddet</button>
                        <button onclick="approveJob(${j.id})" class="bg-brand-primary text-white rounded py-2 text-sm font-bold hover:bg-orange-700">Onayla</button>
                    </div>
                </div>
            `).join('') : '<div class="col-span-3 text-center text-gray-400 py-10">Yeni talep yok.</div>';
            updateBadge();
        }

        // --- İŞLEMLER ---
        function approveJob(id) { db.ref('jobs/'+id).update({status: 'pending'}); showToast("Onaylandı!"); }
        
        function deleteJob(id) { 
            if(confirm("DİKKAT: Bu kayıt tamamen silinecek! Geçmişten de silinir. Emin misiniz?")) { 
                db.ref('jobs/'+id).remove(); 
                closeModal(); 
            } 
        }

        function archiveJob(id) {
            if(confirm("Bu işlem ekrandan kaldırıp ARŞİVE atar. Geçmişte görebilirsiniz. Onaylıyor musunuz?")) {
                db.ref('jobs/'+id).update({status: 'archived'});
                closeModal();
                showToast("Arşivlendi");
            }
        }

        // --- DETAY MODALI ---
        function openDetail(id) {
            const j = localData.jobs.find(x => x.id === id);
            if(!j) return;
            
            tempJobParts = j.parts ? JSON.parse(JSON.stringify(j.parts)) : [];

            document.getElementById('modal-overlay').classList.remove('hidden');
            
            const archiveBtnHTML = j.status === 'completed' 
                ? `<button onclick="archiveJob(${id})" class="bg-gray-500 hover:bg-gray-600 text-white px-4 py-2 rounded font-bold transition">Ekrandan Kaldır (Arşivle)</button>` 
                : '';

            document.getElementById('modal-content').innerHTML = `
                <div class="grid md:grid-cols-2 gap-6 h-full">
                    <div class="space-y-4 bg-gray-50 p-5 rounded-xl border border-gray-200">
                        <div class="flex justify-between items-center border-b pb-2">
                             <h4 class="font-bold text-gray-700">Araç Bilgileri</h4>
                             <button onclick="showHistory('${j.plate}')" class="text-xs bg-white border border-gray-300 px-2 py-1 rounded hover:bg-gray-100 font-bold"><i class="fa-solid fa-clock-rotate-left"></i> GEÇMİŞİ GÖR</button>
                        </div>
                        
                        <div><label class="block text-xs font-bold text-gray-500 mb-1">PLAKA</label>
                        <input id="d-plate" value="${j.plate}" class="w-full border p-2 rounded font-bold uppercase bg-white"></div>
                        
                        <div><label class="block text-xs font-bold text-gray-500 mb-1">MODEL</label>
                        <input id="d-model" value="${j.model}" class="w-full border p-2 rounded bg-white"></div>
                        
                        <div><label class="block text-xs font-bold text-gray-500 mb-1">MÜŞTERİ</label>
                        <input id="d-cust" value="${j.customer}" class="w-full border p-2 rounded bg-white"></div>

                        <div><label class="block text-xs font-bold text-gray-500 mb-1">TELEFON</label>
                        <input id="d-phone" value="${j.phone}" class="w-full border p-2 rounded bg-white"></div>
                        
                        <div>
                            <label class="block text-xs font-bold text-gray-500 mb-1">DURUM</label>
                            <div class="flex gap-2">
                                <select id="d-status" onchange="updateWhatsappBtn(${id})" class="w-full border p-2 rounded font-bold bg-white">
                                    <option value="pending" ${j.status==='pending'?'selected':''}>Sırada (Bekliyor)</option>
                                    <option value="in-progress" ${j.status==='in-progress'?'selected':''}>İşlemde (Liftte)</option>
                                    <option value="ready" ${j.status==='ready'?'selected':''}>Hazır (Teslim Bekliyor)</option>
                                    <option value="completed" ${j.status==='completed'?'selected':''}>Tamamlandı</option>
                                    <option value="archived" disabled ${j.status==='archived'?'selected':''}>Arşivlenmiş</option>
                                </select>
                                <a id="btn-whatsapp" href="#" target="_blank" class="bg-green-500 hover:bg-green-600 text-white p-2 rounded w-12 flex items-center justify-center">
                                    <i class="fa-brands fa-whatsapp text-xl"></i>
                                </a>
                            </div>
                        </div>
                    </div>
                    
                    <div class="flex flex-col">
                        <h4 class="font-bold border-b pb-2 mb-2">Parçalar & İşçilik</h4>
                        <div id="parts-list-area" class="flex-1 bg-gray-50 p-3 rounded mb-2 overflow-y-auto min-h-[150px] border border-gray-200"></div>
                        
                        <div class="flex gap-2 mb-4 bg-white p-2 border rounded shadow-sm">
                            <input id="add-p-name" placeholder="Yapılan İş / Parça" class="border p-2 w-full text-sm rounded">
                            <input id="add-p-price" placeholder="Fiyat" type="number" class="border p-2 w-24 text-sm rounded font-bold">
                            <button onclick="addPartLocal()" class="bg-gray-800 text-white px-4 rounded hover:bg-black font-bold">+</button>
                        </div>
                        
                        <div class="flex justify-between items-center mb-6 bg-brand-primary/10 p-3 rounded border border-brand-primary/20">
                            <label class="text-sm font-bold text-brand-primary">İŞÇİLİK ÜCRETİ:</label>
                            <div class="flex items-center gap-1">
                                <input id="d-labor" type="number" value="${j.labor||0}" onchange="updateWhatsappBtn(${id})" class="w-24 border p-2 rounded font-bold text-right text-lg">
                                <span class="font-bold">₺</span>
                            </div>
                        </div>

                        <div class="flex justify-end gap-2 pt-4 border-t flex-wrap">
                            <button onclick="deleteJob(${id})" class="text-red-500 font-bold px-4 py-2 hover:bg-red-50 rounded transition">SİL</button>
                            ${archiveBtnHTML}
                            <button onclick="printInvoice(${id})" class="border border-gray-300 px-4 py-2 rounded font-bold hover:bg-gray-50 transition">Yazdır</button>
                            <button onclick="saveJob(${id})" class="bg-brand-primary text-white px-6 py-2 rounded font-bold hover:bg-orange-700 shadow-lg transition transform active:scale-95">Kaydet</button>
                        </div>
                    </div>
                </div>
            `;
            
            renderModalParts();
            updateWhatsappBtn(id);
        }

        // --- YEREL PARÇA YÖNETİMİ ---
        function renderModalParts() {
            const container = document.getElementById('parts-list-area');
            if(!container) return;
            container.innerHTML = tempJobParts.map((p,i) => `
                <div class="flex justify-between border-b py-2 items-center">
                    <span class="font-medium">${p.name}</span>
                    <div class="flex items-center gap-2">
                        <span class="font-bold">${p.price}₺</span> 
                        <button onclick="removePartLocal(${i})" class="text-red-500 bg-red-50 p-1 rounded hover:bg-red-100"><i class="fa-solid fa-trash"></i></button>
                    </div>
                </div>`).join('');
        }
        function addPartLocal() {
            const name = document.getElementById('add-p-name').value;
            const price = Number(document.getElementById('add-p-price').value);
            if(!name) return alert("Parça adı giriniz");
            tempJobParts.push({name, price});
            renderModalParts();
            document.getElementById('add-p-name').value = '';
            document.getElementById('add-p-price').value = '';
        }
        function removePartLocal(idx) { tempJobParts.splice(idx, 1); renderModalParts(); }

        // --- KAYDET ---
        function saveJob(id) {
            const updates = {
                plate: document.getElementById('d-plate').value.toUpperCase(),
                model: document.getElementById('d-model').value,
                customer: document.getElementById('d-cust').value,
                phone: document.getElementById('d-phone').value,
                status: document.getElementById('d-status').value,
                labor: Number(document.getElementById('d-labor').value),
                parts: tempJobParts 
            };
            db.ref('jobs/'+id).update(updates).then(() => { closeModal(); showToast("Kayıt Başarılı!"); }).catch(e => alert("Hata: " + e.message));
        }

        // --- WHATSAPP (FIXED API LINK) ---
        function updateWhatsappBtn(id) {
            const status = document.getElementById('d-status').value;
            const labor = Number(document.getElementById('d-labor').value || 0);
            const phoneVal = document.getElementById('d-phone').value;
            const btn = document.getElementById('btn-whatsapp');
            const partsTotal = tempJobParts.reduce((a,b) => a + Number(b.price), 0);
            const total = labor + partsTotal;
            let msg = "";
            if(status === 'pending') msg = "Motosikletiniz servis sırasına alınmıştır.";
            if(status === 'in-progress') msg = "Motosikletiniz işleme alınmıştır, tamir süreci başlamıştır.";
            if(status === 'ready') msg = `Motosikletinizin işlemleri tamamlanmıştır. Toplam Tutar: ${total} TL. Teslim alabilirsiniz.`;
            if(status === 'completed') msg = "Bizi tercih ettiğiniz için teşekkür ederiz. Güvenli sürüşler.";
            const cleanPhone = phoneVal.replace(/[^0-9]/g, '');
            if(cleanPhone.length > 9) {
                let targetPhone = cleanPhone; if(targetPhone.length === 10) targetPhone = "90" + targetPhone; 
                btn.href = `https://api.whatsapp.com/send?phone=${targetPhone}&text=${encodeURIComponent(msg)}`;
                btn.classList.remove('opacity-50', 'pointer-events-none');
            } else {
                btn.href = "#"; btn.classList.add('opacity-50', 'pointer-events-none');
            }
        }

        // --- DİĞER ---
        function submitCustomerRequest(e) { const id = Date.now(); const newJob = { id: id, status: 'request', date: new Date().toLocaleDateString('tr-TR'), plate: document.getElementById('c-plate').value.toUpperCase(), model: document.getElementById('c-model').value, customer: document.getElementById('c-name').value, phone: document.getElementById('c-phone').value, issue: document.getElementById('c-issue').value, parts: [], labor: 0 }; db.ref('jobs/' + id).set(newJob).then(() => { alert("Talebiniz alınmıştır."); e.target.reset(); returnToLanding(); }); e.preventDefault(); }
        function renderInventory() { document.getElementById('inventory-table-body').innerHTML = localData.inventory.map(i => `<tr class="border-b"><td class="p-3 font-bold">${i.name}</td><td class="p-3 bg-gray-50 text-center font-bold rounded">${i.stock}</td><td class="p-3 text-right">${i.price}₺</td><td class="p-3 text-right"><button onclick="deleteStock(${i.id})" class="text-red-500 hover:bg-red-50 p-2 rounded"><i class="fa-solid fa-trash"></i></button></td></tr>`).join(''); }
        function addStockItem() { const n = prompt("Parça Adı:"); if(!n) return; const s = prompt("Stok Adedi:"); const p = prompt("Satış Fiyatı:"); const id = Date.now(); db.ref('inventory/'+id).set({id, name:n, stock:Number(s), price:Number(p)}); }
        function deleteStock(id) { if(confirm("Stok silinsin mi?")) db.ref('inventory/'+id).remove(); }
        function checkStatus() {
            const plate = document.getElementById('status-plate').value.toUpperCase().replace(/\s/g,'');
            const job = localData.jobs.filter(j => j.plate.replace(/\s/g,'') === plate).sort((a,b)=>b.id-a.id)[0];
            const div = document.getElementById('status-result'); div.classList.remove('hidden');
            if(job) {
                const total = (job.labor||0) + (job.parts?.reduce((a,b)=>a+b.price,0)||0);
                const statusMap = {'request':'Talep Alındı', 'pending':'Sırada', 'in-progress':'İşlemde', 'ready':'Hazır', 'completed':'Tamamlandı', 'archived':'Arşivde'};
                div.innerHTML = `<h3 class="font-bold text-lg">${job.model}</h3><p class="text-brand-primary font-bold text-xl my-2">${statusMap[job.status]}</p><p class="text-sm">Tutar: ${total} ₺</p>`;
            } else { div.innerHTML = `<p class="text-red-500 font-bold">Kayıt bulunamadı.</p>`; }
        }
        function showToast(msg) { const t = document.createElement('div'); t.className = 'bg-gray-800 text-white px-4 py-2 rounded shadow-lg animate-fade border border-gray-600'; t.innerText = msg; document.getElementById('toast-container').appendChild(t); setTimeout(()=>t.remove(), 3000); }
        function closeModal() { document.getElementById('modal-overlay').classList.add('hidden'); }
        function newJobModal() { const id = Date.now(); db.ref('jobs/'+id).set({id, status:'pending', date:new Date().toLocaleDateString('tr-TR'), plate:'', model:'', customer:'', phone:'', issue:'', parts:[], labor:0}); setTimeout(()=>openDetail(id), 300); }
        function printInvoice(id) { const j = localData.jobs.find(x=>x.id===id); const total = (j.labor||0) + (j.parts?.reduce((a,b)=>a+b.price,0)||0); const w = window.open('', '', 'height=600,width=800'); w.document.write(`<html><body style="font-family:sans-serif; padding:40px;"><h2>GÜVEN MOTO</h2><p>${j.plate} - ${j.customer}</p><hr>${(j.parts||[]).map(p=>`<div style="display:flex;justify-content:space-between"><span>${p.name}</span><span>${p.price}₺</span></div>`).join('')}<div style="display:flex;justify-content:space-between"><span>İşçilik</span><span>${j.labor}₺</span></div><hr><h3>TOPLAM: ${total}₺</h3></body></html>`); w.print(); }
    </script>
</body>
</html>
https://guvenmoto.netlify.app/ 
Firebase
