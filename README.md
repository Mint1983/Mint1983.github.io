# Mint1983.github.io
```html
<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบบันทึกการขาย POS & Dashboard</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Kanit:wght@300;400;500;600;700&display=swap');
        body {
            font-family: 'Kanit', sans-serif;
        }
    </style>
</head>
<body class="bg-slate-100 min-h-screen pb-20">

    <!-- Header Navigation with Tabs -->
    <header class="bg-slate-800 text-white sticky top-0 z-50 shadow-md">
        <div class="max-w-5xl mx-auto px-4 py-3 flex justify-between items-center">
            <div class="flex items-center space-x-2">
                <i data-lucide="store" class="text-amber-400 w-7 h-7"></i>
                <h1 class="text-xl font-bold tracking-wide hidden sm:inline">POS & Analytics</h1>
                <h1 class="text-xl font-bold tracking-wide sm:hidden">POS</h1>
            </div>

            <!-- Tab Switcher Navigation -->
            <div class="flex bg-slate-700/80 p-1 rounded-xl border border-slate-600">
                <button onclick="switchTab('pos')" id="tabBtnPos" class="flex items-center gap-1.5 px-3 py-1.5 rounded-lg text-xs sm:text-sm font-semibold bg-indigo-600 text-white shadow-sm transition">
                    <i data-lucide="shopping-cart" class="w-4 h-4"></i>
                    ขายสินค้า
                </button>
                <button onclick="switchTab('dashboard')" id="tabBtnDash" class="flex items-center gap-1.5 px-3 py-1.5 rounded-lg text-xs sm:text-sm font-semibold text-slate-300 hover:text-white transition">
                    <i data-lucide="bar-chart-3" class="w-4 h-4"></i>
                    สถิติการขาย
                </button>
            </div>

            <!-- Header Info -->
            <div class="hidden md:flex items-center space-x-2 text-xs">
                <span class="bg-slate-700 px-2.5 py-1 rounded-full flex items-center gap-1.5 border border-slate-600">
                    <i data-lucide="user" class="w-3.5 h-3.5 text-emerald-400"></i>
                    <span id="headerUserDisplay">ไม่ระบุชื่อ</span>
                </span>
                <span class="bg-slate-700 px-2.5 py-1 rounded-full flex items-center gap-1.5 border border-slate-600">
                    <i data-lucide="map-pin" class="w-3.5 h-3.5 text-rose-400"></i>
                    <span id="headerBranchDisplay">Central Airport</span>
                </span>
            </div>
        </div>
    </header>

    <!-- TAB 1: POS SYSTEM -->
    <main id="posTabContent" class="max-w-5xl mx-auto p-4 md:p-6 grid grid-cols-1 md:grid-cols-12 gap-6">

        <!-- Left Column: Setup & Menu Options -->
        <div class="md:col-span-7 space-y-6">

            <!-- Card 1: User & Branch Setup -->
            <div class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5">
                <h2 class="text-lg font-semibold text-slate-800 mb-4 flex items-center gap-2 border-b pb-2">
                    <i data-lucide="sliders" class="w-5 h-5 text-indigo-600"></i>
                    ข้อมูลการขาย (สาขา / ผู้ลงบันทึก)
                </h2>
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                    <div>
                        <label class="block text-sm font-medium text-slate-700 mb-1">สาขา</label>
                        <select id="branchSelect" onchange="updateInfoDisplay()" class="w-full bg-slate-50 border border-slate-300 rounded-xl px-3 py-2.5 text-slate-800 font-medium focus:ring-2 focus:ring-indigo-500 focus:outline-none">
                            <option value="Central Airport">1. Central Airport</option>
                            <option value="สาขาตลาดมาริน">2. สาขาตลาดมาริน</option>
                            <option value="สาขา DIY บ้านท่อ">3. สาขา DIY บ้านท่อ</option>
                            <option value="Event">4. Event</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-sm font-medium text-slate-700 mb-1">ชื่อผู้ลงบันทึก (พิมพ์ระบุชื่อ)</label>
                        <input type="text" id="userInput" value="" oninput="updateInfoDisplay()" placeholder="เช่น สมชาย, นิ่ม..." class="w-full bg-slate-50 border border-slate-300 rounded-xl px-3 py-2.5 text-slate-800 font-medium focus:ring-2 focus:ring-indigo-500 focus:outline-none">
                    </div>
                </div>
            </div>

            <!-- Card 2: Product Menu Cards Grid -->
            <div class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5">
                <h2 class="text-lg font-semibold text-slate-800 mb-4 flex items-center gap-2 border-b pb-2">
                    <i data-lucide="utensils" class="w-5 h-5 text-amber-600"></i>
                    เลือกรายการสินค้า
                </h2>
                <div class="grid grid-cols-2 sm:grid-cols-3 gap-3" id="productGrid">
                    <!-- Dynamic Product Cards Injected Here -->
                </div>
            </div>

            <!-- Card 3: Settings Panel -->
            <div class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5">
                <button onclick="toggleConfig()" class="w-full flex justify-between items-center text-left font-medium text-slate-600 hover:text-slate-900 transition">
                    <span class="flex items-center gap-2 text-sm">
                        <i data-lucide="settings" class="w-4 h-4"></i>
                        ตั้งค่าการเชื่อมต่อ (Google Apps Script / Webhook)
                    </span>
                    <i data-lucide="chevron-down" id="configChevron" class="w-4 h-4 transition-transform"></i>
                </button>
                <div id="configPanel" class="hidden mt-4 pt-4 border-t border-slate-100 space-y-3">
                    <div>
                        <label class="block text-xs font-semibold text-slate-600 mb-1">Google Apps Script URL / Webhook</label>
                        <input type="url" id="webhookUrl" placeholder="https://script.google.com/macros/s/.../exec" class="w-full text-xs bg-slate-50 border border-slate-300 rounded-lg p-2.5 font-mono focus:ring-2 focus:ring-indigo-500 focus:outline-none">
                    </div>
                </div>
            </div>

        </div>

        <!-- Right Column: Cart & Summary -->
        <div class="md:col-span-5">
            <div class="bg-white rounded-2xl shadow-sm border border-slate-200 p-5 sticky top-20">
                <div class="flex justify-between items-center border-b pb-3 mb-3">
                    <h2 class="text-lg font-semibold text-slate-800 flex items-center gap-2">
                        <i data-lucide="shopping-cart" class="w-5 h-5 text-indigo-600"></i>
                        รายการที่เลือก
                    </h2>
                    <button onclick="clearCart()" class="text-xs text-rose-600 hover:text-rose-800 hover:bg-rose-50 px-2 py-1 rounded-lg transition">
                        ล้างรายการ
                    </button>
                </div>

                <!-- Cart Items List -->
                <div id="cartItems" class="space-y-3 max-h-[240px] overflow-y-auto pr-1 mb-4">
                    <div id="emptyCartMessage" class="text-center py-8 text-slate-400">
                        <i data-lucide="shopping-bag" class="w-12 h-12 mx-auto stroke-1 mb-2"></i>
                        <p class="text-sm">ยังไม่มีรายการสินค้า</p>
                        <p class="text-xs text-slate-400 mt-1">กดเลือกสินค้าจากเมนูด้านข้าง</p>
                    </div>
                </div>

                <!-- Payment Method Selection -->
                <div class="border-t border-slate-100 pt-3 mb-4">
                    <label class="block text-xs font-bold text-slate-700 uppercase tracking-wider mb-2">
                        วิธีการชำระเงิน
                    </label>
                    <div class="grid grid-cols-3 gap-2">
                        <button type="button" onclick="selectPayment('เงินสด')" id="pay-cash" class="payment-btn flex flex-col items-center justify-center p-2 rounded-xl border border-indigo-600 bg-indigo-50 text-indigo-700 font-semibold text-xs transition">
                            <i data-lucide="banknote" class="w-5 h-5 mb-1"></i>
                            เงินสด
                        </button>
                        <button type="button" onclick="selectPayment('เงินโอน')" id="pay-transfer" class="payment-btn flex flex-col items-center justify-center p-2 rounded-xl border border-slate-200 bg-white text-slate-600 font-semibold text-xs hover:border-slate-300 transition">
                            <i data-lucide="qr-code" class="w-5 h-5 mb-1"></i>
                            เงินโอน
                        </button>
                        <button type="button" onclick="selectPayment('โครงการรัฐ')" id="pay-gov" class="payment-btn flex flex-col items-center justify-center p-2 rounded-xl border border-slate-200 bg-white text-slate-600 font-semibold text-xs hover:border-slate-300 transition">
                            <i data-lucide="landmark" class="w-5 h-5 mb-1"></i>
                            โครงการรัฐ
                        </button>
                    </div>
                </div>

                <!-- Total Summary -->
                <div class="bg-slate-50 p-4 rounded-xl border border-slate-200 space-y-2 mb-5">
                    <div class="flex justify-between text-sm text-slate-600">
                        <span>จำนวนรายการ:</span>
                        <span id="totalItemsCount" class="font-medium text-slate-800">0 ชิ้น</span>
                    </div>
                    <div class="flex justify-between text-sm text-slate-600">
                        <span>ชำระโดย:</span>
                        <span id="selectedPaymentText" class="font-semibold text-indigo-600">เงินสด</span>
                    </div>
                    <div class="flex justify-between items-baseline pt-2 border-t border-slate-200">
                        <span class="text-base font-bold text-slate-800">ราคารวมทั้งหมด:</span>
                        <span class="text-2xl font-black text-indigo-600"><span id="totalPrice">0</span> <span class="text-sm font-normal text-slate-600">บาท</span></span>
                    </div>
                </div>

                <!-- Action Buttons -->
                <div class="space-y-2">
                    <button onclick="copyForLine()" id="copyLineBtn" class="w-full bg-emerald-500 hover:bg-emerald-600 text-white font-bold py-3 px-4 rounded-xl shadow-md shadow-emerald-500/20 active:scale-[0.98] transition flex justify-center items-center gap-2 text-base">
                        <i data-lucide="copy" class="w-5 h-5"></i>
                        คัดลอกข้อความส่ง LINE (บิลปัจจุบัน)
                    </button>

                    <button onclick="submitOrder()" id="submitBtn" class="w-full bg-slate-800 hover:bg-slate-900 text-white font-medium py-3 px-4 rounded-xl active:scale-[0.98] transition flex justify-center items-center gap-2 text-sm">
                        <i data-lucide="check-circle-2" class="w-4 h-4"></i>
                        บันทึกการขายลงระบบ
                    </button>
                </div>
            </div>
        </div>

    </main>

    <!-- TAB 2: DASHBOARD & STATS -->
    <main id="dashboardTabContent" class="max-w-5xl mx-auto p-4 md:p-6 space-y-6 hidden">
        
        <!-- Dashboard Header & Controls -->
        <div class="flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4 bg-white p-5 rounded-2xl border border-slate-200 shadow-sm">
            <div>
                <h2 class="text-xl font-bold text-slate-800 flex items-center gap-2">
                    <i data-lucide="pie-chart" class="w-6 h-6 text-indigo-600"></i>
                    แดชบอร์ดสถิติการขาย
                </h2>
                <p class="text-xs text-slate-500 mt-1">สรุปยอดขายจากรายการบันทึกในเครื่องและระบบ</p>
            </div>
            <div class="flex flex-wrap items-center gap-2">
                <button onclick="copyDashboardReportForLine()" class="bg-emerald-500 hover:bg-emerald-600 text-white px-3.5 py-2 rounded-xl text-xs font-bold shadow-sm shadow-emerald-500/20 transition flex items-center gap-1.5 active:scale-95">
                    <i data-lucide="share-2" class="w-4 h-4"></i>
                    คัดลอกรายงานสรุปส่ง LINE
                </button>
                <button onclick="refreshDashboard()" class="bg-indigo-50 text-indigo-600 hover:bg-indigo-100 px-3 py-2 rounded-xl text-xs font-semibold transition flex items-center gap-1.5">
                    <i data-lucide="refresh-cw" class="w-4 h-4"></i>
                    รีเฟรชข้อมูล
                </button>
                <button onclick="clearSalesHistory()" class="bg-rose-50 text-rose-600 hover:bg-rose-100 px-3 py-2 rounded-xl text-xs font-semibold transition flex items-center gap-1.5">
                    <i data-lucide="trash-2" class="w-4 h-4"></i>
                    ล้างประวัติ
                </button>
            </div>
        </div>

        <!-- Key Metrics Cards Grid -->
        <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
            <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
                <div class="flex justify-between items-center text-slate-500 mb-2">
                    <span class="text-xs font-medium">ยอดขายรวม</span>
                    <i data-lucide="dollar-sign" class="w-4 h-4 text-emerald-500"></i>
                </div>
                <div class="text-2xl font-black text-slate-800" id="statTotalRevenue">0 ฿</div>
                <div class="text-[11px] text-slate-400 mt-1">รายได้ทั้งหมด</div>
            </div>

            <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
                <div class="flex justify-between items-center text-slate-500 mb-2">
                    <span class="text-xs font-medium">จำนวนออเดอร์</span>
                    <i data-lucide="shopping-bag" class="w-4 h-4 text-indigo-500"></i>
                </div>
                <div class="text-2xl font-black text-slate-800" id="statTotalOrders">0</div>
                <div class="text-[11px] text-slate-400 mt-1">รายการขายสำเร็จ</div>
            </div>

            <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
                <div class="flex justify-between items-center text-slate-500 mb-2">
                    <span class="text-xs font-medium">สินค้าขายดีสุด</span>
                    <i data-lucide="trophy" class="w-4 h-4 text-amber-500"></i>
                </div>
                <div class="text-lg font-bold text-slate-800 truncate" id="statTopProduct">-</div>
                <div class="text-[11px] text-slate-400 mt-1" id="statTopProductQty">0 ชิ้น</div>
            </div>

            <div class="bg-white p-4 rounded-2xl border border-slate-200 shadow-sm">
                <div class="flex justify-between items-center text-slate-500 mb-2">
                    <span class="text-xs font-medium">เฉลี่ยต่อบิล</span>
                    <i data-lucide="trending-up" class="w-4 h-4 text-blue-500"></i>
                </div>
                <div class="text-2xl font-black text-slate-800" id="statAvgBill">0 ฿</div>
                <div class="text-[11px] text-slate-400 mt-1">ยอดขายเฉลี่ย/ออเดอร์</div>
            </div>
        </div>

        <!-- Charts Grid -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <!-- Chart 1: Sales by Product -->
            <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm">
                <h3 class="text-sm font-bold text-slate-800 mb-4 flex items-center gap-2">
                    <i data-lucide="bar-chart-2" class="w-4 h-4 text-indigo-600"></i>
                    ยอดขายแยกตามเมนูสินค้า (บาท)
                </h3>
                <div class="h-64">
                    <canvas id="productChart"></canvas>
                </div>
            </div>

            <!-- Chart 2: Payment Method Breakdown -->
            <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm">
                <h3 class="text-sm font-bold text-slate-800 mb-4 flex items-center gap-2">
                    <i data-lucide="doughnut" class="w-4 h-4 text-emerald-600"></i>
                    สัดส่วนช่องทางชำระเงิน
                </h3>
                <div class="h-64 flex justify-center items-center">
                    <canvas id="paymentChart"></canvas>
                </div>
            </div>
        </div>

        <!-- Sales Table Log -->
        <div class="bg-white rounded-2xl border border-slate-200 shadow-sm p-5">
            <h3 class="text-sm font-bold text-slate-800 mb-4 flex items-center gap-2">
                <i data-lucide="list" class="w-4 h-4 text-slate-600"></i>
                ประวัติการขายล่าสุด
            </h3>
            <div class="overflow-x-auto">
                <table class="w-full text-left text-xs">
                    <thead>
                        <tr class="bg-slate-50 text-slate-500 border-b">
                            <th class="p-2.5">เวลา</th>
                            <th class="p-2.5">สาขา</th>
                            <th class="p-2.5">ผู้บันทึก</th>
                            <th class="p-2.5">รายการ</th>
                            <th class="p-2.5">การชำระ</th>
                            <th class="p-2.5 text-right">ยอดรวม</th>
                        </tr>
                    </thead>
                    <tbody id="salesTableBody" class="divide-y divide-slate-100">
                        <!-- Table Rows -->
                    </tbody>
                </table>
            </div>
        </div>

    </main>

    <!-- Hidden Textarea Fallback for LINE Copy -->
    <textarea id="hiddenCopyTextarea" class="fixed top-0 left-0 opacity-0 pointer-events-none" readonly></textarea>

    <!-- Toast Notification -->
    <div id="toast" class="fixed bottom-5 right-5 bg-slate-900 text-white px-4 py-3 rounded-xl shadow-xl flex items-center gap-2 text-sm translate-y-20 opacity-0 transition-all z-50">
        <i data-lucide="check-circle" class="w-5 h-5 text-emerald-400"></i>
        <span id="toastMsg">คัดลอกเรียบร้อยแล้ว!</span>
    </div>

    <!-- Modal Success Alert -->
    <div id="successModal" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-2xl max-w-sm w-full p-6 text-center shadow-xl transform transition-all scale-95 opacity-0" id="modalCard">
            <div class="w-16 h-16 bg-emerald-100 text-emerald-600 rounded-full flex items-center justify-center mx-auto mb-4">
                <i data-lucide="check" class="w-10 h-10 stroke-[3]"></i>
            </div>
            <h3 class="text-xl font-bold text-slate-800 mb-1">บันทึกการขายเรียบร้อย!</h3>
            <p class="text-sm text-slate-500 mb-4" id="modalDetail">ส่งรายงานข้อมูลเรียบร้อยแล้ว</p>
            <button onclick="closeModal()" class="w-full bg-slate-800 hover:bg-slate-900 text-white font-medium py-2.5 rounded-xl transition">
                ตกลง / ทำรายการต่อไป
            </button>
        </div>
    </div>

    <script>
        // Preset Products Data
        const products = [
            { id: 'p1', name: 'เนื้อ', price: 80, icon: '🥩' },
            { id: 'p2', name: 'หม่าล่า', price: 70, icon: '🌶️' },
            { id: 'p3', name: 'ทูน่า', price: 70, icon: '🐟' },
            { id: 'p4', name: 'ไก่กรอบ', price: 60, icon: '🍗' },
            { id: 'p5', name: 'เคบับไก่', price: 60, icon: '🌯' }
        ]
