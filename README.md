<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Master Kosakata Jepang - 15 BAB Lengkap</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Library WanaKana untuk konversi Romaji ke Kana otomatis -->
    <script src="https://unpkg.com/wanakana@2.3.1/wanakana.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&family=Noto+Sans+JP:wght@400;700&display=swap');
        
        body {
            font-family: 'Inter', 'Noto Sans JP', sans-serif;
            -webkit-font-smoothing: antialiased;
        }

        .japanese-text {
            font-family: 'Noto Sans JP', sans-serif;
        }

        .correct-pulse {
            animation: pulse-green 0.5s ease-in-out;
        }

        @keyframes pulse-green {
            0% { transform: scale(1); }
            50% { transform: scale(1.05); background-color: #dcfce7; }
            100% { transform: scale(1); }
        }

        .fade-in {
            animation: fadeIn 0.4s ease-out;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* Mengatur scrollbar untuk menu */
        #menu-view::-webkit-scrollbar {
            width: 6px;
        }
        #menu-view::-webkit-scrollbar-thumb {
            background-color: #e2e8f0;
            border-radius: 10px;
        }
    </style>
</head>
<body class="bg-slate-50 min-h-screen flex items-center justify-center p-4">

    <!-- Layar Pemuatan (Loading) -->
    <div id="app-loading" class="fixed inset-0 bg-white z-50 flex items-center justify-center">
        <div class="text-center">
            <div class="inline-block w-8 h-8 border-4 border-red-600 border-t-transparent rounded-full animate-spin mb-2"></div>
            <p class="text-slate-500 text-sm italic">Memuat data kuis...</p>
        </div>
    </div>

    <div class="max-w-md w-full bg-white rounded-3xl shadow-xl overflow-hidden border border-slate-100 hidden flex flex-col max-h-[90vh]" id="main-app">
        
        <!-- View 1: Menu Pemilihan BAB -->
        <div id="menu-view" class="p-6 overflow-y-auto">
            <div class="text-center mb-6">
                <h1 class="text-2xl font-bold text-slate-800 tracking-tight text-red-600">Menu Belajar</h1>
                <p class="text-slate-500 text-sm italic">Minna no Nihongo (BAB 1 - 15)</p>
            </div>
            <div class="grid grid-cols-2 gap-3" id="bab-list">
                <!-- Tombol BAB akan dibuat secara dinamis oleh JavaScript -->
            </div>
        </div>

        <!-- View 2: Area Kuis Aktif -->
        <div id="quiz-view" class="hidden flex-1 flex flex-col">
            <!-- Header Kuis -->
            <div class="bg-red-600 p-6 text-white text-center relative shrink-0">
                <button onclick="backToMenu()" class="absolute left-4 top-6 opacity-80 hover:opacity-100 transition-opacity">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7" />
                    </svg>
                </button>
                <h1 id="current-bab-title" class="text-xl font-bold mb-1 italic text-white">BAB</h1>
                <div class="w-full bg-red-800 rounded-full h-2 mt-4">
                    <div id="progress-bar" class="bg-white h-2 rounded-full transition-all duration-500" style="width: 0%"></div>
                </div>
                <div class="flex justify-between mt-2 text-xs font-medium opacity-80">
                    <span id="progress-text">问题 1 / 0</span>
                    <span id="score-text">Skor: 0</span>
                </div>
            </div>

            <!-- Area Pertanyaan -->
            <div class="p-8 flex-1 flex flex-col justify-center">
                <div class="text-center fade-in">
                    <p class="text-slate-500 text-sm font-semibold uppercase tracking-wider mb-2">Bahasa Jepangnya:</p>
                    <h2 id="display-definition" class="text-3xl font-bold text-slate-800 mb-8 min-h-[5rem] flex items-center justify-center px-4 leading-tight">
                        ...
                    </h2>
                    
                    <div class="relative mb-4">
                        <input type="text" id="user-input" 
                            class="japanese-text w-full px-4 py-4 text-center text-3xl font-bold border-2 border-slate-200 rounded-2xl focus:border-red-500 focus:outline-none transition-colors"
                            placeholder="Tulis Hiragana/Katakana..."
                            autocomplete="off"
                            autocapitalize="none">
                        
                        <!-- Notifikasi Status -->
                        <div id="status-notif" class="hidden absolute -top-10 left-0 right-0 font-bold flex items-center justify-center gap-2 fade-in">
                            <span id="notif-text"></span>
                        </div>
                    </div>

                    <!-- Tombol Skip -->
                    <button id="skip-button" class="text-slate-400 hover:text-slate-600 text-sm font-medium transition-colors flex items-center justify-center w-full gap-1 mb-4">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 5l7 7-7 7M5 5l7 7-7 7" />
                        </svg>
                        Lewati (Skip)
                    </button>

                    <div class="bg-blue-50 p-3 rounded-xl border border-blue-100">
                        <p class="text-blue-700 text-[10px] leading-relaxed">
                            <strong>Petunjuk:</strong> Ketik dalam huruf Latin (Romaji), sistem akan otomatis mengubahnya menjadi Hiragana atau Katakana.
                        </p>
                    </div>
                </div>
            </div>
        </div>

        <!-- View 3: Layar Hasil Akhir -->
        <div id="result-view" class="hidden p-8 text-center fade-in flex-1 flex flex-col justify-center">
            <div class="mb-6 text-center">
                <div class="inline-flex items-center justify-center w-20 h-20 bg-emerald-100 text-emerald-600 rounded-full mb-4">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                    </svg>
                </div>
                <h2 class="text-2xl font-bold text-slate-800">Luar Biasa!</h2>
                <p class="text-slate-500 mt-2">Materi BAB ini telah selesai kamu pelajari.</p>
                <div class="mt-4 bg-slate-50 p-4 rounded-xl border border-slate-100">
                    <p class="text-sm text-slate-400">Skor Akhir</p>
                    <p id="final-score" class="text-3xl font-black text-red-600">0</p>
                </div>
            </div>
            <div class="flex flex-col gap-3">
                <button onclick="startQuiz(currentBabIndex)" class="w-full bg-red-600 hover:bg-red-700 text-white font-bold py-4 rounded-2xl transition-all shadow-lg active:scale-95">
                    Ulangi BAB Ini
                </button>
                <button onclick="backToMenu()" class="w-full bg-slate-100 hover:bg-slate-200 text-slate-600 font-bold py-4 rounded-2xl transition-all active:scale-95">
                    Kembali ke Menu Utama
                </button>
            </div>
        </div>
    </div>

    <script>
        /**
         * BASIS DATA KOSAKATA (BAB 1 - 15)
         */
        const allData = {
            1: [
                { word: "わたし", definition: "Saya" }, { word: "あなた", definition: "Kamu" }, { word: "あのひと", definition: "Orang itu (biasa)" }, { word: "あのかた", definition: "Orang itu (sopan)" },
                { word: "さん", definition: "Saudara......" }, { word: "ちゃん", definition: "Panggilan untuk anak" }, { word: "じん", definition: "Orang......" }, { word: "せんせい", definition: "Guru, dosen" },
                { word: "きょうし", definition: "Guru, dosen (sendiri)" }, { word: "がくせい", definition: "Siswa, murid" }, { word: "かいしゃいん", definition: "Pegawai perusahaan" }, { word: "しゃいん", definition: "Pegawai perusahaan" },
                { word: "ぎんこういん", definition: "Pegawai bank" }, { word: "いしゃ", definition: "Dokter" }, { word: "けんきゅうしゃ", definition: "Peneliti" }, { word: "だいがく", definition: "Universitas" },
                { word: "びょういん", definition: "Rumah sakit" }, { word: "だれ", definition: "Siapa (biasa)" }, { word: "どなた", definition: "Siapa (sopan)" }, { word: "さい", definition: "Umur......" },
                { word: "なんさい", definition: "Umur berapa? (biasa)" }, { word: "おいくつ", definition: "Umur berapa? (sopan)" }, { word: "はい", definition: "Iya" }, { word: "いいえ", definition: "Tidak" },
                { word: "はじめまして", definition: "Perkenalkan" }, { word: "からきました", definition: "Datang dari......" }, { word: "どうぞよろしくおねがいします", definition: "Terimalah perkenalan saya" },
                { word: "しつれいですが", definition: "Permisi" }, { word: "おなまえは", definition: "Nama anda siapa?" }, { word: "アメリカ", definition: "Amerika" }, { word: "イギリス", definition: "Inggris" },
                { word: "インド", definition: "India" }, { word: "インドネシア", definition: "Indonesia" }, { word: "かんこく", definition: "Korea" }, { word: "ちゅうごく", definition: "China" },
                { word: "ドイツ", definition: "Jerman" }, { word: "にほん", definition: "Jepang" }, { word: "フランス", definition: "Perancis" }
            ],
            2: [
                { word: "これ", definition: "Ini" }, { word: "それ", definition: "Itu (dekat)" }, { word: "あれ", definition: "Itu (jauh)" }, { word: "この", definition: "～ini" },
                { word: "その", definition: "～itu (dekat)" }, { word: "あの", definition: "～itu (jauh)" }, { word: "ほん", definition: "Buku" }, { word: "じしょ", definition: "Kamus" },
                { word: "ざっし", definition: "Majalah" }, { word: "しんぶん", definition: "Koran" }, { word: "ノート", definition: "Buku catatan" }, { word: "てちょう", definition: "Buku agenda" },
                { word: "めいし", definition: "Kartu nama" }, { word: "カード", definition: "Kartu" }, { word: "えんぴつ", definition: "Pensil" }, { word: "ボールペン", definition: "Bolpoin" },
                { word: "シャープペンシル", definition: "Pensil mekanik" }, { word: "かぎ", definition: "Kunci" }, { word: "とけい", definition: "Jam, arloji" }, { word: "かさ", definition: "Payung" },
                { word: "かばん", definition: "Tas" }, { word: "テレビ", definition: "Televisi" }, { word: "ラジオ", definition: "Radio" }, { word: "カメラ", definition: "Kamera" },
                { word: "コンピューター", definition: "Komputer" }, { word: "くるま", definition: "Mobil" }, { word: "つくえ", definition: "Meja" }, { word: "いす", definition: "Kursi" },
                { word: "チョコレート", definition: "Coklat" }, { word: "コーヒー", definition: "Kopi" }, { word: "おみやげ", definition: "Oleh-oleh" }, { word: "えいご", definition: "Bahasa Inggris" },
                { word: "にほんご", definition: "Bahasa Jepang" }, { word: "ご", definition: "Bahasa ～" }, { word: "なん", definition: "Apa" }, { word: "そう", definition: "Begitu" },
                { word: "あのう", definition: "Eee ... (ragu)" }, { word: "えっ", definition: "Eh (terkejut)" }, { word: "どうぞ", definition: "Silakan" }, { word: "どうもありがとうございます", definition: "Terima kasih" },
                { word: "ですか", definition: "O, begitu, benarkah?" }, { word: "ちがいます", definition: "Bukan, Salah" }, { word: "あ", definition: "O, eh (sadar)" },
                { word: "これからおせわになります", definition: "Mulai sekarang mohon bantuannya" }, { word: "こちらこそよろしくおねがいします", definition: "Sama-sama" }
            ],
            3: [
                { word: "ここ", definition: "Disini" }, { word: "そこ", definition: "Disitu" }, { word: "あそこ", definition: "Disana" }, { word: "どこ", definition: "Dimana?" },
                { word: "こちら", definition: "Disini (sopan)" }, { word: "そちら", definition: "Disitu (sopan)" }, { word: "あちら", definition: "Disana (sopan)" }, { word: "どちら", definition: "Dimana? (sopan)" },
                { word: "きょうしつ", definition: "Ruang kelas" }, { word: "しょくどう", definition: "Kantin" }, { word: "じむしょ", definition: "Kantor" }, { word: "かいぎしつ", definition: "Ruang rapat" },
                { word: "うけつけ", definition: "Meja informasi" }, { word: "ロビー", definition: "Lobi" }, { word: "へや", definition: "Kamar" }, { word: "おてあらい", definition: "WC" },
                { word: "トイレ", definition: "Toilet" }, { word: "かいだん", definition: "Tangga" }, { word: "エレベーター", definition: "Lift" }, { word: "エスカレーター", definition: "Eskalator" },
                { word: "じどうはんばいき", definition: "Mesin jual otomatis" }, { word: "でんわ", definition: "Telepon" }, { word: "おくに", definition: "Negara" }, { word: "かいしゃ", definition: "Perusahaan" },
                { word: "うち", definition: "Rumah" }, { word: "くつ", definition: "Sepatu" }, { word: "ネクタイ", definition: "Dasi" }, { word: "ワイン", definition: "Anggur putih" },
                { word: "うりば", definition: "Tempat penjualan" }, { word: "ちか", definition: "Basement" }, { word: "かい", definition: "Lantai ～" }, { word: "なんがい", definition: "Lantai berapa?" },
                { word: "えん", definition: "～yen" }, { word: "いくら", definition: "Berapa harganya?" }, { word: "ひゃく", definition: "Seratus" }, { word: "せん", definition: "Seribu" },
                { word: "まん", definition: "Puluh ribu" }, { word: "すみません", definition: "Maaf" }, { word: "どうも", definition: "Terima kasih" }, { word: "いらっしゃいませ", definition: "Selamat datang" },
                { word: "みせてください", definition: "Tolong perlihatkan" }, { word: "じゃ", definition: "Kalau begitu" }, { word: "をください", definition: "Tolong ～" }, { word: "イタリア", definition: "Italia" },
                { word: "スイス", definition: "Swiss" }, { word: "フランス", definition: "Perancis" }, { word: "ジャカルタ", definition: "Jakarta" }, { word: "バンコク", definition: "Bangkok" },
                { word: "ベルリン", definition: "Berlin" }, { word: "おくじょう", definition: "Lantai atas" }, { word: "レストラン", definition: "Restoran" }, { word: "ゆうえんち", definition: "Tempat hiburan" },
                { word: "もよおしものかいじょう", definition: "Tempat pertunjukan" }, { word: "めがね", definition: "Kacamata" }
            ],
            4: [
                { word: "おきます", definition: "Bangun tidur" }, { word: "ねます", definition: "Tidur" }, { word: "はたらきます", definition: "Bekerja" }, { word: "やすみます", definition: "Beristirahat, libur" },
                { word: "べんきょうします", definition: "Belajar" }, { word: "おわります", definition: "Selesai" }, { word: "デパート", definition: "Dept. store" }, { word: "ぎんこう", definition: "Bank" },
                { word: "ゆうびんきょく", definition: "Kantor pos" }, { word: "としょかん", definition: "Perpustakaan" }, { word: "びじゅつかん", definition: "Gedung kesenian" }, { word: "いま", definition: "Sekarang" },
                { word: "じ", definition: "Jam / Pukul" }, { word: "ふん", definition: "Menit" }, { word: "はん", definition: "Setengah" }, { word: "なんじ", definition: "Jam berapa?" },
                { word: "なんぶん", definition: "Berapa menit?" }, { word: "ごぜん", definition: "AM (Pagi)" }, { word: "ごご", definition: "PM (Sore/Malam)" }, { word: "あさ", definition: "Pagi" },
                { word: "ひる", definition: "Siang" }, { word: "ばん", definition: "Malam" }, { word: "よる", definition: "Malam" }, { word: "おototoi", definition: "Kemarin lusa" },
                { word: "きのう", definition: "Kemarin" }, { word: "きょう", definition: "Hari ini" }, { word: "あした", definition: "Besok" }, { word: "あさって", definition: "Besok lusa" },
                { word: "けさ", definition: "Tadi pagi" }, { word: "こんばん", definition: "Malam ini" }, { word: "やすみ", definition: "Istirahat, libur" }, { word: "ひるやすみ", definition: "Istirahat siang" },
                { word: "しけん", definition: "Ujian" }, { word: "かいぎ", definition: "Rapat" }, { word: "えいが", definition: "Film" }, { word: "まいあさ", definition: "Setiap pagi" },
                { word: "まいばん", definition: "Setiap malam" }, { word: "まいにち", definition: "Setiap hari" }, { word: "げつようび", definition: "Hari Senin" }, { word: "かようび", definition: "Hari Selasa" },
                { word: "すいようび", definition: "Hari Rabu" }, { word: "もくようび", definition: "Hari Kamis" }, { word: "きんようび", definition: "Hari Jumat" }, { word: "どようび", definition: "Hari Sabtu" },
                { word: "にちようび", definition: "Hari Minggu" }, { word: "なんようび", definition: "Hari apa?" }, { word: "から", definition: "Dari" }, { word: "まで", definition: "Sampai" },
                { word: "と", definition: "Dan" }, { word: "たいへんですね", definition: "Berat ya" }, { word: "ばんごう", definition: "Nomor" }, { word: "なんばん", definition: "Nomor berapa?" },
                { word: "ニューヨーク", definition: "New York" }, { word: "ペキン", definition: "Beijing" }, { word: "ロサンゼルス", definition: "Los Angeles" }, { word: "ロンドン", definition: "London" },
                { word: "けいさつしょ", definition: "Kantor polisi" }, { word: "しょうぼうしょ", definition: "Pemadam kebakaran" }, { word: "じほう", definition: "Pemberitahuan waktu" }, { word: "てんきよほう", definition: "Ramalan cuaca" },
                { word: "でんわばんごうあんない", definition: "Petunjuk nomor telepon" }
            ],
            5: [
                { word: "いきます", definition: "Pergi" }, { word: "きます", definition: "Datang" }, { word: "かえります", definition: "Pulang" }, { word: "がっこう", definition: "Sekolah" },
                { word: "スーパー", definition: "Supermarket" }, { word: "えき", definition: "Stasiun" }, { word: "ひこうき", definition: "Pesawat terbang" }, { word: "ふね", definition: "Kapal laut" },
                { word: "でんしゃ", definition: "Kereta listrik" }, { word: "ちかてつ", definition: "Kereta bawah tanah" }, { word: "しんかんせん", definition: "Shinkansen" }, { word: "バス", definition: "Bus" },
                { word: "タクシー", definition: "Taksi" }, { word: "じてんしゃ", definition: "Sepeda" }, { word: "あるいて", definition: "Jalan kaki" }, { word: "ひと", definition: "Orang" },
                { word: "ともだち", definition: "Teman" }, { word: "かれ", definition: "Dia (laki-laki)" }, { word: "かのじょ", definition: "Dia (perempuan)" }, { word: "かぞく", definition: "Keluarga" },
                { word: "ひとりで", definition: "Sendirian" }, { word: "せんしゅう", definition: "Minggu lalu" }, { word: "こんしゅう", definition: "Minggu ini" }, { word: "らいしゅう", definition: "Minggu depan" },
                { word: "せんげつ", definition: "Bulan lalu" }, { word: "こんげつ", definition: "Bulan ini" }, { word: "らいげつ", definition: "Bulan depan" }, { word: "きょねん", definition: "Tahun lalu" },
                { word: "ことし", definition: "Tahun ini" }, { word: "らいねん", definition: "Tahun depan" }, { word: "ねん", definition: "Tahun" }, { word: "なんねん", definition: "Tahun berapa?" },
                { word: "がつ", definition: "Bulan" }, { word: "なんがつ", definition: "Bulan apa?" }, { word: "ついたち", definition: "Tanggal 1" }, { word: "ふつか", definition: "Tanggal 2 / 2 hari" },
                { word: "みっか", definition: "Tanggal 3 / 3 hari" }, { word: "よっか", definition: "Tanggal 4 / 4 hari" }, { word: "いつか", definition: "Tanggal 5 / 5 hari" }, { word: "むいか", definition: "Tanggal 6 / 6 hari" },
                { word: "なのか", definition: "Tanggal 7 / 7 hari" }, { word: "ようか", definition: "Tanggal 8 / 8 hari" }, { word: "ここのか", definition: "Tanggal 9 / 9 hari" }, { word: "とおか", definition: "Tanggal 10 / 10 hari" },
                { word: "じゅうよっか", definition: "Tanggal 14 / 14 hari" }, { word: "はつか", definition: "Tanggal 20 / 20 hari" }, { word: "にじゅうよっか", definition: "Tanggal 24 / 24 hari" }, { word: "にち", definition: "Tanggal / Hari" },
                { word: "なんにち", definition: "Tanggal berapa?" }, { word: "いつ", definition: "Kapan" }, { word: "たんじょうび", definition: "Hari ulang tahun" }, { word: "そうですね", definition: "Ya begitulah" },
                { word: "どういたしまして", definition: "Sama-sama" }, { word: "ばんせん", definition: "Peron nomor" }, { word: "つぎの", definition: "Yang berikutnya" }, { word: "ふつう", definition: "Biasa" },
                { word: "きゅうこう", definition: "Ekspres" }, { word: "とっきゅう", definition: "Super ekspres" }, { word: "しゅくさいじつ", definition: "Hari libur nasional" }, { word: "がんじつ", definition: "Tahun baru" }
            ],
            6: [
                { word: "たべます", definition: "Makan" }, { word: "のみます", definition: "Minum" }, { word: "すいます", definition: "Merokok" }, { word: "みます", definition: "Melihat / Menonton" },
                { word: "ききます", definition: "Mendengar" }, { word: "よみます", definition: "Membaca" }, { word: "かきます", definition: "Menulis / Menggambar" }, { word: "かいます", definition: "Membeli" },
                { word: "とります", definition: "Mengambil (foto)" }, { word: "します", definition: "Melakukan" }, { word: "あいます", definition: "Bertemu" }, { word: "ごはん", definition: "Nasi" },
                { word: "あさごはん", definition: "Makan pagi" }, { word: "ひるごはん", definition: "Makan siang" }, { word: "ばんごはん", definition: "Makan malam" }, { word: "パン", definition: "Roti" },
                { word: "たまご", definition: "Telur" }, { word: "にく", definition: "Daging" }, { word: "さかな", definition: "Ikan" }, { word: "やさい", definition: "Sayuran" },
                { word: "くだもの", definition: "Buah-buahan" }, { word: "みず", definition: "Air" }, { word: "おちゃ", definition: "Teh Jepang" }, { word: "こうちゃ", definition: "Teh" },
                { word: "ぎゅうにゅう", definition: "Susu" }, { word: "ジュース", definition: "Jus" }, { word: "ビール", definition: "Bir" }, { word: "おさけ", definition: "Minuman keras" },
                { word: "たばこ", definition: "Rokok" }, { word: "てがみ", definition: "Surat" }, { word: "レポート", definition: "Laporan" }, { word: "しゃしん", definition: "Foto" },
                { word: "ビデオ", definition: "Kaset video" }, { word: "みせ", definition: "Toko" }, { word: "にわ", definition: "Halaman" }, { word: "しゅくだい", definition: "PR" },
                { word: "テニス", definition: "Tenis" }, { word: "サッカー", definition: "Sepak bola" }, { word: "はなみ", definition: "Hanami" }, { word: "なに", definition: "Apa" },
                { word: "いっしょに", definition: "Bersama-sama" }, { word: "ちょっと", definition: "Sebentar" }, { word: "いつも", definition: "Selalu" }, { word: "ときどき", definition: "Kadang-kadang" },
                { word: "それから", definition: "Setelah itu" }, { word: "ええ", definition: "Ya" }, { word: "いいですね", definition: "Bagus ya" }, { word: "わかりました", definition: "Mengerti" },
                { word: "メキシコ", definition: "Meksiko" }, { word: "なんですか", definition: "Apa?" }, { word: "じゃ、また", definition: "Ok, sampai jumpa" }
            ],
            7: [
                { word: "きります", definition: "Memotong / Menggunting" }, { word: "おくります", definition: "Mengirim" }, { word: "あげます", definition: "Memberikan" }, { word: "もらいます", definition: "Menerima" },
                { word: "かします", definition: "Meminjamkan" }, { word: "かります", definition: "Meminjam" }, { word: "おしえます", definition: "Mengajar" }, { word: "ならいます", definition: "Belajar (kepada)" },
                { word: "かけます", definition: "Menelepon" }, { word: "て", definition: "Tangan" }, { word: "はし", definition: "Sumpit" }, { word: "スプーン", definition: "Sendok" },
                { word: "ナイフ", definition: "Pisau" }, { word: "フォーク", definition: "Garpu" }, { word: "はさみ", definition: "Gunting" }, { word: "パソコン", definition: "Laptop" },
                { word: "ケータイ", definition: "HP" }, { word: "メール", definition: "Email" }, { word: "ねんがじょう", definition: "Kartu tahun baru" }, { word: "パンチ", definition: "Pelubang kertas" },
                { word: "ホッチキス", definition: "Stapler" }, { word: "セロテープ", definition: "Selotip" }, { word: "けしゴム", definition: "Penghapus karet" }, { word: "かみ", definition: "Kertas" },
                { word: "はな", definition: "Bunga" }, { word: "シャツ", definition: "Kemeja" }, { word: "プレゼント", definition: "Hadiah / Kado" }, { word: "にもつ", definition: "Barang" },
                { word: "おかね", definition: "Uang" }, { word: "きっぷ", definition: "Karcis" }, { word: "クリスマス", definition: "Natal" }, { word: "ちち", definition: "Ayah" },
                { word: "はは", definition: "Ibu" }, { word: "おとうさん", definition: "Ayah orang lain" }, { word: "おかあさん", definition: "Ibu orang lain" }, { word: "もう", definition: "Sudah" },
                { word: "まだ", definition: "Belum" }, { word: "これから", definition: "Mulai dari sekarang" }, { word: "すてきですね", definition: "Bagus / Indah ya" }, { word: "いらっしゃい", definition: "Selamat datang" },
                { word: "どうぞおあがりください", definition: "Silakan masuk" }, { word: "しつれいします", definition: "Permisi" }, { word: "いかがですか", definition: "Bagaimana?" }, { word: "いただきます", definition: "Selamat makan" },
                { word: "ごちそうさまでした", definition: "Terima kasih atas hidangannya" }, { word: "スペイン", definition: "Spanyol" }, { word: "かぞく", definition: "Keluarga" }, { word: "そぼ", definition: "Nenek" },
                { word: "そふ", definition: "Kakek" }, { word: "そふぼ", definition: "Kakek nenek" }
            ],
            8: [
                { word: "ハンサム", definition: "Tampan / Gagah" }, { word: "きれい", definition: "Cantik / Bersih" }, { word: "しずか", definition: "Sunyi / Tenang" }, { word: "にぎやか", definition: "Ramai" },
                { word: "ゆうめい", definition: "Terkenal" }, { word: "しんせつ", definition: "Baik hati" }, { word: "げんき", definition: "Sehat" }, { word: "ひま", definition: "Senggang" },
                { word: "べんり", definition: "Praktis" }, { word: "すてき", definition: "Bagus" }, { word: "おおきい", definition: "Besar" }, { word: "ちいさい", definition: "Kecil" },
                { word: "あたらしい", definition: "Baru" }, { word: "ふるい", definition: "Lama / Tua" }, { word: "いい", definition: "Baik / Bagus" }, { word: "わるい", definition: "Jelek" },
                { word: "あつい", definition: "Panas" }, { word: "さむい", definition: "Dingin (suhu)" }, { word: "つめたい", definition: "Dingin (benda)" }, { word: "むずかしい", definition: "Sulit" },
                { word: "やさしい", definition: "Mudah" }, { word: "たかい", definition: "Mahal / Tinggi" }, { word: "やすい", definition: "Murah" }, { word: "ひくい", definition: "Rendah" },
                { word: "おもしろい", definition: "Menarik" }, { word: "おいしい", definition: "Enak" }, { word: "いそがしい", definition: "Sibuk" }, { word: "たのしい", definition: "Senang" },
                { word: "しろい", definition: "Putih" }, { word: "くろい", definition: "Hitam" }, { word: "あかい", definition: "Merah" }, { word: "あおい", definition: "Biru" },
                { word: "さくら", definition: "Sakura" }, { word: "やま", definition: "Gunung" }, { word: "まち", definition: "Kota" }, { word: "たべもの", definition: "Makanan" },
                { word: "ところ", definition: "Tempat" }, { word: "りょう", definition: "Asrama" }, { word: "レストラン", definition: "Restoran" }, { word: "せいかつ", definition: "Kehidupan" },
                { word: "しごと", definition: "Pekerjaan" }, { word: "どう", definition: "Bagaimana?" }, { word: "どんな", definition: "Yang bagaimana?" }, { word: "とても", definition: "Sangat / Sekali" },
                { word: "amari", definition: "Tidak begitu" }, { word: "soshite", definition: "Kemudian" }, { word: "ga", definition: "Tetapi" }, { word: "ogenki desu ka", definition: "Apa kabar?" },
                { word: "sou desu ne", definition: "Bagaimana ya?" }, { word: "mou ippai ikaga desu ka", definition: "Mau nambah secangkir lagi?" }, { word: "kekkou desu", definition: "Cukup" }, { word: "sorosoro shitsurei shimasu", definition: "Maaf saya pamit dulu" },
                { word: "iie", definition: "Sama-sama" }, { word: "mata irasshatte kudasai", definition: "Silakan datang lagi" }
            ],
            9: [
                { word: "わかります", definition: "Mengerti" }, { word: "あります", definition: "Ada / Mempunyai" }, { word: "すき", definition: "Suka / Gemar" }, { word: "きらい", definition: "Benci" },
                { word: "jouzu", definition: "Pandai" }, { word: "heta", definition: "Tidak pandai" }, { word: "nomimono", definition: "Minuman" }, { word: "ryouri", definition: "Masakan" },
                { word: "supootsu", definition: "Olahraga" }, { word: "yakyuu", definition: "Baseball" }, { word: "dansu", definition: "Dansa" }, { word: "ryokou", definition: "Tamasya" },
                { word: "ongaku", definition: "Musik" }, { word: "uta", definition: "Lagu" }, { word: "kurashikku", definition: "Klasik" }, { word: "jazu", definition: "Jazz" },
                { word: "konsaato", definition: "Konser" }, { word: "karaoke", definition: "Karaoke" }, { word: "kabuki", definition: "Kabuki" }, { word: "e", definition: "Gambar" },
                { word: "ji", definition: "Huruf" }, { word: "kanji", definition: "Kanji" }, { word: "hiragana", definition: "Hiragana" }, { word: "katakana", definition: "Katakana" },
                { word: "roomaji", definition: "Huruf Latin" }, { word: "komakai okane", definition: "Uang kecil" }, { word: "chiketto", definition: "Tiket" }, { word: "jikan", definition: "Waktu" },
                { word: "youji", definition: "Urusan" }, { word: "yakusoku", definition: "Janji" }, { word: "arubaito", definition: "Paruh waktu" }, { word: "goshujin", definition: "Suami orang lain" },
                { word: "shujin", definition: "Suami" }, { word: "okusan", definition: "Istri orang lain" }, { word: "tsuma", definition: "Istri" }, { word: "kodomo", definition: "Anak" },
                { word: "yoku", definition: "Dengan baik" }, { word: "daitai", definition: "Kira-kira" }, { word: "takusan", definition: "Banyak" }, { word: "sukoshi", definition: "Sedikit" },
                { word: "zenzen", definition: "Sama sekali" }, { word: "hayaku", definition: "Cepat" }, { word: "から", definition: "Karena" }, { word: "doushite", definition: "Kenapa" },
                { word: "kashite kudasai", definition: "Tolong pinjamkan" }, { word: "ii desu yo", definition: "Boleh" }, { word: "zannen desu", definition: "Sayang sekali" }, { word: "issho ni ikaga desu ka", definition: "Bagaimana kalau bersama" },
                { word: "chotto", definition: "Tidak bisa" }, { word: "dame desu ka", definition: "Tidak bisa ya" }, { word: "mata kondo onegaishimasu", definition: "Lain kali saja" }, { word: "oo", definition: "Ah / Oh" }
            ],
            10: [
                { word: "ATM", definition: "ATM" }, { word: "コンビニ", definition: "Toko 24 jam" }, { word: "こうえん", definition: "Taman" }, { word: "kissaten", definition: "Coffee shop" },
                { word: "ya", definition: "Toko ～" }, { word: "noriba", definition: "Tempat naik" }, { word: "ken", definition: "Prefektur" }, { word: "ue", definition: "Atas" },
                { word: "shita", definition: "Bawah" }, { word: "mae", definition: "Depan" }, { word: "ushiro", definition: "Belakang" }, { word: "migi", definition: "Kanan" },
                { word: "hidari", definition: "Kiri" }, { word: "naka", definition: "Dalam" }, { word: "soto", definition: "Luar" }, { word: "tonari", definition: "Samping" },
                { word: "chikaku", definition: "Dekat" }, { word: "aida", definition: "Antara" }, { word: "doumo sumimasen", definition: "Maaf / Terima kasih banyak" }, { word: "nanpuraa", definition: "Kecap ikan" },
                { word: "koonaa", definition: "Bagian penjualan" }, { word: "ichiban shita", definition: "Paling bawah" }, { word: "genkan", definition: "Pintu masuk" }, { word: "furoba", definition: "Kamar mandi" },
                { word: "senmenjo", definition: "Tempat cuci tangan" }, { word: "daidokoro", definition: "Dapur" }, { word: "shokudou", definition: "Kantin" }, { word: "ima", definition: "Ruang tamu" },
                { word: "shinshitsu", definition: "Kamar tidur" }, { word: "rouka", definition: "Koridor" }, { word: "beranda", definition: "Teras" }, { word: "あります", definition: "Ada (benda mati)" },
                { word: "います", definition: "Ada (benda hidup)" }, { word: "iroiro", definition: "Berbagai" }, { word: "otoko no hito", definition: "Orang laki-laki" }, { word: "onna no hito", definition: "Orang perempuan" },
                { word: "otoko no ko", definition: "Anak laki-laki" }, { word: "onna no ko", definition: "Anak perempuan" }, { word: "いぬ", definition: "Anjing" }, { word: "ねこ", definition: "Kucing" },
                { word: "panda", definition: "Panda" }, { word: "zou", definition: "Gajah" }, { word: "ki", definition: "Pohon" }, { word: "mono", definition: "Barang" },
                { word: "denchi", definition: "Baterai" }, { word: "hako", definition: "Kotak" }, { word: "suicchi", definition: "Saklar" }, { word: "reizouko", definition: "Kulkas" },
                { word: "teeburu", definition: "Meja makan" }, { word: "beddo", definition: "Tempat tidur" }, { word: "tana", definition: "Lemari" }, { word: "doa", definition: "Pintu" },
                { word: "mado", definition: "Jendela" }, { word: "posuto", definition: "Kotak surat" }, { word: "biru", definition: "Gedung" }
            ],
            11: [
                { word: "います", definition: "Ada / Mempunyai" }, { word: "かかります", definition: "Memerlukan waktu" }, { word: "やすみます", definition: "Libur / Tidak masuk" }, { word: "ひとつ", definition: "1 buah" },
                { word: "ふたつ", definition: "2 buah" }, { word: "みっつ", definition: "3 buah" }, { word: "よっつ", definition: "4 buah" }, { word: "いつつ", definition: "5 buah" },
                { word: "むっつ", definition: "6 buah" }, { word: "ななつ", definition: "7 buah" }, { word: "やっつ", definition: "8 buah" }, { word: "kokonotsu", definition: "9 buah" },
                { word: "too", definition: "10 buah" }, { word: "ikutsu", definition: "Berapa buah?" }, { word: "hitori", definition: "1 orang" }, { word: "futari", definition: "2 orang" },
                { word: "nin", definition: "～ orang" }, { word: "dai", definition: "～ unit" }, { word: "mai", definition: "～ lembar" }, { word: "kai", definition: "～ kali" },
                { word: "ringo", definition: "Apel" }, { word: "mikan", definition: "Jeruk" }, { word: "sandoicchi", definition: "Sandwich" }, { word: "karee", definition: "Kare" },
                { word: "aisukuriimu", definition: "Es krim" }, { word: "kitte", definition: "Perangko" }, { word: "hagaki", definition: "Kartu pos" }, { word: "fuutou", definition: "Amplop" },
                { word: "gaikoku", definition: "Luar negeri" }, { word: "ryuugakusei", definition: "Pelajar asing" }, { word: "kurasu", definition: "Kelas" }, { word: "jikan", definition: "～ jam" },
                { word: "shuukan", definition: "～ minggu" }, { word: "kagetsu", definition: "～ bulan" }, { word: "nen", definition: "～ tahun" }, { word: "gurai", definition: "Kira-kira ～" },
                { word: "donokurai", definition: "Berapa lama?" }, { word: "zenbu de", definition: "Semuanya" }, { word: "minna", definition: "Semua" }, { word: "dake", definition: "Hanya ～" },
                { word: "kashikomarimashita", definition: "Baik / Mengerti" }, { word: "ii tenki desu ne", definition: "Cuacanya bagus ya" }, { word: "odekake desu ka", definition: "Mau keluar?" }, { word: "itterasshai", definition: "Hati-hati" },
                { word: "itte kimasu", definition: "Saya berangkat" }, { word: "funabin", definition: "Pos laut" }, { word: "koukuubin", definition: "Pos udara" }, { word: "eameeru", definition: "Pos udara" },
                { word: "oosutoraria", definition: "Australia" }, { word: "onegaishimasu", definition: "Tolong" }
            ],
            12: [
                { word: "かんたん", definition: "Mudah / Gampang" }, { word: "ちかい", definition: "Dekat" }, { word: "とおい", definition: "Jauh" }, { word: "はやい", definition: "Cepat" },
                { word: "osoi", definition: "Lambat" }, { word: "ooi", definition: "Banyak" }, { word: "sukunai", definition: "Sedikit" }, { word: "atatakai", definition: "Hangat" },
                { word: "suzushii", definition: "Sejuk" }, { word: "amai", definition: "Manis" }, { word: "karai", definition: "Pedas" }, { word: "omoi", definition: "Berat" },
                { word: "karui", definition: "Ringan" }, { word: "ii", definition: "Lebih baik" }, { word: "kisetsu", definition: "Musim" }, { word: "tenki", definition: "Cuaca" },
                { word: "ame", definition: "Hujan" }, { word: "yuki", definition: "Salju" }, { word: "kumori", definition: "Mendung" }, { word: "hoteru", definition: "Hotel" },
                { word: "kuukou", definition: "Bandara" }, { word: "umi", definition: "Laut" }, { word: "sekai", definition: "Dunia" }, { word: "paatii", definition: "Pesta" },
                { word: "matsuri", definition: "Perayaan" }, { word: "remon", definition: "Lemon" }, { word: "ikebana", definition: "Seni merangkai bunga" }, { word: "momiji", definition: "Maple" },
                { word: "dochira", definition: "Yang mana" }, { word: "dochira mo", definition: "Dua-duanya" }, { word: "ichiban", definition: "Yang paling" }, { word: "zutto", definition: "Selamanya" },
                { word: "hajimete", definition: "Pertama kali" }, { word: "tadaima", definition: "Saya pulang" }, { word: "okaerinasai", definition: "Sudah pulang ya" }, { word: "waa, sugoi hito desu ne", definition: "Wah banyak orang" },
                { word: "tsukaremashita", definition: "Capek / Lelah" }, { word: "honkon", definition: "Hongkong" }, { word: "shingapooru", definition: "Singapura" }, { word: "meisho", definition: "Tempat terkenal" },
                { word: "sukiyaki", definition: "Sukiyaki" }, { word: "sashimi", definition: "Sashimi" }, { word: "sushi", definition: "Sushi" }, { word: "tenpura", definition: "Tempura" }
            ],
            13: [
                { word: "あそびます", definition: "Bermain" }, { word: "およぎます", definition: "Berenang" }, { word: "むかえます", definition: "Menjemput" }, { word: "つかれます", definition: "Lelah" },
                { word: "kekkon shimasu", definition: "Menikah" }, { word: "kaimono shimasu", definition: "Berbelanja" }, { word: "shokuji shimasu", definition: "Makan bersama" }, { word: "sanpo shimasu", definition: "Jalan-jalan" },
                { word: "taihen", definition: "Susah / Berat" }, { word: "hoshii", definition: "Ingin" }, { word: "hiroi", definition: "Luas" }, { word: "semai", definition: "Sempit" },
                { word: "puuru", definition: "Kolam renang" }, { word: "kawa", definition: "Sungai" }, { word: "bijutsu", definition: "Seni" }, { word: "tsuri", definition: "Pancing / Memancing" },
                { word: "sukii", definition: "Ski" }, { word: "shuumatsu", definition: "Akhir pekan" }, { word: "oshougatsu", definition: "Tahun baru" }, { word: "goro", definition: "Kira-kira / Sekitar" },
                { word: "nanika", definition: "Sesuatu" }, { word: "dokoka", definition: "Suatu tempat" }, { word: "nodo ga kawakimasu", definition: "Haus" }, { word: "onaka ga sukimasu", definition: "Lapar" },
                { word: "sou shimashou", definition: "Ya ayo" }, { word: "gochuumon wa", definition: "Mau pesan apa?" }, { word: "omachi kudasai", definition: "Tunggu sebentar" }, { word: "betsubetsu ni", definition: "Sendiri-sendiri" },
                { word: "hakubutsukan", definition: "Museum" }, { word: "bijutsukan", definition: "Gedung kesenian" }, { word: "toshokan", definition: "Perpustakaan" }, { word: "eigakan", definition: "Bioskop" },
                { word: "doubutsuen", definition: "Kebun binatang" }, { word: "shokubutsuen", definition: "Kebun raya" }, { word: "otera", definition: "Kuil Budha" }
            ],
            14: [
                { word: "つけます", definition: "Menyalakan / Memasang" }, { word: "けします", definition: "Mematikan / Memadamkan" }, { word: "あけます", definition: "Membuka" }, { word: "しめます", definition: "Menutup" },
                { word: "isogimasu", definition: "Buru-buru" }, { word: "machimasu", definition: "Menunggu" }, { word: "mochimasu", definition: "Membawa" }, { word: "torimasu", definition: "Mengambil" },
                { word: "tetsudaimasu", definition: "Membantu" }, { word: "yobimasu", definition: "Memanggil" }, { word: "hanashimasu", definition: "Berbicara" }, { word: "tsukaimasu", definition: "Memakai" },
                { word: "tomemasu", definition: "Menghentikan" }, { word: "misemasu", definition: "Memperlihatkan" }, { word: "oshiemasu", definition: "Memberitahu" }, { word: "suwarimasu", definition: "Duduk" },
                { word: "たちます", definition: "Berdiri" }, { word: "hairimasu", definition: "Masuk" }, { word: "demasu", definition: "Keluar" }, { word: "furimasu", definition: "Turun hujan" },
                { word: "kopii shimasu", definition: "Memfotokopi" }, { word: "denki", definition: "Listrik / Lampu" }, { word: "eakon", definition: "AC" }, { word: "basupooto", definition: "Paspor" },
                { word: "namae", definition: "Nama" }, { word: "juusho", definition: "Alamat" }, { word: "chizu", definition: "Peta" }, { word: "shio", definition: "Garam" },
                { word: "satou", definition: "Gula" }, { word: "mondai", definition: "Masalah / Soal" }, { word: "kotae", definition: "Jawaban" }, { word: "yomikata", definition: "Cara membaca" },
                { word: "kata", definition: "Cara ～" }, { word: "massugu", definition: "Lurus" }, { word: "yukkuri", definition: "Pelan-pelan" }, { word: "sugu", definition: "Segera / Langsung" },
                { word: "mata", definition: "Lagi" }, { word: "ato de", definition: "Nanti" }, { word: "mou sukoshi", definition: "Sedikit lagi" }, { word: "mou", definition: "～ lagi" },
                { word: "saa", definition: "Mari" }, { word: "are?", definition: "Ah (heran)" }, { word: "kore de onegai shimasu", definition: "Minta dengan ini" }, { word: "otsuri", definition: "Kembalian" },
                { word: "kippu uriba", definition: "Loket penjualan karcis" }, { word: "jidou kenbaiki", definition: "Mesin karcis otomatis" }, { word: "seisanki", definition: "Alat penghitung otomatis" }, { word: "kaisatsuguchi", definition: "Pintu pemeriksaan karcis" },
                { word: "deguchi", definition: "Pintu keluar" }, { word: "iriguchi", definition: "Pintu masuk" }
            ],
            15: [
                { word: "おきます", definition: "Meletakkan / Menaruh" }, { word: "つくります", definition: "Membuat / Memproduksi" }, { word: "うります", definition: "Menjual" }, { word: "shirimasu", definition: "Mengetahui / Mengenal" },
                { word: "sumimasu", definition: "Tinggal" }, { word: "kenkyuu shimasu", definition: "Meneliti" }, { word: "shiryou", definition: "Data / Bahan" }, { word: "katarogu", definition: "Katalog" },
                { word: "jikokuhyou", definition: "Jadwal" }, { word: "fuku", definition: "Baju" }, { word: "seihin", definition: "Produk" }, { word: "sofuto", definition: "Software" },
                { word: "denshi jisho", definition: "Kamus elektronik" }, { word: "keizai", definition: "Ekonomi" }, { word: "shiyakusho", definition: "Kantor Wali Kota" }, { word: "koukou", definition: "SMA" },
                { word: "haisha", definition: "Dokter gigi" }, { word: "dokushin", definition: "Bujangan" }, { word: "sumimasen", definition: "Maaf" }, { word: "renshuu", definition: "Latihan" },
                { word: "minasan", definition: "Semuanya" }, { word: "omoidashimasu", definition: "Teringat" }, { word: "irasshaimasu", definition: "Ada (Hormat)" }
            ]
        };

        let currentBabIndex = 1;
        let quizData = [];
        let currentIndex = 0;
        let score = 0;
        let isProcessing = false;

        const mainApp = document.getElementById('main-app');
        const menuView = document.getElementById('menu-view');
        const quizView = document.getElementById('quiz-view');
        const resultView = document.getElementById('result-view');
        const babList = document.getElementById('bab-list');
        const currentBabTitle = document.getElementById('current-bab-title');
        const displayDefinition = document.getElementById('display-definition');
        const userInput = document.getElementById('user-input');
        const statusNotif = document.getElementById('status-notif');
        const notifText = document.getElementById('notif-text');
        const progressBar = document.getElementById('progress-bar');
        const progressText = document.getElementById('progress-text');
        const scoreText = document.getElementById('score-text');
        const finalScore = document.getElementById('final-score');
        const skipButton = document.getElementById('skip-button');

        // Mengacak urutan soal
        function shuffleArray(array) {
            const shuffled = [...array];
            for (let i = shuffled.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [shuffled[i], shuffled[j]] = [shuffled[j], shuffled[i]];
            }
            return shuffled;
        }

        // Menampilkan Menu BAB
        function renderMenu() {
            babList.innerHTML = "";
            for (let i = 1; i <= 15; i++) {
                const btn = document.createElement('button');
                btn.className = "bg-white border-2 border-slate-100 p-4 rounded-2xl hover:border-red-500 hover:shadow-md transition-all text-center group active:scale-95";
                const hasData = allData[i] && allData[i].length > 0;
                btn.innerHTML = `
                    <div class="text-[10px] font-bold text-slate-400 group-hover:text-red-400 mb-1 uppercase tracking-tighter">Materi</div>
                    <div class="text-lg font-black text-slate-700 group-hover:text-red-600 leading-none">BAB ${i}</div>
                    <div class="text-[9px] mt-1 ${hasData ? 'text-emerald-500 font-bold' : 'text-slate-300 italic'}">
                        ${hasData ? allData[i].length + ' Kata' : '(Kosong)'}
                    </div>
                `;
                btn.onclick = () => hasData ? startQuiz(i) : alert(`Data BAB ${i} belum tersedia.`);
                babList.appendChild(btn);
            }
        }

        // Memulai Kuis BAB
        function startQuiz(babIndex) {
            currentBabIndex = babIndex;
            quizData = shuffleArray(allData[babIndex]);
            currentIndex = 0; score = 0;
            menuView.classList.add('hidden');
            resultView.classList.add('hidden');
            quizView.classList.remove('hidden');
            currentBabTitle.innerText = `BAB ${babIndex}`;
            loadQuestion();
        }

        function backToMenu() {
            quizView.classList.add('hidden');
            resultView.classList.add('hidden');
            menuView.classList.remove('hidden');
            renderMenu();
        }

        // Memuat Pertanyaan
        function loadQuestion() {
            if (currentIndex < quizData.length) {
                const q = quizData[currentIndex];
                displayDefinition.innerText = q.definition;
                userInput.value = ""; userInput.disabled = false; userInput.focus();
                isProcessing = false;
                const progress = (currentIndex / quizData.length) * 100;
                progressBar.style.width = `${progress}%`;
                progressText.innerText = `问题 ${currentIndex + 1} / ${quizData.length}`;
                scoreText.innerText = `Skor: ${score}`;
                statusNotif.classList.add('hidden');
                userInput.classList.remove('border-emerald-500', 'bg-emerald-50', 'border-amber-500', 'bg-amber-50');
            } else {
                showResults();
            }
        }

        function showResults() {
            progressBar.style.width = `100%`;
            quizView.classList.add('hidden');
            resultView.classList.remove('hidden');
            finalScore.innerText = score;
        }

        // Umpan Balik (Feedback)
        function handleFeedback(isCorrect) {
            isProcessing = true;
            statusNotif.classList.remove('hidden');
            userInput.disabled = true;

            if (isCorrect) {
                score += 10;
                notifText.innerText = "正解です！ (Benar)";
                statusNotif.className = "absolute -top-10 left-0 right-0 font-bold flex items-center justify-center gap-2 fade-in text-emerald-600";
                userInput.classList.add('border-emerald-500', 'bg-emerald-50', 'correct-pulse');
            } else {
                const correct = quizData[currentIndex].word;
                notifText.innerText = `Jawaban: ${correct}`;
                statusNotif.className = "absolute -top-10 left-0 right-0 font-bold flex items-center justify-center gap-2 fade-in text-amber-600";
                userInput.value = correct;
                userInput.classList.add('border-amber-500', 'bg-amber-50');
            }
            setTimeout(() => { currentIndex++; loadQuestion(); }, isCorrect ? 800 : 1800);
        }

        // Event Listeners
        userInput.addEventListener('input', (e) => {
            if (isProcessing) return;
            const currentAnswer = quizData[currentIndex].word;
            if (e.target.value.trim() === currentAnswer) handleFeedback(true);
        });

        skipButton.addEventListener('click', () => {
            if (!isProcessing) handleFeedback(false);
        });

        // Inisialisasi awal
        window.addEventListener('load', () => {
            document.getElementById('app-loading').classList.add('hidden');
            mainApp.classList.remove('hidden');
            if (typeof wanakana !== 'undefined') wanakana.bind(userInput);
            renderMenu();
        });
    </script>
</body>
</html>
