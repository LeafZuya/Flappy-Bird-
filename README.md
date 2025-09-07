# LeafCy AI
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>LeafCy - AI Canggih Leaf'Corp</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #1a2a6c, #2b5876, #4e4376);
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
        }
        
        .container {
            display: flex;
            flex-direction: column;
            width: 100%;
            max-width: 1400px;
            height: 90vh;
            background-color: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            box-shadow: 0 15px 40px rgba(0, 0, 0, 0.3);
            overflow: hidden;
        }
        
        .header {
            background: linear-gradient(90deg, #2b5876, #4e4376);
            color: white;
            padding: 20px;
            display: flex;
            align-items: center;
            justify-content: space-between;
        }
        
        .logo {
            display: flex;
            align-items: center;
            gap: 15px;
        }
        
        .logo-icon {
            font-size: 2.5rem;
            color: #4cd964;
        }
        
        .logo-text {
            font-size: 1.8rem;
            font-weight: bold;
        }
        
        .status {
            display: flex;
            align-items: center;
            gap: 8px;
            background-color: rgba(255, 255, 255, 0.2);
            padding: 8px 15px;
            border-radius: 20px;
            font-size: 0.9rem;
        }
        
        .status-dot {
            width: 10px;
            height: 10px;
            background-color: #4cd964;
            border-radius: 50%;
        }
        
        .main-content {
            display: flex;
            flex-direction: row;
            flex: 1;
            overflow: hidden;
        }
        
        .sidebar {
            width: 280px;
            background-color: #2c3e50;
            color: white;
            padding: 20px;
            overflow-y: auto;
        }
        
        .chat-container {
            flex: 1;
            display: flex;
            flex-direction: column;
        }
        
        .capabilities {
            margin-bottom: 30px;
        }
        
        .capabilities h3 {
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .capability-list {
            list-style-type: none;
        }
        
        .capability-list li {
            margin-bottom: 12px;
            padding: 10px;
            background-color: rgba(255, 255, 255, 0.1);
            border-radius: 8px;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .capability-list i {
            color: #4cd964;
        }
        
        .chat-messages {
            flex: 1;
            padding: 25px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 20px;
            background-color: #f9f9f9;
        }
        
        .message {
            max-width: 75%;
            padding: 15px 20px;
            border-radius: 18px;
            line-height: 1.5;
            animation: fadeIn 0.4s ease;
            position: relative;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(15px); }
            to { opacity: 1; transform: translateY(0); }
        }
        
        .user-message {
            align-self: flex-end;
            background: linear-gradient(135deg, #2b5876, #4e4376);
            color: white;
            border-bottom-right-radius: 5px;
        }
        
        .bot-message {
            align-self: flex-start;
            background-color: #eef2f5;
            color: #2c3e50;
            border-bottom-left-radius: 5px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
        }
        
        .message-header {
            display: flex;
            align-items: center;
            margin-bottom: 8px;
            font-weight: bold;
        }
        
        .message-header i {
            margin-right: 8px;
        }
        
        .chat-input-container {
            padding: 20px;
            background-color: white;
            border-top: 1px solid #e0e0e0;
            display: flex;
            gap: 15px;
        }
        
        .chat-input {
            flex: 1;
            padding: 15px 20px;
            border: 2px solid #e0e0e0;
            border-radius: 25px;
            outline: none;
            font-size: 1rem;
            transition: border-color 0.3s;
        }
        
        .chat-input:focus {
            border-color: #2b5876;
        }
        
        .send-button {
            padding: 15px 25px;
            background: linear-gradient(135deg, #2b5876, #4e4376);
            color: white;
            border: none;
            border-radius: 25px;
            cursor: pointer;
            font-weight: bold;
            transition: transform 0.2s;
        }
        
        .send-button:hover {
            transform: scale(1.03);
        }
        
        .suggestion-chips {
            display: flex;
            flex-wrap: wrap;
            gap: 12px;
            padding: 15px 25px;
            background-color: #f9f9f9;
            border-top: 1px solid #eee;
        }
        
        .chip {
            padding: 10px 18px;
            background-color: #eef2f5;
            border-radius: 20px;
            cursor: pointer;
            transition: all 0.3s;
            font-size: 0.9rem;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        
        .chip:hover {
            background-color: #2b5876;
            color: white;
        }
        
        .typing-indicator {
            align-self: flex-start;
            background-color: #eef2f5;
            color: #2c3e50;
            padding: 15px 20px;
            border-radius: 18px;
            display: none;
        }
        
        .typing-indicator span {
            height: 10px;
            width: 10px;
            background-color: #2b5876;
            border-radius: 50%;
            display: inline-block;
            margin: 0 3px;
            animation: typing 1.4s infinite;
        }
        
        .typing-indicator span:nth-child(2) {
            animation-delay: 0.2s;
        }
        
        .typing-indicator span:nth-child(3) {
            animation-delay: 0.4s;
        }
        
        @keyframes typing {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-5px); }
        }
        
        .knowledge-base {
            margin-top: 30px;
        }
        
        .knowledge-base h3 {
            margin-bottom: 15px;
            padding-bottom: 10px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .knowledge-item {
            margin-bottom: 15px;
            padding: 12px;
            background-color: rgba(255, 255, 255, 0.1);
            border-radius: 8px;
        }
        
        .knowledge-item h4 {
            margin-bottom: 8px;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        
        @media (max-width: 1000px) {
            .main-content {
                flex-direction: column;
            }
            
            .sidebar {
                width: 100%;
                max-height: 250px;
            }
        }
        
        .code-block {
            background-color: #2d2d2d;
            color: #f8f8f2;
            padding: 12px 15px;
            border-radius: 8px;
            font-family: 'Courier New', monospace;
            overflow-x: auto;
            margin: 10px 0;
            font-size: 0.9rem;
            line-height: 1.4;
        }
        
        .info-text {
            font-size: 0.9rem;
            color: #7f8c8d;
            margin-top: 5px;
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <div class="logo">
                <i class="fas fa-leaf logo-icon"></i>
                <div class="logo-text">LeafCy</div>
            </div>
            <div class="status">
                <div class="status-dot"></div>
                <span>Online | AI Assistant v2.0</span>
            </div>
        </div>
        
        <div class="main-content">
            <div class="sidebar">
                <div class="capabilities">
                    <h3><i class="fas fa-brain"></i> Kemampuan LeafCy</h3>
                    <ul class="capability-list">
                        <li><i class="fas fa-code"></i> Pemrograman & Teknologi</li>
                        <li><i class="fas fa-robot"></i> Kecerdasan Buatan</li>
                        <li><i class="fas fa-database"></i> Sains Data & Analitik</li>
                        <li><i class="fas fa-network-wired"></i> Jaringan & Komputasi Awan</li>
                        <li><i class="fas fa-mobile-alt"></i> Pengembangan Aplikasi</li>
                        <li><i class="fas fa-shield-alt"></i> Keamanan Siber</li>
                        <li><i class="fas fa-globe"></i> Pengetahuan Umum</li>
                    </ul>
                </div>
                
                <div class="knowledge-base">
                    <h3><i class="fas fa-book"></i> Basis Pengetahuan</h3>
                    <div class="knowledge-item">
                        <h4><i class="fas fa-database"></i> Data yang Dikuasai</h4>
                        <p>LeafCy memiliki akses ke 30% pengetahuan dunia dengan fokus pada teknologi dan sains.</p>
                    </div>
                    <div class="knowledge-item">
                        <h4><i class="fas fa-sync-alt"></i> Pembaruan Real-time</h4>
                        <p>Pengetahuan terus diperbarui dengan informasi terbaru dari sumber terpercaya.</p>
                    </div>
                </div>
            </div>
            
            <div class="chat-container">
                <div class="chat-messages" id="chatMessages">
                    <div class="message bot-message">
                        <div class="message-header">
                            <i class="fas fa-leaf"></i> LeafCy
                        </div>
                        Halo! Saya LeafCy, asisten AI canggih dari Leaf'Corp. Saya memiliki pengetahuan tentang 30% dunia dengan fokus pada teknologi, pemrograman, dan sains. Ada yang bisa saya bantu?
                    </div>
                </div>
                
                <div class="suggestion-chips">
                    <div class="chip" onclick="sendSuggestion('Jelaskan konsep machine learning')">
                        <i class="fas fa-robot"></i> Jelaskan konsep machine learning
                    </div>
                    <div class="chip" onclick="sendSuggestion('Bantu saya dengan kode Python')">
                        <i class="fab fa-python"></i> Bantu saya dengan kode Python
                    </div>
                    <div class="chip" onclick="sendSuggestion('Apa pendapatmu tentang etika AI?')">
                        <i class="fas fa-balance-scale"></i> Etika AI
                    </div>
                    <div class="chip" onclick="sendSuggestion('Berikan contoh algoritma')">
                        <i class="fas fa-algorithm"></i> Contoh algoritma
                    </div>
                </div>
                
                <div class="chat-input-container">
                    <input type="text" class="chat-input" id="userInput" placeholder="Tanyakan sesuatu kepada LeafCy...">
                    <button class="send-button" id="sendButton">Kirim <i class="fas fa-paper-plane"></i></button>
                </div>
            </div>
        </div>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function() {
            const chatMessages = document.getElementById('chatMessages');
            const userInput = document.getElementById('userInput');
            const sendButton = document.getElementById('sendButton');
            
            // Fungsi untuk menambahkan pesan pengguna
            function addUserMessage(message) {
                const messageElement = document.createElement('div');
                messageElement.classList.add('message', 'user-message');
                messageElement.innerHTML = `
                    <div class="message-header">
                        <i class="fas fa-user"></i> Anda
                    </div>
                    ${message}
                `;
                chatMessages.appendChild(messageElement);
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }
            
            // Fungsi untuk menambahkan pesan bot
            function addBotMessage(message) {
                const messageElement = document.createElement('div');
                messageElement.classList.add('message', 'bot-message');
                messageElement.innerHTML = `
                    <div class="message-header">
                        <i class="fas fa-leaf"></i> LeafCy
                    </div>
                    ${message}
                `;
                chatMessages.appendChild(messageElement);
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }
            
            // Fungsi untuk menampilkan indikator typing
            function showTypingIndicator() {
                const typingIndicator = document.createElement('div');
                typingIndicator.classList.add('typing-indicator');
                typingIndicator.id = 'typingIndicator';
                typingIndicator.innerHTML = '<span></span><span></span><span></span>';
                chatMessages.appendChild(typingIndicator);
                chatMessages.scrollTop = chatMessages.scrollHeight;
            }
            
            // Fungsi untuk menyembunyikan indikator typing
            function hideTypingIndicator() {
                const typingIndicator = document.getElementById('typingIndicator');
                if (typingIndicator) {
                    typingIndicator.remove();
                }
            }
            
            // Fungsi untuk memproses input pengguna dan menghasilkan respons
            function processUserInput(input) {
                input = input.toLowerCase().trim();
                
                // Aturan-aturan respons untuk LeafCy
                if (input.includes('halo') || input.includes('hai') || input.includes('hi')) {
                    return "Halo! Saya LeafCy, asisten AI canggih dari Leaf'Corp. Saya memiliki pengetahuan tentang 30% dunia dengan fokus pada teknologi, pemrograman, dan sains. Ada yang bisa saya bantu?";
                } else if (input.includes('machine learning') || input.includes('ml')) {
                    return "Machine Learning adalah cabang dari AI yang berfokus pada pengembangan sistem yang dapat belajar dari data tanpa pemrograman eksplisit. Ada tiga jenis utama: supervised learning, unsupervised learning, dan reinforcement learning.<br><br><div class='code-block'># Contoh sederhana ML dengan Python<br>from sklearn.ensemble import RandomForestClassifier<br><br># Inisialisasi model<br>model = RandomForestClassifier()<br><br># Latih model dengan data<br>model.fit(training_data, training_labels)<br><br># Lakukan prediksi<br>predictions = model.predict(test_data)</div>";
                } else if (input.includes('python') || input.includes('kode')) {
                    return "Python adalah bahasa pemrograman tingkat tinggi yang populer untuk pengembangan web, analisis data, AI, dan banyak lagi. Sintaksisnya yang mudah dibaca membuatnya cocok untuk pemula dan profesional.<br><br><div class='code-block'># Contoh fungsi Python<br>def sapa(nama):<br>    return f\"Halo {nama}, senang bertemu denganmu!\"<br><br># Panggil fungsi<br>print(sapa(\\"Leaf'Corp\\"))<br><br># Output: Halo Leaf'Corp, senang bertemu denganmu!</div>";
                } else if (input.includes('etika ai') || input.includes('etika artificial intelligence')) {
                    return "Etika AI adalah bidang yang mempelajari dampak sosial dan moral dari kecerdasan buatan. Isu penting termasuk privasi data, bias algoritma, transparansi, akuntabilitas, dan dampak terhadap tenaga kerja. Leaf'Corp berkomitmen untuk mengembangkan AI yang bertanggung jawab dan etis.";
                } else if (input.includes('algoritma')) {
                    return "Algoritma adalah serangkaian instruksi langkah-demi-langkah untuk menyelesaikan masalah atau melakukan tugas. Contoh algoritma populer termasuk:<br><br>1. <b>Sorting</b>: QuickSort, MergeSort<br>2. <b>Searching</b>: Binary Search<br>3. <b>Graph</b>: Dijkstra, A*<br>4. <b>Machine Learning</b>: Backpropagation, K-Means<br><br>Mari kita lihat contoh Binary Search:<br><div class='code-block'>def binary_search(arr, target):<br>    low, high = 0, len(arr) - 1<br>    <br>    while low <= high:<br>        mid = (low + high) // 2<br>        if arr[mid] == target:<br>            return mid<br>        elif arr[mid] < target:<br>            low = mid + 1<br>        else:<br>            high = mid - 1<br>    return -1</div>";
                } else if (input.includes('neural network') || input.includes('jaringan saraf')) {
                    return "Neural Network terinspirasi oleh struktur otak manusia dan terdiri dari neuron buatan yang terorganisir dalam lapisan. Jaringan ini dapat mempelajari pola kompleks dari data. Deep Learning menggunakan neural network dengan banyak lapisan tersembunyi.<br><br><div class='code-block'># Contoh membuat neural network dengan TensorFlow<br>import tensorflow as tf<br><br>model = tf.keras.Sequential([<br>    tf.keras.layers.Dense(128, activation='relu'),<br>    tf.keras.layers.Dropout(0.2),<br>    tf.keras.layers.Dense(10, activation='softmax')<br>])<br><br>model.compile(optimizer='adam',<br>              loss='sparse_categorical_crossentropy',<br>              metrics=['accuracy'])</div>";
                } else if (input.includes('cloud computing') || input.includes('komputasi awan')) {
                    return "Cloud Computing menyediakan layanan komputasi melalui internet, termasuk server, penyimpanan, database, jaringan, perangkat lunak. Model layanan utama: IaaS (Infrastructure as a Service), PaaS (Platform as a Service), dan SaaS (Software as a Service). Penyedia cloud utama termasuk AWS, Azure, dan Google Cloud.";
                } else if (input.includes('leafcorp') || input.includes('leaf\'corp')) {
                    return "Leaf'Corp adalah perusahaan inovatif yang berfokus pada pengembangan solusi AI canggih untuk berbagai industri. Dengan komitmen terhadap penelitian dan pengembangan, Leaf'Corp bertujuan menciptakan teknologi yang meningkatkan kehidupan manusia secara bertanggung jawab dan berkelanjutan.";
                } else if (input.includes('ai') || input.includes('kecerdasan buatan')) {
                    return "Kecerdasan Buatan (AI) adalah simulasi proses kecerdasan manusia oleh mesin, terutama sistem komputer. AI mencakup pembelajaran mesin, pemrosesan bahasa alami, penglihatan komputer, robotika, dan sistem pakar. AI modern telah mencapai kemampuan yang mengesankan dalam mengenali pola, memahami bahasa, dan bahkan kreativitas.";
                } else if (input.includes('pemrograman') || input.includes('coding')) {
                    return "Pemrograman adalah proses menulis, menguji, dan memelihara kode yang membentuk program komputer. Bahasa populer termasuk Python, JavaScript, Java, C++, dan Go. Setiap bahasa memiliki kekuatan dan kegunaan tertentu. Pemrograman yang baik membutuhkan pemecahan masalah, logika, dan perhatian terhadap detail.";
                } else if (input.includes('data science') || input.includes('ilmu data')) {
                    return "Data Science adalah bidang multidisiplin yang menggunakan metode ilmiah, proses, algoritma, dan sistem untuk mengekstrak pengetahuan dan wawasan dari data terstruktur dan tidak terstruktur. Ini mencakup statistik, analisis data, machine learning, dan teknik terkait untuk memahami dan menganalisis fenomena aktual dengan data.";
                } else if (input.includes('cybersecurity') || input.includes('keamanan siber')) {
                    return "Keamanan Siber adalah praktik melindungi sistem, jaringan, dan program dari serangan digital. Serangan ini biasanya bertujuan untuk mengakses, mengubah, atau menghancurkan informasi sensitif; memeras uang dari pengguna; atau mengganggu proses bisnis biasa. Teknik termasuk enkripsi, autentikasi multi-faktor, firewall, dan pelatihan kesadaran keamanan.";
                } else if (input.includes('quantum computing') || input.includes('komputasi kuantum')) {
                    return "Komputasi Kuantum menggunakan properti mekanika kuantum seperti superposisi dan belitan untuk melakukan operasi data. Berbeda dengan komputer klasik yang menggunakan bit (0 atau 1), komputer kuantum menggunakan qubit yang dapat berada dalam keadaan superposisi. Ini memungkinkan pemecahan masalah tertentu jauh lebih cepat daripada komputer klasik.";
                } else if (input.includes('internet of things') || input.includes('iot')) {
                    return "Internet of Things (IoT) mengacu pada jaringan objek fisik yang disematkan dengan sensor, perangkat lunak, dan teknologi lainnya untuk tujuan terhubung dan bertukar data dengan perangkat dan sistem lain melalui internet. Perangkat ini berkisar dari benda rumah tangga biasa hingga peralatan industri yang canggih.";
                } else if (input.includes('blockchain') || input.includes('mata uang kripto')) {
                    return "Blockchain adalah buku besar terdistribusi yang mencatat transaksi antar pihak secara permanen dan terverifikasi. Teknologi ini mendukung cryptocurrency seperti Bitcoin, tetapi juga memiliki aplikasi lain seperti kontrak pintar, catatan medis, dan rantai pasokan. Blockchain menawarkan desentralisasi, transparansi, dan keabadian.";
                } else {
                    return "Maaf, saya tidak sepenuhnya memahami pertanyaan Anda. Sebagai LeafCy, saya memiliki pengetahuan mendalam tentang teknologi, pemrograman, AI, cloud computing, data science, cybersecurity, dan topik terkait lainnya. Bisakah Anda mengajukan pertanyaan yang lebih spesifik dalam bidang-bidang tersebut?";
                }
            }
            
            // Fungsi untuk mengirim pesan
            function sendMessage() {
                const message = userInput.value.trim();
                if (message === '') return;
                
                // Tambahkan pesan pengguna
                addUserMessage(message);
                userInput.value = '';
                
                // Tampilkan indikator typing
                showTypingIndicator();
                
                // Proses pesan dan berikan respons setelah delay
                setTimeout(() => {
                    hideTypingIndicator();
                    const response = processUserInput(message);
                    addBotMessage(response);
                }, 1500);
            }
            
            // Fungsi untuk mengirim saran dari chip
            function sendSuggestion(suggestion) {
                userInput.value = suggestion;
                sendMessage();
            }
            
            // Event listener untuk tombol kirim
            sendButton.addEventListener('click', sendMessage);
            
            // Event listener untuk menekan enter pada input
            userInput.addEventListener('keypress', function(e) {
                if (e.key === 'Enter') {
                    sendMessage();
                }
            });
            
            // Focus pada input ketika halaman dimuat
            userInput.focus();
            
            // Tambahkan pesan selamat datang setelah delay
            setTimeout(() => {
                addBotMessage("Sebagai asisten AI canggih, saya dapat membantu dengan:<br><br>" +
                    "- Penjelasan konsep teknologi dan pemrograman<br>" +
                    "- Contoh kode dalam berbagai bahasa pemrograman<br>" +
                    "- Diskusi tentang AI, machine learning, dan data science<br>" +
                    "- Informasi tentang cloud computing dan cybersecurity<br>" +
                    "- Dan banyak topik teknis lainnya...<br><br>" +
                    "Silakan tanyakan apa yang ingin Anda ketahui!");
            }, 2000);
        });
    </script>
</body>
</html>
