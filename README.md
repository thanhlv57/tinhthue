<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ứng dụng tính thuế TNCN</title>

    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: #f8f9fa;
            margin: 0;
            padding: 20px;
            color: #333;
        }

        .container {
            max-width: 900px;
            margin: auto;
            background: #ffffff;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.05);
        }

        h2 {
            text-align: left;
            margin-bottom: 25px;
            color: #1a1a1a;
        }

        /* Chia cột phần nhập liệu */
        .input-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-bottom: 20px;
        }

        /* Style cho từng cụm (Card) */
        .input-group {
            background: #ffffff;
            border: 1px solid #eef0f2;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 2px 8px rgba(0,0,0,0.02);
        }

        .input-group h3 {
            margin-top: 0;
            font-size: 18px;
            color: #007bff;
            border-bottom: 2px solid #f0f2f5;
            padding-bottom: 10px;
            margin-bottom: 15px;
        }

        .field {
            margin-bottom: 15px;
        }

        label {
            display: block;
            font-weight: 600;
            margin-bottom: 8px;
            font-size: 14px;
        }

        input {
            width: 100%;
            padding: 12px;
            font-size: 16px;
            border: 1px solid #ced4da;
            border-radius: 6px;
            box-sizing: border-box; /* Quan trọng để input không tràn */
            transition: border-color 0.2s;
        }

        input:focus {
            outline: none;
            border-color: #007bff;
            box-shadow: 0 0 0 3px rgba(0,123,255,0.1);
        }

        /* Ô thuế đã nộp nằm ngang hàng dưới */
        .full-width {
            grid-column: span 2;
        }

        button {
            width: 100%;
            padding: 15px;
            font-size: 18px;
            font-weight: bold;
            background: #007bff;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            transition: background 0.3s;
        }

        button:hover {
            background: #0056b3;
        }

        .result {
            background: #fff9e6;
            border-left: 5px solid #ffcc00;
            padding: 20px;
            margin-top: 25px;
            border-radius: 8px;
            line-height: 1.8;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 25px;
            background: white;
        }

        th, td {
            border: 1px solid #dee2e6;
            padding: 12px;
            text-align: right;
        }

        th {
            background: #f8f9fa;
            font-weight: 600;
        }

        .left { text-align: left; }

        @media (max-width: 768px) {
            .input-grid { grid-template-columns: 1fr; }
            .full-width { grid-column: span 1; }
        }
    </style>
</head>
<body>

<div class="container">
    <h2>Ứng dụng tính thuế thu nhập cá nhân</h2>

    <div class="input-grid">
        <div class="input-group">
            <h3>Thu nhập</h3>
            <div class="field">
                <label>Tổng thu nhập năm (VNĐ)</label>
                <input type="text" id="income" value="500.000.000">
            </div>
            <div class="field">
                <label>Số người phụ thuộc</label>
                <input type="number" id="dependents" value="2">
            </div>
        </div>

        <div class="input-group">
            <h3>Giảm trừ</h3>
            <div class="field">
                <label>Giảm trừ bản thân / tháng (VNĐ)</label>
                <input type="text" id="selfDeduction" value="11.000.000">
            </div>
            <div class="field">
                <label>Giảm trừ người phụ thuộc / tháng / người</label>
                <input type="text" id="dependentDeduction" value="4.400.000">
            </div>
        </div>

        <div class="input-group full-width">
            <label>Số tiền thuế đã nộp (VNĐ)</label>
            <input type="text" id="paidTax" value="600.000">
        </div>
    </div>

    <button onclick="calculateTax()">Tính toán thuế</button>

    <div class="result" id="result"></div>

    <table>
        <thead>
            <tr>
                <th>Bậc</th>
                <th class="left">Thu nhập theo bậc</th>
                <th>Thuế suất</th>
                <th>Thuế</th>
            </tr>
        </thead>
        <tbody id="taxBody"></tbody>
    </table>
</div>

<script>
    const incomeInput = document.getElementById("income");
    const dependentsInput = document.getElementById("dependents");
    const selfDeductionInput = document.getElementById("selfDeduction");
    const dependentDeductionInput = document.getElementById("dependentDeduction");
    const paidTaxInput = document.getElementById("paidTax");
    const taxBody = document.getElementById("taxBody");
    const result = document.getElementById("result");

    const inputs = [incomeInput, selfDeductionInput, dependentDeductionInput, paidTaxInput];
    
    // Thêm sự kiện format cho các ô tiền tệ
    inputs.forEach(input => {
        input.addEventListener("input", () => {
            formatInput(input);
            calculateTax();
        });
    });
    
    dependentsInput.addEventListener("input", calculateTax);

    function parseMoney(value) {
        return Number(value.replace(/\./g, "")) || 0;
    }

    function formatMoney(number) {
        return number.toLocaleString("vi-VN");
    }

    function formatInput(input) {
        let val = parseMoney(input.value);
        input.value = formatMoney(val);
    }

    function calculateTax() {
        const income = parseMoney(incomeInput.value);
        const dependents = Number(dependentsInput.value);
        const selfDeductionYear = parseMoney(selfDeductionInput.value) * 12;
        const dependentDeductionYear = parseMoney(dependentDeductionInput.value) * 12 * dependents;
        const paidTax = parseMoney(paidTaxInput.value);

        const totalDeduction = selfDeductionYear + dependentDeductionYear;
        let taxableIncome = Math.max(0, income - totalDeduction);

        const brackets = [
            { to: 60000000, rate: 0.05 },
            { to: 120000000, rate: 0.10 },
            { to: 216000000, rate: 0.15 },
            { to: 384000000, rate: 0.20 },
            { to: 624000000, rate: 0.25 },
            { to: 960000000, rate: 0.30 },
            { to: Infinity, rate: 0.35 }
        ];

        let remaining = taxableIncome;
        let lower = 0;
        let totalTax = 0;
        taxBody.innerHTML = "";

        brackets.forEach((b, i) => {
            if (remaining <= 0) return;
            const upper = b.to - lower;
            const incomeInBracket = Math.min(remaining, upper);
            const tax = incomeInBracket * b.rate;
            totalTax += tax;

            taxBody.innerHTML += `
                <tr>
                    <td>${i + 1}</td>
                    <td class="left">${formatMoney(incomeInBracket)}</td>
                    <td>${b.rate * 100}%</td>
                    <td>${formatMoney(tax)}</td>
                </tr>`;
            remaining -= incomeInBracket;
            lower = b.to;
        });

        const taxPayable = Math.max(0, totalTax - paidTax);

        result.innerHTML = `
            <b>Tổng giảm trừ:</b> ${formatMoney(totalDeduction)} VNĐ<br>
            <b>Thu nhập chịu thuế:</b> ${formatMoney(taxableIncome)} VNĐ<br>
            <b>Tổng thuế phải nộp:</b> ${formatMoney(totalTax)} VNĐ<br>
            <b>Thuế đã nộp:</b> ${formatMoney(paidTax)} VNĐ<br>
            <b style="color:#d9534f; font-size:22px">Thuế còn phải nộp: ${formatMoney(taxPayable)} VNĐ</b>
        `;
    }

    // Chạy lần đầu
    calculateTax();
</script>

</body>
</html>
