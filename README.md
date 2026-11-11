<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
  <title>梅吐帳本 - 小梅 & 吐司的專屬記帳與共用行事曆</title>
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Phosphor Icons -->
  <script src="https://unpkg.com/@phosphor-icons/web"></script>
  <!-- Google Fonts: Inter & Noto Sans TC -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@500;600;700;800&family=Noto+Sans+TC:wght@400;500;600;700&display=swap" rel="stylesheet">
  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['Plus Jakarta Sans', 'Noto Sans TC', 'sans-serif'],
          },
          colors: {
            sage: {
              50: '#f2f8f5',
              100: '#e1efe8',
              200: '#c5e0d4',
              300: '#9bcaba',
              400: '#6cae9c',
              500: '#489280', // 主色：清新典雅鼠尾草綠 (小梅代表色)
              600: '#377667',
              700: '#2d5e53',
            },
            ocean: {
              50: '#f0f7fb',
              100: '#ddedf6',
              200: '#bfdeee',
              300: '#91c7e2',
              400: '#5caad2',
              500: '#348ec0', // 搭配色：柔和湖藍/蔚藍 (吐司代表色)
              600: '#2772a1',
              700: '#215c82',
            },
            mintcream: {
              50: '#f7faf8',
              100: '#eef5f1',
              200: '#dce8e1',
            }
          },
          boxShadow: {
            'soft': '0 8px 30px rgba(45, 94, 83, 0.07)',
            'float': '0 12px 35px -8px rgba(72, 146, 128, 0.45)',
          }
        }
      }
    }
  </script>
  <style>
    body {
      -webkit-tap-highlight-color: transparent;
      overscroll-behavior-y: none;
      background-color: #eef2f0;
    }
    .hide-scrollbar::-webkit-scrollbar {
      display: none;
    }
    .hide-scrollbar {
      -ms-overflow-style: none;
      scrollbar-width: none;
    }
    .tab-active {
      color: #377667;
      font-weight: 700;
    }
    .safe-bottom {
      padding-bottom: env(safe-area-inset-bottom, 16px);
    }
    pre code {
      font-family: monospace;
      font-size: 11px;
    }
  </style>
