index.html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>nyam — Умный шеф-повар у тебя дома</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome для иконок -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Plus Jakarta Sans', sans-serif; }
        .glass-card { background: rgba(255, 255, 255, 0.85); backdrop-filter: blur(12px); }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex flex-col antialiased">

    <!-- Навигация -->
    <header class="sticky top-0 z-30 bg-white/80 backdrop-blur-md border-b border-slate-200">
        <div class="max-w-6xl mx-auto px-4 h-16 flex items-center justify-between">
            <div class="flex items-center space-x-3 cursor-pointer" onclick="switchTab('search')">
                <div class="w-10 h-10 bg-amber-500 rounded-xl flex items-center justify-center text-white font-extrabold text-2xl shadow-lg shadow-amber-500/30">
                    n
                </div>
                <div>
                    <span class="text-xl font-extrabold tracking-tight text-slate-900">nyam<span class="text-amber-500">.</span></span>
                    <span class="block text-[10px] font-semibold tracking-wider text-slate-400 uppercase -mt-1">Smart Recipes</span>
                </div>
            </div>

            <!-- Вкладки -->
            <nav class="flex items-center space-x-1 sm:space-x-2">
                <button id="nav-search" onclick="switchTab('search')" class="px-3 py-2 rounded-xl text-sm font-semibold flex items-center space-x-2 bg-amber-50 text-amber-600 transition-all">
                    <i class="fa-solid fa-utensils"></i>
                    <span class="hidden sm:inline">Подбор</span>
                </button>
                <button id="nav-favs" onclick="switchTab('favs')" class="px-3 py-2 rounded-xl text-sm font-semibold text-slate-600 hover:bg-slate-100 flex items-center space-x-2 transition-all relative">
                    <i class="fa-solid fa-heart"></i>
                    <span class="hidden sm:inline">Избранное</span>
                    <span id="fav-badge" class="hidden absolute -top-1 -right-1 bg-rose-500 text-white text-[10px] font-bold w-4 h-4 rounded-full flex items-center justify-center">0</span>
                </button>
                <button id="nav-list" onclick="switchTab('list')" class="px-3 py-2 rounded-xl text-sm font-semibold text-slate-600 hover:bg-slate-100 flex items-center space-x-2 transition-all relative">
                    <i class="fa-solid fa-basket-shopping"></i>
                    <span class="hidden sm:inline">Покупки</span>
                    <span id="list-badge" class="hidden absolute -top-1 -right-1 bg-amber-500 text-white text-[10px] font-bold w-4 h-4 rounded-full flex items-center justify-center">0</span>
                </button>
            </nav>
        </div>
    </header>

    <!-- Основной контент -->
    <main class="flex-grow max-w-6xl w-full mx-auto px-4 py-6">

        <!-- Вкладка: Подбор рецептов -->
        <div id="tab-search" class="space-y-8">
            <!-- Главный Баннер -->
            <div class="relative overflow-hidden rounded-3xl bg-gradient-to-r from-amber-500 to-orange-600 p-6 sm:p-10 text-white shadow-xl shadow-amber-500/10">
                <div class="relative z-10 max-w-2xl">
                    <span class="inline-block px-3 py-1 bg-white/20 backdrop-blur-md text-xs font-bold tracking-wider uppercase rounded-full mb-3">Холодильник забит?</span>
                    <h1 class="text-3xl sm:text-5xl font-black leading-tight mb-3">Готовим из того, что есть под рукой</h1>
                    <p class="text-amber-100 text-sm sm:text-base mb-6">Выбери ингредиенты из списка или добавь свои — умный алгоритм сразу предложит лучшие варианты!</p>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-12 gap-8">
                <!-- Селектор продуктов (Слева) -->
                <div class="lg:col-span-5 space-y-6">
                    <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-4">
                        <div class="flex items-center justify-between">
                            <h2 class="text-lg font-bold text-slate-900 flex items-center gap-2">
                                <i class="fa-solid fa-box-open text-amber-500"></i> Мой холодильник
                            </h2>
                            <button onclick="clearPantry()" class="text-xs font-semibold text-slate-400 hover:text-rose-500 transition-colors">Сбросить</button>
                        </div>

                        <!-- Ручной ввод -->
                        <div class="flex gap-2">
                            <input type="text" id="custom-ingredient" placeholder="Добавить свой продукт..." class="flex-grow px-4 py-2.5 rounded-xl border border-slate-200 text-sm focus:outline-none focus:border-amber-500 focus:ring-1 focus:ring-amber-500">
                            <button onclick="addCustomIngredient()" class="px-4 py-2.5 bg-slate-900 text-white rounded-xl font-medium text-sm hover:bg-slate-800 transition-colors">
                                <i class="fa-solid fa-plus"></i>
                            </button>
                        </div>

                        <!-- Фильтр категорий -->
                        <div class="flex gap-2 overflow-x-auto pb-2 no-scrollbar text-xs font-medium">
                            <button onclick="filterCat('all')" class="cat-btn px-3 py-1.5 rounded-lg bg-slate-900 text-white whitespace-nowrap" data-cat="all">Все</button>
                            <button onclick="filterCat('dairy')" class="cat-btn px-3 py-1.5 rounded-lg bg-slate-100 text-slate-600 hover:bg-slate-200 whitespace-nowrap" data-cat="dairy">🥛 Молочка</button>
                            <button onclick="filterCat('meat')" class="cat-btn px-3 py-1.5 rounded-lg bg-slate-100 text-slate-600 hover:bg-slate-200 whitespace-nowrap" data-cat="meat">🥩 Мясо/Рыба</button>
                            <button onclick="filterCat('veg')" class="cat-btn px-3 py-1.5 rounded-lg bg-slate-100 text-slate-600 hover:bg-slate-200 whitespace-nowrap" data-cat="veg">🥦 Овощи</button>
                            <button onclick="filterCat('pantry')" class="cat-btn px-3 py-1.5 rounded-lg bg-slate-100 text-slate-600 hover:bg-slate-200 whitespace-nowrap" data-cat="pantry">🥫 Бакалея</button>
                        </div>

                        <!-- Список кнопок-ингредиентов -->
                        <div id="ingredients-pool" class="flex flex-wrap gap-2 max-h-64 overflow-y-auto pr-1">
                            <!-- Заполняется JS -->
                        </div>
                    </div>

                    <!-- Выбранные ингредиенты -->
                    <div class="bg-amber-500/10 border border-amber-500/20 p-5 rounded-3xl space-y-3">
                        <div class="flex items-center justify-between">
                            <span class="text-xs font-bold text-amber-900 uppercase tracking-wider">Выбрано ингредиентов:</span>
                            <span id="selected-count" class="text-xs font-black bg-amber-500 text-white px-2 py-0.5 rounded-full">0</span>
                        </div>
                        <div id="selected-chips" class="flex flex-wrap gap-2">
                            <span class="text-xs text-slate-400 italic">Нажмите на ингредиенты выше, чтобы добавить...</span>
                        </div>
                    </div>
                </div>

                <!-- Список найденных рецептов (Справа) -->
                <div class="lg:col-span-7 space-y-4">
                    <div class="flex items-center justify-between">
                        <h2 class="text-xl font-bold text-slate-900">Подходящие рецепты</h2>
                        <span id="results-count" class="text-xs font-semibold text-slate-500">Найдено: 0</span>
                    </div>

                    <div id="recipes-grid" class="space-y-4">
                        <!-- Заполняется динамически -->
                    </div>
                </div>
            </div>
        </div>

        <!-- Вкладка: Избранное -->
        <div id="tab-favs" class="hidden space-y-6">
            <h2 class="text-2xl font-black text-slate-900">Любимые рецепты</h2>
            <div id="favs-grid" class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <!-- Карточки избранного -->
            </div>
        </div>

        <!-- Вкладка: Список покупок -->
        <div id="tab-list" class="hidden space-y-6 max-w-2xl mx-auto">
            <div class="flex items-center justify-between">
                <h2 class="text-2xl font-black text-slate-900">Список покупок</h2>
                <button onclick="clearShoppingList()" class="text-xs font-semibold text-rose-500 hover:underline">Очистить всё</button>
            </div>
            <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm space-y-3" id="shopping-list-container">
                <!-- Пункты списка -->
            </div>
        </div>

    </main>

    <!-- Модальное окно Рецепта -->
    <div id="recipe-modal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 hidden flex items-center justify-center p-4">
        <div class="bg-white w-full max-w-2xl rounded-3xl max-h-[90vh] overflow-y-auto shadow-2xl relative">
            <button onclick="closeModal()" class="absolute top-4 right-4 w-10 h-10 bg-white/80 backdrop-blur-md rounded-full flex items-center justify-center text-slate-700 shadow-md hover:bg-white z-10">
                <i class="fa-solid fa-xmark"></i>
            </button>

            <div id="modal-content">
                <!-- Заполняется через JS -->
            </div>
        </div>
    </div>

    <!-- JS Логика -->
    <script>
        // База данных ингредиентов
        const INGREDIENTS_DB = [
            { id: 'eggs', name: 'Яйца', icon: '🥚', cat: 'dairy' },
            { id: 'milk', name: 'Молоко', icon: '🥛', cat: 'dairy' },
            { id: 'cheese', name: 'Сыр', icon: '🧀', cat: 'dairy' },
            { id: 'butter', name: 'Сливочное масло', icon: '🧈', cat: 'dairy' },
            { id: 'chicken', name: 'Куриное филе', icon: '🍗', cat: 'meat' },
            { id: 'minced_meat', name: 'Фарш', icon: '🥩', cat: 'meat' },
            { id: 'sausage', name: 'Сосиски/Колбаса', icon: '🌭', cat: 'meat' },
            { id: 'fish', name: 'Рыба', icon: '🐟', cat: 'meat' },
            { id: 'tomatoes', name: 'Помидоры', icon: '🍅', cat: 'veg' },
            { id: 'potatoes', name: 'Картошка', icon: '🥔', cat: 'veg' },
            { id: 'onion', name: 'Лук', icon: '🧅', cat: 'veg' },
            { id: 'garlic', name: 'Чеснок', icon: '🧄', cat: 'veg' },
            { id: 'cucumber', name: 'Огурец', icon: '🥒', cat: 'veg' },
            { id: 'flour', name: 'Мука', icon: '🌾', cat: 'pantry' },
            { id: 'rice', name: 'Рис', icon: '🍚', cat: 'pantry' },
            { id: 'pasta', name: 'Макароны', icon: '🍝', cat: 'pantry' },
            { id: 'oil', name: 'Растительное масло', icon: '🍾', cat: 'pantry' }
        ];

        // База данных рецептов
        const RECIPES_DB = [
            {
                id: 'pancakes',
                title: 'Пышные блины на молоке',
                time: '20 мин',
                cal: '220 ккал',
                diff: 'Легко',
                image: 'https://images.unsplash.com/photo-1567620905732-2d1ec7ab7445?auto=format&fit=crop&w=600&q=80',
                ingredients: ['milk', 'flour', 'eggs', 'butter', 'oil'],
                steps: [
                    'Взбей яйца с щепоткой соли и сахаром.',
                    'Влей молоко, тщательно перемешай венчиком.',
                    'Постепенно добавляй муку, чтобы не было комочков.',
                    'Добавь ложку растительного масла.',
                    'Жарь на раскаленной сковороде до золотистого цвета с двух сторон.'
                ]
            },
            {
                id: 'omelette',
                title: 'Пышный омлет с сыром и томатами',
                time: '10 мин',
                cal: '180 ккал',
                diff: 'Очень просто',
                image: 'https://images.unsplash.com/photo-1510693206972-df098062cb71?auto=format&fit=crop&w=600&q=80',
                ingredients: ['eggs', 'milk', 'cheese', 'tomatoes', 'butter'],
                steps: [
                    'Взбей яйца с молоком и солью.',
                    'Нарежь помидоры кубиками, натри сыр.',
                    'Вылей яичную смесь на разогретое масло, сверху выложи помидоры и сыр.',
                    'Готовь под крышкой 5-7 минут на среднем огне.'
                ]
            },
            {
                id: 'carbonara_simple',
                title: 'Сытная паста с сыром и колбасой',
                time: '15 мин',
                cal: '450 ккал',
                diff: 'Средне',
                image: 'https://images.unsplash.com/photo-1612874742237-6526221588e3?auto=format&fit=crop&w=600&q=80',
                ingredients: ['pasta', 'sausage', 'cheese', 'eggs', 'oil'],
                steps: [
                    'Отвари макароны в подсоленной воде.',
                    'Обжарь нарезанную колбасу/сосиски на растительном масле.',
                    'Смешай горячую пасту с колбасой, сыром и взбитым яйцом, быстро перемешай.'
                ]
            },
            {
                id: 'fried_potatoes',
                title: 'Жареная картошечка с луком',
                time: '25 мин',
                cal: '300 ккал',
                diff: 'Легко',
                image: 'https://images.unsplash.com/photo-1518013431117-eb1465fa5752?auto=format&fit=crop&w=600&q=80',
                ingredients: ['potatoes', 'onion', 'oil', 'garlic'],
                steps: [
                    'Нарежь картофель брусочками, а лук — полукольцами.',
                    'Разогрей масло в сковороде, выложи картофель.',
                    'За 10 минут до готовности добавь лук, чеснок и соль.'
                ]
            }
        ];

        // Состояние приложения
        let selectedPantry = new Set(['eggs', 'milk']);
        let favorites = new Set();
        let shoppingList = new Set();
        let currentTab = 'search';

        // Инициализация
        document.addEventListener('DOMContentLoaded', () => {
            renderIngredientsPool('all');
            renderSelectedChips();
            updateRecipeResults();
        });

        // Отрисовка доступных ингредиентов
        function renderIngredientsPool(cat) {
            const pool = document.getElementById('ingredients-pool');
            pool.innerHTML = '';

            INGREDIENTS_DB.forEach(ing => {
                if (cat !== 'all' && ing.cat !== cat) return;

                const isSelected = selectedPantry.has(ing.id);
                const btn = document.createElement('button');
                btn.className = `px-3 py-2 rounded-xl text-xs font-semibold border transition-all flex items-center space-x-1.5 ${
                    isSelected 
                    ? 'bg-amber-500 border-amber-500 text-white shadow-sm' 
                    : 'bg-white border-slate-200 text-slate-700 hover:border-amber-400'
                }`;
                btn.innerHTML = `<span>${ing.icon}</span> <span>${ing.name}</span>`;
                btn.onclick = () => toggleIngredient(ing.id);
                pool.appendChild(btn);
            });
        }

        // Переключение ингредиента
        function toggleIngredient(id) {
            if (selectedPantry.has(id)) {
                selectedPantry.delete(id);
            } else {
                selectedPantry.add(id);
            }
            renderIngredientsPool(document.querySelector('.cat-btn.bg-slate-900').dataset.cat);
            renderSelectedChips();
            updateRecipeResults();
        }

        // Чипсы выбранных продуктов
        function renderSelectedChips() {
            const container = document.getElementById('selected-chips');
            const count = document.getElementById('selected-count');
            count.innerText = selectedPantry.size;

            if (selectedPantry.size === 0) {
                container.innerHTML = `<span class="text-xs text-slate-400 italic">Ничего не выбрано...</span>`;
                return;
            }

            container.innerHTML = '';
            selectedPantry.forEach(id => {
                const item = INGREDIENTS_DB.find(i => i.id === id) || { name: id, icon: '📌' };
                const chip = document.createElement('span');
                chip.className = 'inline-flex items-center gap-1.5 px-2.5 py-1 bg-white border border-amber-200 text-slate-800 rounded-lg text-xs font-semibold shadow-sm';
                chip.innerHTML = `${item.icon} ${item.name} <i class="fa-solid fa-xmark text-slate-400 hover:text-rose-500 cursor-pointer ml-1" onclick="toggleIngredient('${id}')"></i>`;
                container.appendChild(chip);
            });
        }

        // Фильтр категорий
        function filterCat(cat) {
            document.querySelectorAll('.cat-btn').forEach(btn => {
                btn.className = 'cat-btn px-3 py-1.5 rounded-lg bg-slate-100 text-slate-600 hover:bg-slate-200 whitespace-nowrap';
            });
            event.target.className = 'cat-btn px-3 py-1.5 rounded-lg bg-slate-900 text-white whitespace-nowrap';
            renderIngredientsPool(cat);
        }

        // Сброс
        function clearPantry() {
            selectedPantry.clear();
            renderIngredientsPool('all');
            renderSelectedChips();
            updateRecipeResults();
        }

        // Добавление кастомного ингредиента
        function addCustomIngredient() {
            const input = document.getElementById('custom-ingredient');
            const val = input.value.trim();
            if (!val) return;

            const customId = 'custom_' + Date.now();
            INGREDIENTS_DB.push({ id: customId, name: val, icon: '🛒', cat: 'pantry' });
            selectedPantry.add(customId);
            input.value = '';

            renderIngredientsPool('all');
            renderSelectedChips();
            updateRecipeResults();
        }

        // Расчет совпадений
        function updateRecipeResults() {
            const grid = document.getElementById('recipes-grid');
            grid.innerHTML = '';

            const scored = RECIPES_DB.map(recipe => {
                const matched = recipe.ingredients.filter(i => selectedPantry.has(i));
                const matchPct = Math.round((matched.length / recipe.ingredients.length) * 100);
                return { ...recipe, matched, matchPct };
            }).sort((a, b) => b.matchPct - a.matchPct);

            document.getElementById('results-count').innerText = `Найдено: ${scored.length}`;

            scored.forEach(recipe => {
                const isFav = favorites.has(recipe.id);
                const card = document.createElement('div');
                card.className = 'bg-white rounded-2xl border border-slate-200 p-4 hover:shadow-md transition-all flex flex-col sm:flex-row gap-4 items-center relative';
                card.innerHTML = `
                    <img src="${recipe.image}" class="w-full sm:w-32 h-28 object-cover rounded-xl">
                    <div class="flex-grow space-y-1 w-full">
                        <div class="flex items-center justify-between">
                            <span class="text-[11px] font-bold px-2 py-0.5 rounded-md ${recipe.matchPct === 100 ? 'bg-emerald-100 text-emerald-700' : 'bg-amber-100 text-amber-700'}">
                                ${recipe.matchPct}% совпадение
                            </span>
                            <button onclick="toggleFav('${recipe.id}')" class="text-slate-300 hover:text-rose-500 ${isFav ? 'text-rose-500' : ''}">
                                <i class="fa-solid fa-heart text-lg"></i>
                            </button>
                        </div>
                        <h3 class="font-bold text-slate-900 cursor-pointer hover:text-amber-600" onclick="openRecipe('${recipe.id}')">${recipe.title}</h3>
                        <p class="text-xs text-slate-400 space-x-3">
                            <span><i class="fa-regular fa-clock"></i> ${recipe.time}</span>
                            <span><i class="fa-solid fa-fire"></i> ${recipe.cal}</span>
                        </p>
                    </div>
                `;
                grid.appendChild(card);
            });
        }

        // Открытие рецепта
        function openRecipe(id) {
            const recipe = RECIPES_DB.find(r => r.id === id);
            const modal = document.getElementById('recipe-modal');
            const content = document.getElementById('modal-content');

            content.innerHTML = `
                <img src="${recipe.image}" class="w-full h-56 object-cover">
                <div class="p-6 space-y-6">
                    <div>
                        <h2 class="text-2xl font-black text-slate-900">${recipe.title}</h2>
                        <p class="text-xs text-slate-400 space-x-4 mt-1">
                            <span>⏱ ${recipe.time}</span>
                            <span>🔥 ${recipe.cal}</span>
                            <span>📊 ${recipe.diff}</span>
                        </p>
                    </div>

                    <div>
                        <h4 class="font-bold text-slate-900 mb-2">Ингредиенты:</h4>
                        <ul class="space-y-2 text-sm">
                            ${recipe.ingredients.map(ingId => {
                                const ing = INGREDIENTS_DB.find(i => i.id === ingId) || { name: ingId, icon: '📌' };
                                const has = selectedPantry.has(ingId);
                                return `
                                    <li class="flex items-center justify-between border-b border-slate-100 pb-1.5">
                                        <span>${ing.icon} ${ing.name}</span>
                                        ${has 
                                            ? '<span class="text-xs text-emerald-600 font-semibold">✓ В наличии</span>' 
                                            : `<button onclick="addToShoppingList('${ing.name}')" class="text-xs text-amber-600 hover:underline">+ В покупки</button>`}
                                    </li>
                                `;
                            }).join('')}
                        </ul>
                    </div>

                    <div>
                        <h4 class="font-bold text-slate-900 mb-2">Шаги приготовления:</h4>
                        <ol class="list-decimal list-inside space-y-2 text-sm text-slate-600">
                            ${recipe.steps.map(step => `<li>${step}</li>`).join('')}
                        </ol>
                    </div>
                </div>
            `;

            modal.classList.remove('hidden');
        }

        function closeModal() {
            document.getElementById('recipe-modal').classList.add('hidden');
        }

        // Переключение закладок (Избранное)
        function toggleFav(id) {
            if (favorites.has(id)) favorites.delete(id);
            else favorites.add(id);

            document.getElementById('fav-badge').innerText = favorites.size;
            document.getElementById('fav-badge').classList.toggle('hidden', favorites.size === 0);
            updateRecipeResults();
            renderFavs();
        }

        function renderFavs() {
            const grid = document.getElementById('favs-grid');
            grid.innerHTML = '';
            if (favorites.size === 0) {
                grid.innerHTML = `<p class="text-slate-400 text-sm italic">Список избранного пуст...</p>`;
                return;
            }

            favorites.forEach(id => {
                const recipe = RECIPES_DB.find(r => r.id === id);
                const card = document.createElement('div');
                card.className = 'bg-white p-4 rounded-2xl border border-slate-200 flex gap-3 items-center';
                card.innerHTML = `
                    <img src="${recipe.image}" class="w-16 h-16 object-cover rounded-xl">
                    <div>
                        <h4 class="font-bold text-slate-900 text-sm cursor-pointer" onclick="openRecipe('${recipe.id}')">${recipe.title}</h4>
                        <span class="text-xs text-slate-400">${recipe.time}</span>
                    </div>
                `;
                grid.appendChild(card);
            });
        }

        // Покупки
        function addToShoppingList(item) {
            shoppingList.add(item);
            document.getElementById('list-badge').innerText = shoppingList.size;
            document.getElementById('list-badge').classList.remove('hidden');
            renderShoppingList();
        }

        function renderShoppingList() {
            const container = document.getElementById('shopping-list-container');
            if (shoppingList.size === 0) {
                container.innerHTML = `<p class="text-slate-400 text-sm italic">Список покупок пуст...</p>`;
                return;
            }

            container.innerHTML = '';
            shoppingList.forEach(item => {
                const row = document.createElement('div');
                row.className = 'flex items-center justify-between border-b border-slate-100 pb-2';
                row.innerHTML = `
                    <span class="text-sm font-medium text-slate-800">🛒 ${item}</span>
                    <button onclick="removeFromShoppingList('${item}')" class="text-slate-300 hover:text-rose-500"><i class="fa-solid fa-trash text-xs"></i></button>
                `;
                container.appendChild(row);
            });
        }

        function removeFromShoppingList(item) {
            shoppingList.delete(item);
            document.getElementById('list-badge').innerText = shoppingList.size;
            if (shoppingList.size === 0) document.getElementById('list-badge').classList.add('hidden');
            renderShoppingList();
        }

        function clearShoppingList() {
            shoppingList.clear();
            document.getElementById('list-badge').classList.add('hidden');
            renderShoppingList();
        }

        // Навигация
        function switchTab(tab) {
            currentTab = tab;
            document.getElementById('tab-search').classList.toggle('hidden', tab !== 'search');
            document.getElementById('tab-favs').classList.toggle('hidden', tab !== 'favs');
            document.getElementById('tab-list').classList.toggle('hidden', tab !== 'list');

            if (tab === 'favs') renderFavs();
            if (tab === 'list') renderShoppingList();
        }
    </script>
</body>
</html>
