<style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; background-color: #f8fafc; }
        .protein-card { transition: all 0.2s ease; }
        .protein-card:hover { transform: translateY(-2px); }
    </style>
</head>
<body class="p-4 md:p-8">

    <div class="max-w-4xl mx-auto">
        <header class="mb-8 flex flex-col md:flex-row md:items-end justify-between gap-4">
            <div>
                <h1 class="text-3xl font-bold text-slate-900 tracking-tight">Protein Planner <span class="text-sm font-normal text-slate-500 ml-2">MVP v1</span></h1>
                <p class="text-slate-600">Vegetarian & Pescatarian Daily Target: 80–100g</p>
            </div>
            
            <div id="progress-container" class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 min-w-[200px]">
                <div class="flex justify-between items-center mb-2">
                    <span class="text-sm font-semibold text-slate-700">Daily Total</span>
                    <span id="total-display" class="text-2xl font-bold text-slate-900">0g</span>
                </div>
                <div class="w-full bg-slate-100 rounded-full h-3 overflow-hidden">
                    <div id="progress-bar" class="bg-yellow-400 h-full transition-all duration-500" style="width: 0%"></div>
                </div>
                <p id="status-message" class="text-xs mt-2 font-medium text-slate-500 uppercase tracking-wider text-center">Under Target</p>
            </div>
        </header>

        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            
            <div class="lg:col-span-2 space-y-6">
                <div id="meal-slots" class="space-y-4">
                    </div>
            </div>

            <div class="space-y-6">
                <div id="gap-panel" class="bg-indigo-50 border border-indigo-100 p-5 rounded-2xl hidden">
                    <h3 class="text-indigo-900 font-bold mb-3 flex items-center">
                        <svg class="w-5 h-5 mr-2" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="id-13 10V3L4 14h7v7l9-11h-7z"></path></svg>
                        Close the Gap
                    </h3>
                    <div id="suggestions-list" class="space-y-3 text-sm">
                        </div>
                </div>

                <div id="search-container" class="bg-white border border-slate-200 rounded-2xl shadow-sm sticky top-4 overflow-hidden hidden">
                    <div class="p-4 border-b border-slate-100 bg-slate-50 flex justify-between items-center">
                        <h3 class="font-bold text-slate-800">Add to <span id="current-slot-label">...</span></h3>
                        <button onclick="closeSearch()" class="text-slate-400 hover:text-slate-600 text-xl">&times;</button>
                    </div>
                    <div class="p-4">
                        <input type="text" id="food-search" placeholder="Search protein foods..." class="w-full p-2 border border-slate-200 rounded-lg mb-4 focus:ring-2 focus:ring-indigo-500 outline-none">
                        <div id="food-results" class="max-height-[400px] overflow-y-auto space-y-2">
                            </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <script>
        /**
         * 1. DATA LAYER
         * Curated Vegetarian/Pescatarian Database
         */
        const FOOD_DATABASE = [
            { id: "greek_yogurt", name: "Greek Yogurt (2%)", serving_size: "1 cup (245g)", protein_g: 20, category: "dairy", diet_type: ["vegetarian", "pescatarian"] },
            { id: "cottage_cheese", name: "Cottage Cheese (Low Fat)", serving_size: "1 cup (225g)", protein_g: 28, category: "dairy", diet_type: ["vegetarian", "pescatarian"] },
            { id: "egg_large", name: "Large Egg", serving_size: "1 egg (50g)", protein_g: 6, category: "dairy", diet_type: ["vegetarian", "pescatarian"] },
            { id: "tofu_firm", name: "Extra Firm Tofu", serving_size: "1/2 cup (126g)", protein_g: 10, category: "legumes", diet_type: ["vegetarian", "pescatarian"] },
            { id: "tempeh", name: "Tempeh", serving_size: "3 oz (85g)", protein_g: 15, category: "legumes", diet_type: ["vegetarian", "pescatarian"] },
            { id: "lentils_cooked", name: "Lentils (Cooked)", serving_size: "1 cup (198g)", protein_g: 18, category: "legumes", diet_type: ["vegetarian", "pescatarian"] },
            { id: "edamame", name: "Edamame (Shelled)", serving_size: "1 cup (155g)", protein_g: 18, category: "legumes", diet_type: ["vegetarian", "pescatarian"] },
            { id: "salmon_fillet", name: "Salmon Fillet", serving_size: "4 oz (113g)", protein_g: 25, category: "fish", diet_type: ["pescatarian"] },
            { id: "tuna_canned", name: "Canned Tuna (Water)", serving_size: "1 can (142g)", protein_g: 32, category: "fish", diet_type: ["pescatarian"] },
            { id: "shrimp_cooked", name: "Shrimp (Cooked)", serving_size: "3 oz (85g)", protein_g: 20, category: "fish", diet_type: ["pescatarian"] },
            { id: "quinoa_cooked", name: "Quinoa (Cooked)", serving_size: "1 cup (185g)", protein_g: 8, category: "grains", diet_type: ["vegetarian", "pescatarian"] },
            { id: "whey_protein", name: "Whey Protein Scoop", serving_size: "1 scoop (30g)", protein_g: 24, category: "supplements", diet_type: ["vegetarian", "pescatarian"] },
            { id: "seitan", name: "Seitan", serving_size: "3 oz (85g)", protein_g: 21, category: "legumes", diet_type: ["vegetarian", "pescatarian"] }
        ];

        /**
         * 2. STATE MANAGEMENT
         */
        let currentPlan = [
            { id: 'breakfast', label: 'Breakfast', items: [] },
            { id: 'm_snack', label: 'Morning Snack', items: [] },
            { id: 'lunch', label: 'Lunch', items: [] },
            { id: 'a_snack', label: 'Afternoon Snack', items: [] },
            { id: 'dinner', label: 'Dinner', items: [] }
        ];

        let activeSlotId = null;

        /**
         * 3. CORE LOGIC
         */
        function calculateTotal() {
            return currentPlan.reduce((total, slot) => {
                return total + slot.items.reduce((slotTotal, item) => slotTotal + (item.protein_g * item.multiplier), 0);
            }, 0);
        }

        function updateUI() {
            const total = calculateTotal();
            const targetMin = 80;
            const targetMax = 100;

            // Update Total Displays
            document.getElementById('total-display').innerText = `${Math.round(total)}g`;
            const bar = document.getElementById('progress-bar');
            const status = document.getElementById('status-message');
            
            // Progress Bar Logic
            const percentage = Math.min((total / targetMax) * 100, 100);
            bar.style.width = `${percentage}%`;

            if (total < targetMin) {
                bar.className = "bg-yellow-400 h-full transition-all duration-500";
                status.innerText = "Under Target";
                status.className = "text-xs mt-2 font-medium text-amber-500 uppercase tracking-wider text-center";
                showGapSuggestions(total);
            } else if (total <= targetMax) {
                bar.className = "bg-green-500 h-full transition-all duration-500";
                status.innerText = "Goal Met! Perfect Range";
                status.className = "text-xs mt-2 font-medium text-green-600 uppercase tracking-wider text-center";
                document.getElementById('gap-panel').classList.add('hidden');
            } else {
                bar.className = "bg-orange-500 h-full transition-all duration-500";
                status.innerText = "Over Target Range";
                status.className = "text-xs mt-2 font-medium text-orange-500 uppercase tracking-wider text-center";
                document.getElementById('gap-panel').classList.add('hidden');
            }

            renderSlots();
        }

        function renderSlots() {
            const container = document.getElementById('meal-slots');
            container.innerHTML = currentPlan.map(slot => `
                <div class="bg-white rounded-2xl border border-slate-200 shadow-sm overflow-hidden">
                    <div class="p-4 border-b border-slate-50 flex justify-between items-center">
                        <h4 class="font-bold text-slate-700">${slot.label}</h4>
                        <button onclick="openSearch('${slot.id}')" class="text-xs font-bold text-indigo-600 hover:text-indigo-800 uppercase tracking-tighter cursor-pointer">
                            + Add Food
                        </button>
                    </div>
                    <div class="p-4 space-y-3">
                        ${slot.items.length === 0 ? '<p class="text-slate-400 text-sm italic">No protein added yet</p>' : ''}
                        ${slot.items.map((item, idx) => `
                            <div class="flex items-center justify-between text-sm animate-in fade-in">
                                <div class="flex-1">
                                    <span class="font-semibold text-slate-800">${item.name}</span>
                                    <span class="text-slate-500 block text-xs">${item.serving_size}</span>
                                </div>
                                <div class="flex items-center gap-4">
                                    <select onchange="updateMultiplier('${slot.id}', ${idx}, this.value)" class="bg-slate-50 border border-slate-200 rounded p-1 text-xs">
                                        <option value="0.5" ${item.multiplier === 0.5 ? 'selected' : ''}>0.5x</option>
                                        <option value="1" ${item.multiplier === 1 ? 'selected' : ''}>1x</option>
                                        <option value="1.5" ${item.multiplier === 1.5 ? 'selected' : ''}>1.5x</option>
                                        <option value="2" ${item.multiplier === 2 ? 'selected' : ''}>2x</option>
                                    </select>
                                    <span class="font-bold text-slate-700 w-8 text-right">${Math.round(item.protein_g * item.multiplier)}g</span>
                                    <button onclick="removeItem('${slot.id}', ${idx})" class="text-slate-300 hover:text-red-500 px-1">&times;</button>
                                </div>
                            </div>
                        `).join('')}
                    </div>
                </div>
            `).join('');
        }

        function showGapSuggestions(currentTotal) {
            const gap = 80 - currentTotal;
            const panel = document.getElementById('gap-panel');
            const list = document.getElementById('suggestions-list');
            
            // Logic: Protein >= 30% of gap, sorted by density
            let suggestions = FOOD_DATABASE
                .filter(f => f.protein_g >= (gap * 0.3))
                .sort((a, b) => (b.protein_g) - (a.protein_g))
                .slice(0, 3);

            if (suggestions.length > 0) {
                panel.classList.remove('hidden');
                list.innerHTML = suggestions.map(f => `
                    <div class="bg-white p-3 rounded-lg border border-indigo-100 flex justify-between items-center shadow-sm">
                        <div>
                            <p class="font-semibold text-indigo-900">${f.name}</p>
                            <p class="text-xs text-indigo-500">+${f.protein_g}g protein</p>
                        </div>
                        <button onclick="quickAdd('${f.id}')" class="bg-indigo-600 text-white px-3 py-1 rounded text-xs font-bold hover:bg-indigo-700 transition">
                            Add
                        </button>
                    </div>
                `).join('');
            } else {
                panel.classList.add('hidden');
            }
        }

        /**
         * 4. INTERACTION HANDLERS
         */
        function openSearch(slotId) {
            activeSlotId = slotId;
            const slot = currentPlan.find(s => s.id === slotId);
            document.getElementById('current-slot-label').innerText = slot.label;
            document.getElementById('search-container').classList.remove('hidden');
            renderSearchResults("");
            document.getElementById('food-search').focus();
        }

        function closeSearch() {
            document.getElementById('search-container').classList.add('hidden');
            activeSlotId = null;
        }

        function renderSearchResults(query) {
            const results = FOOD_DATABASE.filter(f => 
                f.name.toLowerCase().includes(query.toLowerCase()) || 
                f.category.toLowerCase().includes(query.toLowerCase())
            );

            document.getElementById('food-results').innerHTML = results.map(f => `
                <button onclick="addFood('${f.id}')" class="w-full text-left p-3 hover:bg-indigo-50 rounded-xl border border-transparent hover:border-indigo-100 transition-all flex justify-between items-center group">
                    <div>
                        <p class="font-medium text-slate-800">${f.name}</p>
                        <p class="text-xs text-slate-500">${f.serving_size} • <span class="capitalize">${f.category}</span></p>
                    </div>
                    <div class="text-right">
                        <span class="font-bold text-indigo-600">${f.protein_g}g</span>
                        <span class="block text-[10px] text-slate-400 font-bold uppercase group-hover:text-indigo-400">Select</span>
                    </div>
                </button>
            `).join('');
        }

        function addFood(foodId) {
            if (!activeSlotId) return;
            const food = FOOD_DATABASE.find(f => f.id === foodId);
            const slot = currentPlan.find(s => s.id === activeSlotId);
            
            slot.items.push({ ...food, multiplier: 1 });
            updateUI();
            closeSearch();
        }

        function quickAdd(foodId) {
            // Default to snack slots if they have fewer items, otherwise dinner
            const food = FOOD_DATABASE.find(f => f.id === foodId);
            const targetSlot = currentPlan.find(s => s.id === 'a_snack') || currentPlan[0];
            targetSlot.items.push({ ...food, multiplier: 1 });
            updateUI();
        }

        function updateMultiplier(slotId, itemIndex, val) {
            const slot = currentPlan.find(s => s.id === slotId);
            slot.items[itemIndex].multiplier = parseFloat(val);
            updateUI();
        }

        function removeItem(slotId, itemIndex) {
            const slot = currentPlan.find(s => s.id === slotId);
            slot.items.splice(itemIndex, 1);
            updateUI();
        }

        // Search listener
        document.getElementById('food-search').addEventListener('input', (e) => {
            renderSearchResults(e.target.value);
        });

        // Initialize
        updateUI();
    </script>
</body>
</html