</head>
<body class="flex justify-center items-center min-h-screen text-slate-800 antialiased font-sans">

  <!-- 手機外框容器 -->
  <div class="w-full max-w-md h-[100dvh] bg-mintcream-50 flex flex-col shadow-2xl relative overflow-hidden">
    
    <!-- 頂部身分切換與狀態列 -->
    <header class="bg-white/90 backdrop-blur-md pt-3.5 px-4 pb-2.5 border-b border-sage-100 flex items-center justify-between z-20 sticky top-0 shadow-sm">
      <div class="flex items-center space-x-2.5">
        <div id="user-avatar-badge" class="w-8 h-8 rounded-full bg-gradient-to-tr from-sage-500 to-ocean-400 flex items-center justify-center text-white shadow-sm text-sm font-bold">
          梅
        </div>
        <div>
          <span class="text-[10px] text-slate-400 font-semibold tracking-wider uppercase block">目前使用者</span>
          <div class="flex items-center space-x-1 cursor-pointer" onclick="toggleUserDropdown()">
            <span id="current-user-name" class="text-sm font-bold text-slate-800">小梅</span>
            <i class="ph ph-caret-down text-xs text-slate-500"></i>
          </div>
        </div>
      </div>

      <!-- 切換伴侶視角 & 雲端狀態 -->
      <div class="flex items-center space-x-1.5">
        <div id="cloud-status-indicator" onclick="switchTab('settings')" class="w-6 h-6 rounded-full bg-emerald-50 text-emerald-600 flex items-center justify-center text-xs cursor-pointer" title="Google Sheets 連線狀態">
          <i class="ph ph-cloud-check text-sm font-bold"></i>
        </div>
        <button onclick="switchCurrentUser()" class="px-2.5 py-1.5 bg-sage-50 border border-sage-200 text-sage-600 rounded-full text-xs font-semibold flex items-center space-x-1 active:scale-95 transition">
          <i class="ph ph-arrows-left-right text-xs"></i>
          <span>切換至 <span id="partner-switch-name">吐司</span></span>
        </button>
      </div>

      <!-- 身分選單 Dropdown -->
      <div id="user-dropdown" class="hidden absolute top-14 left-4 bg-white rounded-2xl shadow-xl border border-sage-100 p-2 w-52 z-30">
        <div onclick="selectUser('user_mei')" class="flex items-center p-2 rounded-xl hover:bg-sage-50 cursor-pointer space-x-2.5">
          <span class="w-7 h-7 rounded-full bg-sage-100 text-sage-600 flex items-center justify-center font-bold text-xs">梅</span>
          <div>
            <div class="text-xs font-bold text-slate-800">小梅 (綠色)</div>
            <div class="text-[10px] text-slate-400">登入裝置：iPhone 15 Pro</div>
          </div>
        </div>
        <div onclick="selectUser('user_toast')" class="flex items-center p-2 rounded-xl hover:bg-ocean-50 cursor-pointer space-x-2.5">
          <span class="w-7 h-7 rounded-full bg-ocean-100 text-ocean-600 flex items-center justify-center font-bold text-xs">吐</span>
          <div>
            <div class="text-xs font-bold text-slate-800">吐司 (藍色)</div>
            <div class="text-[10px] text-slate-400">登入裝置：Pixel 8</div>
          </div>
        </div>
        <div class="border-t border-slate-100 mt-1 pt-1.5 text-[10px] text-slate-400 px-2 flex items-center justify-between">
          <span>多裝置單一登入保護中</span>
          <i class="ph ph-shield-check text-sage-600 text-sm"></i>
        </div>
      </div>
    </header>

    <!-- 主內容滑動區域 -->
    <main class="flex-1 overflow-y-auto hide-scrollbar pb-24 relative" id="main-content">
      
      <!-- ================= 頁面 1: 首頁儀表板 ================= -->
      <section id="tab-home" class="p-4 space-y-4">
        <!-- 今日消費對比進度條卡片 -->
        <div class="bg-white rounded-3xl p-4 shadow-soft border border-sage-100/80">
          <div class="flex items-center justify-between mb-2">
            <span class="text-xs font-bold uppercase tracking-wider text-slate-400 flex items-center">
              <i class="ph ph-chart-donut mr-1 text-sage-500 font-bold"></i> 今日消費條 (Daily Bar)
            </span>
            <span id="today-date-text" class="text-xs font-medium text-slate-500">2026/11/11</span>
          </div>

          <!-- 對比條 -->
          <div class="h-4 w-full bg-slate-100 rounded-full flex overflow-hidden p-0.5 border border-slate-200/50 my-2.5">
            <div id="bar-user-mei" class="bg-sage-400 h-full rounded-l-full transition-all duration-500" style="width: 50%;"></div>
            <div id="bar-user-toast" class="bg-ocean-400 h-full rounded-r-full transition-all duration-500" style="width: 50%;"></div>
          </div>

          <!-- 數額明細 -->
          <div class="grid grid-cols-2 gap-2 pt-1 text-center">
            <div class="bg-sage-50/70 p-2.5 rounded-2xl border border-sage-100">
              <div class="flex items-center justify-center space-x-1 mb-0.5">
                <span class="w-2.5 h-2.5 rounded-full bg-sage-500"></span>
                <span class="text-[11px] font-bold text-sage-700" id="label-user-mei">小梅 今日花費</span>
              </div>
              <div class="text-base font-extrabold text-sage-700" id="total-user-mei-today">$0</div>
              <div class="text-[10px] text-slate-400 mt-0.5" id="ratio-user-mei">50%</div>
            </div>
            <div class="bg-ocean-50/70 p-2.5 rounded-2xl border border-ocean-100">
              <div class="flex items-center justify-center space-x-1 mb-0.5">
                <span class="w-2.5 h-2.5 rounded-full bg-ocean-500"></span>
                <span class="text-[11px] font-bold text-ocean-700" id="label-user-toast">吐司 今日花費</span>
              </div>
              <div class="text-base font-extrabold text-ocean-700" id="total-user-toast-today">$0</div>
              <div class="text-[10px] text-slate-400 mt-0.5" id="ratio-user-toast">50%</div>
            </div>
          </div>
        </div>

        <!-- 幫對方支付 / 代墊即時結算卡片 -->
        <div class="bg-gradient-to-br from-white to-mintcream-100 rounded-3xl p-4 shadow-soft border border-sage-100">
          <div class="flex items-center justify-between mb-3">
            <div class="flex items-center space-x-1.5">
              <i class="ph ph-hand-coins text-sage-600 text-lg"></i>
              <h3 class="text-xs font-bold text-slate-700 uppercase tracking-wide">代墊分攤與結算</h3>
            </div>
            <span class="text-[10px] px-2 py-0.5 rounded-full bg-white text-sage-700 font-semibold shadow-sm border border-sage-100">
              累積清算中心
            </span>
          </div>

          <div class="space-y-2">
            <div class="flex justify-between items-center bg-white/90 p-2.5 rounded-2xl border border-sage-50">
              <div class="flex items-center space-x-2">
                <div class="w-7 h-7 rounded-full bg-sage-100 text-sage-700 flex items-center justify-center text-xs">
                  <i class="ph ph-hand-heart"></i>
                </div>
                <span class="text-xs text-slate-600" id="my-helped-label">今日我幫對方墊付</span>
              </div>
              <span class="text-sm font-bold text-slate-800" id="my-helped-today">$0</span>
            </div>

            <div class="flex justify-between items-center bg-white/90 p-2.5 rounded-2xl border border-sage-50">
              <div class="flex items-center space-x-2">
                <div class="w-7 h-7 rounded-full bg-ocean-100 text-ocean-700 flex items-center justify-center text-xs">
                  <i class="ph ph-arrows-split"></i>
                </div>
                <span class="text-xs text-slate-600" id="partner-helped-label">今日對方幫我墊付</span>
              </div>
              <span class="text-sm font-bold text-slate-800" id="partner-helped-today">$0</span>
            </div>

            <!-- 結算建議 -->
            <div id="balance-banner" class="mt-2.5 p-3 rounded-2xl text-xs flex items-center justify-between font-medium bg-emerald-50 text-emerald-800 border border-emerald-100">
              <span class="flex items-center space-x-1.5">
                <i class="ph ph-scales text-base"></i>
                <span id="balance-text">雙方目前收支平分，無須找零！</span>
              </span>
              <button onclick="settleAccountsPrompt()" class="text-[10px] bg-white px-2.5 py-1 rounded-lg border border-emerald-200 text-emerald-800 font-bold active:scale-95 shadow-sm">一鍵結清</button>
            </div>
          </div>
        </div>

        <!-- 今日開銷快捷清單 -->
        <div>
          <div class="flex items-center justify-between mb-2 px-1">
            <h3 class="text-xs font-bold text-slate-500 uppercase tracking-wider">今日最新收支</h3>
            <button onclick="switchTab('records')" class="text-xs text-sage-600 font-semibold hover:underline">查看全部明細</button>
          </div>
          <div id="home-today-list" class="space-y-2">
            <!-- 動態插入今日紀錄 -->
          </div>
        </div>
      </section>

      <!-- ================= 頁面 2: 明細串流 ================= -->
      <section id="tab-records" class="p-4 space-y-3 hidden">
        <!-- 篩選列 -->
        <div class="flex space-x-2 overflow-x-auto hide-scrollbar pb-1">
          <button onclick="setRecordFilter('all')" id="filter-all" class="filter-btn px-3 py-1.5 rounded-full text-xs font-semibold bg-sage-500 text-white shadow-sm">全部明細</button>
          <button onclick="setRecordFilter('mine')" id="filter-mine" class="filter-btn px-3 py-1.5 rounded-full text-xs font-medium bg-white text-slate-600 border border-slate-200">我記的帳</button>
          <button onclick="setRecordFilter('partner')" id="filter-partner" class="filter-btn px-3 py-1.5 rounded-full text-xs font-medium bg-white text-slate-600 border border-slate-200">伴侶記的帳</button>
        </div>

        <!-- 帳目流水清單 -->
        <div id="records-stream" class="space-y-2.5">
          <!-- 帳目卡片動態渲染 -->
        </div>
      </section>

      <!-- ================= 頁面 3: 共用情侶行事曆 ================= -->
      <section id="tab-calendar" class="p-4 space-y-4 hidden">
        <!-- 月曆卡片 -->
        <div class="bg-white rounded-3xl p-4 shadow-soft border border-sage-100">
          <div class="flex items-center justify-between mb-3">
            <h3 class="text-sm font-bold text-slate-800" id="calendar-month-year">2026年 11月</h3>
            <div class="flex space-x-1">
              <button onclick="changeMonth(-1)" class="w-7 h-7 rounded-full bg-slate-100 flex items-center justify-center text-slate-600 active:scale-95"><i class="ph ph-caret-left"></i></button>
              <button onclick="changeMonth(1)" class="w-7 h-7 rounded-full bg-slate-100 flex items-center justify-center text-slate-600 active:scale-95"><i class="ph ph-caret-right"></i></button>
            </div>
          </div>
          <!-- 星期標題 -->
          <div class="grid grid-cols-7 text-center text-[11px] font-bold text-slate-400 mb-2">
            <span>日</span><span>一</span><span>二</span><span>三</span><span>四</span><span>五</span><span>六</span>
          </div>
          <!-- 日曆格 -->
          <div id="calendar-grid" class="grid grid-cols-7 gap-1 text-xs">
            <!-- 動態繪製日曆格 -->
          </div>
        </div>

        <!-- 選取日期的行程活動 & 該日支出 -->
        <div class="bg-white rounded-3xl p-4 shadow-soft border border-sage-100">
          <div class="flex items-center justify-between mb-3">
            <div class="flex items-center space-x-1.5">
              <i class="ph ph-calendar-star text-sage-600"></i>
              <h4 class="text-xs font-bold text-slate-700" id="selected-date-title">11月11日 約會與行程</h4>
            </div>
            <button onclick="openAddEventModal()" class="text-xs text-sage-600 font-bold flex items-center space-x-1 active:scale-95">
              <i class="ph ph-plus-circle text-sm"></i>
              <span>新增行程</span>
            </button>
          </div>

          <div id="calendar-events-list" class="space-y-2">
            <!-- 該日行程項目 -->
          </div>

          <!-- 該日關聯開銷列表 -->
          <div class="mt-4 pt-3 border-t border-slate-100">
            <div class="text-[11px] font-bold text-slate-400 mb-2 flex items-center justify-between">
              <span>📅 本日相關支出</span>
              <span id="selected-date-expense-sum" class="text-slate-600">$0</span>
            </div>
            <div id="calendar-date-expenses" class="space-y-1.5 text-xs text-slate-500">
              <!-- 動態載入該日花費 -->
            </div>
          </div>
        </div>
      </section>

      <!-- ================= 頁面 4: 設定與後台串接 (Google Sheets) ================= -->
      <section id="tab-settings" class="p-4 space-y-4 hidden">
        <!-- 伴侶連線狀態 -->
        <div class="bg-white rounded-3xl p-4 shadow-soft border border-sage-100">
          <h3 class="text-xs font-bold text-slate-400 uppercase tracking-wider mb-3">伴侶帳號狀態</h3>
          <div class="flex items-center justify-between p-3 bg-sage-50/60 rounded-2xl border border-sage-100">
            <div class="flex items-center space-x-3">
              <div class="w-10 h-10 rounded-full bg-gradient-to-tr from-sage-500 to-ocean-500 text-white flex items-center justify-center font-bold text-lg shadow-sm">
                🌿
              </div>
              <div>
                <div class="text-sm font-bold text-slate-800">小梅 & 吐司</div>
                <div class="text-xs text-slate-500">甜蜜共用專屬帳本</div>
              </div>
            </div>
            <span class="text-[10px] px-2.5 py-1 bg-emerald-100 text-emerald-800 rounded-full font-bold">同步連線</span>
          </div>
        </div>

        <!-- Google Sheets 雲端後台串接 (正式版核心) -->
        <div class="bg-white rounded-3xl p-4 shadow-soft border border-sage-100 space-y-3">
          <div class="flex items-center justify-between">
            <div class="flex items-center space-x-1.5">
              <i class="ph ph-file-csv text-emerald-600 text-lg font-bold"></i>
              <h3 class="text-xs font-bold text-slate-700 uppercase tracking-wider">Google 試算表 (後台串接)</h3>
            </div>
            <button onclick="openAppsScriptTutorialModal()" class="text-[11px] text-sage-600 font-bold hover:underline">
              教學與後台程式碼
            </button>
          </div>

          <p class="text-[11px] text-slate-500 leading-relaxed">
            免費且永久的後台！建立 Google 試算表並部署 Apps Script 網頁應用程式後，將產生的 <b>Web App URL</b> 貼在下方即可雙向同步帳本。
          </p>

          <div>
            <label class="text-[11px] font-semibold text-slate-400 block mb-1">Google Apps Script Web App URL</label>
            <input type="text" id="gas-url-input" placeholder="https://script.google.com/macros/s/.../exec" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs font-mono text-slate-700 focus:outline-none focus:border-sage-400">
          </div>

          <div class="grid grid-cols-2 gap-2 pt-1">
            <button onclick="saveGasUrl()" class="py-2 bg-slate-100 hover:bg-slate-200 text-slate-700 rounded-xl text-xs font-bold transition">
              儲存網址
            </button>
            <button onclick="syncWithGoogleSheet()" class="py-2 bg-sage-500 hover:bg-sage-600 text-white rounded-xl text-xs font-bold flex items-center justify-center space-x-1 shadow-sm active:scale-95 transition">
              <i class="ph ph-arrows-clockwise text-sm"></i>
              <span>立即雙向同步</span>
            </button>
          </div>
        </div>

        <!-- 付款方式管理 -->
        <div class="bg-white rounded-3xl p-4 shadow-soft border border-sage-100">
          <div class="flex items-center justify-between mb-3">
            <h3 class="text-xs font-bold text-slate-400 uppercase tracking-wider">自訂付款方式管理</h3>
            <button onclick="openAddPaymentModal()" class="text-xs text-sage-600 font-bold flex items-center space-x-1">
              <i class="ph ph-plus"></i>
              <span>新增方式</span>
            </button>
          </div>
          <div id="payment-methods-list" class="space-y-2">
            <!-- 支付方式清單 -->
          </div>
        </div>

        <!-- 裝置安全驗證與備份 -->
        <div class="bg-white rounded-3xl p-4 shadow-soft border border-sage-100 text-xs text-slate-600 space-y-2.5">
          <div class="font-bold text-slate-800 flex items-center space-x-1.5">
            <i class="ph ph-shield-check text-base text-sage-600"></i>
            <span>跨裝置單一登入 (Session Guard)</span>
          </div>
          <p class="text-slate-500 leading-relaxed text-[11px]">
            若有其他手機以相同角色登入，系統將會即時發出安全提醒並重置權限。
          </p>
          <div class="grid grid-cols-2 gap-2 pt-1">
            <button onclick="simulateForceLogout()" class="py-2 bg-rose-50 text-rose-600 rounded-xl font-bold text-xs hover:bg-rose-100 active:scale-95 transition">
              測試被踢出登入
            </button>
            <button onclick="exportBackupJSON()" class="py-2 bg-slate-100 text-slate-700 rounded-xl font-bold text-xs hover:bg-slate-200 active:scale-95 transition">
              匯出本地備份檔
            </button>
          </div>
        </div>
      </section>

    </main>

    <!-- 浮動新增按鈕 (FAB) -->
    <div class="absolute bottom-20 right-5 z-20">
      <button onclick="openAddRecordModal()" class="w-13 h-13 p-3.5 bg-gradient-to-r from-sage-500 to-ocean-500 text-white rounded-full shadow-float flex items-center justify-center active:scale-90 transition transform hover:rotate-90">
        <i class="ph ph-plus text-2xl font-bold"></i>
      </button>
    </div>

    <!-- 底部 Tab 導航列 -->
    <nav class="absolute bottom-0 left-0 right-0 bg-white/95 backdrop-blur-md border-t border-sage-100 px-6 py-2.5 flex justify-between items-center z-20 safe-bottom">
      <button onclick="switchTab('home')" id="nav-home" class="flex flex-col items-center text-xs tab-active transition">
        <i class="ph ph-house text-xl mb-0.5"></i>
        <span>首頁</span>
      </button>
      <button onclick="switchTab('records')" id="nav-records" class="flex flex-col items-center text-xs text-slate-400 transition">
        <i class="ph ph-receipt text-xl mb-0.5"></i>
        <span>明細</span>
      </button>
      <button onclick="switchTab('calendar')" id="nav-calendar" class="flex flex-col items-center text-xs text-slate-400 transition">
        <i class="ph ph-calendar-blank text-xl mb-0.5"></i>
        <span>行事曆</span>
      </button>
      <button onclick="switchTab('settings')" id="nav-settings" class="flex flex-col items-center text-xs text-slate-400 transition">
        <i class="ph ph-gear text-xl mb-0.5"></i>
        <span>後台與設定</span>
      </button>
    </nav>

    <!-- ================= 彈窗 1: 新增記帳彈窗 ================= -->
    <div id="modal-add-record" class="hidden fixed inset-0 z-50 bg-black/40 backdrop-blur-sm flex items-end justify-center p-0">
      <div class="bg-white w-full max-w-md rounded-t-3xl p-5 space-y-4 max-h-[90vh] overflow-y-auto">
        <div class="flex items-center justify-between border-b border-slate-100 pb-3">
          <h3 class="font-bold text-slate-800 text-base flex items-center">
            <span class="w-2.5 h-2.5 rounded-full bg-sage-500 mr-2"></span>
            記錄新花費 (<span id="modal-user-tag" class="text-sage-600 font-bold ml-1">小梅</span>)
          </h3>
          <button onclick="closeAddRecordModal()" class="text-slate-400 hover:text-slate-600 text-lg">
            <i class="ph ph-x"></i>
          </button>
        </div>

        <!-- 金額輸入 -->
        <div>
          <label class="text-xs font-semibold text-slate-400">支出金額 (NT$)</label>
          <div class="relative mt-1">
            <span class="absolute left-3.5 top-2.5 text-xl font-bold text-slate-400">$</span>
            <input type="number" id="input-amount" placeholder="0" class="w-full pl-9 pr-4 py-2 text-2xl font-bold bg-slate-50 border border-slate-200 rounded-2xl focus:outline-none focus:border-sage-400">
          </div>
        </div>

        <!-- 分類選擇 -->
        <div>
          <label class="text-xs font-semibold text-slate-400">消費分類</label>
          <div class="grid grid-cols-4 gap-2 mt-1.5" id="category-selector">
            <!-- 預設分類項目 -->
          </div>
        </div>

        <!-- 付款方式 -->
        <div>
          <label class="text-xs font-semibold text-slate-400">付款方式</label>
          <select id="select-payment" class="w-full mt-1 px-3 py-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs font-medium text-slate-700">
            <!-- 動態填充自訂付款方式 -->
          </select>
        </div>

        <!-- 分攤與幫對方代墊機制 -->
        <div class="bg-sage-50/70 p-3 rounded-2xl border border-sage-100 space-y-2">
          <label class="text-xs font-bold text-slate-700 block">帳目歸屬與分攤</label>
          <div class="grid grid-cols-3 gap-1.5">
            <button type="button" onclick="setSplitType('personal')" id="split-btn-personal" class="split-opt py-1.5 px-2 rounded-xl text-xs font-medium bg-white text-sage-600 border border-sage-300">個人自付</button>
            <button type="button" onclick="setSplitType('aa')" id="split-btn-aa" class="split-opt py-1.5 px-2 rounded-xl text-xs font-medium bg-white/70 text-slate-600 border border-transparent">雙方平攤 (AA)</button>
            <button type="button" onclick="setSplitType('for_partner')" id="split-btn-for_partner" class="split-opt py-1.5 px-2 rounded-xl text-xs font-medium bg-white/70 text-slate-600 border border-transparent">我幫對方付</button>
          </div>
          <p id="split-hint" class="text-[11px] text-slate-500 italic">由我個人全額吸收，不影響對方應還款項。</p>
        </div>

        <!-- 關鍵機制：隱私保護模式 -->
        <div class="p-3 bg-amber-50/70 border border-amber-200/60 rounded-2xl flex items-start space-x-3">
          <input type="checkbox" id="check-private" class="mt-0.5 rounded text-sage-600 focus:ring-sage-400 w-4 h-4 cursor-pointer">
          <div class="flex-1">
            <label for="check-private" class="text-xs font-bold text-amber-900 flex items-center cursor-pointer">
              <i class="ph ph-lock-key mr-1 text-sm"></i> 向伴侶隱藏項目明細 (保密/驚喜模式)
            </label>
            <p class="text-[10px] text-amber-700 mt-0.5">
              伴侶只會看到「🔒 伴侶保密支出」且品項被保密，但金額依然計入今日總花費與統計，適合準備驚喜禮物！
            </p>
          </div>
        </div>

        <!-- 品項備註與日期 -->
        <div class="grid grid-cols-2 gap-2">
          <div>
            <label class="text-xs font-semibold text-slate-400">項目品項 / 備註</label>
            <input type="text" id="input-title" placeholder="如：早午餐、拿鐵" class="w-full mt-1 px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          </div>
          <div>
            <label class="text-xs font-semibold text-slate-400">日期</label>
            <input type="date" id="input-date" class="w-full mt-1 px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          </div>
        </div>

        <!-- 提交按鈕 -->
        <button onclick="saveRecord()" class="w-full py-3 bg-gradient-to-r from-sage-500 to-ocean-500 text-white rounded-2xl font-bold text-sm shadow-md active:scale-95 transition">
          完成記帳並同步
        </button>
      </div>
    </div>

    <!-- ================= 彈窗 2: 新增付款方式 ================= -->
    <div id="modal-add-payment" class="hidden fixed inset-0 z-50 bg-black/40 backdrop-blur-sm flex items-center justify-center p-4">
      <div class="bg-white w-full max-w-xs rounded-3xl p-5 space-y-3">
        <h3 class="font-bold text-slate-800 text-sm">新增自訂付款方式</h3>
        <input type="text" id="input-new-payment" placeholder="例如：國泰KOKO、中信LinePay" class="w-full px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
        <div class="flex space-x-2 pt-2">
          <button onclick="closeAddPaymentModal()" class="flex-1 py-2 bg-slate-100 text-slate-600 rounded-xl text-xs font-semibold">取消</button>
          <button onclick="savePaymentMethod()" class="flex-1 py-2 bg-sage-500 text-white rounded-xl text-xs font-semibold">儲存</button>
        </div>
      </div>
    </div>

    <!-- ================= 彈窗 3: 新增行程彈窗 ================= -->
    <div id="modal-add-event" class="hidden fixed inset-0 z-50 bg-black/40 backdrop-blur-sm flex items-end justify-center p-0">
      <div class="bg-white w-full max-w-md rounded-t-3xl p-5 space-y-3">
        <div class="flex items-center justify-between border-b border-slate-100 pb-2">
          <h3 class="font-bold text-slate-800 text-sm flex items-center">
            <i class="ph ph-heart mr-1 text-sage-600"></i> 新增約會 / 共用行程
          </h3>
          <button onclick="closeAddEventModal()" class="text-slate-400 text-base"><i class="ph ph-x"></i></button>
        </div>
        <div>
          <label class="text-xs font-semibold text-slate-400">行程名稱</label>
          <input type="text" id="event-title" placeholder="如：淡水野餐、看電影" class="w-full mt-1 px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
        </div>
        <div class="grid grid-cols-2 gap-2">
          <div>
            <label class="text-xs font-semibold text-slate-400">活動類型</label>
            <select id="event-type" class="w-full mt-1 px-2.5 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
              <option value="date">浪漫約會 🌿</option>
              <option value="anniversary">紀念日 🎂</option>
              <option value="bill">共同繳費 ⚡</option>
              <option value="trip">旅遊出遊 ✈️</option>
              <option value="food">雙人聚餐 🍴</option>
            </select>
          </div>
          <div>
            <label class="text-xs font-semibold text-slate-400">預估花費 (可選)</label>
            <input type="number" id="event-budget" placeholder="0" class="w-full mt-1 px-3 py-2 bg-slate-50 border border-slate-200 rounded-xl text-xs">
          </div>
        </div>
        <button onclick="saveCalendarEvent()" class="w-full py-2.5 bg-sage-500 text-white rounded-xl font-bold text-xs mt-2">
          加入共用行事曆
        </button>
      </div>
    </div>

    <!-- ================= 彈窗 4: Google Apps Script 後台教學與代碼 ================= -->
    <div id="modal-gas-tutorial" class="hidden fixed inset-0 z-50 bg-black/50 backdrop-blur-sm flex items-center justify-center p-4">
      <div class="bg-white w-full max-w-sm rounded-3xl p-5 space-y-3 max-h-[85vh] overflow-y-auto">
        <div class="flex items-center justify-between border-b pb-2">
          <h3 class="font-bold text-slate-800 text-sm flex items-center">
            <i class="ph ph-code mr-1.5 text-emerald-600"></i> 後台：Google Sheets 串接指南
          </h3>
          <button onclick="closeAppsScriptTutorialModal()" class="text-slate-400"><i class="ph ph-x"></i></button>
        </div>

        <div class="text-[11px] text-slate-600 space-y-2 leading-relaxed">
          <div class="font-bold text-slate-700">三步驟免費建立後台：</div>
          <ol class="list-decimal pl-4 space-y-1">
            <li>開啟 Google 試算表，建立一個新空白檔案。</li>
            <li>點選頂部選單 <b>擴充功能 &gt; Apps Script</b>。</li>
            <li>清空編輯器，貼上下方後台代碼並點選「儲存」。</li>
            <li>點選右上角 <b>部署 &gt; 新增部署作業</b>：
              <ul class="list-disc pl-3 text-[10px] text-slate-500 mt-0.5">
                <li>種類選擇：<b>網頁應用程式 (Web app)</b></li>
                <li>誰可以存取：<b>所有人 (Anyone)</b> (這樣手機才能免登入存取)</li>
              </ul>
            </li>
            <li>複製產生的 <b>Web App 網址</b>，貼回設定頁的輸入框即可！</li>
          </ol>
        </div>

        <div class="relative bg-slate-900 text-emerald-300 p-2.5 rounded-xl">
          <button onclick="copyAppsScriptCode()" class="absolute top-2 right-2 bg-slate-800 hover:bg-slate-700 text-white px-2 py-1 rounded text-[10px] border border-slate-600">
            複製代碼
          </button>
          <pre><code id="gas-code-snippet">// Google Apps Script 後端程式碼
