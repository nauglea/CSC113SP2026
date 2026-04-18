<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Protein Meal Planner (MVP)</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<style>
/* =========================
   BASIC STYLING
========================= */
body {
  font-family: Arial, sans-serif;
  margin: 20px;
  background: #f5f7fa;
}

h1 {
  text-align: center;
}

.container {
  max-width: 1000px;
  margin: auto;
}

.meal-slot {
  background: white;
  padding: 12px;
  margin-bottom: 10px;
  border-radius: 8px;
  border: 1px solid #ddd;
}

button {
  padding: 5px 10px;
  margin: 3px;
  cursor: pointer;
}

.food-item {
  display: flex;
  justify-content: space-between;
  margin-top: 5px;
}

.progress {
  height: 20px;
  border-radius: 10px;
  background: #ddd;
  margin-top: 10px;
  overflow: hidden;
}

.progress-bar {
  height: 100%;
  text-align: center;
  color: white;
  font-size: 12px;
}

.green { background: #4caf50; }
.yellow { background: #ffb300; }
.orange { background: #f44336; }

.panel {
  background: white;
  padding: 10px;
  border-radius: 8px;
  margin-top: 15px;
  border: 1px solid #ddd;
}

.food-list {
  max-height: 200px;
  overflow-y: auto;
  border: 1px solid #ccc;
  padding: 5px;
  margin-top: 5px;
}
</style>
</head>

<body>
<div class="container">

<h1>Protein Meal Planner</h1>

<!-- =========================
     TOTAL PROTEIN DISPLAY
========================= -->
<div class="panel">
  <strong>Total Protein:</strong>
  <span id="totalProtein">0</span>g (Target: 80–100g)

  <div class="progress">
    <div id="progressBar" class="progress-bar yellow" style="width:0%">0%</div>
  </div>
</div>

<!-- =========================
     MEAL SLOTS
========================= -->
<div id="mealSlots"></div>

<!-- =========================
     GAP SUGGESTIONS PANEL
========================= -->
<div id="gapPanel" class="panel" style="display:none;">
  <h3>Close the Gap</h3>
  <div id="suggestions"></div>
</div>

<!-- =========================
     FOOD SELECTOR
========================= -->
<div class="panel">
  <h3>Add Food</h3>
  <select id="slotSelect"></select>
  <input type="text" id="search" placeholder="Search food...">
  <div id="foodList" class="food-list"></div>
</div>

</div>

<script>
/* =========================
   FOOD DATABASE (CURATED)
========================= */
const foods = [
  {id:"greek_yogurt", name:"Greek Yogurt", protein:20, category:"dairy"},
  {id:"cottage_cheese", name:"Cottage Cheese", protein:25, category:"dairy"},
  {id:"eggs", name:"Eggs (2)", protein:12, category:"dairy"},
  {id:"lentils", name:"Lentils (1 cup)", protein:18, category:"legume"},
  {id:"chickpeas", name:"Chickpeas (1 cup)", protein:15, category:"legume"},
  {id:"tofu", name:"Tofu", protein:20, category:"legume"},
  {id:"tempeh", name:"Tempeh", protein:21, category:"legume"},
  {id:"salmon", name:"Salmon", protein:22, category:"fish"},
  {id:"tuna", name:"Tuna", protein:25, category:"fish"},
  {id:"shrimp", name:"Shrimp", protein:20, category:"fish"},
  {id:"quinoa", name:"Quinoa", protein:8, category:"grain"},
  {id:"chia", name:"Chia Seeds", protein:5, category:"grain"},
  {id:"protein_powder", name:"Protein Powder", protein:24, category:"supplement"}
];

/* =========================
   APP STATE
========================= */
const slots = [
  {id:"breakfast", label:"Breakfast", items:[]},
  {id:"morning_snack", label:"Morning Snack", items:[]},
  {id:"lunch", label:"Lunch", items:[]},
  {id:"afternoon_snack", label:"Afternoon Snack", items:[]},
  {id:"dinner", label:"Dinner", items:[]}
];

/* =========================
   INITIALIZE UI
========================= */
const mealSlotsDiv = document.getElementById("mealSlots");
const slotSelect = document.getElementById("slotSelect");

slots.forEach(slot => {
  slotSelect.innerHTML += `<option value="${slot.id}">${slot.label}</option>`;
});

/* =========================
   RENDER MEAL SLOTS
========================= */
function renderSlots() {
  mealSlotsDiv.innerHTML = "";

  slots.forEach(slot => {
    let div = document.createElement("div");
    div.className = "meal-slot";

    let html = `<strong>${slot.label}</strong>`;

    slot.items.forEach((item, index) => {
      html += `
        <div class="food-item">
          ${item.name} (${item.protein}g)
          <button onclick="removeItem('${slot.id}', ${index})">X</button>
        </div>
      `;
    });

    div.innerHTML = html;
    mealSlotsDiv.appendChild(div);
  });

  updateTotals();
}

/* =========================
   ADD / REMOVE FOOD
========================= */
function addFood(foodId) {
  const slotId = slotSelect.value;
  const food = foods.find(f => f.id === foodId);
  const slot = slots.find(s => s.id === slotId);

  slot.items.push(food);
  renderSlots();
}

function removeItem(slotId, index) {
  const slot = slots.find(s => s.id === slotId);
  slot.items.splice(index, 1);
  renderSlots();
}

/* =========================
   TOTAL PROTEIN CALCULATION
========================= */
function updateTotals() {
  let total = 0;

  slots.forEach(slot => {
    slot.items.forEach(item => total += item.protein);
  });

  document.getElementById("totalProtein").innerText = total;

  updateProgress(total);
  updateGap(total);
}

/* =========================
   PROGRESS BAR LOGIC
========================= */
function updateProgress(total) {
  const bar = document.getElementById("progressBar");

  let percent = Math.min((total / 100) * 100, 100);
  bar.style.width = percent + "%";
  bar.innerText = total + "g";

  bar.className = "progress-bar";

  if (total < 80) bar.classList.add("yellow");
  else if (total <= 100) bar.classList.add("green");
  else bar.classList.add("orange");
}

/* =========================
   GAP SUGGESTIONS
========================= */
function updateGap(total) {
  const panel = document.getElementById("gapPanel");
  const suggestionsDiv = document.getElementById("suggestions");

  if (total >= 80) {
    panel.style.display = "none";
    return;
  }

  panel.style.display = "block";

  const gap = 80 - total;

  const suggestions = foods
    .filter(f => f.protein >= gap * 0.3)
    .sort((a,b) => b.protein - a.protein)
    .slice(0,3);

  suggestionsDiv.innerHTML = "";

  suggestions.forEach(f => {
    suggestionsDiv.innerHTML += `
      <div>
        ${f.name} (${f.protein}g)
        <button onclick="quickAdd('${f.id}')">Add</button>
      </div>
    `;
  });
}

function quickAdd(foodId) {
  const snackSlot = slots.find(s => s.id.includes("snack"));
  snackSlot.items.push(foods.find(f => f.id === foodId));
  renderSlots();
}

/* =========================
   FOOD SEARCH / LIST
========================= */
const foodListDiv = document.getElementById("foodList");
const searchInput = document.getElementById("search");

function renderFoodList() {
  const query = searchInput.value.toLowerCase();

  foodListDiv.innerHTML = "";

  foods
    .filter(f => f.name.toLowerCase().includes(query))
    .forEach(f => {
      foodListDiv.innerHTML += `
        <div>
          ${f.name} (${f.protein}g)
          <button onclick="addFood('${f.id}')">Add</button>
        </div>
      `;
    });
}

searchInput.addEventListener("input", renderFoodList);

/* =========================
   INITIAL RENDER
========================= */
renderSlots();
renderFoodList();

</script>
</body>
</html>
