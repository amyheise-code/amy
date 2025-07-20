# amy
TJ's Costing 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Food Cost Calculator</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom styles for better aesthetics and responsiveness */
        body {
            font-family: "Inter", sans-serif;
            background-color: #f3f4f6; /* Light gray background */
        }
        .container {
            max-width: 1200px;
            margin: 2rem auto;
            padding: 1.5rem;
            background-color: #ffffff;
            border-radius: 1rem;
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
        }
        .section-title {
            font-size: 1.75rem; /* text-3xl */
            font-weight: 700; /* font-bold */
            color: #1f2937; /* gray-900 */
            margin-bottom: 1.5rem;
            border-bottom: 2px solid #e5e7eb; /* gray-200 */
            padding-bottom: 0.75rem;
        }
        .table-header th {
            padding: 0.75rem 1rem;
            text-align: left;
            font-size: 0.875rem; /* text-sm */
            font-weight: 600; /* font-semibold */
            color: #4b5563; /* gray-600 */
            background-color: #f9fafb; /* gray-50 */
            border-bottom: 1px solid #e5e7eb;
        }
        .table-row td {
            padding: 0.75rem 1rem;
            border-bottom: 1px solid #e5e7eb;
        }
        input[type="text"], input[type="number"], select {
            padding: 0.5rem 0.75rem;
            border: 1px solid #d1d5db; /* gray-300 */
            border-radius: 0.5rem; /* rounded-lg */
            width: 100%;
            box-shadow: inset 0 1px 2px 0 rgba(0, 0, 0, 0.05);
            transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
        }
        input[type="text"]:focus, input[type="number"]:focus, select:focus {
            outline: none;
            border-color: #2563eb; /* blue-600 */
            box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.2); /* blue-600 with opacity */
        }
        .btn-primary {
            background-color: #2563eb; /* blue-600 */
            color: white;
            padding: 0.75rem 1.5rem;
            border-radius: 0.75rem; /* rounded-xl */
            font-weight: 600;
            transition: background-color 0.2s ease-in-out, transform 0.1s ease-in-out;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
        }
        .btn-primary:hover {
            background-color: #1d4ed8; /* blue-700 */
            transform: translateY(-1px);
        }
        .btn-remove {
            background-color: #ef4444; /* red-500 */
            color: white;
            padding: 0.4rem 0.8rem;
            border-radius: 0.5rem;
            font-size: 0.875rem;
            transition: background-color 0.2s ease-in-out;
        }
        .btn-remove:hover {
            background-color: #dc2626; /* red-600 */
        }
        .total-cost {
            font-size: 1.25rem; /* text-xl */
            font-weight: 700; /* font-bold */
            color: #10b981; /* emerald-500 */
        }
        .tooltip {
            position: relative;
            display: inline-block;
            cursor: help;
        }

        .tooltip .tooltiptext {
            visibility: hidden;
            width: 200px;
            background-color: #333;
            color: #fff;
            text-align: center;
            border-radius: 6px;
            padding: 5px 0;
            position: absolute;
            z-index: 1;
            bottom: 125%; /* Position above the element */
            left: 50%;
            margin-left: -100px;
            opacity: 0;
            transition: opacity 0.3s;
        }

        .tooltip .tooltiptext::after {
            content: "";
            position: absolute;
            top: 100%; /* At the bottom of the tooltip */
            left: 50%;
            margin-left: -5px;
            border-width: 5px;
            border-style: solid;
            border-color: #333 transparent transparent transparent;
        }

        .tooltip:hover .tooltiptext {
            visibility: visible;
            opacity: 1;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="text-4xl font-extrabold text-center text-gray-900 mb-8">Food Cost Calculator</h1>
        <div class="text-center text-gray-500 mb-4" id="userIdDisplay">Loading...</div>
        <div class="text-center text-blue-500 mb-4" id="loadingIndicator">Loading data...</div>

        <!-- Ingredients Section -->
        <div class="mb-8">
            <h2 class="section-title">1. Ingredients List</h2>
            <p class="text-gray-600 mb-4">List all your raw ingredients with their unit cost. This will be used to calculate the cost of your menu items. <strong>Please adjust the costs to reflect your actual purchasing prices.</strong></p>
            <table class="min-w-full bg-white rounded-lg overflow-hidden shadow-sm">
                <thead>
                    <tr class="table-header">
                        <th class="w-1/3">Ingredient Name</th>
                        <th class="w-1/6">Unit</th>
                        <th class="w-1/6">Cost per Unit ($)</th>
                        <th class="w-1/6"></th>
                    </tr>
                </thead>
                <tbody id="ingredientsTableBody">
                    <!-- Ingredient rows will be added here by JavaScript -->
                </tbody>
            </table>
            <button id="addIngredientBtn" class="btn-primary mt-4">Add Ingredient</button>
        </div>

        <!-- Menu Items Section -->
        <div>
            <h2 class="section-title">2. Menu Items</h2>
            <p class="text-gray-600 mb-4">Build your menu items by selecting ingredients and specifying quantities. The total food cost per item will be calculated automatically. <strong>Quantities are estimates and should be adjusted based on your actual recipes and portion sizes.</strong></p>
            <div id="menuItemsContainer" class="space-y-6">
                <!-- Menu item cards will be added here by JavaScript -->
            </div>
            <button id="addMenuItemBtn" class="btn-primary mt-4">Add Menu Item</button>
        </div>
    </div>

    <script type="module">
        // Firebase Imports
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, setDoc, deleteDoc, onSnapshot, collection, query, getDocs } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Global Firebase variables
        let app;
        let db;
        let auth;
        let currentUserId = null;
        let ingredientsCollectionRef;
        let menuItemsCollectionRef;

        // Global arrays to store data
        let ingredients = [];
        let menuItems = [];
        let ingredientIdCounter = 0;
        let menuItemIdCounter = 0;

        // Define common units for the dropdown
        const commonUnits = [
            'kg', 'g', 'L', 'ml', 'piece', 'bunch', 'head', 'dozen', 'lbs', 'oz', 'case', 'bag'
        ];

        /**
         * Defines conversion factors between units.
         * Factors are relative to a base unit for each type (kg for weight, L for volume, piece for discrete).
         * Example: 'g': 0.001 means 1 gram = 0.001 kg.
         */
        const unitConversionFactors = {
            'weight': {
                'kg': 1,
                'g': 0.001,
                'lbs': 0.453592,
                'oz': 0.0283495
            },
            'volume': {
                'L': 1,
                'ml': 0.001
            },
            'discrete': {
                'piece': 1,
                'bunch': 1,
                'head': 1,
                'case': 1,
                'bag': 1,
                'dozen': 12 // 1 dozen = 12 pieces
            }
        };

        /**
         * Maps each unit to its type (weight, volume, discrete).
         */
        const unitTypes = {
            'kg': 'weight', 'g': 'weight', 'lbs': 'weight', 'oz': 'weight',
            'L': 'volume', 'ml': 'volume',
            'piece': 'discrete', 'bunch': 'discrete', 'head': 'discrete', 'dozen': 'discrete', 'case': 'discrete', 'bag': 'discrete'
        };

        /**
         * Converts a quantity from a source unit to a target unit.
         * Returns 0 if units are incompatible (e.g., kg to L).
         * @param {number} quantity - The quantity to convert.
         * @param {string} fromUnit - The unit of the quantity.
         * @param {string} toUnit - The desired target unit.
         * @returns {number} The converted quantity.
         */
        function convertQuantity(quantity, fromUnit, toUnit) {
            if (fromUnit === toUnit) {
                return quantity;
            }

            const fromType = unitTypes[fromUnit];
            const toType = unitTypes[toUnit];

            // If units are of different types or not recognized, they are incompatible for conversion
            if (!fromType || !toType || fromType !== toType) {
                console.warn(`Incompatible units for conversion: Cannot convert from ${fromUnit} to ${toUnit}.`);
                return 0;
            }

            const fromFactor = unitConversionFactors[fromType][fromUnit];
            const toFactor = unitConversionFactors[toType][toUnit];

            if (fromFactor === undefined || toFactor === undefined) {
                console.warn(`Conversion factor not found for units: ${fromUnit} or ${toUnit}.`);
                return 0;
            }

            // Convert from 'fromUnit' to its base unit (factor is how many base units in 'fromUnit')
            // Then convert from base unit to 'toUnit' (divide by how many base units in 'toUnit')
            return (quantity * fromFactor) / toFactor;
        }

        /**
         * Debounce function to limit how often a function is called.
         * @param {function} func - The function to debounce.
         * @param {number} delay - The delay in milliseconds.
         * @returns {function} The debounced function.
         */
        function debounce(func, delay) {
            let timeout;
            return function(...args) {
                const context = this;
                clearTimeout(timeout);
                timeout = setTimeout(() => func.apply(context, args), delay);
            };
        }

        // Debounced save functions for Firestore
        const debouncedSaveIngredient = debounce(async (ingredient) => {
            if (!currentUserId) return;
            try {
                await setDoc(doc(ingredientsCollectionRef, ingredient.id), ingredient);
                console.log("Ingredient saved:", ingredient.name);
            } catch (e) {
                console.error("Error saving ingredient:", e);
            }
        }, 500); // Save after 500ms of no changes

        const debouncedSaveMenuItem = debounce(async (menuItem) => {
            if (!currentUserId) return;
            try {
                // Firestore doesn't like nested arrays directly, so stringify ingredients
                const menuItemToSave = {
                    ...menuItem,
                    ingredients: JSON.stringify(menuItem.ingredients)
                };
                await setDoc(doc(menuItemsCollectionRef, menuItem.id), menuItemToSave);
                console.log("Menu item saved:", menuItem.name);
            } catch (e) {
                console.error("Error saving menu item:", e);
            }
        }, 500); // Save after 500ms of no changes


        // --- Ingredient Functions ---

        /**
         * Adds a new ingredient row to the ingredients table.
         * @param {Object} [ingredientData] - Optional data to pre-fill the row (e.g., { id, name, unit, cost }).
         * @param {boolean} [fromFirestore=false] - True if data is coming from Firestore.
         */
        async function addIngredientRow(ingredientData = {}, fromFirestore = false) {
            const id = ingredientData.id || `ing-${ingredientIdCounter++}`;
            const name = ingredientData.name || '';
            const unit = ingredientData.unit || 'piece'; // Default to 'piece'
            const cost = ingredientData.cost || '';

            const newRow = document.createElement('tr');
            newRow.id = id;
            newRow.classList.add('table-row');
            newRow.innerHTML = `
                <td><input type="text" value="${name}" placeholder="e.g., Chicken Breast" data-field="name" class="rounded-lg"></td>
                <td>
                    <select data-field="unit" class="rounded-lg" onchange="updateIngredientData('${id}', 'unit', this.value)">
                        <option value="">Select Unit</option>
                        ${commonUnits.map(u => `<option value="${u}" ${u === unit ? 'selected' : ''}>${u}</option>`).join('')}
                    </select>
                </td>
                <td><input type="number" value="${cost}" step="0.01" min="0" placeholder="0.00" data-field="cost" class="rounded-lg"></td>
                <td>
                    <button class="btn-remove" onclick="removeIngredient('${id}')">Remove</button>
                </td>
            `;
            document.getElementById('ingredientsTableBody').appendChild(newRow);

            // Add to ingredients array if not already present (for initial load or new add)
            if (!ingredients.some(ing => ing.id === id)) {
                const newIngredient = { id, name, unit, cost: parseFloat(cost) || 0 };
                ingredients.push(newIngredient);
                if (!fromFirestore) {
                    await debouncedSaveIngredient(newIngredient);
                }
            }

            // Add event listeners for input changes (only for name and cost, unit handled by onchange)
            newRow.querySelector('input[data-field="name"]').addEventListener('input', (event) => {
                updateIngredientData(id, event.target.dataset.field, event.target.value);
            });
            newRow.querySelector('input[data-field="cost"]').addEventListener('input', (event) => {
                updateIngredientData(id, event.target.dataset.field, event.target.value);
            });
        }

        /**
         * Updates the data for a specific ingredient and recalculates menu item costs.
         * @param {string} id - The ID of the ingredient.
         * @param {string} field - The field to update (name, unit, cost).
         * @param {string} value - The new value for the field.
         */
        function updateIngredientData(id, field, value) {
            const index = ingredients.findIndex(ing => ing.id === id);
            if (index !== -1) {
                if (field === 'cost') {
                    ingredients[index][field] = parseFloat(value) || 0;
                } else {
                    ingredients[index][field] = value;
                }
                debouncedSaveIngredient(ingredients[index]);
                // After updating an ingredient, we need to update all menu items
                // that use this ingredient and then re-render their costs.
                updateAllMenuItemCosts();
                populateIngredientDropdowns(); // Update dropdowns if names or units change
            }
        }

        /**
         * Removes an ingredient row and its data.
         * @param {string} id - The ID of the ingredient to remove.
         */
        async function removeIngredient(id) {
            // Remove from DOM
            const row = document.getElementById(id);
            if (row) {
                row.remove();
            }

            // Remove from array
            ingredients = ingredients.filter(ing => ing.id !== id);

            // Remove this ingredient from any menu items that use it
            menuItems.forEach(menuItem => {
                menuItem.ingredients = menuItem.ingredients.filter(itemIng => itemIng.ingredientId !== id);
                debouncedSaveMenuItem(menuItem); // Save updated menu item
            });

            // Delete from Firestore
            if (currentUserId) {
                try {
                    await deleteDoc(doc(ingredientsCollectionRef, id));
                    console.log("Ingredient deleted from Firestore:", id);
                } catch (e) {
                    console.error("Error deleting ingredient:", e);
                }
            }

            // Update all menu item costs and dropdowns
            updateAllMenuItemCosts();
            populateIngredientDropdowns();
        }

        /**
         * Populates all ingredient dropdowns in menu item rows with the current list of ingredients.
         */
        function populateIngredientDropdowns() {
            document.querySelectorAll('.ingredient-select').forEach(selectElement => {
                const currentSelectedId = selectElement.value; // Preserve current selection
                selectElement.innerHTML = '<option value="">-- Select Ingredient --</option>'; // Clear existing options

                ingredients.forEach(ing => {
                    const option = document.createElement('option');
                    option.value = ing.id;
                    option.textContent = ing.name || 'Unnamed Ingredient';
                    selectElement.appendChild(option);
                });

                // Restore previous selection if it still exists
                if (currentSelectedId && ingredients.some(ing => ing.id === currentSelectedId)) {
                    selectElement.value = currentSelectedId;
                } else {
                    selectElement.value = ''; // Reset if selected ingredient was removed
                }
            });
            // After populating dropdowns, ensure units are displayed correctly for existing items
            menuItems.forEach(menuItem => {
                menuItem.ingredients.forEach(itemIng => {
                    const ingredient = ingredients.find(ing => ing.id === itemIng.ingredientId);
                    const unitSelect = document.getElementById(`ingredientUnit-${itemIng.rowId}`);
                    if (unitSelect && ingredient) {
                        // If the ingredient's unit changed, update the menu item's unit to match,
                        // or keep the current if it's still a valid option.
                        if (!commonUnits.includes(itemIng.itemUnit) || !unitTypes[ingredient.unit] || !unitTypes[itemIng.itemUnit] || unitTypes[ingredient.unit] !== unitTypes[itemIng.itemUnit]) {
                             // If the current itemUnit is no longer compatible with the ingredient's new unit type, reset to ingredient's unit
                            itemIng.itemUnit = ingredient.unit;
                        }
                        // Repopulate the dropdown with compatible units
                        unitSelect.innerHTML = commonUnits.filter(u => unitTypes[u] === unitTypes[ingredient.unit])
                                                        .map(u => `<option value="${u}" ${u === (itemIng.itemUnit || ingredient.unit) ? 'selected' : ''}>${u}</option>`).join('');
                        unitSelect.value = itemIng.itemUnit || ingredient.unit || '';
                    } else if (unitSelect) {
                        unitSelect.innerHTML = '<option value="">Select Unit</option>';
                        unitSelect.value = '';
                    }
                });
            });
        }

        // --- Menu Item Functions ---

        /**
         * Adds a new menu item card.
         * @param {Object} [menuItemData] - Optional data to pre-fill the menu item (e.g., { id, name, ingredients: [{ ingredientId, quantity, itemUnit }] }).
         * @param {boolean} [fromFirestore=false] - True if data is coming from Firestore.
         */
        async function addMenuItemCard(menuItemData = {}, fromFirestore = false) {
            const id = menuItemData.id || `menu-${menuItemIdCounter++}`;
            const name = menuItemData.name || '';
            // Parse ingredients if coming from Firestore (they were stringified)
            const itemIngredients = typeof menuItemData.ingredients === 'string' ? JSON.parse(menuItemData.ingredients) : (menuItemData.ingredients || []);

            const newCard = document.createElement('div');
            newCard.id = id;
            newCard.classList.add('bg-white', 'p-6', 'rounded-lg', 'shadow-md', 'border', 'border-gray-200');
            newCard.innerHTML = `
                <div class="flex justify-between items-center mb-4">
                    <input type="text" value="${name}" placeholder="Menu Item Name (e.g., Pub Burger)"
                           class="text-xl font-semibold w-full mr-4 rounded-lg" data-field="name"
                           oninput="updateMenuItemData('${id}', 'name', this.value)">
                    <button class="btn-remove" onclick="removeMenuItem('${id}')">Remove Item</button>
                </div>
                <h3 class="text-lg font-medium text-gray-700 mb-3">Ingredients for this item:</h3>
                <table class="min-w-full bg-white rounded-lg overflow-hidden shadow-sm mb-4">
                    <thead>
                        <tr class="table-header">
                            <th class="w-1/2">Ingredient</th>
                            <th class="w-1/4">Quantity</th>
                            <th class="w-1/4">Cost</th>
                            <th></th>
                        </tr>
                    </thead>
                    <tbody id="menuItemIngredientsTableBody-${id}">
                        <!-- Ingredient rows for this menu item will go here -->
                    </tbody>
                </table>
                <button class="btn-primary py-2 px-4 text-sm rounded-lg" onclick="addIngredientToMenuItem('${id}')">Add Ingredient to Item</button>
                <div class="text-right mt-4 pt-4 border-t border-gray-200">
                    <span class="text-gray-700 text-lg">Total Food Cost: </span>
                    <span id="totalCost-${id}" class="total-cost">$0.00</span>
                    <div class="tooltip ml-2">
                        <span class="text-gray-500 text-sm">?</span>
                        <span class="tooltiptext">This is the raw cost of ingredients for one serving of this menu item.</span>
                    </div>
                </div>
            `;
            document.getElementById('menuItemsContainer').appendChild(newCard);

            // Add to menuItems array if not already present
            if (!menuItems.some(item => item.id === id)) {
                const newMenuItem = { id, name, ingredients: itemIngredients };
                menuItems.push(newMenuItem);
                if (!fromFirestore) {
                    await debouncedSaveMenuItem(newMenuItem);
                }
            }

            // If pre-filling, add existing ingredients to this menu item
            itemIngredients.forEach(itemIng => {
                addIngredientToMenuItem(id, itemIng.ingredientId, itemIng.quantity, itemIng.itemUnit);
            });

            // Recalculate cost for the newly added menu item
            updateMenuItemCost(id);
        }

        /**
         * Updates the data for a specific menu item (e.g., its name).
         * @param {string} id - The ID of the menu item.
         * @param {string} field - The field to update (e.g., 'name').
         * @param {string} value - The new value.
         */
        function updateMenuItemData(id, field, value) {
            const index = menuItems.findIndex(item => item.id === id);
            if (index !== -1) {
                menuItems[index][field] = value;
                debouncedSaveMenuItem(menuItems[index]);
            }
        }

        /**
         * Removes a menu item card and its data.
         * @param {string} id - The ID of the menu item to remove.
         */
        async function removeMenuItem(id) {
            // Remove from DOM
            const card = document.getElementById(id);
            if (card) {
                card.remove();
            }

            // Remove from array
            menuItems = menuItems.filter(item => item.id !== id);

            // Delete from Firestore
            if (currentUserId) {
                try {
                    await deleteDoc(doc(menuItemsCollectionRef, id));
                    console.log("Menu item deleted from Firestore:", id);
                } catch (e) {
                    console.error("Error deleting menu item:", e);
                }
            }
        }

        /**
         * Adds an ingredient row to a specific menu item's table.
         * @param {string} menuItemId - The ID of the menu item.
         * @param {string} [selectedIngredientId] - Optional ID of an ingredient to pre-select.
         * @param {number} [quantity] - Optional quantity to pre-fill.
         * @param {string} [itemUnit] - Optional unit to pre-fill for this item's quantity.
         */
        function addIngredientToMenuItem(menuItemId, selectedIngredientId = '', quantity = 1, itemUnit = '') {
            const menuItem = menuItems.find(item => item.id === menuItemId);
            if (!menuItem) return;

            const rowId = `${menuItemId}-itemIng-${menuItem.ingredients.length}-${Date.now()}`; // More unique ID

            // Find the selected ingredient to get its base unit
            const selectedIngredient = ingredients.find(ing => ing.id === selectedIngredientId);
            const defaultItemUnit = itemUnit || (selectedIngredient ? selectedIngredient.unit : '');

            // Filter commonUnits to only show compatible units based on the selected ingredient's type
            const compatibleUnits = selectedIngredient ? commonUnits.filter(u => unitTypes[u] === unitTypes[selectedIngredient.unit]) : commonUnits;

            const newRow = document.createElement('tr');
            newRow.id = rowId;
            newRow.classList.add('table-row');
            newRow.innerHTML = `
                <td>
                    <select class="ingredient-select w-full rounded-lg"
                            onchange="updateMenuItemIngredient('${menuItemId}', '${rowId}', 'ingredientId', this.value)">
                        <option value="">-- Select Ingredient --</option>
                        ${ingredients.map(ing => `<option value="${ing.id}" ${ing.id === selectedIngredientId ? 'selected' : ''}>${ing.name || 'Unnamed Ingredient'}</option>`).join('')}
                    </select>
                </td>
                <td class="flex items-center">
                    <input type="number" value="${quantity}" min="0.01" step="0.01" placeholder="1"
                           class="w-2/3 rounded-lg mr-2"
                           oninput="updateMenuItemIngredient('${menuItemId}', '${rowId}', 'quantity', this.value)">
                    <select id="ingredientUnit-${rowId}" class="text-gray-600 text-sm w-1/3 rounded-lg"
                            onchange="updateMenuItemIngredient('${menuItemId}', '${rowId}', 'itemUnit', this.value)">
                        ${compatibleUnits.map(u => `<option value="${u}" ${u === defaultItemUnit ? 'selected' : ''}>${u}</option>`).join('')}
                    </select>
                </td>
                <td id="ingredientCost-${rowId}">$0.00</td>
                <td>
                    <button class="btn-remove" onclick="removeIngredientFromMenuItem('${menuItemId}', '${rowId}')">X</button>
                </td>
            `;
            document.getElementById(`menuItemIngredientsTableBody-${menuItemId}`).appendChild(newRow);

            // Add to the menu item's ingredients array
            menuItem.ingredients.push({
                rowId: rowId, // Store rowId to easily find and update this specific entry
                ingredientId: selectedIngredientId,
                quantity: parseFloat(quantity) || 0,
                itemUnit: defaultItemUnit // Store the unit selected for this specific item use
            });

            // Save the updated menu item
            debouncedSaveMenuItem(menuItem);

            // Update costs immediately after adding
            updateMenuItemCost(menuItemId);
        }

        /**
         * Updates an ingredient's data within a specific menu item and recalculates costs.
         * @param {string} menuItemId - The ID of the menu item.
         * @param {string} rowId - The unique ID of the ingredient row within the menu item.
         * @param {string} field - The field to update ('ingredientId', 'quantity', or 'itemUnit').
         * @param {string} value - The new value.
         */
        function updateMenuItemIngredient(menuItemId, rowId, field, value) {
            const menuItem = menuItems.find(item => item.id === menuItemId);
            if (!menuItem) return;

            const itemIngredient = menuItem.ingredients.find(itemIng => itemIng.rowId === rowId);
            if (itemIngredient) {
                if (field === 'quantity') {
                    itemIngredient[field] = parseFloat(value) || 0;
                } else if (field === 'itemUnit') {
                    itemIngredient[field] = value;
                } else { // field is 'ingredientId'
                    itemIngredient[field] = value;
                    // When ingredient changes, update the itemUnit to match the ingredient's base unit
                    const selectedIngredient = ingredients.find(ing => ing.id === value);
                    if (selectedIngredient) {
                        itemIngredient.itemUnit = selectedIngredient.unit;
                        const unitSelect = document.getElementById(`ingredientUnit-${rowId}`);
                        if (unitSelect) {
                            // Repopulate the dropdown with compatible units for the newly selected ingredient
                            const compatibleUnits = commonUnits.filter(u => unitTypes[u] === unitTypes[selectedIngredient.unit]);
                            unitSelect.innerHTML = compatibleUnits.map(u => `<option value="${u}" ${u === selectedIngredient.unit ? 'selected' : ''}>${u}</option>`).join('');
                            unitSelect.value = selectedIngredient.unit;
                        }
                    } else {
                        itemIngredient.itemUnit = ''; // Clear unit if no ingredient selected
                        const unitSelect = document.getElementById(`ingredientUnit-${rowId}`);
                        if (unitSelect) {
                            unitSelect.innerHTML = '<option value="">Select Unit</option>';
                            unitSelect.value = '';
                        }
                    }
                }
                debouncedSaveMenuItem(menuItem); // Save the updated menu item
                updateMenuItemCost(menuItemId);
            }
        }

        /**
         * Removes an ingredient row from a specific menu item.
         * @param {string} menuItemId - The ID of the menu item.
         * @param {string} rowId - The unique ID of the ingredient row to remove.
         */
        function removeIngredientFromMenuItem(menuItemId, rowId) {
            // Remove from DOM
            const row = document.getElementById(rowId);
            if (row) {
                row.remove();
            }

            // Remove from menu item's ingredients array
            const menuItem = menuItems.find(item => item.id === menuItemId);
            if (menuItem) {
                menuItem.ingredients = menuItem.ingredients.filter(itemIng => itemIng.rowId !== rowId);
                debouncedSaveMenuItem(menuItem); // Save the updated menu item
                updateMenuItemCost(menuItemId);
            }
        }

        /**
         * Calculates and updates the total cost for a single menu item.
         * Also updates the individual ingredient cost displayed in the menu item's table.
         * @param {string} menuItemId - The ID of the menu item to update.
         */
        function updateMenuItemCost(menuItemId) {
            const menuItem = menuItems.find(item => item.id === menuItemId);
            if (!menuItem) return;

            let totalCost = 0;
            menuItem.ingredients.forEach(itemIng => {
                const ingredient = ingredients.find(ing => ing.id === itemIng.ingredientId);
                let ingredientCalculatedCost = 0;
                if (ingredient && itemIng.quantity > 0 && ingredient.unit && itemIng.itemUnit) {
                    // Convert the quantity from the menu item's unit to the ingredient's base unit
                    const convertedQuantity = convertQuantity(itemIng.quantity, itemIng.itemUnit, ingredient.unit);
                    ingredientCalculatedCost = (ingredient.cost || 0) * convertedQuantity;
                    totalCost += ingredientCalculatedCost;
                }
                // Update individual ingredient cost display
                const costCell = document.getElementById(`ingredientCost-${itemIng.rowId}`);
                if (costCell) {
                    costCell.textContent = `$${ingredientCalculatedCost.toFixed(2)}`;
                }
            });

            document.getElementById(`totalCost-${menuItemId}`).textContent = `$${totalCost.toFixed(2)}`;
        }

        /**
         * Iterates through all menu items and updates their costs.
         * This is called when an ingredient's base cost changes.
         */
        function updateAllMenuItemCosts() {
            menuItems.forEach(item => updateMenuItemCost(item.id));
        }

        /**
         * Loads data from Firestore and populates the UI.
         */
        async function loadDataFromFirestore() {
            document.getElementById('loadingIndicator').style.display = 'block';

            // Clear existing UI and data arrays to re-populate from Firestore
            document.getElementById('ingredientsTableBody').innerHTML = '';
            document.getElementById('menuItemsContainer').innerHTML = '';
            ingredients = [];
            menuItems = [];
            ingredientIdCounter = 0;
            menuItemIdCounter = 0;

            try {
                // Listen for real-time updates to ingredients
                onSnapshot(ingredientsCollectionRef, (snapshot) => {
                    const loadedIngredients = [];
                    snapshot.forEach(doc => {
                        loadedIngredients.push(doc.data());
                    });
                    // Only update if there are actual changes to prevent re-rendering on every update
                    if (JSON.stringify(ingredients) !== JSON.stringify(loadedIngredients)) {
                        ingredients = loadedIngredients;
                        document.getElementById('ingredientsTableBody').innerHTML = ''; // Clear for re-render
                        ingredients.forEach(ing => addIngredientRow(ing, true));
                        // Update counters to avoid ID conflicts with new additions
                        const maxIngId = ingredients.reduce((max, ing) => Math.max(max, parseInt(ing.id.split('-')[1] || 0)), 0);
                        ingredientIdCounter = maxIngId + 1;
                        populateIngredientDropdowns(); // Ensure dropdowns are updated after ingredients load
                        updateAllMenuItemCosts(); // Recalculate costs based on potentially new ingredient data
                    }
                });

                // Listen for real-time updates to menu items
                onSnapshot(menuItemsCollectionRef, (snapshot) => {
                    const loadedMenuItems = [];
                    snapshot.forEach(doc => {
                        loadedMenuItems.push(doc.data());
                    });
                    // Only update if there are actual changes
                    if (JSON.stringify(menuItems.map(item => ({...item, ingredients: JSON.stringify(item.ingredients)}))) !== JSON.stringify(loadedMenuItems)) {
                        menuItems = loadedMenuItems.map(item => ({
                            ...item,
                            ingredients: JSON.parse(item.ingredients) // Parse ingredients back to array
                        }));
                        document.getElementById('menuItemsContainer').innerHTML = ''; // Clear for re-render
                        menuItems.forEach(item => addMenuItemCard(item, true));
                        // Update counters
                        const maxMenuItemId = menuItems.reduce((max, item) => Math.max(max, parseInt(item.id.split('-')[1] || 0)), 0);
                        menuItemIdCounter = maxMenuItemId + 1;
                        updateAllMenuItemCosts(); // Recalculate costs
                    }
                });

                // Check if there's any data already. If not, load default data.
                const initialIngredientsSnapshot = await getDocs(ingredientsCollectionRef);
                const initialMenuItemsSnapshot = await getDocs(menuItemsCollectionRef);

                if (initialIngredientsSnapshot.empty && initialMenuItemsSnapshot.empty) {
                    console.log("No existing data found in Firestore. Loading default menu items.");
                    loadDefaultData();
                } else {
                    console.log("Existing data found in Firestore. Loading from Firestore.");
                }

            } catch (error) {
                console.error("Error loading data from Firestore:", error);
                document.getElementById('loadingIndicator').textContent = 'Error loading data. Please try again.';
            } finally {
                document.getElementById('loadingIndicator').style.display = 'none';
            }
        }

        /**
         * Loads initial default menu data if Firestore is empty.
         */
        async function loadDefaultData() {
            // PRODUCE
            await addIngredientRow({ name: 'Arugula', unit: 'kg', cost: 0.63 / 0.453592 });
            await addIngredientRow({ name: 'Lemons', unit: 'piece', cost: 0.63 });
            await addIngredientRow({ name: 'Avocado', unit: 'kg', cost: 39.55 / (1 * 0.453592 * 10) });
            await addIngredientRow({ name: 'Limes', unit: 'piece', cost: 38.80 / 48 });
            await addIngredientRow({ name: 'Hot House Tomatoes', unit: 'kg', cost: 31.70 / (1 * 0.453592 * 10) });
            await addIngredientRow({ name: 'Mint', unit: 'bunch', cost: 1.81 });
            await addIngredientRow({ name: 'Berries', unit: 'kg', cost: 5.00 });
            await addIngredientRow({ name: 'Oranges', unit: 'piece', cost: 0.75 });
            await addIngredientRow({ name: 'Carrots', unit: 'kg', cost: 0.98 / 0.453592 });
            await addIngredientRow({ name: 'Parsley', unit: 'bunch', cost: 1.81 });
            await addIngredientRow({ name: 'Celery', unit: 'bunch', cost: 2.00 });
            await addIngredientRow({ name: 'Red Onions', unit: 'kg', cost: 49.70 / 25 });
            await addIngredientRow({ name: 'Cilantro', unit: 'bunch', cost: 1.50 });
            await addIngredientRow({ name: 'Red Peppers', unit: 'kg', cost: 2.45 / 0.453592 });
            await addIngredientRow({ name: 'Coleslaw Mix', unit: 'kg', cost: 33.80 / 10 });
            await addIngredientRow({ name: 'Roma Tomatoes', unit: 'kg', cost: 1.90 / 0.453592 });
            await addIngredientRow({ name: 'Cucumber', unit: 'piece', cost: 0.50 });
            await addIngredientRow({ name: 'Romaine Lettuce', unit: 'bag', cost: 3.98 });
            await addIngredientRow({ name: 'Dill', unit: 'bunch', cost: 1.81 });
            await addIngredientRow({ name: 'Russet Potatoes', unit: 'kg', cost: 1.50 });
            await addIngredientRow({ name: 'French Fries', unit: 'kg', cost: 3.00 });
            await addIngredientRow({ name: 'Shallots', unit: 'kg', cost: 8.00 });
            await addIngredientRow({ name: 'Green Peppers', unit: 'kg', cost: 2.02 / 0.453592 });
            await addIngredientRow({ name: 'Spinach', unit: 'kg', cost: 8.00 });
            await addIngredientRow({ name: 'Green Onions', unit: 'bunch', cost: 0.75 });
            await addIngredientRow({ name: 'Strawberries', unit: 'kg', cost: 7.00 });
            await addIngredientRow({ name: 'Green Cabbage', unit: 'piece', cost: 4.68 });
            await addIngredientRow({ name: 'White Onions', unit: 'kg', cost: 69.99 / 25 });
            await addIngredientRow({ name: 'Iceberg Lettuce', unit: 'head', cost: 2.07 });
            await addIngredientRow({ name: 'Jalapenos', unit: 'kg', cost: 4.00 });

            // MEAT/DAIRY
            await addIngredientRow({ name: 'Chicken Wings', unit: 'kg', cost: 139 / 18 });
            await addIngredientRow({ name: 'Homo Milk', unit: 'L', cost: 39.20 / 12 });
            await addIngredientRow({ name: 'Chicken Tenders', unit: 'kg', cost: 77.70 / 2 });
            await addIngredientRow({ name: '2% Milk', unit: 'L', cost: 30.65 / 12 });
            await addIngredientRow({ name: 'Frozen Chicken Breast (4-6oz)', unit: 'kg', cost: 84.50 / (15 * 5 * 0.0283495) });
            await addIngredientRow({ name: 'Buttermilk', unit: 'L', cost: 3.95 });
            await addIngredientRow({ name: 'Sour Cream', unit: 'L', cost: 29.28 / 4 });
            await addIngredientRow({ name: 'Ground Chuck', unit: 'kg', cost: 174 / 50 });
            await addIngredientRow({ name: 'Pub Mix Cheese', unit: 'kg', cost: 112.29 / 3 });
            await addIngredientRow({ name: 'Frozen Chicken Thighs', unit: 'kg', cost: 64.94 / 5 });
            await addIngredientRow({ name: 'American Cheddar Cheese', unit: 'kg', cost: 58.49 / 2 });
            await addIngredientRow({ name: 'Vegan Nuggets', unit: 'kg', cost: 115.86 / (2 * 5 * 0.453592) });
            await addIngredientRow({ name: 'Swiss Cheese', unit: 'kg', cost: 23.94 / 3 });
            await addIngredientRow({ name: 'Smoked Turkey', unit: 'kg', cost: 120.05 / 7 });
            await addIngredientRow({ name: 'Aged White Cheddar Cheese', unit: 'kg', cost: 93.83 / 4.54 });
            await addIngredientRow({ name: 'Smoked Meat', unit: 'kg', cost: 93.51 / 4.6 });
            await addIngredientRow({ name: 'Cheese Curds', unit: 'kg', cost: 155.99 / 10 });
            await addIngredientRow({ name: 'Beyond Burger Patty', unit: 'piece', cost: 110.32 / 40 });
            await addIngredientRow({ name: 'Vegan Cheese', unit: 'kg', cost: 25.00 });
            await addIngredientRow({ name: 'Frozen Cod', unit: 'kg', cost: 55.56 / (10 * 0.453592) });
            await addIngredientRow({ name: 'Parmesan Cheese', unit: 'kg', cost: 40.47 / 2 });
            await addIngredientRow({ name: 'Peameal Bacon', unit: 'kg', cost: 76.65 / 5 });
            await addIngredientRow({ name: 'Whipping Cream', unit: 'L', cost: 9.95 });
            await addIngredientRow({ name: 'Breakfast Sausage', unit: 'kg', cost: 46.19 / 5 });
            await addIngredientRow({ name: 'Ice Cream (Vanilla)', unit: 'L', cost: 18.47 / 4 });
            await addIngredientRow({ name: 'Ground Lamb', unit: 'kg', cost: 99.90 / (10 * 0.453592) });
            await addIngredientRow({ name: 'Eggs', unit: 'dozen', cost: 36.70 / 7.5 });

            // DRY GOODS SYSCO
            await addIngredientRow({ name: 'Beef Gravy', unit: 'kg', cost: 94.68 / 8 });
            await addIngredientRow({ name: 'Onion Powder', unit: 'kg', cost: 115.40 / 5.76 });
            await addIngredientRow({ name: 'Veggie Gravy', unit: 'kg', cost: 68.88 / 2.37 });
            await addIngredientRow({ name: 'Cumin', unit: 'kg', cost: 180.76 / 5.1 });
            await addIngredientRow({ name: 'Queso Cheese Sauce', unit: 'L', cost: 145.46 / 15.6 });
            await addIngredientRow({ name: 'Paprika', unit: 'kg', cost: 163.29 / 6 });
            await addIngredientRow({ name: 'Blue Cheese Dressing', unit: 'L', cost: 98.26 / 7.6 });
            await addIngredientRow({ name: 'Cayenne Pepper', unit: 'kg', cost: 101.57 / 3.03 });
            await addIngredientRow({ name: 'Ranch Dip', unit: 'L', cost: 67.15 / 7.6 });
            await addIngredientRow({ name: 'Garlic Powder', unit: 'kg', cost: 225.77 / 6 });
            await addIngredientRow({ name: 'Smokey BBQ Sauce', unit: 'L', cost: 57.01 / 8 });
            await addIngredientRow({ name: 'Mustard', unit: 'L', cost: 36.71 / 8 });
            await addIngredientRow({ name: 'Diana\'s Mild Sauce', unit: 'L', cost: 71.12 / 7.6 });
            await addIngredientRow({ name: 'Relish', unit: 'L', cost: 39.97 / 8 });
            await addIngredientRow({ name: 'Buffalo Sauce', unit: 'L', cost: 53.39 / 7.6 });
            await addIngredientRow({ name: 'Mayo', unit: 'L', cost: 137.85 / 16 });
            await addIngredientRow({ name: 'Hot Sauce', unit: 'L', cost: 44.06 / 7.6 });
            await addIngredientRow({ name: 'Chipotle Peppers in Adobo', unit: 'kg', cost: 74.01 / 4.7627 });
            await addIngredientRow({ name: 'Jerk Sauce', unit: 'L', cost: 67.21 / 4.4 });
            await addIngredientRow({ name: 'Malt Vinegar', unit: 'L', cost: 24.79 / 10 });
            await addIngredientRow({ name: 'Honey Garlic Sauce', unit: 'L', cost: 73.15 / 8 });
            await addIngredientRow({ name: 'White Vinegar', unit: 'L', cost: 25.37 / 10 });
            await addIngredientRow({ name: 'Cajun Seasoning', unit: 'kg', cost: 133.88 / 4.5 });
            await addIngredientRow({ name: 'Apple Cider Vinegar', unit: 'L', cost: 25.37 / 10 });
            await addIngredientRow({ name: 'Chipotle Mango Seasoning', unit: 'kg', cost: 280.57 / 7.8 });
            await addIngredientRow({ name: 'Red Wine Vinegar', unit: 'L', cost: 26.28 / 10 });
            await addIngredientRow({ name: 'Salsa', unit: 'L', cost: 83.94 / 16.8 });
            await addIngredientRow({ name: 'Extra Virgin Olive Oil', unit: 'L', cost: 238.70 / 12 });
            await addIngredientRow({ name: 'Caesar Dressing', unit: 'L', cost: 83.60 / 7.6 });
            await addIngredientRow({ name: 'Canola Oil', unit: 'L', cost: 31.50 / 16 });
            await addIngredientRow({ name: 'Plum Sauce', unit: 'L', cost: 53.94 / 8 });
            await addIngredientRow({ name: 'Ketchup', unit: 'L', cost: 86.63 / 16.8 });
            await addIngredientRow({ name: 'Tartar Sauce', unit: 'L', cost: 67.19 / 7.6 });
            await addIngredientRow({ name: 'Celery Salt', unit: 'kg', cost: 107.57 / (12 * 0.9) });
            await addIngredientRow({ name: 'Kosher Salt', unit: 'kg', cost: 23.34 / 20 });
            await addIngredientRow({ name: 'Black Pepper', unit: 'kg', cost: 77.60 / 2.1 });

            // BREADS/FROZEN (OTHER)
            await addIngredientRow({ name: 'Square Buns', unit: 'piece', cost: 0.50 });
            await addIngredientRow({ name: 'Large Flour Tortillas (12")', unit: 'piece', cost: 84.84 / 96 });
            await addIngredientRow({ name: 'Sweet Potato Fries', unit: 'kg', cost: 53.92 / 6.80389 });
            await addIngredientRow({ name: 'Frozen Cauliflower', unit: 'kg', cost: 65.96 / 5.44311 });
            await addIngredientRow({ name: 'Small Flour Tortillas (4.5")', unit: 'piece', cost: 75.39 / 288 });
            await addIngredientRow({ name: 'Corn Tortillas', unit: 'piece', cost: 66.18 / 720 });
            await addIngredientRow({ name: 'Chocolate Cake Base', unit: 'kg', cost: 6.00 });
            await addIngredientRow({ name: 'Gluten-Free Buns', unit: 'piece', cost: 1.00 });
            await addIngredientRow({ name: 'Beer Batter Mix', unit: 'kg', cost: 5.00 });
            await addIngredientRow({ name: 'Pico de Gallo', unit: 'kg', cost: 3.50 });
            await addIngredientRow({ name: 'Lime Crema', unit: 'L', cost: 15.00 });
            await addIngredientRow({ name: 'Guacamole', unit: 'kg', cost: 12.00 });
            await addIngredientRow({ name: 'Tortilla Chips', unit: 'kg', cost: 4.00 });
            await addIngredientRow({ name: 'Bell Peppers', unit: 'piece', cost: 0.80 });
            await addIngredientRow({ name: 'Black Bean Dip', unit: 'kg', cost: 7.00 });
            await addIngredientRow({ name: 'Feta Cheese', unit: 'kg', cost: 18.00 });
            await addIngredientRow({ name: 'Pickles', unit: 'kg', cost: 3.00 });
            await addIngredientRow({ name: 'Dijon Aioli', unit: 'L', cost: 10.00 });
            await addIngredientRow({ name: 'Mac Sauce', unit: 'L', cost: 8.00 });
            await addIngredientRow({ name: 'Roasted Garlic Aioli', unit: 'L', cost: 12.00 });
            await addIngredientRow({ name: 'Taco Beef', unit: 'kg', cost: 9.00 });
            await addIngredientRow({ name: 'Croutons', unit: 'kg', cost: 4.50 });
            await addIngredientRow({ name: 'Irish Cream Liqueur', unit: 'L', cost: 25.00 });
            await addIngredientRow({ name: 'Cream Cheese Frosting', unit: 'kg', cost: 7.00 });
            await addIngredientRow({ name: 'Graham Cracker Crust', unit: 'kg', cost: 2.50 });
            await addIngredientRow({ name: 'Cheesecake Filling', unit: 'kg', cost: 8.00 });
            await addIngredientRow({ name: 'Cherry Topping', unit: 'L', cost: 6.00 });
            await addIngredientRow({ name: 'Macaroni Noodles', unit: 'kg', cost: 2.00 });
            await addIngredientRow({ name: 'Veggie Sticks', unit: 'kg', cost: 5.00 });
            await addIngredientRow({ name: 'Bacon', unit: 'kg', cost: 54.00 / (1 * 0.453592 * 10) });
            await addIngredientRow({ name: 'Burger Buns', unit: 'piece', cost: 0.50 });
            await addIngredientRow({ name: 'Brioche Bun', unit: 'piece', cost: 0.75 });
            await addIngredientRow({ name: 'Pulled Chicken', unit: 'kg', cost: 8.00 });

            // Populate dropdowns after initial ingredients are added
            populateIngredientDropdowns();

            // Add menu items based on T. J. O'Shea's menu
            // Quantities are estimated and should be adjusted by the user.

            // --- Apps & Snacks ---
            await addMenuItemCard({
                name: 'Classic Chicken Wings (1 LB)',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Chicken Wings')?.id, quantity: 0.45, itemUnit: 'kg' }, // 1 LB = ~0.45 kg
                    { ingredientId: ingredients.find(ing => ing.name === 'Hot Sauce')?.id, quantity: 0.050, itemUnit: 'L' }, // 50ml sauce
                    { ingredientId: ingredients.find(ing => ing.name === 'Blue Cheese Dressing')?.id, quantity: 0.030, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Celery')?.id, quantity: 2, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Carrots')?.id, quantity: 2, itemUnit: 'piece' }
                ]
            });
            await addMenuItemCard({
                name: 'Cauliflower Bites (1/2 LB)',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Frozen Cauliflower')?.id, quantity: 0.225, itemUnit: 'kg' }, // 1/2 LB = ~0.225 kg
                    { ingredientId: ingredients.find(ing => ing.name === 'Beer Batter Mix')?.id, quantity: 0.02, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Buffalo Sauce')?.id, quantity: 0.030, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Ranch Dip')?.id, quantity: 0.020, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Celery')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Carrots')?.id, quantity: 1, itemUnit: 'piece' }
                ]
            });
            await addMenuItemCard({
                name: 'Snug Nachos (Full Size)',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Tortilla Chips')?.id, quantity: 0.2, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Queso Cheese Sauce')?.id, quantity: 0.100, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pub Mix Cheese')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pico de Gallo')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Peppers')?.id, quantity: 0.5, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Lime Crema')?.id, quantity: 0.020, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Guacamole')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Onions')?.id, quantity: 0.1, itemUnit: 'bunch' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Salsa')?.id, quantity: 0.050, itemUnit: 'L' }
                ]
            });
            await addMenuItemCard({
                name: 'Vegetarian Quesadilla',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Large Flour Tortillas (12")')?.id, quantity: 2, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pub Mix Cheese')?.id, quantity: 0.07, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pico de Gallo')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Bell Peppers')?.id, quantity: 0.25, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Salsa')?.id, quantity: 0.030, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Sour Cream')?.id, quantity: 0.020, itemUnit: 'L' }
                ]
            });
            await addMenuItemCard({
                name: 'Vegan "Chicken" Fingers',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Vegan Nuggets')?.id, quantity: 0.2, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.3, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Plum Sauce')?.id, quantity: 0.030, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Ranch Dip')?.id, quantity: 0.030, itemUnit: 'L' }
                ]
            });
            await addMenuItemCard({
                name: 'Snug Poutine',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.4, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Cheese Curds')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Beef Gravy')?.id, quantity: 0.150, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Onions')?.id, quantity: 0.05, itemUnit: 'bunch' }
                ]
            });
            await addMenuItemCard({
                name: 'Garlic-Parm Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.35, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Parmesan Cheese')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Onions')?.id, quantity: 0.02, itemUnit: 'bunch' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Roasted Garlic Aioli')?.id, quantity: 0.020, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Jalapenos')?.id, quantity: 0.01, itemUnit: 'kg' }
                ]
            });
            await addMenuItemCard({
                name: 'Sweet Potato Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Sweet Potato Fries')?.id, quantity: 0.3, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Chipotle Mayo')?.id, quantity: 0.030, itemUnit: 'L' }
                ]
            });
            await addMenuItemCard({
                name: 'French Fries', // Standalone item
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.35, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Ketchup')?.id, quantity: 0.020, itemUnit: 'L' }
                ]
            });
            await addMenuItemCard({
                name: 'Colcannon Croquettes',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Russet Potatoes')?.id, quantity: 0.2, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Cabbage')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Bacon')?.id, quantity: 0.02, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pub Mix Cheese')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Beer Batter Mix')?.id, quantity: 0.01, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Ranch Dip')?.id, quantity: 0.020, itemUnit: 'L' }
                ]
            });
            await addMenuItemCard({
                name: 'Creamy Spinach and Artichoke Dip',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Spinach')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Artichoke Hearts')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Cream Cheese')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pub Mix Cheese')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Tortilla Chips')?.id, quantity: 0.1, itemUnit: 'kg' }
                ]
            });

            // --- Tacos ---
            await addMenuItemCard({
                name: 'Fish Tacos',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Small Flour Tortillas (4.5")')?.id, quantity: 2, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Frozen Cod')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Coleslaw Mix')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pico de Gallo')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Chipotle Mayo')?.id, quantity: 0.015, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Onions')?.id, quantity: 0.01, itemUnit: 'bunch' }
                ]
            });
            await addMenuItemCard({
                name: 'Pulled Buffalo Chicken Tacos',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Small Flour Tortillas (4.5")')?.id, quantity: 2, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pulled Chicken')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Buffalo Sauce')?.id, quantity: 0.020, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Coleslaw Mix')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pico de Gallo')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Chipotle Mayo')?.id, quantity: 0.015, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Onions')?.id, quantity: 0.01, itemUnit: 'bunch' }
                ]
            });
            await addMenuItemCard({
                name: 'Cauliflower Tacos',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Small Flour Tortillas (4.5")')?.id, quantity: 2, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Frozen Cauliflower')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Buffalo Sauce')?.id, quantity: 0.020, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Coleslaw Mix')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pico de Gallo')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Honey Dill Sauce')?.id, quantity: 0.015, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Onions')?.id, quantity: 0.01, itemUnit: 'bunch' }
                ]
            });

            // --- Salads ---
            await addMenuItemCard({
                name: 'Taco Salad',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Romaine Lettuce')?.id, quantity: 0.2, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Salsa')?.id, quantity: 0.040, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Peppers')?.id, quantity: 0.5, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pico de Gallo')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pub Mix Cheese')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Taco Beef')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Black Bean Dip')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Guacamole')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Lime Crema')?.id, quantity: 0.015, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Tortilla Chips')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Onions')?.id, quantity: 0.02, itemUnit: 'bunch' }
                ]
            });
            await addMenuItemCard({
                name: 'House Salad',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Romaine Lettuce')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Arugula')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Hot House Tomatoes')?.id, quantity: 0.25, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Peppers')?.id, quantity: 0.25, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Cucumber')?.id, quantity: 0.25, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Red Wine Vinegar')?.id, quantity: 0.015, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Extra Virgin Olive Oil')?.id, quantity: 0.015, itemUnit: 'L' }
                ]
            });
            await addMenuItemCard({
                name: 'Caesar Salad',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Romaine Lettuce')?.id, quantity: 0.25, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Bacon')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Croutons')?.id, quantity: 0.02, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Parmesan Cheese')?.id, quantity: 0.02, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Caesar Dressing')?.id, quantity: 0.040, itemUnit: 'L' }
                ]
            });

            // --- Entrees ---
            await addMenuItemCard({
                name: 'Fried Chicken Sandwich with Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Frozen Chicken Breast (4-6oz)')?.id, quantity: 0.2, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Buttermilk')?.id, quantity: 0.050, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Burger Buns')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Coleslaw Mix')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pickles')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Honey Dill Sauce')?.id, quantity: 0.030, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.3, itemUnit: 'kg' }
                ]
            });
            await addMenuItemCard({
                name: 'Veggie Wrap With Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Large Flour Tortillas (12")')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Black Bean Dip')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Arugula')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Cucumber')?.id, quantity: 0.1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Bell Peppers')?.id, quantity: 0.2, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pico de Gallo')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Frozen Cauliflower')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Feta Cheese')?.id, quantity: 0.02, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Honey Dill Sauce')?.id, quantity: 0.020, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.3, itemUnit: 'kg' }
                ]
            });
            await addMenuItemCard({
                name: 'Turkey Avocado Club With Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Square Buns')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Smoked Turkey')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'American Cheddar Cheese')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Bacon')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Iceberg Lettuce')?.id, quantity: 0.05, itemUnit: 'head' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Hot House Tomatoes')?.id, quantity: 0.25, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Red Onions')?.id, quantity: 0.02, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Avocado')?.id, quantity: 0.04, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Chipotle Mayo')?.id, quantity: 0.020, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.3, itemUnit: 'kg' }
                ]
            });
            await addMenuItemCard({
                name: 'Chicken Caesar Wrap With Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Large Flour Tortillas (12")')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Frozen Chicken Breast (4-6oz)')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Romaine Lettuce')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Caesar Dressing')?.id, quantity: 0.030, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Bacon')?.id, quantity: 0.02, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Croutons')?.id, quantity: 0.01, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Parmesan Cheese')?.id, quantity: 0.01, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.3, itemUnit: 'kg' }
                ]
            });
            await addMenuItemCard({
                name: 'Chicken Tenders and Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Chicken Tenders')?.id, quantity: 0.25, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Plum Sauce')?.id, quantity: 0.030, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Ranch Dip')?.id, quantity: 0.030, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.3, itemUnit: 'kg' }
                ]
            });
            await addMenuItemCard({
                name: 'Classic Burger With Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Ground Chuck')?.id, quantity: 0.2, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Burger Buns')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Arugula')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Hot House Tomatoes')?.id, quantity: 0.25, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Red Onions')?.id, quantity: 0.02, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Dijon Aioli')?.id, quantity: 0.020, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.3, itemUnit: 'kg' }
                ]
            });
            await addMenuItemCard({
                name: 'Oshea\'s House Burger With Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Ground Chuck')?.id, quantity: 0.2, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Bacon')?.id, quantity: 0.02, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Burger Buns')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'White Onions')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Iceberg Lettuce')?.id, quantity: 0.05, itemUnit: 'head' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pickles')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Mac Sauce')?.id, quantity: 0.025, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pub Mix Cheese')?.id, quantity: 0.04, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.3, itemUnit: 'kg' }
                ]
            });
            await addMenuItemCard({
                name: 'T.J\'s Smash Burger With Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Ground Chuck')?.id, quantity: 0.2268 * 2, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Burger Buns')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'American Cheddar Cheese')?.id, quantity: 0.04, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'White Onions')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Pickles')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Roasted Garlic Aioli')?.id, quantity: 0.025, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.3, itemUnit: 'kg' }
                ]
            });
            await addMenuItemCard({
                name: 'Veggie Burger With Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Beyond Burger Patty')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Brioche Bun')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'American Cheddar Cheese')?.id, quantity: 0.02, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Arugula')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Hot House Tomatoes')?.id, quantity: 0.25, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Red Onions')?.id, quantity: 0.02, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Dijon Aioli')?.id, quantity: 0.020, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.3, itemUnit: 'kg' }
                ]
            });
            await addMenuItemCard({
                name: 'Mac N Cheese',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Macaroni Noodles')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Aged White Cheddar Cheese')?.id, quantity: 0.200, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Homo Milk')?.id, quantity: 0.050, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Onions')?.id, quantity: 0.05, itemUnit: 'bunch' }
                ]
            });
            await addMenuItemCard({
                name: 'Fish N\' Chips (1 Piece)',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Frozen Cod')?.id, quantity: 0.18, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Beer Batter Mix')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.3, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Coleslaw Mix')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Tartar Sauce')?.id, quantity: 0.030, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Lemons')?.id, quantity: 1, itemUnit: 'piece' }
                ]
            });
            await addMenuItemCard({
                name: 'Fish N\' Chips (2 Pieces)',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Frozen Cod')?.id, quantity: 0.36, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Beer Batter Mix')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.4, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Coleslaw Mix')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Tartar Sauce')?.id, quantity: 0.045, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Lemons')?.id, quantity: 2, itemUnit: 'piece' }
                ]
            });
            await addMenuItemCard({
                name: 'Corned Beef & Cabbage Sandwich with Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Square Buns')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Smoked Meat')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Green Cabbage')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'White Onions')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Swiss Cheese')?.id, quantity: 0.03, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Hot Honey Mustard')?.id, quantity: 0.020, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.3, itemUnit: 'kg' }
                ]
            });
            await addMenuItemCard({
                name: 'Shepherd\'s Pie w/ garlic toast',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Ground Lamb')?.id, quantity: 0.2, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'White Onions')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Carrots')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Peas')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Corn')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Russet Potatoes')?.id, quantity: 0.25, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Beef Gravy')?.id, quantity: 0.100, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Square Buns')?.id, quantity: 0.1, itemUnit: 'piece' }
                ]
            });

            // --- Desserts ---
            await addMenuItemCard({
                name: 'Guinness Cake',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Chocolate Cake Base')?.id, quantity: 0.2, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Irish Cream Liqueur')?.id, quantity: 0.020, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Cream Cheese Frosting')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Whipping Cream')?.id, quantity: 0.015, itemUnit: 'L' }
                ]
            });
            await addMenuItemCard({
                name: 'Classic Cheesecake',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Graham Cracker Crust')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Cheesecake Filling')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Whipping Cream')?.id, quantity: 0.015, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Cherry Topping')?.id, quantity: 0.020, itemUnit: 'L' }
                ]
            });

            // --- Kid's Menu ---
            await addMenuItemCard({
                name: 'Kids Cheese Burger with Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Ground Chuck')?.id, quantity: 0.1, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Burger Buns')?.id, quantity: 1, itemUnit: 'piece' },
                    { ingredientId: ingredients.find(ing => ing.name === 'American Cheddar Cheese')?.id, quantity: 0.01, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Ketchup')?.id, quantity: 0.010, itemUnit: 'L' }
                ]
            });
            await addMenuItemCard({
                name: 'Kids Chicken Tenders with Fries',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Chicken Tenders')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'French Fries')?.id, quantity: 0.15, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Plum Sauce')?.id, quantity: 0.020, itemUnit: 'L' }
                ]
            });
            await addMenuItemCard({
                name: 'Kids Mac & Cheese with Veggie Sticks',
                ingredients: [
                    { ingredientId: ingredients.find(ing => ing.name === 'Macaroni Noodles')?.id, quantity: 0.05, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Aged White Cheddar Cheese')?.id, quantity: 0.100, itemUnit: 'kg' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Homo Milk')?.id, quantity: 0.020, itemUnit: 'L' },
                    { ingredientId: ingredients.find(ing => ing.name === 'Veggie Sticks')?.id, quantity: 0.1, itemUnit: 'kg' }
                ]
            });
        }


        // --- Initial Setup and Event Listeners ---

        document.addEventListener('DOMContentLoaded', async () => {
            // Initialize Firebase
            const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
            const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');

            if (Object.keys(firebaseConfig).length === 0) {
                console.error("Firebase config is empty. Cannot initialize Firebase.");
                document.getElementById('loadingIndicator').textContent = 'Error: Firebase not configured.';
                return;
            }

            app = initializeApp(firebaseConfig);
            db = getFirestore(app);
            auth = getAuth(app);

            // Authenticate and load data
            onAuthStateChanged(auth, async (user) => {
                if (user) {
                    currentUserId = user.uid;
                    document.getElementById('userIdDisplay').textContent = `User ID: ${currentUserId}`;
                    ingredientsCollectionRef = collection(db, `artifacts/${appId}/users/${currentUserId}/ingredients`);
                    menuItemsCollectionRef = collection(db, `artifacts/${appId}/users/${currentUserId}/menuItems`);
                    await loadDataFromFirestore();
                } else {
                    try {
                        if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                            await signInWithCustomToken(auth, __initial_auth_token);
                        } else {
                            await signInAnonymously(auth);
                        }
                    } catch (error) {
                        console.error("Firebase authentication failed:", error);
                        document.getElementById('loadingIndicator').textContent = 'Error: Authentication failed.';
                    }
                }
            });

            // Event listeners for Add buttons
            document.getElementById('addIngredientBtn').addEventListener('click', () => addIngredientRow());
            document.getElementById('addMenuItemBtn').addEventListener('click', () => addMenuItemCard());
        });
    </script>
</body>
</html>
