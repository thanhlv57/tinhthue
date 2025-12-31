<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Tính thuế TNCN 2025-2026</title>

    <style>
        * { box-sizing: border-box; }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: #f0f2f5;
            margin: 0;
            padding: 10px; /* Giảm padding ngoài trên mobile */
            color: #333;
            line-height: 1.4;
        }

        .container {
            max-width: 900px;
            margin: auto;
            background: #ffffff;
            padding: 15px; /* Giảm padding container */
            border-radius: 12px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.08);
            width: 100%; /* Đảm bảo luôn full width */
        }

        .header-area {
            display: flex;
            flex-wrap: wrap; /* Cho phép rớt dòng trên mobile */
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            border-bottom: 2px solid #eee;
            padding-bottom: 10px;
            gap: 10px;
        }

        h2 { 
            margin: 0; 
            color: #1a1a1a; 
            font-size: 1.2rem; /* Cỡ chữ linh hoạt */
        }

        .year-selector {
            font-size: 14px;
            padding: 6px 10px;
            border-radius: 6px;
            border: 2px solid #007bff;
            font-weight: bold;
            color: #007bff;
            background: white;
        }

        .input-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(280px, 1fr)); /* Tự nhảy dòng khi màn hình hẹp */
            gap: 15px;
            margin-bottom: 15px;
        }

        .input-group {
            background: #fff;
            border: 1px solid #eef0f2;
            padding: 15px;
            border-radius: 10px;
        }

        .input-group h3 {
            margin-top: 0;
            font-size: 16px;
            color: #007bff;
            margin-bottom: 12px;
        }

        .field { margin-bottom: 12px; }

        label {
            display: block;
            font-weight: 600;
            margin-bottom: 5px;
            font-size: 13px;
        }

        input {
            width: 100%;
            padding: 10px;
            font-size: 15px;
            border: 1px solid #ced4da;
            border-radius: 6px;
            outline: none;
        }

        .full-width { grid-column: 1 / -1; } /* Chiếm hết hàng bất kể bao nhiêu cột */

        button {
            width: 100%;
            padding: 12px;
            font-size: 16px;
            font-weight: bold;
            background: #28a745;
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            margin-top: 5px;
        }

        .result {
            background: #fff9e6;
            border-left: 5px solid #ffcc00;
            padding: 15px;
            margin-top: 20px;
            border-radius: 8px;
            font-size: 14px;
        }

        /* Tối ưu hiển thị bảng trên Mobile */
        .table-responsive {
            width: 100%;
            overflow-x: auto; /* Cuộn ngang riêng cho bảng nếu màn hình quá nhỏ */
            margin-top: 20px;
            -webkit-overflow-scrolling: touch;
        }

        table {
            width: 100%;
            border-collapse: collapse;
            min-width: 400px; /* Đảm bảo bảng không bị ép quá nhỏ gây khó đọc */
        }

        th, td {
            border: 1px solid #dee2e6;
            padding: 10px 8px;
            text-align: right;
            font-size: 13px;
        }

        th { background: #f8f9fa; }
        .left { text-align: left; }

        @media (max-width: 480px) {
            body { padding: 5px; }
            .container { padding: 10px; }
            h2 { font-size: 1.1rem; }
            .input-grid { grid-template-columns: 1fr; }
        }
    </style>
</head>
<body>

<div class="container">
    <div class="header-area">
        <h2>Tính thuế TNCN</h2>
        <div>
            <select id="taxYear" class="year-selector" onchange="updateYearDefaults(); saveToStorage();">
                <option value="2025">Năm 2025</option>
                <option value="2026">Năm 2026</option>
            </select>
        </div>
    </div>

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
                <label>Bản thân / tháng (VNĐ)</label>
                <input type="text" id="selfDeduction" value="11.000.000">
            </div>
            <div class="field">
                <label>Người phụ thuộc / tháng</label>
                <input type="text" id="dependentDeduction" value="4.400.000">
            </div>
            <div class="field">
                <label>Giảm trừ khác (VNĐ - Năm)</label>
                <input type="text" id="otherDeduction" value="0">
            </div>
        </div>

        <div class="input-group full-width">
            <label>Số tiền thuế đã nộp (VNĐ)</label>
            <input type="text" id="paidTax" value="600.000">
        </div>
    </div>

    <button onclick="calculateTax()">Tính toán thuế</button>

    <div class="result" id="result"></div>

    <div class="table-responsive">
        <table>
            <thead>
                <tr>
                    <th>Bậc</th>
                    <th class="left">Thu nhập bậc (Năm)</th>
                    <th>Thuế %</th>
                    <th>Thuế</th>
                </tr>
            </thead>
            <tbody id="taxBody"></tbody>
        </table>
    </div>
</div>

<script>
    // Logic Javascript giữ nguyên như các phiên bản trước
    const taxYearSelect = document.getElementById("taxYear");
    const incomeInput = document.getElementById("income");
    const dependentsInput = document.getElementById("dependents");
    const selfDeductionInput = document.getElementById("selfDeduction");
    const dependentDeductionInput = document.getElementById("dependentDeduction");
    const otherDeductionInput = document.getElementById("otherDeduction");
    const paidTaxInput = document.getElementById("paidTax");
    const taxBody = document.getElementById("taxBody");
    const result = document.getElementById("result");

    const inputs = [incomeInput, selfDeductionInput, dependentDeductionInput, otherDeductionInput, paidTaxInput];
    
    window.onload = () => {
        loadFromStorage();
        calculateTax();
    };

    inputs.forEach(input => {
        input.addEventListener("input", () => {
            if(input.type === "text") formatInput(input);
            calculateTax();
            saveToStorage();
        });
    });
    
    dependentsInput.addEventListener("input", () => {
        calculateTax();
        saveToStorage();
    });

    function updateYearDefaults() {
        const year = taxYearSelect.value;
        if (year === "2026") {
            selfDeductionInput.value = "15.500.000";
            dependentDeductionInput.value = "6.200.000";
        } else {
            selfDeductionInput.value = "11.000.000";
            dependentDeductionInput.value = "4.400.000";
        }
        calculateTax();
    }

    function parseMoney(value) {
        return Number(value.replace(/\./g, "")) || 0;
    }

    function formatMoney(number) {
        return number.toLocaleString("vi-VN");
    }

    function formatInput(input) {
        input.value = formatMoney(parseMoney(input.value));
    }

    function saveToStorage() {
        const data = {
            year: taxYearSelect.value,
            income: incomeInput.value,
            dependents: dependentsInput.value,
            selfDeduction: selfDeductionInput.value,
            dependentDeduction: dependentDeductionInput.value,
            otherDeduction: otherDeductionInput.value,
            paidTax: paidTaxInput.value
        };
        localStorage.setItem("tax_calculator_data", JSON.stringify(data));
    }

    function loadFromStorage() {
        const savedData = localStorage.getItem("tax_calculator_data");
        if (savedData) {
            const data = JSON.parse(savedData);
            taxYearSelect.value = data.year;
            incomeInput.value = data.income;
            dependentsInput.value = data.dependents;
            selfDeductionInput.value = data.selfDeduction;
            dependentDeductionInput.value = data.dependentDeduction;
            otherDeductionInput.value = data.otherDeduction;
            paidTaxInput.value = data.paidTax;
        }
    }

    function calculateTax() {
        const year = taxYearSelect.value;
        const income = parseMoney(incomeInput.value);
        const dependents = Number(dependentsInput.value);
        const selfYear = parseMoney(selfDeductionInput.value) * 12;
        const depYear = parseMoney(dependentDeductionInput.value) * 12 * dependents;
        const otherYear = parseMoney(otherDeductionInput.value);
        const paidTax = parseMoney(paidTaxInput.value);

        const totalDeduction = selfYear + depYear + otherYear;
        let taxableIncome = Math.max(0, income - totalDeduction);

        let brackets = [];
        if (year === "2026") {
            brackets = [{ to: 120000000, rate: 0.05 }, { to: 360000000, rate: 0.10 }, { to: 720000000, rate: 0.20 }, { to: 1200000000, rate: 0.30 }, { to: Infinity, rate: 0.35 }];
        } else {
            brackets = [{ to: 60000000, rate: 0.05 }, { to: 120000000, rate: 0.10 }, { to: 216000000, rate: 0.15 }, { to: 384000000, rate: 0.20 }, { to: 624000000, rate: 0.25 }, { to: 960000000, rate: 0.30 }, { to: Infinity, rate: 0.35 }];
        }

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
            taxBody.innerHTML += `<tr><td>${i + 1}</td><td class="left">${formatMoney(incomeInBracket)}</td><td>${b.rate * 100}%</td><td>${formatMoney(tax)}</td></tr>`;
            remaining -= incomeInBracket;
            lower = b.to;
        });

        let resultLabel = "Thuế còn phải nộp:";
        let resultValue = totalTax - paidTax;
        let resultColor = "#d9534f";

        if (resultValue < 0) {
            resultLabel = "Tổng thuế nộp thừa:";
            resultValue = Math.abs(resultValue);
            resultColor = "#007bff";
        }

        result.innerHTML = `
            <div style="font-size:12px; color:#666">Biểu thuế ${year}</div>
            <b>Giảm trừ:</b> ${formatMoney(totalDeduction)} VNĐ | <b>Chịu thuế:</b> ${formatMoney(taxableIncome)} VNĐ<br>
            <b style="color:${resultColor}; font-size:1.2rem">${resultLabel} ${formatMoney(resultValue)} VNĐ</b>
        `;
    }
</script>

</body>
</html>
