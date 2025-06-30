<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>FoodBunny Delivery Sheet</title>
  <style>
    body {
      font-family: 'Poppins', sans-serif;
      padding: 0.5rem;
      margin: 0;
      background-color: #f4f6f8;
    }
    h2 {
      text-align: center;
      margin-bottom: 0.5rem;
      font-size: 1.25rem;
      color: #333;
    }
    .table-container {
      overflow-x: auto;
      background: white;
      border-radius: 6px;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
    }
    table {
      width: 100%;
      border-collapse: collapse;
      font-size: 0.85rem;
      min-width: 600px;
    }
    th, td {
      border: 1px solid #e0e0e0;
      padding: 0.5rem;
      text-align: left;
    }
    th {
      background-color: #fce8b2;
      color: #333;
    }
    td[contenteditable="true"] {
      background-color: #fefefe;
    }
    input.text-input, input.number-input {
      width: 100%;
      padding: 0.25rem;
      font-size: 0.85rem;
      border: none;
      outline: none;
      background: transparent;
    }
    .currency-wrapper {
      display: flex;
      align-items: center;
      gap: 4px;
    }
    .currency-wrapper span {
      font-weight: bold;
    }
    .actions {
      margin-top: 1rem;
      text-align: center;
    }
    button, .whatsapp-btn {
      padding: 0.6rem 1rem;
      margin: 0.2rem;
      border: none;
      border-radius: 5px;
      background-color: #000;
      color: white;
      font-size: 0.9rem;
      cursor: pointer;
      transition: background 0.3s ease;
      text-decoration: none;
      display: inline-block;
    }
    button:hover {
      background-color: #333;
    }
    .whatsapp-btn {
      background-color: #25D366;
    }
    .whatsapp-btn:hover {
      background-color: #1ebe5d;
    }
    .delete-btn {
      background-color: #dc3545;
    }
    .delete-btn:hover {
      background-color: #a71d2a;
    }
    .no-export {
      display: table-cell;
    }
    @media (max-width: 600px) {
      table, th, td {
        font-size: 0.75rem;
      }
      button, .whatsapp-btn {
        width: 100%;
        margin-top: 0.4rem;
      }
    }
  </style>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">
</head>
<body>

  <h2>📦 FoodBunny Delivery Sheet</h2>

  <div class="table-container">
    <table id="deliveryTable">
      <thead>
        <tr>
          <th>Customer Name</th>
          <th>Address Location</th>
          <th>Order Details</th>
          <th>Customer Number</th>
          <th>Delivery Charges</th>
          <th class="no-export">Delete</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td><input class="text-input" type="text" value="Ali Khan" /></td>
          <td><input class="text-input" type="text" value="Chakwal City" /></td>
          <td><input class="text-input" type="text" value="Grocery Items" /></td>
          <td><input class="number-input" type="number" value="03001234567" /></td>
          <td><div class="currency-wrapper"><span>Rs.</span><input class="number-input" type="number" value="100" oninput="this.value = this.value.replace(/[^0-9]/g, '')" /></div></td>
          <td class="no-export"><button class="delete-btn" onclick="deleteRow(this)">❌</button></td>
        </tr>
      </tbody>
    </table>
  </div>

  <div class="actions">
    <button onclick="addRow()">➕ Add Row</button>
    <button onclick="downloadPDF()">📄 Download PDF</button>
    <button onclick="downloadImage()">🖼️ Download Image</button>
    <a class="whatsapp-btn" id="whatsappShare" target="_blank">📤 Share on WhatsApp</a>
  </div>

  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

  <script>
    function addRow() {
      const table = document.getElementById("deliveryTable").getElementsByTagName('tbody')[0];
      const row = table.insertRow();

      row.insertCell(0).outerHTML = '<td><input class="text-input" type="text" /></td>';
      row.insertCell(1).outerHTML = '<td><input class="text-input" type="text" /></td>';
      row.insertCell(2).outerHTML = '<td><input class="text-input" type="text" /></td>';
      row.insertCell(3).outerHTML = '<td><input class="number-input" type="number" /></td>';
      row.insertCell(4).outerHTML = '<td><div class="currency-wrapper"><span>Rs.</span><input class="number-input" type="number" value="100" oninput="this.value = this.value.replace(/[^0-9]/g, '')" /></div></td>';
      row.insertCell(5).outerHTML = '<td class="no-export"><button class="delete-btn" onclick="deleteRow(this)">❌</button></td>';
    }

    function deleteRow(btn) {
      const row = btn.parentNode.parentNode;
      row.remove();
    }

    async function downloadPDF() {
      const { jsPDF } = window.jspdf;
      const doc = new jsPDF();
      const table = document.getElementById("deliveryTable").cloneNode(true);

      table.querySelectorAll(".no-export").forEach(el => el.remove());
      table.querySelectorAll("input").forEach(input => {
        const td = input.parentNode;
        const isCurrency = td.querySelector('span');
        td.textContent = isCurrency ? `Rs.${input.value}` : input.value;
      });

      await html2canvas(table).then(canvas => {
        const imgData = canvas.toDataURL('image/png');
        const imgProps = doc.getImageProperties(imgData);
        const pdfWidth = doc.internal.pageSize.getWidth();
        const pdfHeight = (imgProps.height * pdfWidth) / imgProps.width;
        doc.addImage(imgData, 'PNG', 0, 0, pdfWidth, pdfHeight);
        doc.save("FoodBunny-Delivery-Sheet.pdf");
      });
    }

    function downloadImage() {
      const table = document.getElementById("deliveryTable").cloneNode(true);
      table.querySelectorAll(".no-export").forEach(el => el.remove());
      table.querySelectorAll("input").forEach(input => {
        const td = input.parentNode;
        const isCurrency = td.querySelector('span');
        td.textContent = isCurrency ? `Rs.${input.value}` : input.value;
      });

      const container = document.createElement("div");
      container.appendChild(table);
      document.body.appendChild(container);

      html2canvas(container).then(canvas => {
        const link = document.createElement("a");
        link.download = "FoodBunny-Delivery-Sheet.png";
        link.href = canvas.toDataURL();
        link.click();
        document.body.removeChild(container);
      });
    }

    document.getElementById("whatsappShare").addEventListener("click", function (e) {
      e.preventDefault();
      const phone = prompt("Enter rider's WhatsApp number (without +92):");
      if (!phone) return;
      const message = encodeURIComponent("📦 FoodBunny Delivery Sheet ready. Please check your assigned deliveries.");
      const url = `https://wa.me/92${phone}?text=${message}`;
      window.open(url, '_blank');
    });
  </script>

</body>
</html>
