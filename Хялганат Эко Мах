<!DOCTYPE html>
<html lang="mn">
<head>
  <meta charset="UTF-8">
  <title>Малчны бүртгэл</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script src="https://cdn.jsdelivr.net/npm/qrcode/build/qrcode.min.js"></script>
  <style>
    body {
      font-family: "Segoe UI", sans-serif;
      background-color: #f8fafc;
      padding: 20px;
      margin: 0;
      color: #1e293b;
    }

    .container {
      max-width: 720px;
      margin: auto;
      background: #ffffff;
      padding: 24px;
      border-radius: 16px;
      border: 1px solid #e2e8f0;
      box-shadow: 0 4px 10px rgba(0,0,0,0.05);
    }

    button:disabled {
      background-color: #cbd5e1 !important;
      color: #ffffff;
      cursor: not-allowed;
      border: none;
    }
    
    h2 {
      text-align: center;
      margin-bottom: 20px;
      color: #18715A;
    }

    input, button {
      width: 100%;
      padding: 12px;
      margin: 10px 0;
      border-radius: 8px;
      border: 1px solid #e2e8f0;
      font-size: 16px;
    }

    button {
      background-color: #18715A;
      color: white;
      font-weight: bold;
      cursor: pointer;
    }

    button:hover {
      background-color: #125643;
    }

    #resultArea {
      display: none;
      margin-top: 30px;
    }

    .print-container {
      display: block;
    }

    .receipt {
      border: 1px solid #e2e8f0;
      padding: 16px;
      margin-bottom: 40px;
      box-sizing: border-box;
      page-break-inside: avoid;
    }

    .title-area {
      text-align: center;
      margin-bottom: 10px;
    }

    .subtitle {
      font-size: 14px;
      color: gray;
    }

    .nowrap {
      white-space: nowrap;
    }

    #qrcode {
      margin-top: 10px;
      text-align: center;
    }

    @media print {
      input, button, h2 {
        display: none !important;
      }

      body {
        background: white;
        margin: 0;
      }

      .container {
        padding: 0;
        box-shadow: none;
        border: none;
        max-width: 100%;
      }

      .print-container {
        display: block;
      }

      .receipt {
        padding: 16px;
        box-sizing: border-box;
        border: none;
        page-break-inside: avoid;
      }

      @page {
        margin: 5mm;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <h2>Малчны бүртгэлийн систем</h2>
    <input type="date" id="date" placeholder="Огноо">
    <input type="text" id="herder" placeholder="Малчны нэр">
    <input type="text" id="account" placeholder="Дансны дугаар">
    <input type="text" id="phone" placeholder="Утасны дугаар">
    <input type="number" id="horseCount" placeholder="Адууны тоо">
    <button onclick="generateWeightFields()">Жингийн талбарууд</button>
    <div id="weightFields"></div>
    <input type="number" id="priceOver100" placeholder="1 кг-ийн үнэ (100 кг-аас их)">
    <div id="under100PriceField" style="display:none;">
      <input type="number" id="priceUnder100" placeholder="1 кг-ийн үнэ (100 кг-аас бага)">
    </div>
    <button id="calculateBtn" onclick="generateReceipt()" disabled>Бодох</button>
    <button onclick="window.print()">Хэвлэж гаргах</button>
    <button onclick="resetForm()">Арилгах</button>

    <div id="resultArea"></div>
  </div>

  <script>
    const SCRIPT_URL = "https://script.google.com/macros/s/AKfycbwt7FDZnpne91PWlembu7Nqd4J5FvyMaH98IiUc7bQe8LeFfxGNOrD5u5DHWUDJtq6doQ/exec";

    function generateWeightFields() {
      const count = parseInt(document.getElementById('horseCount').value);
      const container = document.getElementById('weightFields');
      container.innerHTML = '';
      for (let i = 0; i < count; i++) {
        const input = document.createElement('input');
        input.type = 'number';
        input.placeholder = `${i + 1}-р адууны жин (кг)`;
        input.className = 'horseWeight';
        input.onchange = checkUnder100Weight;
        input.oninput = validateForm;
        container.appendChild(input);
        
      }
      validateForm();
    }

    function checkUnder100Weight() {
      const weights = Array.from(document.getElementsByClassName('horseWeight'))
        .map(el => parseFloat(el.value)).filter(w => !isNaN(w));
      const hasUnder100 = weights.some(w => w < 101);
      document.getElementById('under100PriceField').style.display = hasUnder100 ? 'block' : 'none';
      validateForm();
    }

    function createReceipt(copyLabel) {
      const date = document.getElementById('date').value;
      const herder = document.getElementById('herder').value;
      const account = document.getElementById('account').value;
      const phone = document.getElementById('phone').value;
      const weights = Array.from(document.getElementsByClassName('horseWeight'))
        .map(el => parseFloat(el.value)).filter(w => !isNaN(w));
      const priceOver100 = parseFloat(document.getElementById('priceOver100').value);
      const priceUnder100Input = document.getElementById('priceUnder100');
      const priceUnder100 = priceUnder100Input ? parseFloat(priceUnder100Input.value) : 0;

      let totalOver = 0, totalUnder = 0, sumOver = 0, sumUnder = 0;
      weights.forEach(w => {
        if (w >= 101) {
          totalOver += w;
          sumOver += w * priceOver100;
        } else {
          totalUnder += w;
          sumUnder += w * priceUnder100;
        }
      });

      const totalWeight = totalOver + totalUnder;
      const totalSum = sumOver + sumUnder;
      const receiptId = `REG-${Date.now()}`;
      const qrData = `Малчин: ${herder}, Огноо: ${date}, Дугаар: ${receiptId}`;

      return {
        html: `
        <div class="receipt">
          <div class="title-area">
            <strong>Хялганат эко мах ХХК</strong><br>
            <span class="subtitle">Холбогдох утас: 99363105</span><br>
            <span><em>${copyLabel}</em></span>
          </div>
          <p><strong>Бүртгэлийн дугаар:</strong> ${receiptId}</p>
          <p><strong>Огноо:</strong> ${date}</p>
          <p><strong>Малчны нэр:</strong> ${herder}</p>
          <p><strong>Дансны дугаар:</strong> ${account}</p>
          <p><strong>Утасны дугаар:</strong> ${phone}</p>
          <p><strong>Адууны тоо:</strong> ${weights.length}</p>
          <p><strong>Нийт жин:</strong> ${totalWeight.toFixed(2)} кг</p>
          <p><strong>100-аас дээш:</strong> ${totalOver.toFixed(2)} кг * ${priceOver100.toLocaleString()} ₮ = <span class="nowrap">${sumOver.toLocaleString()} ₮</span></p>
          ${totalUnder > 0 ? `<p><strong>100-аас доош:</strong> ${totalUnder.toFixed(2)} кг * ${priceUnder100.toLocaleString()} ₮ = <span class="nowrap">${sumUnder.toLocaleString()} ₮</span></p>` : ''}
          <p><strong>Нийт дүн:</strong> <span class="nowrap">${totalSum.toLocaleString()} ₮</span></p>
          <br>
          <p><strong>Малчин гарын үсэг: ______________________</strong></p>
          <p><strong>Ажилтны нэр:</strong> М.Эхнжаргал</p>
          <div id="qrcode-${receiptId}"></div>
        </div>`,
        qrData,
        qrId: `qrcode-${receiptId}`,
        data: {
          receiptId,
          date,
          herder,
          account,
          phone,
          horseCount: weights.length,
          weights,
          priceOver100,
          priceUnder100: totalUnder > 0 ? priceUnder100 : '',
          totalWeight: totalWeight.toFixed(2),
          totalSum: totalSum.toFixed(2)
        }
      };
    }

    function generateReceipt() {
      const area = document.getElementById('resultArea');
      const receipt1 = createReceipt("Малчинд олгох");
      const receipt2 = createReceipt("Компанид үлдэх");

      area.innerHTML = `<div class="print-container">${receipt1.html}${receipt2.html}</div>`;
      area.style.display = 'block';

      QRCode.toCanvas(document.getElementById(receipt1.qrId), receipt1.qrData);
      QRCode.toCanvas(document.getElementById(receipt2.qrId), receipt2.qrData);

      fetch(SCRIPT_URL, {
        method: "POST",
        mode: "no-cors",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify(receipt1.data)
      })
      .then(() => console.log("Амжилттай хадгаллаа"))
      .catch(err => console.error("Хадгалахад алдаа:", err));
    }

    function resetForm() {
      document.querySelectorAll("input").forEach(input => input.value = '');
      document.getElementById('weightFields').innerHTML = '';
      document.getElementById('under100PriceField').style.display = 'none';
      document.getElementById('resultArea').style.display = 'none';
      document.getElementById('resultArea').innerHTML = '';
      document.getElementById('calculateBtn').disabled = true;
    }

    function validateForm() {
      const required = ['date', 'herder', 'account', 'phone', 'horseCount', 'priceOver100'];
      let isValid = required.every(id => {
        const el = document.getElementById(id);
        return el && el.value.trim() !== '';
      });

      if (!isValid) {
        document.getElementById('calculateBtn').disabled = true;
        return;
      }

      const weights = Array.from(document.getElementsByClassName('horseWeight'));
      if (weights.length === 0) {
        document.getElementById('calculateBtn').disabled = true;
        return;
      }

      for (let input of weights) {
        if (input.value.trim() === '' || isNaN(parseFloat(input.value))) {
          document.getElementById('calculateBtn').disabled = true;
          return;
        }
      }

      if (document.getElementById('under100PriceField').style.display === 'block') {
        const underPrice = document.getElementById('priceUnder100');
        if (!underPrice || underPrice.value.trim() === '') {
          document.getElementById('calculateBtn').disabled = true;
          return;
        }
      }

      document.getElementById('calculateBtn').disabled = false;
    }

    document.body.addEventListener('input', validateForm);
  </script>
</body>
</html>
