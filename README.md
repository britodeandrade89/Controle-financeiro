<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle Financeiro</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        .glass-card {
            background: rgba(23, 23, 23, 0.6);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 1rem;
        }
        .modal-glass {
            background: rgba(10, 10, 10, 0.7);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        #app-container::-webkit-scrollbar, #ai-response-container::-webkit-scrollbar { width: 6px; }
        #app-container::-webkit-scrollbar-track, #ai-response-container::-webkit-scrollbar-track { background: transparent; }
        #app-container::-webkit-scrollbar-thumb, #ai-response-container::-webkit-scrollbar-thumb { background: #404040; border-radius: 3px; }
        #app-container::-webkit-scrollbar-thumb:hover, #ai-response-container::-webkit-scrollbar-thumb:hover { background: #525252; }
        .paid-item {
            text-decoration: line-through;
            opacity: 0.5;
        }
    </style>
</head>
<body class="antialiased bg-neutral-900 flex items-center justify-center min-h-screen p-4">
    <!-- Phone Frame -->
    <div class="w-full max-w-sm mx-auto bg-black rounded-[40px] shadow-2xl p-2 border-4 border-neutral-700 relative">
        <div class="absolute top-3 left-1/2 -translate-x-1/2 w-20 h-4 bg-neutral-900 rounded-b-lg z-10"></div>
        <div id="app-container" class="h-[750px] overflow-y-auto rounded-[30px] bg-[#0a0a0a]">
            <div id="app" class="p-4 text-white">
                <!-- Header -->
                <header class="flex flex-col items-center gap-4 mb-8">
                    <div>
                        <h1 class="text-3xl font-bold text-center">Controle <span class="text-cyan-400">Financeiro</span></h1>
                        <p class="text-neutral-400 text-center text-sm">Seu painel de controle financeiro.</p>
                    </div>
                    <div class="flex items-center gap-2 mt-2 glass-card p-2">
                        <button id="prev-month" class="p-2 rounded-full hover:bg-neutral-700 transition-colors">
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m15 18-6-6 6-6"/></svg>
                        </button>
                        <h2 id="current-month-display" class="text-lg font-semibold w-36 text-center"></h2>
                        <button id="next-month" class="p-2 rounded-full hover:bg-neutral-700 transition-colors">
                            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="m9 18 6-6-6-6"/></svg>
                        </button>
                    </div>
                </header>

                <!-- Loading state -->
                <div id="loading-state" class="text-center py-20">
                    <p class="text-lg text-neutral-400">Carregando seus dados...</p>
                </div>

                <main id="main-content" class="hidden">
                    <!-- Summary Cards -->
                    <div class="grid grid-cols-2 gap-4 mb-6">
                        <div class="glass-card p-4">
                            <p class="text-neutral-400 text-xs mb-1">Entradas</p>
                            <p id="summary-income" class="text-lg font-bold"></p>
                        </div>
                        <div class="glass-card p-4">
                            <p class="text-neutral-400 text-xs mb-1">Saídas</p>
                            <p id="summary-expenses" class="text-lg font-bold"></p>
                        </div>
                        <div class="glass-card p-4">
                            <p class="text-neutral-400 text-xs mb-1">Pendente</p>
                            <p id="summary-pending" class="text-lg font-bold"></p>
                        </div>
                        <div class="glass-card p-4">
                            <p class="text-neutral-400 text-xs mb-1">Saldo Final</p>
                            <p id="summary-balance" class="text-lg font-bold"></p>
                        </div>
                    </div>
                    
                    <!-- AI Analysis Button -->
                    <div class="mb-6">
                        <button id="ai-analysis-btn" class="w-full flex items-center justify-center gap-2 bg-gradient-to-r from-purple-500 to-cyan-500 text-white font-semibold py-3 px-4 rounded-lg transition-transform hover:scale-105 shadow-lg shadow-cyan-500/20">
                            <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="opacity-80"><path d="M12 3c.433 0 .865.045 1.288.132M5 12a7 7 0 1 0 14 0 7 7 0 1 0-14 0"/><path d="M12 3v-1.5m0 16.5V19.5m7-11.5h1.5m-16.5 0H5m13.288 1.212 1.06-1.06M4.652 4.652l1.06 1.06m12.126 12.126 1.06 1.06m-14.246-1.06-1.06 1.06"/></svg>
                            Analisar com IA ✨
                        </button>
                    </div>

                    <!-- Main Content Area -->
                    <div class="flex flex-col gap-6">
                        <!-- Income Section -->
                         <div class="glass-card p-4">
                            <div class="flex justify-between items-center mb-4">
                                <h3 class="text-lg font-semibold">Entradas</h3>
                                <button id="add-income-btn" class="flex items-center gap-2 bg-green-500/20 hover:bg-green-500/40 text-green-400 font-semibold py-2 px-3 rounded-lg transition-colors text-sm">
                                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M5 12h14"/><path d="M12 5v14"/></svg>
                                    Adicionar
                                </button>
                            </div>
                            <div id="income-list" class="space-y-3 max-h-[150px] overflow-y-auto pr-2"></div>
                        </div>

                        <!-- Expenses Section -->
                        <div class="glass-card p-4">
                            <div class="flex justify-between items-center mb-4">
                                <h3 class="text-lg font-semibold">Despesas do Mês</h3>
                                <button id="add-expense-btn" class="flex items-center gap-2 bg-cyan-500 hover:bg-cyan-600 text-white font-semibold py-2 px-3 rounded-lg transition-colors text-sm">
                                    <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M5 12h14"/><path d="M12 5v14"/></svg>
                                    Adicionar
                                </button>
                            </div>
                            <div class="space-y-4 max-h-[350px] overflow-y-auto pr-2">
                                 <div id="fixed-expenses-list">
                                    <h4 class="text-md font-semibold text-neutral-300 mb-2 border-b border-neutral-700 pb-2">Fixas</h4>
                                    <div id="fixed-list-items" class="space-y-3"></div>
                                 </div>
                                 <div id="variable-expenses-list" class="mt-6">
                                    <h4 class="text-md font-semibold text-neutral-300 mb-2 border-b border-neutral-700 pb-2">Variáveis</h4>
                                    <div id="variable-list-items" class="space-y-3"></div>
                                 </div>
                            </div>
                        </div>

                        <!-- Chart Section -->
                        <div class="glass-card p-4">
                            <h3 class="text-lg font-semibold mb-4 text-center">Distribuição de Despesas</h3>
                            <div class="flex justify-center items-center h-full min-h-[200px]">
                                <canvas id="expenses-chart"></canvas>
                            </div>
                        </div>
                    </div>
                </main>
            </div>
        </div>
    </div>


    <!-- Modal for adding transactions -->
    <div id="modal" class="fixed inset-0 bg-black/70 flex items-center justify-center p-4 hidden z-50">
        <div id="modal-content" class="modal-glass w-full max-w-md p-6 rounded-2xl shadow-2xl">
            <div class="flex justify-between items-center mb-6">
                <h2 id="modal-title" class="text-2xl font-bold text-white"></h2>
                <button id="close-modal-btn" class="p-2 rounded-full hover:bg-neutral-700 transition-colors text-white">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
                </button>
            </div>
            <form id="modal-form" class="text-white">
                <div class="mb-4">
                    <label for="description" class="block text-sm font-medium text-neutral-400 mb-2">Descrição</label>
                    <input type="text" id="description" name="description" class="w-full bg-neutral-800 border border-neutral-600 rounded-lg p-3 focus:ring-2 focus:ring-cyan-500 focus:border-cyan-500 outline-none transition" required>
                </div>
                <div class="mb-4">
                    <label for="amount" class="block text-sm font-medium text-neutral-400 mb-2">Valor</label>
                    <input type="number" id="amount" name="amount" step="0.01" class="w-full bg-neutral-800 border border-neutral-600 rounded-lg p-3 focus:ring-2 focus:ring-cyan-500 focus:border-cyan-500 outline-none transition" required>
                </div>
                <div id="expense-fields" class="hidden">
                    <div class="mb-4">
                        <label for="category" class="block text-sm font-medium text-neutral-400 mb-2">Tipo de Despesa</label>
                        <select id="category" name="category" class="w-full bg-neutral-800 border border-neutral-600 rounded-lg p-3 focus:ring-2 focus:ring-cyan-500 focus:border-cyan-500 outline-none transition">
                            <option value="variavel">Variável (Pagamento Único)</option>
                            <option value="fixa">Fixa (Recorrente)</option>
                        </select>
                    </div>
                    <div id="installments-fields" class="hidden grid grid-cols-2 gap-4">
                         <div>
                            <label for="current-installment" class="block text-sm font-medium text-neutral-400 mb-2">Parcela Atual</label>
                            <input type="number" id="current-installment" name="current-installment" value="1" class="w-full bg-neutral-800 border border-neutral-600 rounded-lg p-3 focus:ring-2 focus:ring-cyan-500 focus:border-cyan-500 outline-none transition">
                        </div>
                        <div>
                            <label for="total-installments" class="block text-sm font-medium text-neutral-400 mb-2">Total de Parcelas</label>
                            <input type="number" id="total-installments" name="total-installments" value="1" class="w-full bg-neutral-800 border border-neutral-600 rounded-lg p-3 focus:ring-2 focus:ring-cyan-500 focus:border-cyan-500 outline-none transition">
                        </div>
                    </div>
                </div>
                <button type="submit" id="modal-submit-btn" class="w-full font-semibold py-3 px-4 rounded-lg transition-colors mt-4"></button>
            </form>
        </div>
    </div>

    <!-- Modal for AI Analysis -->
    <div id="ai-modal" class="fixed inset-0 bg-black/70 flex items-center justify-center p-4 hidden z-50">
        <div id="ai-modal-content" class="modal-glass w-full max-w-md p-6 rounded-2xl shadow-2xl flex flex-col">
            <div class="flex justify-between items-center mb-4">
                <h2 id="ai-modal-title" class="text-2xl font-bold text-white">Análise Financeira ✨</h2>
                <button id="close-ai-modal-btn" class="p-2 rounded-full hover:bg-neutral-700 transition-colors text-white">
                    <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg>
                </button>
            </div>
            <div id="ai-response-container" class="text-neutral-300 space-y-4 max-h-[60vh] overflow-y-auto pr-2">
                <!-- AI response will be injected here -->
            </div>
        </div>
    </div>
    
    <script type="module">
        // Firebase SDK
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, setDoc, onSnapshot, updateDoc, arrayUnion, arrayRemove, runTransaction } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // --- PLACEHOLDER VARIABLES (DO NOT CHANGE) ---
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'finansync-app-default';
        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : { apiKey: "AIza...", authDomain: "...", projectId: "..." };
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : undefined;

        // --- FIREBASE INITIALIZATION ---
        let app, auth, db, userId;
        let unsubscribeFromMonth = null;
        
        async function initializeFirebase() {
            try {
                app = initializeApp(firebaseConfig);
                auth = getAuth(app);
                db = getFirestore(app);
                
                return new Promise((resolve) => {
                    onAuthStateChanged(auth, async (user) => {
                        if (user) {
                            userId = user.uid;
                            console.log("User is signed in with UID:", userId);
                            resolve();
                        } else if (initialAuthToken) {
                            await signInWithCustomToken(auth, initialAuthToken);
                        } else {
                            await signInAnonymously(auth);
                        }
                    });
                });

            } catch (error) {
                console.error("Firebase initialization failed:", error);
                document.getElementById('loading-state').innerText = 'Falha ao conectar. Verifique o console para mais detalhes.';
            }
        }
        
        // --- APP STATE ---
        const state = {
            currentDate: new Date('2025-09-24T12:00:00Z'), // Starting from Sep 2025
            chartInstance: null,
        };

        // --- UI ELEMENTS ---
        const UIElements = {
            loadingState: document.getElementById('loading-state'),
            mainContent: document.getElementById('main-content'),
            currentMonthDisplay: document.getElementById('current-month-display'),
            prevMonthBtn: document.getElementById('prev-month'),
            nextMonthBtn: document.getElementById('next-month'),
            // Summary cards
            summaryIncome: document.getElementById('summary-income'),
            summaryExpenses: document.getElementById('summary-expenses'),
            summaryPending: document.getElementById('summary-pending'),
            summaryBalance: document.getElementById('summary-balance'),
            // Lists
            incomeList: document.getElementById('income-list'),
            fixedExpensesList: document.getElementById('fixed-list-items'),
            variableExpensesList: document.getElementById('variable-list-items'),
            // Modals
            modal: document.getElementById('modal'),
            modalTitle: document.getElementById('modal-title'),
            modalForm: document.getElementById('modal-form'),
            closeModalBtn: document.getElementById('close-modal-btn'),
            addIncomeBtn: document.getElementById('add-income-btn'),
            addExpenseBtn: document.getElementById('add-expense-btn'),
            expenseFields: document.getElementById('expense-fields'),
            installmentsFields: document.getElementById('installments-fields'),
            categorySelect: document.getElementById('category'),
            modalSubmitBtn: document.getElementById('modal-submit-btn'),
            // AI Modal
            aiAnalysisBtn: document.getElementById('ai-analysis-btn'),
            aiModal: document.getElementById('ai-modal'),
            closeAiModalBtn: document.getElementById('close-ai-modal-btn'),
            aiResponseContainer: document.getElementById('ai-response-container'),
        };

        // --- UTILS ---
        const formatCurrency = (value) => new Intl.NumberFormat('pt-BR', { style: 'currency', currency: 'BRL' }).format(value || 0);
        const getMonthYearId = (date) => `${date.getFullYear()}-${String(date.getMonth() + 1).padStart(2, '0')}`;

        // --- INITIAL DATA (PARSED FROM CSV) ---
        function getInitialDataForSeptember2025() {
             // Helper to parse installment strings like "9 de 12"
            const parseInstallments = (str) => {
                if (!str || !str.includes(' de ')) return { current: 1, total: 1 };
                const parts = str.split(' de ');
                return { current: parseInt(parts[0], 10), total: parseInt(parts[1], 10) };
            };

            return {
                incomes: [
                    { id: crypto.randomUUID(), description: 'SALARIO MARCELLY', amount: 3349.92 },
                    { id: crypto.randomUUID(), description: 'SALARIO ANDRE', amount: 3349.92 },
                    { id: crypto.randomUUID(), description: 'MUMBUCA MARCELLY', amount: 617.50 },
                    { id: crypto.randomUUID(), description: 'MUMBUCA ANDRE', amount: 617.50 },
                ],
                expenses: [
                    { id: crypto.randomUUID(), description: "ABASTECIMENTO MENSAL (SEPARAR NO SOFISA)", amount: 500.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "ALUGUEL", amount: 1300.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "APPAI DA MARCELLY", amount: 110.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "APPAI DO ANDRÉ", amount: 116.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "CIDADANIA PORTUGUESA", amount: 140.00, ...parseInstallments("12 de 36"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "CONDOMÍNIO", amount: 260.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "ESCOLA DO ANDRÉ", amount: 560.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "FACULDADE DA MARCELLY", amount: 250.00, ...parseInstallments("1 de 6"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "LUZ", amount: 200.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "PLANO ODONTOLÓGICO DO ANDRÉ", amount: 22.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "REMÉDIOS DO ANDRÉ (SEPARAR NO SOFISA)", amount: 400.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "PSICÓLOGA DA MARCELLY", amount: 280.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "SEGURO DO CARRO (SEPARAR NO SOFISA)", amount: 143.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "PEÇAS DO CARRO - CONSERTO DE DEZEMBRO", amount: 67.70, ...parseInstallments("9 de 10"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "EMPRÉSTIMO DA TIA CÉLIA", amount: 400.00, ...parseInstallments("7 de 10"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "PARCELAMENTO CARTÃO DE MENOR LIMITE DA MARCELLY", amount: 67.79, ...parseInstallments("4 de 4"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "FATURA DO CARTÃO DO ANDRÉ", amount: 100.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "MÃO DE OBRA DO DAVI - CARTÃO DA MINHA MÃE", amount: 108.33, ...parseInstallments("2 de 3"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: "FATURA CARTÃO DE MAIOR LIMITE MARCELLY", amount: 200.00, ...parseInstallments("9 de 12"), category: "fixa", paid: false },
                    { id: crypto.randomUUID(), description: 'ABASTECIMENTO', amount: 183.07, ...parseInstallments('1 de 1'), category: 'variavel', paid: false },
                    { id: crypto.randomUUID(), description: 'MERCADO', amount: 74.51, ...parseInstallments('1 de 1'), category: 'variavel', paid: false },
                    { id: crypto.randomUUID(), description: 'PADARIA', amount: 42.74, ...parseInstallments('1 de 1'), category: 'variavel', paid: false },
                    { id: crypto.randomUUID(), description: 'DROGARIA', amount: 61.32, ...parseInstallments('1 de 1'), category: 'variavel', paid: false },
                    { id: crypto.randomUUID(), description: 'MERCADO', amount: 14.97, ...parseInstallments('1 de 1'), category: 'variavel', paid: false },
                    { id: crypto.randomUUID(), description: 'ABASTECIMENTO', amount: 208.37, ...parseInstallments('1 de 1'), category: 'variavel', paid: false },
                    { id: crypto.randomUUID(), description: 'MERCADO', amount: 58.83, ...parseInstallments('1 de 1'), category: 'variavel', paid: false },
                    { id: crypto.randomUUID(), description: 'CONVENIÊNCIA', amount: 23.48, ...parseInstallments('1 de 1'), category: 'variavel', paid: false },
                    { id: crypto.randomUUID(), description: 'PADARIA', amount: 9.60, ...parseInstallments('1 de 1'), category: 'variavel', paid: false }
                ]
            };
        }
        
        // --- DATA HANDLING ---
        async function loadAndDisplayMonthData(date) {
            UIElements.mainContent.classList.add('hidden');
            UIElements.loadingState.classList.remove('hidden');

            const monthId = getMonthYearId(date);
            const docRef = doc(db, `artifacts/${appId}/users/${userId}/finances/${monthId}`);
            
            if (unsubscribeFromMonth) {
                unsubscribeFromMonth();
            }

            unsubscribeFromMonth = onSnapshot(docRef, (docSnap) => {
                if (docSnap.exists()) {
                    renderUI(docSnap.data());
                } else {
                    console.log(`No data for ${monthId}. Attempting to create from previous month.`);
                    createNextMonthData(date);
                }
                UIElements.loadingState.classList.add('hidden');
                UIElements.mainContent.classList.remove('hidden');
            });
        }
        
        async function createNextMonthData(date) {
            const monthId = getMonthYearId(date);
            const prevDate = new Date(date);
            prevDate.setMonth(prevDate.getMonth() - 1);
            const prevMonthId = getMonthYearId(prevDate);

            const prevDocRef = doc(db, `artifacts/${appId}/users/${userId}/finances/${prevMonthId}`);
            const prevDocSnap = await getDoc(prevDocRef);

            let dataToSet;
            if (prevDocSnap.exists()) {
                const prevData = prevDocSnap.data();
                const nextExpenses = prevData.expenses
                    .filter(exp => exp.category === 'fixa' && exp.current < exp.total)
                    .map(exp => ({
                        ...exp,
                        id: crypto.randomUUID(),
                        current: exp.current + 1,
                        paid: false,
                    }));
                
                const nextIncomes = prevData.incomes;

                dataToSet = { incomes: nextIncomes, expenses: nextExpenses };
            } else {
                if (monthId === '2025-09') {
                     dataToSet = getInitialDataForSeptember2025();
                } else {
                    dataToSet = { incomes: [], expenses: [] };
                    console.warn("Could not find previous month's data to generate new month.");
                }
            }
            
            const newDocRef = doc(db, `artifacts/${appId}/users/${userId}/finances/${monthId}`);
            await setDoc(newDocRef, dataToSet);
        }

        // --- RENDERING ---
        function renderUI(data) {
            updateMonthDisplay();
            const incomes = data.incomes || [];
            const expenses = data.expenses || [];
            
            const totalIncome = incomes.reduce((sum, item) => sum + item.amount, 0);
            const totalExpenses = expenses.reduce((sum, item) => sum + item.amount, 0);
            const paidExpenses = expenses.filter(e => e.paid).reduce((sum, item) => sum + item.amount, 0);
            const pendingAmount = totalExpenses - paidExpenses;
            const balance = totalIncome - totalExpenses;

            UIElements.summaryIncome.textContent = formatCurrency(totalIncome);
            UIElements.summaryExpenses.textContent = formatCurrency(totalExpenses);
            UIElements.summaryPending.textContent = formatCurrency(pendingAmount);
            UIElements.summaryBalance.textContent = formatCurrency(balance);
            UIElements.summaryBalance.className = `text-lg font-bold ${balance >= 0 ? 'text-green-400' : 'text-red-400'}`;
            
            renderIncomeList(incomes);
            renderExpensesList(expenses);
            updateChart(expenses);
        }
        
        function createIncomeItemHTML(item) {
            return `
                <div class="flex justify-between items-center bg-neutral-800/50 p-3 rounded-lg" data-id="${item.id}">
                    <p class="text-neutral-300 text-sm">${item.description}</p>
                    <div class="flex items-center gap-3">
                        <p class="font-semibold text-green-400 text-sm">${formatCurrency(item.amount)}</p>
                        <button class="delete-income-btn text-neutral-500 hover:text-red-400 transition-colors">
                             <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 6h18"/><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6"/><path d="M8 6V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"/></svg>
                        </button>
                    </div>
                </div>`;
        }
        
        function createExpenseItemHTML(item) {
            const isPaid = item.paid;
            const installmentText = item.total > 1 ? `<span class="text-xs font-mono bg-neutral-700 px-2 py-1 rounded-md">${item.current}/${item.total}</span>` : '';
            return `
                <div class="flex justify-between items-center bg-neutral-800/50 p-3 rounded-lg" data-id="${item.id}">
                    <div class="flex items-center gap-3 flex-1 min-w-0">
                        <button class="pay-btn p-1 rounded-full border-2 ${isPaid ? 'border-cyan-500 bg-cyan-500' : 'border-neutral-500'} transition-all flex-shrink-0">
                             <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="3" stroke-linecap="round" stroke-linejoin="round"><path d="M20 6 9 17l-5-5"/></svg>
                        </button>
                        <div class="${isPaid ? 'paid-item' : ''} truncate">
                            <p class="text-neutral-200 text-sm truncate">${item.description}</p>
                        </div>
                    </div>
                    <div class="flex items-center gap-2">
                        ${installmentText}
                        <p class="font-semibold text-neutral-300 text-sm w-20 text-right">${formatCurrency(item.amount)}</p>
                         <button class="delete-expense-btn text-neutral-500 hover:text-red-400 transition-colors">
                             <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M3 6h18"/><path d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6"/><path d="M8 6V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"/></svg>
                        </button>
                    </div>
                </div>`;
        }

        function renderIncomeList(incomes) {
            UIElements.incomeList.innerHTML = incomes.length > 0
                ? incomes.map(createIncomeItemHTML).join('')
                : `<p class="text-neutral-500 text-center py-4 text-sm">Nenhuma entrada registrada.</p>`;
        }

        function renderExpensesList(expenses) {
            const fixed = expenses.filter(e => e.category === 'fixa');
            const variable = expenses.filter(e => e.category === 'variavel');
            
            UIElements.fixedExpensesList.innerHTML = fixed.length > 0
                ? fixed.map(createExpenseItemHTML).join('')
                : `<p class="text-neutral-500 text-center py-4 text-sm">Nenhuma despesa fixa.</p>`;
            
            UIElements.variableExpensesList.innerHTML = variable.length > 0
                ? variable.map(createExpenseItemHTML).join('')
                : `<p class="text-neutral-500 text-center py-4 text-sm">Nenhuma despesa variável.</p>`;
        }

        function updateChart(expenses) {
            const ctx = document.getElementById('expenses-chart').getContext('2d');
            const fixedTotal = expenses.filter(e => e.category === 'fixa').reduce((sum, e) => sum + e.amount, 0);
            const variableTotal = expenses.filter(e => e.category === 'variavel').reduce((sum, e) => sum + e.amount, 0);
            
            const data = {
                labels: ['Fixas', 'Variáveis'],
                datasets: [{
                    data: [fixedTotal, variableTotal],
                    backgroundColor: ['rgba(34, 211, 238, 0.7)', 'rgba(234, 179, 8, 0.7)'],
                    borderColor: ['#06b6d4', '#ca8a04'],
                    borderWidth: 1,
                }]
            };

            if (state.chartInstance) {
                state.chartInstance.data = data;
                state.chartInstance.update();
            } else {
                state.chartInstance = new Chart(ctx, {
                    type: 'doughnut',
                    data: data,
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        plugins: {
                            legend: {
                                position: 'bottom',
                                labels: { color: '#f5f5f5', font: { size: 10 } }
                            }
                        }
                    }
                });
            }
        }

        function updateMonthDisplay() {
            const monthName = state.currentDate.toLocaleString('pt-BR', { month: 'long' });
            const year = state.currentDate.getFullYear();
            UIElements.currentMonthDisplay.textContent = `${monthName.charAt(0).toUpperCase() + monthName.slice(1)} ${year}`;
        }

        // --- GEMINI API INTEGRATION ---
        function formatResponseForHTML(text) {
            if (!text) return '<p>Não foi possível gerar a análise.</p>';
            const lines = text.split('\n');
            let html = '';
            let inList = false;
            lines.forEach(line => {
                line = line.trim();
                if (!line) return;
                line = line.replace(/\*\*(.*?)\*\*/g, '<strong class="text-cyan-400">$1</strong>');
                if (line.startsWith('- ') || line.startsWith('* ')) {
                    if (!inList) {
                        html += '<ul>';
                        inList = true;
                    }
                    html += `<li class="ml-4 list-disc mb-2">${line.substring(2)}</li>`;
                } else {
                    if (inList) {
                        html += '</ul>';
                        inList = false;
                    }
                    html += `<p class="mb-3">${line}</p>`;
                }
            });
            if (inList) html += '</ul>';
            return html;
        }

        async function getFinancialAnalysis(monthData) {
            const apiKey = ""; // Canvas will provide the key.
            const apiUrl = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-05-20:generateContent?key=${apiKey}`;

            const totalIncome = monthData.incomes.reduce((sum, item) => sum + item.amount, 0);
            const totalExpenses = monthData.expenses.reduce((sum, item) => sum + item.amount, 0);
            const balance = totalIncome - totalExpenses;
            const incomeDetails = monthData.incomes.map(i => `- ${i.description}: ${formatCurrency(i.amount)}`).join('\n');
            const expenseDetails = monthData.expenses.map(e => `- ${e.description}: ${formatCurrency(e.amount)} (${e.paid ? 'Pago' : 'Pendente'})`).join('\n');
            const monthName = state.currentDate.toLocaleString('pt-BR', { month: 'long', year: 'numeric' });

            const prompt = `Você é um consultor financeiro amigável e prestativo para um usuário no Brasil. Analise os seguintes dados financeiros para o mês de ${monthName}. Seu objetivo é fornecer uma análise clara, útil e encorajadora. **Instruções:** 1. Comece com uma saudação e um resumo geral do mês em um parágrafo. 2. Crie uma seção "**Pontos de Destaque**" onde você lista 2-3 observações importantes. 3. Crie uma seção "**Sugestões para Melhorar**" com 2-3 dicas práticas e acionáveis, baseadas especificamente nos dados. 4. Termine com uma mensagem motivacional curta. 5. Use formatação com asteriscos para negrito (ex: **Título**) e hífens para listas. Responda inteiramente em português do Brasil. **Dados Financeiros de ${monthName}:** **Entradas:** ${incomeDetails || 'Nenhuma.'} **Despesas:** ${expenseDetails || 'Nenhuma.'} **Resumo Geral:** - Total de Entradas: ${formatCurrency(totalIncome)} - Total de Saídas: ${formatCurrency(totalExpenses)} - Saldo Final: ${formatCurrency(balance)}`;
            const payload = { contents: [{ parts: [{ text: prompt }] }] };
            
            let response;
            let attempts = 0;
            const maxAttempts = 4;
            while (attempts < maxAttempts) {
                try {
                    response = await fetch(apiUrl, { method: 'POST', headers: { 'Content-Type': 'application/json' }, body: JSON.stringify(payload) });
                    if (response.ok) {
                        const result = await response.json();
                        return result.candidates?.[0]?.content?.parts?.[0]?.text;
                    } else if (response.status === 429 || response.status >= 500) {
                        throw new Error(`API Error with status ${response.status}`);
                    } else {
                        const errorResult = await response.json();
                        console.error("API Error:", errorResult);
                        return `Ocorreu um erro: ${errorResult.error?.message || 'Erro desconhecido.'}`;
                    }
                } catch (error) {
                    attempts++;
                    if (attempts >= maxAttempts) {
                        console.error("API call failed after multiple retries:", error);
                        return "Não foi possível obter a análise. Tente novamente mais tarde.";
                    }
                    const delay = Math.pow(2, attempts) * 1000;
                    await new Promise(res => setTimeout(res, delay));
                }
            }
        }
        
        // --- EVENT HANDLERS & MODAL LOGIC ---
        function changeMonth(offset) {
            state.currentDate.setMonth(state.currentDate.getMonth() + offset);
            loadAndDisplayMonthData(state.currentDate);
        }
        
        function setupModalForIncome() {
            UIElements.modalTitle.textContent = 'Nova Entrada';
            UIElements.expenseFields.classList.add('hidden');
            UIElements.modalSubmitBtn.textContent = 'Adicionar Entrada';
            UIElements.modalSubmitBtn.className = 'w-full font-semibold py-3 px-4 rounded-lg transition-colors mt-4 bg-green-500 hover:bg-green-600 text-white';
            UIElements.modal.dataset.type = 'income';
            UIElements.modal.classList.remove('hidden');
        }

        function setupModalForExpense() {
            UIElements.modalTitle.textContent = 'Nova Despesa';
            UIElements.expenseFields.classList.remove('hidden');
            UIElements.modalSubmitBtn.textContent = 'Adicionar Despesa';
            UIElements.modalSubmitBtn.className = 'w-full font-semibold py-3 px-4 rounded-lg transition-colors mt-4 bg-cyan-500 hover:bg-cyan-600 text-white';
            UIElements.modal.dataset.type = 'expense';
            UIElements.modal.classList.remove('hidden');
        }

        async function handleAiAnalysisClick() {
            UIElements.aiModal.classList.remove('hidden');
            UIElements.aiResponseContainer.innerHTML = '<p>Analisando suas finanças... Isso pode levar alguns segundos. ✨</p>';
            const monthId = getMonthYearId(state.currentDate);
            const docRef = doc(db, `artifacts/${appId}/users/${userId}/finances/${monthId}`);
            const docSnap = await getDoc(docRef);
            if (docSnap.exists()) {
                const monthData = docSnap.data();
                if ((monthData.incomes?.length || 0) === 0 && (monthData.expenses?.length || 0) === 0) {
                    UIElements.aiResponseContainer.innerHTML = '<p>Não há dados financeiros neste mês para analisar.</p>';
                    return;
                }
                const analysisText = await getFinancialAnalysis(monthData);
                UIElements.aiResponseContainer.innerHTML = formatResponseForHTML(analysisText);
            } else {
                UIElements.aiResponseContainer.innerHTML = '<p>Não foi possível encontrar os dados para este mês.</p>';
            }
        }
        
        async function handleFormSubmit(e) {
            e.preventDefault();
            const formData = new FormData(e.target);
            const data = Object.fromEntries(formData.entries());
            const type = UIElements.modal.dataset.type;

            const monthId = getMonthYearId(state.currentDate);
            const docRef = doc(db, `artifacts/${appId}/users/${userId}/finances/${monthId}`);
            
            if (type === 'income') {
                const newIncome = { id: crypto.randomUUID(), description: data.description, amount: parseFloat(data.amount) };
                await updateDoc(docRef, { incomes: arrayUnion(newIncome) });
            } else { // expense
                const newExpense = { id: crypto.randomUUID(), description: data.description, amount: parseFloat(data.amount), category: data.category, paid: false, current: data.category === 'fixa' ? parseInt(data['current-installment']) : 1, total: data.category === 'fixa' ? parseInt(data['total-installments']) : 1, };
                await updateDoc(docRef, { expenses: arrayUnion(newExpense) });
            }

            UIElements.modalForm.reset();
            UIElements.modal.classList.add('hidden');
        }

        async function handleItemClick(e) {
            const target = e.target.closest('button');
            if (!target) return;

            const itemElement = target.closest('[data-id]');
            const itemId = itemElement.dataset.id;
            const monthId = getMonthYearId(state.currentDate);
            const docRef = doc(db, `artifacts/${appId}/users/${userId}/finances/${monthId}`);

            try {
                await runTransaction(db, async (transaction) => {
                    const docSnap = await transaction.get(docRef);
                    if (!docSnap.exists()) throw "Document does not exist!";
                    const data = docSnap.data();

                    if (target.classList.contains('pay-btn')) {
                        const expenses = data.expenses.map(exp => exp.id === itemId ? { ...exp, paid: !exp.paid } : exp);
                        transaction.update(docRef, { expenses });
                    } else if (target.classList.contains('delete-income-btn')) {
                        const incomeToRemove = data.incomes.find(inc => inc.id === itemId);
                        if (incomeToRemove) transaction.update(docRef, { incomes: arrayRemove(incomeToRemove) });
                    } else if (target.classList.contains('delete-expense-btn')) {
                        const expenseToRemove = data.expenses.find(exp => exp.id === itemId);
                        if(expenseToRemove) transaction.update(docRef, { expenses: arrayRemove(expenseToRemove) });
                    }
                });
            } catch (error) {
                console.error("Transaction failed: ", error);
            }
        }
        
        function addEventListeners() {
            UIElements.prevMonthBtn.addEventListener('click', () => changeMonth(-1));
            UIElements.nextMonthBtn.addEventListener('click', () => changeMonth(1));
            
            UIElements.addIncomeBtn.addEventListener('click', setupModalForIncome);
            UIElements.addExpenseBtn.addEventListener('click', setupModalForExpense);
            
            UIElements.closeModalBtn.addEventListener('click', () => UIElements.modal.classList.add('hidden'));
            UIElements.modal.addEventListener('click', (e) => { if (e.target === UIElements.modal) UIElements.modal.classList.add('hidden') });

            UIElements.aiAnalysisBtn.addEventListener('click', handleAiAnalysisClick);
            UIElements.closeAiModalBtn.addEventListener('click', () => UIElements.aiModal.classList.add('hidden'));
            UIElements.aiModal.addEventListener('click', (e) => { if (e.target === UIElements.aiModal) UIElements.aiModal.classList.add('hidden') });

            UIElements.modalForm.addEventListener('submit', handleFormSubmit);
            
            UIElements.categorySelect.addEventListener('change', (e) => {
                UIElements.installmentsFields.classList.toggle('hidden', e.target.value !== 'fixa');
            });

            document.getElementById('income-list').addEventListener('click', handleItemClick);
            document.getElementById('fixed-expenses-list').addEventListener('click', handleItemClick);
            document.getElementById('variable-expenses-list').addEventListener('click', handleItemClick);
        }

        // --- APP INITIALIZATION ---
        async function main() {
            await initializeFirebase();
            if(userId) {
                addEventListeners();
                loadAndDisplayMonthData(state.currentDate);
            }
        }

        main();

    </script>
</body>
</html>

