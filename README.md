<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Proforma Dinámica</title>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.9.2/html2pdf.bundle.min.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Poppins:wght@400;600&display=swap" rel="stylesheet">
  <style>
    body {
      font-family: 'Poppins', sans-serif;
      background: url('https://www.transparenttextures.com/patterns/dark-mosaic.png'), rgba(0, 0, 0, 0.9);
      color: white;
      text-align: center;
      margin: 0;
      padding: 0;
      animation: fadeIn 1.5s ease-in;
    }
    @keyframes fadeIn {
      from { opacity: 0; }
      to { opacity: 1; }
    }
    .container {
      width: 90%;
      margin: 30px auto;
      background: linear-gradient(135deg, #ff0000, #000000);
      padding: 30px;
      box-shadow: 0 0 25px rgba(255, 0, 0, 0.8);
      border-radius: 20px;
      animation: slideIn 1.2s ease-out;
    }
    @keyframes slideIn {
      from { transform: translateY(-50px); opacity: 0; }
      to { transform: translateY(0); opacity: 1; }
    }
    table {
      width: 100%;
      border-collapse: collapse;
      margin-bottom: 20px;
      background: rgba(255, 255, 255, 0.98);
      border-radius: 15px;
      overflow: hidden;
    }
    th, td {
      padding: 15px;
      text-align: center;
      border: 1px solid #000000;
    }
    th {
      background: #000000;
      color: white;
      text-transform: uppercase;
    }
    input {
      width: 80%;
      padding: 10px;
      border: 1px solid #000000;
      border-radius: 8px;
      text-align: center;
      transition: 0.3s;
    }
    input:focus {
      box-shadow: 0 0 10px #000000;
    }
    button {
      padding: 12px 20px;
      margin: 10px;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      transition: background 0.3s ease;
    }
    #addRowButton, #exportToPDF, #exportToWord { background: #000000; color: white; }
    #addRowButton:hover, #exportToPDF:hover, #exportToWord:hover { background: #333333; }
    .deleteRow { background: #dc3545; color: white; display: inline-block; }
    .deleteRow:hover { background: #c82333; }
    tfoot tr:nth-child(1), tfoot tr:nth-child(2) {
      font-weight: bold;
      color: blue;
    }
    tfoot tr:last-child {
      background: yellow;
      font-weight: bold;
      color: black;
    }
    @media print {
      button, .deleteRow { display: none; }
    }
  </style>
</head>
<body>
<div class="container">
  <h1>💼 PROFORMA SFC</h1>
  <table id="proformaTable">
    <thead>
      <tr>
        <th>N°</th>
        <th>Descripción</th>
        <th>Cantidad</th>
        <th>Precio Unitario</th>
        <th>Precio Total</th>
        <th>Eliminar</th>
      </tr>
    </thead>
    <tbody></tbody>
    <tfoot>
      <tr><td colspan="5">SUBTOTAL:</td><td><span id="subtotal">0.00</span></td></tr>
      <tr><td colspan="5">IGV (18%):</td><td><span id="igv">0.00</span></td></tr>
      <tr><td colspan="5">TOTAL:</td><td><span id="total">0.00</span></td></tr>
    </tfoot>
  </table>
  <button id="addRowButton">➕ Agregar Fila</button>
  <button id="exportToPDF">📄 Exportar a PDF</button>
  <button id="exportToWord">📝 Exportar a Word</button>
</div>
<script>
document.addEventListener("DOMContentLoaded", () => {
  const tableBody = document.querySelector("#proformaTable tbody");

  function updateNumbers() {
    tableBody.querySelectorAll("tr").forEach((row, index) => {
      row.querySelector("td:first-child").textContent = (index + 1).toString().padStart(2, "0");
    });
  }

  function calculate() {
    let subtotal = 0;
    tableBody.querySelectorAll("tr").forEach(row => {
      const cantidad = parseFloat(row.querySelector(".cantidad")?.value) || 0;
      const precio = parseFloat(row.querySelector(".unitario")?.value) || 0;
      let total = cantidad * precio;
      row.querySelector(".total").textContent = total.toFixed(2);
      subtotal += total;
    });
    document.getElementById("subtotal").textContent = subtotal.toFixed(2);
    document.getElementById("igv").textContent = (subtotal * 0.18).toFixed(2);
    document.getElementById("total").textContent = (subtotal * 1.18).toFixed(2);
  }

  function addRow() {
    const row = document.createElement("tr");
    row.innerHTML = `
      <td></td>
      <td><input type="text" placeholder="Descripción"></td>
      <td><input type="number" class="cantidad" placeholder="0" min="0"></td>
      <td><input type="number" class="unitario" placeholder="0.00" min="0" step="0.01"></td>
      <td><span class="total">0.00</span></td>
      <td><button class="deleteRow">❌</button></td>
    `;
    tableBody.appendChild(row);
    updateNumbers();
    row.querySelectorAll("input").forEach(input => input.addEventListener("input", calculate));
    row.querySelector(".deleteRow").addEventListener("click", () => {
      row.remove();
      updateNumbers();
      calculate();
    });
  }

  document.getElementById("addRowButton").addEventListener("click", addRow);
  addRow();

  document.getElementById("exportToPDF").addEventListener("click", () => {
    html2pdf().from(document.querySelector(".container")).save("Proforma.pdf");
  });

  document.getElementById("exportToWord").addEventListener("click", () => {
    const content = document.querySelector(".container").outerHTML;
    const blob = new Blob([content], { type: "application/msword" });
    saveAs(blob, "Proforma.doc");
  });
});
</script>
</body>
</html>
