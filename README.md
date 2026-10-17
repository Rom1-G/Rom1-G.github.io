# Rom1-G.github.io
Bibliothèque
<!DOCTYPE html>
<html lang="fr" class="h-full bg-slate-50">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Ma Bibliothèque</title>
    
    <!-- PWA & Mobile Optimization Tags -->
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Bibliothèque">
    <meta name="theme-color" content="#4f46e5">

    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    
    <!-- HTML5-QRCode Library -->
    <script src="https://unpkg.com/html5-qrcode" type="text/javascript"></script>

    <!-- Inter Font -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">

    <style>
        body {
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
            -webkit-tap-highlight-color: transparent;
            touch-action: manipulation;
        }
        
        /* Hide html5-qrcode standard visual artifacts */
        #reader video {
            object-fit: cover !important;
            border-radius: 0.75rem;
        }
        #reader {
            border: none !important;
        }
        #reader__dashboard {
            display: none !important;
        }

        /* Custom Scrollbar */
        ::-webkit-scrollbar {
            width: 4px;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
    </style>
</head>
<body class="h-full text-slate-800 flex flex-col bg-slate-100">

    <!-- App Header -->
    <header class="bg-indigo-600 text-white pt-10 pb-4 px-4 sticky top-0 z-30 shadow-md">
        <div class="max-w-md mx-auto flex justify-between items-center">
            <div class="flex items-center space-x-2">
                <i data-lucide="book-open" class="w-7 h-7"></i>
                <h1 class="text-xl font-bold tracking-tight">Ma Bibliothèque</h1>
            </div>
            <div class="flex items-center space-x-2">
                <button onclick="toggleModal('infoModal')" class="p-2 rounded-full hover:bg-indigo-700 transition">
                    <i data-lucide="help-circle" class="w-6 h-6"></i>
                </button>
                <button onclick="toggleModal('settingsModal')" class="p-2 rounded-full hover:bg-indigo-700 transition">
                    <i data-lucide="settings" class="w-6 h-6"></i>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Content Area -->
    <main class="flex-1 max-w-md w-full mx-auto p-4 pb-24 overflow-y-auto">
        
        <!-- Search & Filter Controls -->
        <section class="bg-white rounded-2xl p-4 shadow-sm mb-4 space-y-3 border border-slate-200">
            <!-- Search Bar -->
            <div class="relative">
                <i data-lucide="search" class="w-5 h-5 absolute left-3 top-1/2 -translate-y-1/2 text-slate-400"></i>
                <input type="text" id="searchInput" oninput="renderCollection()" placeholder="Titre, auteur, éditeur..." 
                    class="w-full pl-10 pr-4 py-2.5 bg-slate-100 border-none rounded-xl text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
            </div>

            <!-- Age Filter Pills -->
            <div class="flex gap-2 overflow-x-auto pb-1 no-scrollbar text-xs">
                <button onclick="setFilter('all')" id="filter-all" class="filter-pill px-3 py-1.5 rounded-full font-medium bg-indigo-600 text-white whitespace-nowrap shadow-sm">
                    Tous (<span id="count-all">0</span>)
                </button>
                <button onclick="setFilter('0-3 ans')" id="filter-0-3 ans" class="filter-pill px-3 py-1.5 rounded-full font-medium bg-slate-100 text-slate-600 whitespace-nowrap">
                    0-3 ans
                </button>
                <button onclick="setFilter('4-6 ans')" id="filter-4-6 ans" class="filter-pill px-3 py-1.5 rounded-full font-medium bg-slate-100 text-slate-600 whitespace-nowrap">
                    4-6 ans
                </button>
                <button onclick="setFilter('7-10 ans')" id="filter-7-10 ans" class="filter-pill px-3 py-1.5 rounded-full font-medium bg-slate-100 text-slate-600 whitespace-nowrap">
                    7-10 ans
                </button>
                <button onclick="setFilter('11-14 ans')" id="filter-11-14 ans" class="filter-pill px-3 py-1.5 rounded-full font-medium bg-slate-100 text-slate-600 whitespace-nowrap">
                    11-14 ans
                </button>
                <button onclick="setFilter('Adolescent')" id="filter-Adolescent" class="filter-pill px-3 py-1.5 rounded-full font-medium bg-slate-100 text-slate-600 whitespace-nowrap">
                    Ado
                </button>
                <button onclick="setFilter('Adulte')" id="filter-Adulte" class="filter-pill px-3 py-1.5 rounded-full font-medium bg-slate-100 text-slate-600 whitespace-nowrap">
                    Adulte
                </button>
            </div>
        </section>

        <!-- Book Collection Grid/List -->
        <section id="bookList" class="space-y-3">
            <!-- Books rendered here dynamically -->
        </section>

        <!-- Empty State -->
        <div id="emptyState" class="hidden text-center py-12 px-4">
            <div class="w-16 h-16 bg-indigo-50 text-indigo-500 rounded-full flex items-center justify-center mx-auto mb-3">
                <i data-lucide="library" class="w-8 h-8"></i>
            </div>
            <h3 class="font-semibold text-slate-700 text-lg">Aucun livre trouvé</h3>
            <p class="text-slate-500 text-sm mt-1">Scannez un code-barres pour ajouter votre premier livre à la collection.</p>
        </div>

    </main>

    <!-- Floating Action Bar / Scan Trigger -->
    <div class="fixed bottom-0 left-0 right-0 p-4 bg-gradient-to-t from-slate-100 via-slate-100/90 to-transparent pointer-events-none z-20">
        <div class="max-w-md mx-auto flex justify-center pointer-events-auto">
            <button onclick="startScanner()" class="flex items-center space-x-2 bg-indigo-600 text-white font-semibold px-6 py-3.5 rounded-full shadow-lg shadow-indigo-600/30 hover:bg-indigo-700 active:scale-95 transition">
                <i data-lucide="scan-barcode" class="w-6 h-6"></i>
                <span>Scanner un livre</span>
            </button>
        </div>
    </div>

    <!-- Scanner Modal -->
    <div id="scannerModal" class="fixed inset-0 bg-slate-900/90 z-50 hidden flex flex-col justify-between p-4 backdrop-blur-sm">
        <div class="flex justify-between items-center text-white pt-6">
            <h2 class="text-lg font-semibold">Alignez le code-barres (ISBN)</h2>
            <button onclick="stopScanner()" class="p-2 bg-white/10 rounded-full hover:bg-white/20 transition">
                <i data-lucide="x" class="w-6 h-6"></i>
            </button>
        </div>

        <div class="my-auto relative w-full max-w-sm mx-auto overflow-hidden rounded-2xl bg-black shadow-2xl">
            <div id="reader" class="w-full"></div>
            <!-- Loading Indicator during API lookup -->
            <div id="scanLoading" class="absolute inset-0 bg-slate-900/80 hidden flex flex-col items-center justify-center text-white space-y-3">
                <div class="w-8 h-8 border-4 border-indigo-500 border-t-transparent rounded-full animate-spin"></div>
                <p class="text-sm font-medium">Recherche du livre en cours...</p>
            </div>
        </div>

        <!-- Manual Input Option -->
        <div class="text-center pb-6">
            <button onclick="promptManualISBN()" class="text-white/80 hover:text-white text-sm underline">
                Entrer un code-barres ou ISBN manuellement
            </button>
        </div>
    </div>

    <!-- Book Details & Age Selection Modal -->
    <div id="bookModal" class="fixed inset-0 bg-slate-900/60 z-50 hidden flex items-end sm:items-center justify-center p-0 sm:p-4 backdrop-blur-sm">
        <div class="bg-white w-full max-w-md rounded-t-3xl sm:rounded-2xl max-h-[90vh] flex flex-col overflow-hidden shadow-2xl animate-in slide-in-from-bottom duration-200">
            
            <div class="p-4 border-b border-slate-100 flex justify-between items-center">
                <h3 class="font-bold text-slate-800">Ajouter à la bibliothèque</h3>
                <button onclick="toggleModal('bookModal', false)" class="p-1 rounded-full text-slate-400 hover:text-slate-600">
                    <i data-lucide="x" class="w-6 h-6"></i>
                </button>
            </div>

            <div class="p-5 overflow-y-auto space-y-4">
                <div class="flex space-x-4">
                    <img id="modalCover" src="" alt="Couverture" class="w-24 h-36 object-cover rounded-lg shadow-md bg-slate-100 border border-slate-200 flex-shrink-0">
                    <div class="space-y-1.5 flex-1 min-w-0">
                        <h4 id="modalTitle" class="font-bold text-slate-900 text-base leading-snug line-clamp-2">Titre du livre</h4>
                        <p id="modalAuthor" class="text-slate-600 text-sm font-medium line-clamp-1">Auteur</p>
                        <p id="modalPublisher" class="text-slate-400 text-xs line-clamp-1">Éditeur</p>
                        <p id="modalISBN" class="text-slate-400 text-xs font-mono">ISBN: -</p>
                    </div>
                </div>

                <!-- Age Category Picker -->
                <div class="pt-2 border-t border-slate-100">
                    <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-2">Tranche d'âge recommandée *</label>
                    <div class="grid grid-cols-3 gap-2" id="modalAgeGrid">
                        <button type="button" onclick="selectAgeGroup('0-3 ans')" class="age-btn px-3 py-2 text-xs font-semibold rounded-xl border border-slate-200 text-slate-700 hover:border-indigo-500 transition">0-3 ans</button>
                        <button type="button" onclick="selectAgeGroup('4-6 ans')" class="age-btn px-3 py-2 text-xs font-semibold rounded-xl border border-slate-200 text-slate-700 hover:border-indigo-500 transition">4-6 ans</button>
                        <button type="button" onclick="selectAgeGroup('7-10 ans')" class="age-btn px-3 py-2 text-xs font-semibold rounded-xl border border-slate-200 text-slate-700 hover:border-indigo-500 transition">7-10 ans</button>
                        <button type="button" onclick="selectAgeGroup('11-14 ans')" class="age-btn px-3 py-2 text-xs font-semibold rounded-xl border border-slate-200 text-slate-700 hover:border-indigo-500 transition">11-14 ans</button>
                        <button type="button" onclick="selectAgeGroup('Adolescent')" class="age-btn px-3 py-2 text-xs font-semibold rounded-xl border border-slate-200 text-slate-700 hover:border-indigo-500 transition">Adolescent</button>
                        <button type="button" onclick="selectAgeGroup('Adulte')" class="age-btn px-3 py-2 text-xs font-semibold rounded-xl border border-slate-200 text-slate-700 hover:border-indigo-500 transition">Adulte</button>
                    </div>
                </div>

                <!-- Description -->
                <div class="pt-2">
                    <label class="block text-xs font-semibold text-slate-500 uppercase tracking-wider mb-1">Résumé</label>
                    <p id="modalDescription" class="text-slate-600 text-xs leading-relaxed max-h-24 overflow-y-auto">Aucune description disponible.</p>
                </div>
            </div>

            <div class="p-4 border-t border-slate-100 bg-slate-50 flex space-x-3">
                <button onclick="toggleModal('bookModal', false)" class="flex-1 py-3 text-sm font-semibold text-slate-600 rounded-xl hover:bg-slate-200 transition">Annuler</button>
                <button onclick="saveBook()" class="flex-1 py-3 text-sm font-semibold text-white bg-indigo-600 rounded-xl hover:bg-indigo-700 shadow-sm transition">Enregistrer</button>
            </div>
        </div>
    </div>

    <!-- Info / PWA Instructions Modal -->
    <div id="infoModal" class="fixed inset-0 bg-slate-900/60 z-50 hidden flex items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white w-full max-w-md rounded-2xl p-6 space-y-4 shadow-2xl">
            <div class="flex justify-between items-center border-b border-slate-100 pb-3">
                <h3 class="font-bold text-slate-800 text-lg flex items-center gap-2">
                    <i data-lucide="smartphone" class="w-5 h-5 text-indigo-600"></i> Installer sur iPhone
                </h3>
                <button onclick="toggleModal('infoModal', false)" class="text-slate-400 hover:text-slate-600">
                    <i data-lucide="x" class="w-6 h-6"></i>
                </button>
            </div>
            
            <ol class="space-y-3 text-sm text-slate-600">
                <li class="flex items-start gap-3">
                    <span class="bg-indigo-100 text-indigo-700 font-bold rounded-full w-6 h-6 flex items-center justify-center flex-shrink-0 text-xs">1</span>
                    <span>Ouvrez cette page web dans <strong>Safari</strong> sur votre iPhone.</span>
                </li>
                <li class="flex items-start gap-3">
                    <span class="bg-indigo-100 text-indigo-700 font-bold rounded-full w-6 h-6 flex items-center justify-center flex-shrink-0 text-xs">2</span>
                    <span>Appuyez sur le bouton de partage <i data-lucide="share" class="w-4 h-4 inline text-indigo-600"></i> en bas de votre écran Safari.</span>
                </li>
                <li class="flex items-start gap-3">
                    <span class="bg-indigo-100 text-indigo-700 font-bold rounded-full w-6 h-6 flex items-center justify-center flex-shrink-0 text-xs">3</span>
                    <span>Faites défiler vers le bas et sélectionnez <strong>"Sur l'écran d'accueil"</strong>.</span>
                </li>
                <li class="flex items-start gap-3">
                    <span class="bg-indigo-100 text-indigo-700 font-bold rounded-full w-6 h-6 flex items-center justify-center flex-shrink-0 text-xs">4</span>
                    <span>L'application sera installée sur votre iPhone comme une vraie application !</span>
                </li>
            </ol>

            <button onclick="toggleModal('infoModal', false)" class="w-full py-3 bg-indigo-600 text-white font-semibold rounded-xl text-sm hover:bg-indigo-700 transition">Compris !</button>
        </div>
    </div>

    <!-- Settings & Data Management Modal -->
    <div id="settingsModal" class="fixed inset-0 bg-slate-900/60 z-50 hidden flex items-center justify-center p-4 backdrop-blur-sm">
        <div class="bg-white w-full max-w-md rounded-2xl p-6 space-y-4 shadow-2xl">
            <div class="flex justify-between items-center border-b border-slate-100 pb-3">
                <h3 class="font-bold text-slate-800 text-lg">Gestion des Données</h3>
                <button onclick="toggleModal('settingsModal', false)" class="text-slate-400 hover:text-slate-600">
                    <i data-lucide="x" class="w-6 h-6"></i>
                </button>
            </div>

            <div class="space-y-3">
                <button onclick="exportData()" class="w-full flex items-center justify-between p-3 bg-slate-50 border border-slate-200 rounded-xl hover:bg-slate-100 transition text-sm font-medium text-slate-700">
                    <span class="flex items-center gap-2"><i data-lucide="download" class="w-4 h-4 text-indigo-600"></i> Exporter la collection (JSON)</span>
                    <i data-lucide="chevron-right" class="w-4 h-4 text-slate-400"></i>
                </button>

                <label class="w-full flex items-center justify-between p-3 bg-slate-50 border border-slate-200 rounded-xl hover:bg-slate-100 transition text-sm font-medium text-slate-700 cursor-pointer">
                    <span class="flex items-center gap-2"><i data-lucide="upload" class="w-4 h-4 text-indigo-600"></i> Importer une sauvegarde</span>
                    <input type="file" id="importFile" accept=".json" onchange="importData(event)" class="hidden">
                    <i data-lucide="chevron-right" class="w-4 h-4 text-slate-400"></i>
                </label>

                <button onclick="clearCollection()" class="w-full flex items-center justify-between p-3 bg-red-50 border border-red-200 rounded-xl hover:bg-red-100 transition text-sm font-medium text-red-600">
                    <span class="flex items-center gap-2"><i data-lucide="trash-2" class="w-4 h-4"></i> Effacer toute la bibliothèque</span>
                </button>
            </div>
        </div>
    </div>

    <!-- JavaScript Application Logic -->
    <script>
        // State Management
        let books = [];
        let activeFilter = 'all';
        let currentScannedBook = null;
        let html5QrCode = null;

        // Initialize App
        window.addEventListener('DOMContentLoaded', () => {
            lucide.createIcons();
            loadBooks();
            renderCollection();
        });

        // Local Storage Handler
        function loadBooks() {
            const stored = localStorage.getItem('my_book_collection');
            books = stored ? JSON.parse(stored) : [];
        }

        function saveBooksToStorage() {
            localStorage.setItem('my_book_collection', JSON.stringify(books));
            renderCollection();
        }

        // Camera & Scanner Logic
        async function startScanner() {
            toggleModal('scannerModal', true);
            
            if (!html5QrCode) {
                html5QrCode = new Html5Qrcode("reader");
            }

            const config = {
                fps: 10,
                qrbox: { width: 250, height: 150 },
                aspectRatio: 1.0
            };

            try {
                await html5QrCode.start(
                    { facingMode: "environment" },
                    config,
                    onScanSuccess
                );
            } catch (err) {
                console.error("Camera access error:", err);
                alert("Impossible d'accéder à la caméra. Vérifiez vos autorisations.");
                stopScanner();
            }
        }

        async function stopScanner() {
            if (html5QrCode && html5QrCode.isScanning) {
                await html5QrCode.stop();
            }
            toggleModal('scannerModal', false);
        }

        function onScanSuccess(decodedText) {
            // Clean ISBN string (remove hyphens or spaces)
            const cleanedISBN = decodedText.replace(/[- ]/g, '');
            stopScanner();
            fetchBookDetails(cleanedISBN);
        }

        function promptManualISBN() {
            stopScanner();
            const isbn = prompt("Veuillez entrer le numéro ISBN du livre :");
            if (isbn) {
                fetchBookDetails(isbn.replace(/[- ]/g, ''));
            }
        }

        // API Lookup via Google Books
        async function fetchBookDetails(isbn) {
            document.getElementById('scanLoading').classList.remove('hidden');
            toggleModal('scannerModal', true);

            try {
                const response = await fetch(`https://www.googleapis.com/books/v1/volumes?q=isbn:${isbn}`);
                const data = await response.json();

                if (data.totalItems > 0) {
                    const item = data.items[0].volumeInfo;
                    
                    // Fallback cover image
                    let coverUrl = item.imageLinks?.thumbnail || item.imageLinks?.smallThumbnail || '';
                    if (coverUrl.startsWith('http:')) {
                        coverUrl = coverUrl.replace('http:', 'https:');
                    }
                    if (!coverUrl) {
                        coverUrl = 'https://placehold.co/150x220/e2e8f0/64748b?text=Pas+d%27image';
                    }

                    currentScannedBook = {
                        id: Date.now().toString(),
                        isbn: isbn,
                        title: item.title || 'Titre inconnu',
                        authors: item.authors ? item.authors.join(', ') : 'Auteur inconnu',
                        publisher: item.publisher || 'Éditeur non spécifié',
                        description: item.description || 'Aucun résumé disponible pour ce livre.',
                        cover: coverUrl,
                        ageGroup: '7-10 ans', // Default category
                        addedAt: new Date().toISOString()
                    };

                    populateBookModal(currentScannedBook);
                    toggleModal('scannerModal', false);
                    toggleModal('bookModal', true);
                } else {
                    alert("Avertissement: Aucun livre trouvé pour l'ISBN " + isbn);
                    toggleModal('scannerModal', false);
                }
            } catch (error) {
                console.error("API Fetch Error:", error);
                alert("Erreur de connexion lors de la recherche du livre.");
                toggleModal('scannerModal', false);
            } finally {
                document.getElementById('scanLoading').classList.add('hidden');
            }
        }

        // Modal Populate Logic
        function populateBookModal(book) {
            document.getElementById('modalCover').src = book.cover;
            document.getElementById('modalTitle').textContent = book.title;
            document.getElementById('modalAuthor').textContent = book.authors;
            document.getElementById('modalPublisher').textContent = book.publisher;
            document.getElementById('modalISBN').textContent = 'ISBN: ' + book.isbn;
            document.getElementById('modalDescription').textContent = book.description;
            
            selectAgeGroup(book.ageGroup);
        }

        function selectAgeGroup(age) {
            if (currentScannedBook) {
                currentScannedBook.ageGroup = age;
            }
            
            document.querySelectorAll('#modalAgeGrid .age-btn').forEach(btn => {
                if (btn.textContent.trim() === age) {
                    btn.className = "age-btn px-3 py-2 text-xs font-semibold rounded-xl bg-indigo-600 text-white shadow-sm transition";
                } else {
                    btn.className = "age-btn px-3 py-2 text-xs font-semibold rounded-xl border border-slate-200 text-slate-700 hover:border-indigo-500 transition";
                }
            });
        }

        function saveBook() {
            if (!currentScannedBook) return;
            
            // Check if book already exists in collection
            const exists = books.some(b => b.isbn === currentScannedBook.isbn);
            if (exists) {
                if (!confirm("Ce livre existe déjà dans votre bibliothèque. Voulez-vous l'ajouter à nouveau ?")) {
                    toggleModal('bookModal', false);
                    return;
                }
            }

            books.unshift(currentScannedBook);
            saveBooksToStorage();
            toggleModal('bookModal', false);
            currentScannedBook = null;
        }

        function deleteBook(id) {
            if (confirm("Voulez-vous supprimer ce livre de votre bibliothèque ?")) {
                books = books.filter(b => b.id !== id);
                saveBooksToStorage();
            }
        }

        // Filtering and Rendering
        function setFilter(filter) {
            activeFilter = filter;
            document.querySelectorAll('.filter-pill').forEach(btn => {
                btn.className = "filter-pill px-3 py-1.5 rounded-full font-medium bg-slate-100 text-slate-600 whitespace-nowrap";
            });
            
            const activeBtn = document.getElementById(`filter-${filter}`);
            if (activeBtn) {
                activeBtn.className = "filter-pill px-3 py-1.5 rounded-full font-medium bg-indigo-600 text-white whitespace-nowrap shadow-sm";
            }
            
            renderCollection();
        }

        function renderCollection() {
            const listEl = document.getElementById('bookList');
            const emptyEl = document.getElementById('emptyState');
            const searchQuery = document.getElementById('searchInput').value.toLowerCase();

            // Filter logic
            const filtered = books.filter(book => {
                const matchesFilter = activeFilter === 'all' || book.ageGroup === activeFilter;
                const matchesSearch = book.title.toLowerCase().includes(searchQuery) || 
                                      book.authors.toLowerCase().includes(searchQuery) ||
                                      book.publisher.toLowerCase().includes(searchQuery);
                return matchesFilter && matchesSearch;
            });

            // Update Count Badge
            document.getElementById('count-all').textContent = books.length;

            if (filtered.length === 0) {
                listEl.innerHTML = '';
                emptyEl.classList.remove('hidden');
                return;
            }

            emptyEl.classList.add('hidden');
            listEl.innerHTML = filtered.map(book => `
                <div class="bg-white rounded-2xl p-3 shadow-sm border border-slate-200 flex space-x-3 relative items-start">
                    <img src="${book.cover}" alt="${book.title}" class="w-16 h-24 object-cover rounded-lg bg-slate-100 flex-shrink-0 shadow-sm border">
                    <div class="flex-1 min-w-0 pr-6 space-y-1">
                        <span class="inline-block px-2 py-0.5 bg-indigo-50 text-indigo-700 text-[10px] font-bold rounded-md uppercase tracking-wider">
                            ${book.ageGroup}
                        </span>
                        <h4 class="font-bold text-slate-800 text-sm leading-tight line-clamp-1">${book.title}</h4>
                        <p class="text-slate-600 text-xs font-medium line-clamp-1">${book.authors}</p>
                        <p class="text-slate-400 text-[11px] line-clamp-1">${book.publisher}</p>
                    </div>
                    <button onclick="deleteBook('${book.id}')" class="absolute top-3 right-3 p-1 text-slate-300 hover:text-red-500 transition">
                        <i data-lucide="trash-2" class="w-4 h-4"></i>
                    </button>
                </div>
            `).join('');

            lucide.createIcons();
        }

        // Modal Helpers
        function toggleModal(id, show) {
            const el = document.getElementById(id);
            if (!el) return;
            if (show === undefined) {
                el.classList.toggle('hidden');
            } else if (show) {
                el.classList.remove('hidden');
            } else {
                el.classList.add('hidden');
            }
        }

        // Data Export & Import
        function exportData() {
            if (books.length === 0) {
                alert("Votre bibliothèque est vide.");
                return;
            }
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(books, null, 2));
            const downloadAnchor = document.createElement('a');
            downloadAnchor.setAttribute("href", dataStr);
            downloadAnchor.setAttribute("download", `bibliotheque_backup_${new Date().toISOString().slice(0,10)}.json`);
            document.body.appendChild(downloadAnchor);
            downloadAnchor.click();
            downloadAnchor.remove();
        }

        function importData(event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const importedBooks = JSON.parse(e.target.result);
                    if (Array.isArray(importedBooks)) {
                        books = importedBooks;
                        saveBooksToStorage();
                        alert("Collection importée avec succès !");
                        toggleModal('settingsModal', false);
                    } else {
                        alert("Le fichier JSON sélectionné n'est pas valide.");
                    }
                } catch (err) {
                    alert("Erreur lors de la lecture du fichier JSON.");
                }
            };
            reader.readAsText(file);
        }

        function clearCollection() {
            if (confirm("Êtes-vous sûr de vouloir supprimer tous les livres ? Cette action est irréversible.")) {
                books = [];
                saveBooksToStorage();
                toggleModal('settingsModal', false);
            }
        }
    </script>
</body>
</html>