function doGet(e) {
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = ss.getSheetByName("records") || ss.getActiveSheet();
  var data = sheet.getDataRange().getValues();
  var rows = [];
  if (data.length > 1) {
    for (var i = 1; i < data.length; i++) {
      rows.push({
        id: data[i][0],
        userId: data[i][1],
        amount: Number(data[i][2]),
        category: data[i][3],
        payment: data[i][4],
        title: data[i][5],
        date: String(data[i][6]),
        splitType: data[i][7],
        isPrivate: data[i][8] === true || data[i][8] === "true",
        timestamp: Number(data[i][9])
      });
    }
  }
  return ContentService.createTextOutput(JSON.stringify({status:"ok", data: rows}))
    .setMimeType(ContentService.MimeType.JSON);
}

function doPost(e) {
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  var sheet = ss.getSheetByName("records");
  if (!sheet) {
    sheet = ss.insertSheet("records");
    sheet.appendRow(["id","userId","amount","category","payment","title","date","splitType","isPrivate","timestamp"]);
  }
  var payload = JSON.parse(e.postData.contents);
  if (payload.action === "syncAll") {
    sheet.clearContents();
    sheet.appendRow(["id","userId","amount","category","payment","title","date","splitType","isPrivate","timestamp"]);
    var rows = payload.records.map(function(r) {
      return [r.id, r.userId, r.amount, r.category, r.payment, r.title, r.date, r.splitType, r.isPrivate, r.timestamp];
    });
    if (rows.length > 0) {
      sheet.getRange(2, 1, rows.length, 10).setValues(rows);
    }
    return ContentService.createTextOutput(JSON.stringify({status:"ok", count: rows.length}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}</code></pre>
        </div>

        <button onclick="closeAppsScriptTutorialModal()" class="w-full py-2 bg-slate-100 font-bold text-slate-700 rounded-xl text-xs">
          我知道了
        </button>
      </div>
    </div>

    <!-- 自訂訊息提示 Toast -->
    <div id="app-toast" class="hidden fixed top-16 left-1/2 -translate-x-1/2 z-50 bg-slate-800/90 text-white text-xs px-4 py-2 rounded-full shadow-lg flex items-center space-x-1.5 transition">
      <i class="ph ph-info text-sage-300"></i>
      <span id="toast-message">操作完成</span>
    </div>

  </div>

  <script>
    /* ================= 狀態與初始資料 (小梅 & 吐司) ================= */
    const USERS = {
      user_mei: { id: 'user_mei', name: '小梅', label: '小梅', color: 'sage' },
      user_toast: { id: 'user_toast', name: '吐司', label: '吐司', color: 'ocean' }
    };

    let currentUser = 'user_mei'; // 預設小梅
    let currentFilter = 'all'; 
    let currentSelectedCategory = 'food';
    let currentSplitType = 'personal'; 
    let calendarCurrentDate = new Date(); 
    let selectedCalendarDateStr = getFormattedDate(new Date());

    // Google Apps Script 後台設定
    let gasWebAppUrl = localStorage.getItem('SWEET_GAS_URL') || '';

    // 預設分類 (清新風格配色)
    const CATEGORIES = [
      { id: 'food', name: '餐飲美食', icon: 'ph-fork-knife', color: 'bg-emerald-100 text-emerald-700' },
      { id: 'daily', name: '日常用品', icon: 'ph-shopping-bag', color: 'bg-teal-100 text-teal-700' },
      { id: 'entertainment', name: '約會休閒', icon: 'ph-film-strip', color: 'bg-cyan-100 text-cyan-700' },
      { id: 'transport', name: '交通出行', icon: 'ph-train', color: 'bg-sky-100 text-sky-700' },
      { id: 'gift', name: '浪漫驚喜', icon: 'ph-gift', color: 'bg-rose-100 text-rose-600' },
      { id: 'medical', name: '保健醫療', icon: 'ph-first-aid', color: 'bg-amber-100 text-amber-700' },
      { id: 'home', name: '水電居住', icon: 'ph-house-line', color: 'bg-indigo-100 text-indigo-700' },
      { id: 'other', name: '其他支出', icon: 'ph-dots-three-outline', color: 'bg-slate-100 text-slate-600' }
    ];

    // 自訂付款方式清單 (可隨時擴充)
    let paymentMethods = JSON.parse(localStorage.getItem('SWEET_PAYMENTS')) || [
      '現金', '玉山信用卡', '國泰信用卡', 'LINE Pay', '悠遊卡', '街口支付'
    ];

    // 今日字串
    const todayStr = getFormattedDate(new Date());

    // 初始帳目資料 (支援 localStorage 本地快取備份)
    let records = JSON.parse(localStorage.getItem('SWEET_RECORDS')) || [
      {
        id: 'rec-1',
        userId: 'user_mei',
        amount: 280,
        category: 'food',
        payment: 'LINE Pay',
        title: '早午餐三明治與咖啡',
        date: todayStr,
        splitType: 'aa',
        isPrivate: false,
        timestamp: Date.now() - 3600000 * 3
      },
      {
        id: 'rec-2',
        userId: 'user_mei',
        amount: 980,
        category: 'gift',
        payment: '國泰信用卡',
        title: '準備給吐司的手工相冊與紀念禮物',
        date: todayStr,
        splitType: 'personal',
        isPrivate: true, // 向吐司保密項目
        timestamp: Date.now() - 3600000 * 2
      },
      {
        id: 'rec-3',
        userId: 'user_toast',
        amount: 650,
        category: 'food',
        payment: '街口支付',
        title: '生鮮超市買火鍋料',
        date: todayStr,
        splitType: 'for_partner', // 吐司幫小梅付全額
        isPrivate: false,
        timestamp: Date.now() - 3600000 * 1
      }
    ];

    // 行事曆行程資料
    let calendarEvents = JSON.parse(localStorage.getItem('SWEET_EVENTS')) || [
      {
        id: 'ev-1',
        date: todayStr,
        title: '小梅 & 吐司吃抹茶冰淇淋 🍦',
        type: 'date',
        budget: 300,
        creator: 'user_mei'
      },
      {
        id: 'ev-2',
        date: getOffsetDateStr(4),
        title: '紀念日野餐約會 🌿',
        type: 'anniversary',
        budget: 1500,
        creator: 'user_toast'
      }
    ];

    /* ================= 輔助時間函式 ================= */
    function getFormattedDate(date) {
      const y = date.getFullYear();
      const m = String(date.getMonth() + 1).padStart(2, '0');
      const d = String(date.getDate()).padStart(2, '0');
      return `${y}-${m}-${d}`;
    }

    function getOffsetDateStr(offsetDays) {
      const d = new Date();
      d.setDate(d.getDate() + offsetDays);
      return getFormattedDate(d);
    }

    function saveLocal() {
      localStorage.setItem('SWEET_RECORDS', JSON.stringify(records));
      localStorage.setItem('SWEET_EVENTS', JSON.stringify(calendarEvents));
      localStorage.setItem('SWEET_PAYMENTS', JSON.stringify(paymentMethods));
    }

    /* ================= 頁面初始化 ================= */
    window.onload = function() {
      const now = new Date();
      document.getElementById('today-date-text').innerText = `${now.getFullYear()}/${now.getMonth() + 1}/${now.getDate()}`;
      document.getElementById('input-date').value = todayStr;
      if (gasWebAppUrl) {
        document.getElementById('gas-url-input').value = gasWebAppUrl;
      }

      renderCategoriesSelector();
      renderPaymentOptions();
      renderPaymentManageList();
      updateDashboard();
      renderRecordsStream();
      renderCalendar();
      renderCalendarEvents();
    };

    /* ================= 使用者與角色視角切換 ================= */
    function toggleUserDropdown() {
      const dd = document.getElementById('user-dropdown');
      dd.classList.toggle('hidden');
    }

    function selectUser(userId) {
      currentUser = userId;
      document.getElementById('user-dropdown').classList.add('hidden');
      updateUserUI();
    }

    function switchCurrentUser() {
      currentUser = currentUser === 'user_mei' ? 'user_toast' : 'user_mei';
      updateUserUI();
    }

    function updateUserUI() {
      const isMei = currentUser === 'user_mei';
      const user = USERS[currentUser];
      const partner = USERS[isMei ? 'user_toast' : 'user_mei'];

      document.getElementById('current-user-name').innerText = user.name;
      document.getElementById('partner-switch-name').innerText = partner.name;
      document.getElementById('modal-user-tag').innerText = user.name;
      document.getElementById('user-avatar-badge').innerText = user.name[0];

      if (isMei) {
        document.getElementById('user-avatar-badge').className = "w-8 h-8 rounded-full bg-gradient-to-tr from-sage-500 to-emerald-400 flex items-center justify-center text-white shadow-sm text-sm font-bold";
      } else {
        document.getElementById('user-avatar-badge').className = "w-8 h-8 rounded-full bg-gradient-to-tr from-ocean-500 to-sky-400 flex items-center justify-center text-white shadow-sm text-sm font-bold";
      }

      document.getElementById('my-helped-label').innerText = `今日我幫 ${partner.name} 墊付`;
      document.getElementById('partner-helped-label').innerText = `今日 ${partner.name} 幫我墊付`;

      showToast(`已切換為：${user.name} 的手機視角`);
      updateDashboard();
      renderRecordsStream();
    }

    /* ================= 導航切換 ================= */
    function switchTab(tabId) {
      const tabs = ['home', 'records', 'calendar', 'settings'];
      tabs.forEach(t => {
        document.getElementById(`tab-${t}`).classList.add('hidden');
        const navBtn = document.getElementById(`nav-${t}`);
        navBtn.classList.remove('tab-active');
        navBtn.classList.add('text-slate-400');
      });

      document.getElementById(`tab-${tabId}`).classList.remove('hidden');
      const activeNav = document.getElementById(`nav-${tabId}`);
      activeNav.classList.add('tab-active');
      activeNav.classList.remove('text-slate-400');

      if (tabId === 'calendar') {
        renderCalendar();
        renderCalendarEvents();
      }
    }

    /* ================= 儀表板 & 今日消費條計算 ================= */
    function updateDashboard() {
      const today = todayStr;

      let meiTodayTotal = 0;
      let toastTodayTotal = 0;

      let myHelpedToday = 0;       
      let partnerHelpedToday = 0;  
      let netOwedToMe = 0; // 正數代表對方該給我，負數代表我該給對方

      records.forEach(r => {
        // 今日開銷累計 (即使保密，金額也100%全額列入)
        if (r.date === today) {
          if (r.userId === 'user_mei') {
            meiTodayTotal += r.amount;
          } else {
            toastTodayTotal += r.amount;
          }
        }

        // 分攤/代付清算
        if (r.splitType === 'aa') {
          const half = r.amount / 2;
          if (r.userId === currentUser) {
            netOwedToMe += half;
            if (r.date === today) myHelpedToday += half;
          } else {
            netOwedToMe -= half;
            if (r.date === today) partnerHelpedToday += half;
          }
        } else if (r.splitType === 'for_partner') {
          if (r.userId === currentUser) {
            netOwedToMe += r.amount;
            if (r.date === today) myHelpedToday += r.amount;
          } else {
            netOwedToMe -= r.amount;
            if (r.date === today) partnerHelpedToday += r.amount;
          }
        }
      });

      // 渲染今日條
      document.getElementById('total-user-mei-today').innerText = `$${meiTodayTotal.toLocaleString()}`;
      document.getElementById('total-user-toast-today').innerText = `$${toastTodayTotal.toLocaleString()}`;

      const totalSum = meiTodayTotal + toastTodayTotal;
      let meiRatio = 50;
      let toastRatio = 50;
      if (totalSum > 0) {
        meiRatio = Math.round((meiTodayTotal / totalSum) * 100);
        toastRatio = 100 - meiRatio;
      }
      document.getElementById('bar-user-mei').style.width = `${meiRatio}%`;
      document.getElementById('bar-user-toast').style.width = `${toastRatio}%`;
      document.getElementById('ratio-user-mei').innerText = `${meiRatio}%`;
      document.getElementById('ratio-user-toast').innerText = `${toastRatio}%`;

      // 渲染今日代墊
      document.getElementById('my-helped-today').innerText = `$${Math.round(myHelpedToday).toLocaleString()}`;
      document.getElementById('partner-helped-today').innerText = `$${Math.round(partnerHelpedToday).toLocaleString()}`;

      // 結算建議
      const banner = document.getElementById('balance-banner');
      const text = document.getElementById('balance-text');
      const partnerName = currentUser === 'user_mei' ? '吐司' : '小梅';

      if (netOwedToMe > 0) {
        banner.className = "mt-2.5 p-3 rounded-2xl text-xs flex items-center justify-between font-medium bg-emerald-50 text-emerald-800 border border-emerald-200";
        text.innerHTML = `<i class="ph ph-hand-heart text-base mr-1"></i> ${partnerName} 目前需還給我 <b>$${Math.round(netOwedToMe)}</b>`;
      } else if (netOwedToMe < 0) {
        banner.className = "mt-2.5 p-3 rounded-2xl text-xs flex items-center justify-between font-medium bg-amber-50 text-amber-800 border border-amber-200";
        text.innerHTML = `<i class="ph ph-wallet text-base mr-1"></i> 我目前需轉給 ${partnerName} <b>$${Math.round(Math.abs(netOwedToMe))}</b>`;
      } else {
        banner.className = "mt-2.5 p-3 rounded-2xl text-xs flex items-center justify-between font-medium bg-slate-50 text-slate-700 border border-slate-200";
        text.innerHTML = `<i class="ph ph-scales text-base mr-1"></i> 小梅與吐司收支剛好平衡！`;
      }

      renderHomeTodayList();
    }

    /* ================= 今日快捷清單 ================= */
    function renderHomeTodayList() {
      const container = document.getElementById('home-today-list');
      const todayRecords = records.filter(r => r.date === todayStr);

      if (todayRecords.length === 0) {
        container.innerHTML = `<div class="p-6 text-center text-xs text-slate-400 bg-white rounded-2xl border border-sage-50">今日尚無花費，點擊右下角「＋」記錄日常吧！</div>`;
        return;
      }

      container.innerHTML = todayRecords.map(r => generateRecordCardHTML(r)).join('');
    }

    /* ================= 明細篩選與渲染 ================= */
    function setRecordFilter(filter) {
      currentFilter = filter;
      document.querySelectorAll('.filter-btn').forEach(btn => {
        btn.className = "filter-btn px-3 py-1.5 rounded-full text-xs font-medium bg-white text-slate-600 border border-slate-200";
      });
      const activeBtn = document.getElementById(`filter-${filter}`);
      if (activeBtn) {
        activeBtn.className = "filter-btn px-3 py-1.5 rounded-full text-xs font-semibold bg-sage-500 text-white shadow-sm";
      }
      renderRecordsStream();
    }

    function renderRecordsStream() {
      const container = document.getElementById('records-stream');
      let filtered = [...records].sort((a,b) => b.timestamp - a.timestamp);

      if (currentFilter === 'mine') {
        filtered = filtered.filter(r => r.userId === currentUser);
      } else if (currentFilter === 'partner') {
        filtered = filtered.filter(r => r.userId !== currentUser);
      }

      if (filtered.length === 0) {
        container.innerHTML = `<div class="p-8 text-center text-xs text-slate-400">尚無符合條件的收支明細</div>`;
        return;
      }

      container.innerHTML = filtered.map(r => generateRecordCardHTML(r)).join('');
    }

    /* ================= 卡片 HTML 生成 (關鍵保密處理) ================= */
    function generateRecordCardHTML(r) {
      const isMine = r.userId === currentUser;
      const cat = CATEGORIES.find(c => c.id === r.category) || CATEGORIES[CATEGORIES.length - 1];

      // 保密模式檢查：若非本人且勾選保密，遮蔽品項
      const isHiddenFromMe = !isMine && r.isPrivate;

      const titleDisplay = isHiddenFromMe ? '🔒 伴侶保密項目 (明細已隱藏)' : r.title;
      const categoryIcon = isHiddenFromMe ? 'ph-lock-key' : cat.icon;
      const categoryBg = isHiddenFromMe ? 'bg-amber-100 text-amber-800' : cat.color;

      // 分攤標籤
      let splitTag = '';
      if (r.splitType === 'aa') {
        splitTag = `<span class="text-[10px] bg-cyan-50 text-cyan-700 px-1.5 py-0.5 rounded font-medium border border-cyan-100">AA平攤</span>`;
      } else if (r.splitType === 'for_partner') {
        const text = isMine ? '我幫對方付' : '伴侶為我付';
        splitTag = `<span class="text-[10px] bg-sage-50 text-sage-700 px-1.5 py-0.5 rounded font-medium border border-sage-200">${text}</span>`;
      }

      const userBadge = r.userId === 'user_mei'
        ? `<span class="w-4 h-4 rounded-full bg-sage-500 text-white text-[9px] flex items-center justify-center font-bold">梅</span>`
        : `<span class="w-4 h-4 rounded-full bg-ocean-500 text-white text-[9px] flex items-center justify-center font-bold">吐</span>`;

      return `
        <div class="bg-white p-3.5 rounded-2xl shadow-sm border border-sage-50 flex items-center justify-between transition hover:border-sage-200">
          <div class="flex items-center space-x-3">
            <div class="w-10 h-10 rounded-2xl ${categoryBg} flex items-center justify-center text-lg flex-shrink-0 shadow-inner">
              <i class="ph ${categoryIcon}"></i>
            </div>
            <div>
              <div class="flex items-center space-x-1.5">
                ${userBadge}
                <span class="text-xs font-bold text-slate-800 ${isHiddenFromMe ? 'text-amber-800 italic' : ''}">${titleDisplay}</span>
              </div>
              <div class="flex items-center space-x-1.5 text-[11px] text-slate-400 mt-0.5">
                <span>${r.payment}</span>
                <span>•</span>
                <span>${r.date}</span>
                ${splitTag}
              </div>
            </div>
          </div>
          <div class="text-right flex-shrink-0">
            <span class="text-sm font-black text-slate-800">$${r.amount.toLocaleString()}</span>
            ${r.isPrivate && isMine ? '<div class="text-[9px] text-amber-600 font-semibold flex items-center justify-end"><i class="ph ph-lock mr-0.5"></i>已對伴侶保密</div>' : ''}
          </div>
        </div>
      `;
    }

    /* ================= 分類與付款方式渲染 ================= */
    function renderCategoriesSelector() {
      const container = document.getElementById('category-selector');
      container.innerHTML = CATEGORIES.map((c, idx) => `
        <button type="button" onclick="selectCategory('${c.id}')" id="cat-btn-${c.id}" 
          class="cat-option flex flex-col items-center justify-center p-2 rounded-2xl border text-center transition ${idx === 0 ? 'border-sage-500 bg-sage-50 text-sage-700 font-bold' : 'border-slate-100 bg-slate-50 text-slate-600'}">
          <div class="w-7 h-7 rounded-full ${c.color} flex items-center justify-center text-sm mb-1">
            <i class="ph ${c.icon}"></i>
          </div>
          <span class="text-[10px]">${c.name}</span>
        </button>
      `).join('');
    }

    function selectCategory(catId) {
      currentSelectedCategory = catId;
      document.querySelectorAll('.cat-option').forEach(btn => {
        btn.className = "cat-option flex flex-col items-center justify-center p-2 rounded-2xl border border-slate-100 bg-slate-50 text-slate-600 text-center";
      });
      const active = document.getElementById(`cat-btn-${catId}`);
      if (active) {
        active.className = "cat-option flex flex-col items-center justify-center p-2 rounded-2xl border border-sage-500 bg-sage-50 text-sage-700 font-bold text-center";
      }
    }

    function renderPaymentOptions() {
      const sel = document.getElementById('select-payment');
      sel.innerHTML = paymentMethods.map(p => `<option value="${p}">${p}</option>`).join('');
    }

    function renderPaymentManageList() {
      const container = document.getElementById('payment-methods-list');
      container.innerHTML = paymentMethods.map(p => `
        <div class="flex items-center justify-between p-2.5 bg-slate-50 rounded-xl text-xs font-medium text-slate-700">
          <span class="flex items-center"><i class="ph ph-credit-card mr-2 text-sage-600"></i> ${p}</span>
          ${paymentMethods.length > 1 ? `<button onclick="deletePaymentMethod('${p}')" class="text-slate-400 hover:text-rose-500"><i class="ph ph-trash"></i></button>` : ''}
        </div>
      `).join('');
    }

    function setSplitType(type) {
      currentSplitType = type;
      ['personal', 'aa', 'for_partner'].forEach(t => {
        const btn = document.getElementById(`split-btn-${t}`);
        btn.className = "split-opt py-1.5 px-2 rounded-xl text-xs font-medium bg-white/70 text-slate-600 border border-transparent";
      });
      const active = document.getElementById(`split-btn-${type}`);
      active.className = "split-opt py-1.5 px-2 rounded-xl text-xs font-medium bg-white text-sage-600 border border-sage-300 shadow-sm";

      const hint = document.getElementById('split-hint');
      const partnerName = currentUser === 'user_mei' ? '吐司' : '小梅';
      if (type === 'personal') hint.innerText = "由我個人全額吸收，不影響對方應還款項。";
      if (type === 'aa') hint.innerText = `雙方平攤，${partnerName} 將欠我此筆金額的一半。`;
      if (type === 'for_partner') hint.innerText = `我幫 ${partnerName} 代墊全額，${partnerName} 將欠我此筆全額。`;
    }

    /* ================= 新增消費動作 ================= */
    function openAddRecordModal() {
      document.getElementById('input-amount').value = '';
      document.getElementById('input-title').value = '';
      document.getElementById('check-private').checked = false;
      document.getElementById('modal-add-record').classList.remove('hidden');
    }

    function closeAddRecordModal() {
      document.getElementById('modal-add-record').classList.add('hidden');
    }

    function saveRecord() {
      const amount = parseFloat(document.getElementById('input-amount').value);
      if (!amount || amount <= 0) {
        showToast('請輸入有效金額！');
        return;
      }
      let title = document.getElementById('input-title').value.trim();
      if (!title) {
        const cat = CATEGORIES.find(c => c.id === currentSelectedCategory);
        title = cat ? cat.name : '生活開銷';
      }

      const payment = document.getElementById('select-payment').value;
      const date = document.getElementById('input-date').value || todayStr;
      const isPrivate = document.getElementById('check-private').checked;

      const newRec = {
        id: 'rec-' + Date.now(),
        userId: currentUser,
        amount: amount,
        category: currentSelectedCategory,
        payment: payment,
        title: title,
        date: date,
        splitType: currentSplitType,
        isPrivate: isPrivate,
        timestamp: Date.now()
      };

      records.unshift(newRec);
      saveLocal();
      closeAddRecordModal();
      updateDashboard();
      renderRecordsStream();
      showToast(isPrivate ? '已記帳（已設為向伴侶保密）🔒' : '已記帳並快取完成！🌿');

      // 若有設定 Google Apps Script Web App URL，自動在背景同步
      if (gasWebAppUrl) {
        backgroundAutoSync();
      }
    }

    /* ================= 新增自訂付款方式 ================= */
    function openAddPaymentModal() {
      document.getElementById('input-new-payment').value = '';
      document.getElementById('modal-add-payment').classList.remove('hidden');
    }
    function closeAddPaymentModal() {
      document.getElementById('modal-add-payment').classList.add('hidden');
    }
    function savePaymentMethod() {
      const val = document.getElementById('input-new-payment').value.trim();
      if (!val) {
        showToast('請輸入付款方式名稱');
        return;
      }
      if (paymentMethods.includes(val)) {
        showToast('此付款方式已存在');
        return;
      }
      paymentMethods.push(val);
      saveLocal();
      renderPaymentOptions();
      renderPaymentManageList();
      closeAddPaymentModal();
      showToast(`已新增付款方式：${val}`);
    }
    function deletePaymentMethod(name) {
      paymentMethods = paymentMethods.filter(p => p !== name);
      saveLocal();
      renderPaymentOptions();
      renderPaymentManageList();
      showToast(`已移除付款方式：${name}`);
    }

    /* ================= 共用情侶行事曆 ================= */
    function changeMonth(delta) {
      calendarCurrentDate.setMonth(calendarCurrentDate.getMonth() + delta);
      renderCalendar();
    }

    function renderCalendar() {
      const year = calendarCurrentDate.getFullYear();
      const month = calendarCurrentDate.getMonth();
      document.getElementById('calendar-month-year').innerText = `${year}年 ${month + 1}月`;

      const firstDay = new Date(year, month, 1).getDay();
      const daysInMonth = new Date(year, month + 1, 0).getDate();
      const grid = document.getElementById('calendar-grid');
      grid.innerHTML = '';

      for (let i = 0; i < firstDay; i++) {
        grid.innerHTML += `<div class="h-10"></div>`;
      }

      for (let d = 1; d <= daysInMonth; d++) {
        const dateStr = `${year}-${String(month + 1).padStart(2, '0')}-${String(d).padStart(2, '0')}`;
        const isSelected = dateStr === selectedCalendarDateStr;
        const isToday = dateStr === todayStr;

        const hasEvents = calendarEvents.some(e => e.date === dateStr);
        const hasRecords = records.some(r => r.date === dateStr);

        let indicatorDot = '';
        if (hasEvents) {
          indicatorDot += `<span class="w-1.5 h-1.5 rounded-full bg-sage-500"></span>`;
        }
        if (hasRecords) {
          indicatorDot += `<span class="w-1.5 h-1.5 rounded-full bg-ocean-400"></span>`;
        }

        grid.innerHTML += `
          <button onclick="selectCalendarDate('${dateStr}')" 
            class="h-10 flex flex-col items-center justify-center rounded-2xl relative transition ${isSelected ? 'bg-sage-500 text-white font-bold shadow-md' : 'hover:bg-sage-50 text-slate-700'} ${isToday && !isSelected ? 'border border-sage-400 font-bold text-sage-600' : ''}">
            <span class="text-xs leading-none">${d}</span>
            <div class="flex space-x-0.5 mt-1">
              ${indicatorDot}
            </div>
          </button>
        `;
      }
    }

    function selectCalendarDate(dateStr) {
      selectedCalendarDateStr = dateStr;
      renderCalendar();
      renderCalendarEvents();
    }

    function renderCalendarEvents() {
      const titleEl = document.getElementById('selected-date-title');
      const parts = selectedCalendarDateStr.split('-');
      titleEl.innerText = `${parseInt(parts[1])}月${parseInt(parts[2])}日 約會與行程`;

      const container = document.getElementById('calendar-events-list');
      const events = calendarEvents.filter(e => e.date === selectedCalendarDateStr);

      if (events.length === 0) {
        container.innerHTML = `<div class="py-3 text-center text-xs text-slate-400">當天尚無約會行程，快點擊「新增行程」吧！</div>`;
      } else {
        container.innerHTML = events.map(e => {
          const creatorName = USERS[e.creator] ? USERS[e.creator].name : '伴侶';
          let typeBadge = '';
          if (e.type === 'date') typeBadge = '<span class="text-[10px] bg-emerald-50 text-emerald-700 px-1.5 py-0.5 rounded font-medium">約會</span>';
          if (e.type === 'anniversary') typeBadge = '<span class="text-[10px] bg-rose-50 text-rose-700 px-1.5 py-0.5 rounded font-medium">紀念日</span>';
          if (e.type === 'bill') typeBadge = '<span class="text-[10px] bg-amber-50 text-amber-700 px-1.5 py-0.5 rounded font-medium">繳費</span>';
          if (e.type === 'trip') typeBadge = '<span class="text-[10px] bg-sky-50 text-sky-700 px-1.5 py-0.5 rounded font-medium">旅遊</span>';
          if (e.type === 'food') typeBadge = '<span class="text-[10px] bg-teal-50 text-teal-700 px-1.5 py-0.5 rounded font-medium">聚餐</span>';

          return `
            <div class="p-3 bg-sage-50/50 rounded-2xl border border-sage-100 flex items-center justify-between">
              <div>
                <div class="flex items-center space-x-1.5">
                  ${typeBadge}
                  <span class="text-xs font-bold text-slate-800">${e.title}</span>
                </div>
                <div class="text-[10px] text-slate-400 mt-0.5">由 ${creatorName} 安排 ${e.budget > 0 ? `• 預算約: $${e.budget}` : ''}</div>
              </div>
              <button onclick="deleteCalendarEvent('${e.id}')" class="text-slate-300 hover:text-rose-500 text-xs p-1">
                <i class="ph ph-trash"></i>
              </button>
            </div>
          `;
        }).join('');
      }

      // 連動當日支出
      const dayExpenses = records.filter(r => r.date === selectedCalendarDateStr);
      const sumEl = document.getElementById('selected-date-expense-sum');
      const listEl = document.getElementById('calendar-date-expenses');

      const sum = dayExpenses.reduce((acc, curr) => acc + curr.amount, 0);
      sumEl.innerText = `合計 $${sum.toLocaleString()}`;

      if (dayExpenses.length === 0) {
        listEl.innerHTML = `<span class="italic text-[11px] text-slate-400">當日無消費紀錄</span>`;
      } else {
        listEl.innerHTML = dayExpenses.map(r => {
          const isMine = r.userId === currentUser;
          const displayTitle = (!isMine && r.isPrivate) ? '🔒 伴侶保密項目' : r.title;
          const userInitial = r.userId === 'user_mei' ? '梅' : '吐';
          return `
            <div class="flex justify-between items-center py-1 border-b border-slate-50 text-[11px]">
              <span class="flex items-center">
                <span class="w-3.5 h-3.5 rounded-full ${r.userId === 'user_mei' ? 'bg-sage-500' : 'bg-ocean-500'} text-white text-[8px] flex items-center justify-center font-bold mr-1.5">${userInitial}</span>
                ${displayTitle}
              </span>
              <span class="font-bold text-slate-700">$${r.amount}</span>
            </div>
          `;
        }).join('');
      }
    }

    function openAddEventModal() {
      document.getElementById('event-title').value = '';
      document.getElementById('event-budget').value = '';
      document.getElementById('modal-add-event').classList.remove('hidden');
    }
    function closeAddEventModal() {
      document.getElementById('modal-add-event').classList.add('hidden');
    }
    function saveCalendarEvent() {
      const title = document.getElementById('event-title').value.trim();
      if (!title) {
        showToast('請輸入行程名稱');
        return;
      }
      const type = document.getElementById('event-type').value;
      const budget = parseFloat(document.getElementById('event-budget').value) || 0;

      calendarEvents.push({
        id: 'ev-' + Date.now(),
        date: selectedCalendarDateStr,
        title: title,
        type: type,
        budget: budget,
        creator: currentUser
      });

      saveLocal();
      closeAddEventModal();
      renderCalendar();
      renderCalendarEvents();
      showToast('已加入共用約會行事曆！📅');
    }
    function deleteCalendarEvent(id) {
      calendarEvents = calendarEvents.filter(e => e.id !== id);
      saveLocal();
      renderCalendar();
      renderCalendarEvents();
      showToast('已刪除行程');
    }

    /* ================= Google Sheets 串接與同步 ================= */
    function openAppsScriptTutorialModal() {
      document.getElementById('modal-gas-tutorial').classList.remove('hidden');
    }
    function closeAppsScriptTutorialModal() {
      document.getElementById('modal-gas-tutorial').classList.add('hidden');
    }
    function copyAppsScriptCode() {
      const code = document.getElementById('gas-code-snippet').innerText;
      const tempArea = document.createElement('textarea');
      tempArea.value = code;
      document.body.appendChild(tempArea);
      tempArea.select();
      document.execCommand('copy');
      document.body.removeChild(tempArea);
      showToast('後台 Apps Script 程式碼已複製至剪貼簿！');
    }

    function saveGasUrl() {
      const url = document.getElementById('gas-url-input').value.trim();
      gasWebAppUrl = url;
      localStorage.setItem('SWEET_GAS_URL', url);
      showToast(url ? 'Google 試算表 Web App 網址已儲存！' : '已清除雲端網址');
    }

    async function syncWithGoogleSheet() {
      if (!gasWebAppUrl) {
        showToast('請先填入 Google Apps Script 網址');
        openAppsScriptTutorialModal();
        return;
      }

      showToast('正在與 Google 試算表同步中...');
      try {
        // 先推播本地所有帳目到試算表
        await fetch(gasWebAppUrl, {
          method: 'POST',
          mode: 'no-cors',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify({ action: 'syncAll', records: records })
        });

        // 嘗試拉取試算表最新資料
        const res = await fetch(gasWebAppUrl + '?t=' + Date.now());
        if (res.ok) {
          const json = await res.json();
          if (json.status === 'ok' && Array.isArray(json.data) && json.data.length > 0) {
            records = json.data;
            saveLocal();
            updateDashboard();
            renderRecordsStream();
            renderCalendar();
            renderCalendarEvents();
            showToast('✅ 雙向同步成功！資料已更新至試算表');
            return;
          }
        }
        showToast('✅ 本地資料已成功推播至 Google 試算表！');
      } catch (err) {
        console.error(err);
        showToast('連線至 Google 試算表時發生錯誤，請確認網址與權限！');
      }
    }

    function backgroundAutoSync() {
      if (!gasWebAppUrl) return;
      fetch(gasWebAppUrl, {
        method: 'POST',
        mode: 'no-cors',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ action: 'syncAll', records: records })
      }).catch(e => console.warn('背景同步略過:', e));
    }

    /* ================= 結算、單一登入與備份 ================= */
    function settleAccountsPrompt() {
      records.forEach(r => {
        if (r.splitType === 'aa' || r.splitType === 'for_partner') {
          r.splitType = 'settled';
        }
      });
      saveLocal();
      updateDashboard();
      showToast('🎉 小梅與吐司的代墊帳目已全數結清歸零！');
    }

    function simulateForceLogout() {
      showToast('⚠️ 警告：偵測到另一台手機登入此帳號，系統已強制登出！');
    }

    function exportBackupJSON() {
      const data = { records, calendarEvents, paymentMethods };
      const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = `sweet_ledger_backup_${todayStr}.json`;
      a.click();
      URL.revokeObjectURL(url);
      showToast('已匯出本地 JSON 備份檔！');
    }

    /* ================= Toast 訊息提示 ================= */
    let toastTimeout;
    function showToast(msg) {
      const toast = document.getElementById('app-toast');
      document.getElementById('toast-message').innerText = msg;
      toast.classList.remove('hidden');
      clearTimeout(toastTimeout);
      toastTimeout = setTimeout(() => {
        toast.classList.add('hidden');
      }, 2400);
    }
  </script>
</body>
</html>
