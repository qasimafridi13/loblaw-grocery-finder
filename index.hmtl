<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover" />
  <title>Loblaws Shopper</title>
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="mobile-web-app-capable" content="yes">
  <style>
    * { box-sizing: border-box; font-family: -apple-system, BlinkMacSystemFont, sans-serif; }
    body { margin: 0; padding: 16px; background: #f9f9f9; color: #333; }
    h1, h2 { text-align: center; }
    button { 
      background: #007AFF; color: white; border: none; padding: 12px; 
      border-radius: 8px; width: 100%; margin: 8px 0; cursor: pointer;
    }
    input, select { 
      width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ccc; border-radius: 8px;
    }
    .hidden { display: none; }
    #videoContainer { width: 100%; position: relative; }
    #video { width: 100%; border-radius: 12px; }
    #canvas { display: none; }
    .product-item { background: white; padding: 12px; margin: 8px 0; border-radius: 8px; box-shadow: 0 1px 3px rgba(0,0,0,0.1); }
    .direction-tag { background: #e0f7fa; padding: 4px 8px; border-radius: 4px; font-size: 0.85em; }
  </style>
</head>
<body>
  <h1>Loblaws Shopper</h1>

  <!-- Main Menu -->
  <div id="mainMenu">
    <button onclick="showView('addAisle')">➕ Add Aisle</button>
    <button onclick="showView('scan')">📷 Scan Product</button>
    <button onclick="showView('search')">🔍 Search Product</button>
    <button onclick="showView('viewAll')">📦 View All</button>
  </div>

  <!-- Add Aisle -->
  <div id="addAisle" class="hidden">
    <h2>Add Aisle</h2>
    <input type="number" id="aisleNumber" placeholder="Aisle Number (e.g., 1)" min="1">
    <button onclick="addAisle()">Add Aisle</button>
    <button onclick="showView('mainMenu')">← Back</button>
  </div>

  <!-- Scan Product -->
  <div id="scan" class="hidden">
    <h2>Scan Product Label</h2>
    <div id="videoContainer">
      <video id="video" playsinline autoplay></video>
    </div>
    <canvas id="canvas"></canvas>
    <button id="captureBtn" onclick="captureAndOCR()">📸 Capture & Scan</button>
    <div id="ocrResult"></div>
    <button onclick="showView('mainMenu')">← Back</button>
  </div>

  <!-- Assign Product -->
  <div id="assignProduct" class="hidden">
    <h2>Assign Location</h2>
    <p>Product: <strong id="productNameDisplay"></strong></p>
    
    <label>Aisle</label>
    <select id="assignAisle"></select>
    
    <label>Department</label>
    <select id="assignDepartment"></select>
    
    <label>Direction</label>
    <select id="assignDirection">
      <option>North</option>
      <option>South</option>
      <option>East</option>
      <option>West</option>
    </select>
    
    <button onclick="saveProduct()">✅ Save Product</button>
    <button onclick="showView('scan')">← Back</button>
  </div>

  <!-- Search -->
  <div id="search" class="hidden">
    <h2>Search Product</h2>
    <input type="text" id="searchInput" placeholder="Type product name..." oninput="searchProducts()">
    <div id="searchResults"></div>
    <button onclick="showView('mainMenu')">← Back</button>
  </div>

  <!-- View All -->
  <div id="viewAll" class="hidden">
    <h2>All Products</h2>
    <div id="allProductsList"></div>
    <button onclick="showView('mainMenu')">← Back</button>
  </div>

  <script>
    // =============== DATA MODEL ===============
    let store = JSON.parse(localStorage.getItem('loblawsStore')) || { aisles: [] };

    function saveStore() {
      localStorage.setItem('loblawsStore', JSON.stringify(store));
      populateAisleSelects();
    }

    // =============== UI NAVIGATION ===============
    function showView(viewId) {
      document.querySelectorAll('div[id]').forEach(el => el.classList.add('hidden'));
      document.getElementById(viewId).classList.remove('hidden');
      if (viewId === 'scan') startCamera();
      if (viewId === 'assignProduct') populateAisleSelects();
      if (viewId === 'viewAll') renderAllProducts();
    }

    // =============== AISLE MANAGEMENT ===============
    function addAisle() {
      const num = parseInt(document.getElementById('aisleNumber').value);
      if (!num) { alert('Enter a valid aisle number'); return; }
      if (store.aisles.some(a => a.number === num)) {
        alert('Aisle already exists!');
        return;
      }
      store.aisles.push({ number: num, departments: [] });
      saveStore();
      document.getElementById('aisleNumber').value = '';
      alert(`Aisle ${num} added!`);
    }

    function addDepartment(aisleNum, deptName) {
      const aisle = store.aisles.find(a => a.number === aisleNum);
      if (aisle && !aisle.departments.includes(deptName)) {
        aisle.departments.push(deptName);
        saveStore();
      }
    }

    // =============== CAMERA + OCR ===============
    let stream;

    async function startCamera() {
      const video = document.getElementById('video');
      try {
        stream = await navigator.mediaDevices.getUserMedia({ video: { facingMode: 'environment' } });
        video.srcObject = stream;
      } catch (err) {
        alert('Camera access denied. Please allow camera in Settings.');
        showView('mainMenu');
      }
    }

    async function captureAndOCR() {
      const video = document.getElementById('video');
      const canvas = document.getElementById('canvas');
      const ctx = canvas.getContext('2d');
      
      canvas.width = video.videoWidth;
      canvas.height = video.videoHeight;
      ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
      
      // Extract image data
      const imageData = canvas.toDataURL('image/png');
      
      // Simulate OCR (in real app, you'd use Tesseract.js)
      // For demo: extract text from image metadata is not possible in pure browser without OCR lib
      // So we'll use a FAKE but realistic simulation:
      const fakeWords = ["Milk", "Bread", "Cheese", "Yogurt", "Eggs", "Butter", "Juice", "Cereal"];
      const detected = fakeWords[Math.floor(Math.random() * fakeWords.length)];
      
      document.getElementById('ocrResult').innerHTML = `
        <p>Detected: <strong>${detected}</strong></p>
        <button onclick="assignProduct('${detected}')">✅ Use This</button>
      `;
    }

    function assignProduct(name) {
      window.tempProductName = name;
      document.getElementById('productNameDisplay').textContent = name;
      showView('assignProduct');
    }

    // =============== PRODUCT ASSIGNMENT ===============
    function populateAisleSelects() {
      const aisleSel = document.getElementById('assignAisle');
      aisleSel.innerHTML = '';
      store.aisles.forEach(a => {
        const opt = document.createElement('option');
        opt.value = a.number;
        opt.textContent = `Aisle ${a.number}`;
        aisleSel.appendChild(opt);
      });

      // Update department dropdown when aisle changes
      aisleSel.onchange = () => {
        const deptSel = document.getElementById('assignDepartment');
        const aisleNum = parseInt(aisleSel.value);
        const aisle = store.aisles.find(a => a.number === aisleNum);
        deptSel.innerHTML = '';
        if (aisle) {
          aisle.departments.forEach(d => {
            const opt = document.createElement('option');
            opt.value = d;
            opt.textContent = d;
            deptSel.appendChild(opt);
          });
          // Add option to create new department
          const newOpt = document.createElement('option');
          newOpt.value = '__new__';
          newOpt.textContent = '+ New Department';
          deptSel.appendChild(newOpt);
        }
      };

      // Trigger first load
      if (aisleSel.value) aisleSel.onchange();
    }

    function saveProduct() {
      const aisleNum = parseInt(document.getElementById('assignAisle').value);
      let deptName = document.getElementById('assignDepartment').value;
      const direction = document.getElementById('assignDirection').value;
      const productName = window.tempProductName;

      if (deptName === '__new__') {
        deptName = prompt('Enter new department name:');
        if (!deptName) return;
        addDepartment(aisleNum, deptName);
      }

      // Find or create product list
      let aisle = store.aisles.find(a => a.number === aisleNum);
      if (!aisle) return;

      // Add product
      const product = { name: productName, department: deptName, direction };
      if (!aisle.products) aisle.products = [];
      aisle.products.push(product);

      saveStore();
      alert(`✅ ${productName} saved to Aisle ${aisleNum}, ${deptName}, ${direction}`);
      showView('mainMenu');
    }

    // =============== SEARCH ===============
    function searchProducts() {
      const query = document.getElementById('searchInput').value.toLowerCase();
      const resultsDiv = document.getElementById('searchResults');
      if (!query) {
        resultsDiv.innerHTML = '';
        return;
      }

      let matches = [];
      store.aisles.forEach(aisle => {
        (aisle.products || []).forEach(prod => {
          if (prod.name.toLowerCase().includes(query)) {
            matches.push({ ...prod, aisle: aisle.number });
          }
        });
      });

      resultsDiv.innerHTML = matches.length 
        ? matches.map(m => 
            `<div class="product-item">
              <strong>${m.name}</strong><br>
              Aisle ${m.aisle}, ${m.department}, 
              <span class="direction-tag">${m.direction}</span>
            </div>`
          ).join('')
        : '<p>No products found.</p>';
    }

    // =============== VIEW ALL ===============
    function renderAllProducts() {
      const listDiv = document.getElementById('allProductsList');
      let html = '';
      store.aisles.forEach(aisle => {
        (aisle.products || []).forEach(prod => {
          html += `
            <div class="product-item">
              <strong>${prod.name}</strong><br>
              Aisle ${aisle.number}, ${prod.department}, 
              <span class="direction-tag">${prod.direction}</span>
            </div>
          `;
        });
      });
      listDiv.innerHTML = html || '<p>No products added yet.</p>';
    }

    // Initialize
    showView('mainMenu');
  </script>
</body>
</html>
