# Gh-p-g-c-ng
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Cánh Cửa Nghiệm - Trò Chơi Học Tập</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/tone/14.8.49/Tone.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f8fafc;
            color: #0f172a;
            -webkit-tap-highlight-color: transparent;
        }

        /* Tùy chỉnh thanh cuộn */
        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: #f1f5f9; }
        ::-webkit-scrollbar-thumb { background: #94a3b8; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #64748b; }

        /* Toán học - Phân số và ký hiệu */
        .math { font-family: 'Times New Roman', Times, serif; font-size: 1.1em; }
        .frac {
            display: inline-flex; flex-direction: column; align-items: center;
            vertical-align: middle; margin: 0 0.15rem; line-height: 1.1; font-family: 'Times New Roman', Times, serif;
        }
        .frac > span.num { border-bottom: 1.5px solid currentColor; padding: 0 0.2rem; width: 100%; text-align: center; }
        .frac > span.den { padding: 0 0.2rem; }
        .sym { font-style: normal; margin: 0 0.1rem; }

        /* Giao diện Thẻ & Tương tác */
        .btn-touch { min-height: 48px; display: inline-flex; align-items: center; justify-content: center; }
        
        .option-card {
            cursor: pointer; transition: all 0.2s ease; user-select: none; min-height: 48px;
            border: 2px solid #e2e8f0; background-color: #ffffff; border-radius: 0.5rem;
            display: flex; items-center; padding: 0.75rem 1rem; color: #334155; font-weight: 500;
        }
        .option-card:hover { border-color: #cbd5e1; background-color: #f8fafc; }
        .option-card.selected { border-color: #ca8a04; background-color: #fefce8; color: #854d0e; box-shadow: 0 2px 4px rgba(202, 138, 4, 0.1); }
        .option-card.locked { opacity: 0.7; pointer-events: none; }
        
        .status-mark { display: none; margin-left: auto; flex-shrink: 0; }
        .option-card.error-mark { border-color: #ef4444; background-color: #fef2f2; }
        .option-card.error-mark .status-mark.error { display: block; color: #ef4444; }
        .option-card.success-mark { border-color: #22c55e; background-color: #f0fdf4; }
        .option-card.success-mark .status-mark.success { display: block; color: #22c55e; }

        /* Modal Overlay */
        .modal-overlay { background-color: rgba(15, 23, 42, 0.75); backdrop-filter: blur(2px); }

        /* Chủ đề cánh cửa (Door theme) */
        .door-theme { background: linear-gradient(to bottom, #1e3a8a, #0f172a); color: white; border: 2px solid #ca8a04; }
        .door-panel { background: rgba(255, 255, 255, 0.05); border: 1px solid rgba(202, 138, 4, 0.3); }
        
    </style>
</head>
<body class="min-h-screen flex flex-col">

    <!-- HEADER -->
    <header class="bg-slate-900 text-white shadow-md sticky top-0 z-40 border-b-2 border-yellow-600">
        <div class="max-w-5xl mx-auto px-4 py-3 flex flex-col sm:flex-row justify-between items-center gap-3">
            <div class="flex items-center w-full sm:w-auto justify-between">
                <div class="flex items-center gap-2">
                    <svg class="h-6 w-6 text-yellow-500" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" /></svg>
                    <h1 class="text-lg font-bold tracking-tight uppercase text-yellow-50">Cánh Cửa Nghiệm</h1>
                </div>
                <div class="flex sm:hidden gap-2">
                    <button onclick="toggleAudio()" class="btn-audio text-slate-300 hover:text-white p-2 rounded bg-slate-800"><svg class="h-5 w-5 icon-audio" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z" clip-rule="evenodd" /><path class="audio-off hidden" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2" /></svg></button>
                    <button onclick="openHelp()" class="text-slate-300 hover:text-white p-2 rounded bg-slate-800"><svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8.228 9c.549-1.165 2.03-2 3.772-2 2.21 0 4 1.343 4 3 0 1.4-1.278 2.575-3.006 2.907-.542.104-.994.54-.994 1.093m0 3h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" /></svg></button>
                </div>
            </div>
            
            <div id="stats-bar" class="hidden w-full sm:w-auto flex flex-wrap justify-center gap-2 text-sm font-medium">
                <div class="bg-slate-800 border border-slate-700 px-3 py-1.5 rounded flex items-center gap-1.5"><span class="text-slate-400">Chặng:</span><span class="text-yellow-400 font-bold"><span id="stat-stage">1</span>/3</span></div>
                <div class="bg-slate-800 border border-slate-700 px-3 py-1.5 rounded flex items-center gap-1.5"><span class="text-slate-400">Đã xong:</span><span class="text-green-400 font-bold" id="stat-correct">0</span></div>
                <div class="bg-slate-800 border border-slate-700 px-3 py-1.5 rounded flex items-center gap-1.5"><span class="text-slate-400">Điểm:</span><span class="text-white font-bold"><span id="stat-score">0</span>/30</span></div>
            </div>

            <div class="hidden sm:flex items-center gap-2">
                <button onclick="toggleAudio()" class="btn-audio text-slate-300 hover:text-white px-3 py-1.5 rounded bg-slate-800 border border-slate-700 text-sm flex gap-1 items-center transition-colors"><svg class="h-4 w-4 icon-audio" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z" clip-rule="evenodd" /><path class="audio-off hidden" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2" /></svg> Âm thanh</button>
                <button onclick="openHelp()" class="text-slate-300 hover:text-white px-3 py-1.5 rounded bg-slate-800 border border-slate-700 text-sm flex gap-1 items-center transition-colors"><svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8.228 9c.549-1.165 2.03-2 3.772-2 2.21 0 4 1.343 4 3 0 1.4-1.278 2.575-3.006 2.907-.542.104-.994.54-.994 1.093m0 3h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" /></svg> Hướng dẫn</button>
                <button id="btn-header-restart" onclick="requestRestart()" class="hidden text-red-300 hover:text-red-100 px-3 py-1.5 rounded bg-slate-800 border border-slate-700 text-sm flex gap-1 items-center transition-colors"><svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" /></svg> Chơi lại</button>
            </div>
        </div>
    </header>

    <main class="flex-grow flex justify-center p-3 sm:p-6 w-full max-w-4xl mx-auto">
        
        <!-- MÀN HÌNH GIỚI THIỆU -->
        <div id="screen-intro" class="w-full bg-white rounded-xl shadow-sm border border-slate-200 p-6 md:p-10 self-start mt-4">
            <div class="text-center space-y-4 mb-8">
                <div class="inline-flex justify-center items-center w-16 h-16 bg-yellow-50 border border-yellow-200 rounded-full mb-2">
                    <svg class="w-8 h-8 text-yellow-600" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" /></svg>
                </div>
                <h2 class="text-2xl md:text-3xl font-bold text-slate-800 uppercase tracking-tight">Cánh Cửa Nghiệm</h2>
                <p class="text-slate-600 font-medium">Kiểm tra điều kiện – Chọn đúng họ nghiệm – Mở cửa chính xác.</p>
            </div>

            <div class="bg-slate-50 border-l-4 border-yellow-500 p-5 rounded-r-lg mb-8 text-slate-700 text-sm sm:text-base leading-relaxed">
                <p class="italic mb-3">“Em đang đứng trước ba cánh cửa toán học. Mỗi cánh cửa có một bộ khóa. Hãy kiểm tra điều kiện, chọn đúng quy luật nghiệm và xác định những giá trị được phép để mở cửa.”</p>
                <p class="font-bold text-slate-800 mb-1">Mục tiêu 3 chặng:</p>
                <ul class="list-disc pl-5 space-y-1">
                    <li>Chặng 1: Cửa tồn tại.</li>
                    <li>Chặng 2: Cửa họ nghiệm.</li>
                    <li>Chặng 3: Cửa điều kiện.</li>
                </ul>
            </div>

            <div class="space-y-4 max-w-sm mx-auto">
                <div>
                    <label class="block text-sm font-semibold text-slate-700 mb-1">Mã học sinh (Không bắt buộc)</label>
                    <input type="text" id="student-id" placeholder="Nhập mã..." class="w-full px-4 py-3 border border-slate-300 rounded-lg focus:ring-2 focus:ring-yellow-500 outline-none transition">
                </div>
                <button onclick="startGame()" class="w-full bg-slate-900 hover:bg-slate-800 text-white font-bold btn-touch rounded-lg shadow-sm text-lg transition-colors border-b-4 border-slate-700 active:border-b-0 active:translate-y-1">Bắt đầu mở khóa</button>
            </div>
            <p class="text-center text-xs text-slate-400 mt-6">Điểm trò chơi dùng để ghi nhận hoạt động luyện tập, không thay thế điểm kiểm tra môn học.</p>
        </div>

        <!-- MÀN HÌNH TRÒ CHƠI -->
        <div id="screen-game" class="hidden w-full flex flex-col gap-6 items-start">
            <div class="w-full door-theme rounded-xl shadow-lg overflow-hidden relative">
                <!-- Status Badge Overlay -->
                <div id="task-locked-overlay" class="absolute inset-0 bg-slate-900/80 backdrop-blur-sm z-20 flex flex-col items-center justify-center hidden">
                    <svg class="h-16 w-16 text-green-400 mb-2" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 11V7a4 4 0 118 0m-4 8v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2z" /></svg>
                    <h3 class="text-2xl font-bold text-white mb-4 uppercase">Cửa đã mở</h3>
                    <div id="continue-container" class="w-full max-w-xs px-4">
                        <button id="btn-next" onclick="nextTask()" class="w-full bg-yellow-500 hover:bg-yellow-400 text-slate-900 font-bold btn-touch rounded-lg transition-all shadow-lg text-lg uppercase">Tiếp tục</button>
                    </div>
                    <div id="btn-end-early-container" class="hidden mt-4">
                        <button onclick="confirmSkipBackup()" class="text-slate-300 hover:text-white underline font-medium text-sm p-2">Ghi nhận cần luyện thêm và chuyển chặng</button>
                    </div>
                </div>

                <div class="bg-slate-900 px-5 py-4 border-b border-slate-700 flex justify-between items-center">
                    <div class="flex items-center gap-2">
                        <span id="task-badge" class="bg-yellow-500 text-slate-900 text-xs font-bold px-2 py-1 rounded uppercase">Chính</span>
                        <span id="task-id" class="font-mono text-sm font-semibold text-slate-300">CN01</span>
                    </div>
                    <h3 id="task-title" class="font-bold text-yellow-400">Tên nhiệm vụ</h3>
                </div>
                
                <div class="p-4 sm:p-6 md:p-8">
                    <div class="bg-slate-800 border border-slate-600 rounded-lg p-4 sm:p-5 mb-6 text-slate-200 text-base sm:text-lg">
                        <p id="task-question" class="font-medium leading-relaxed"></p>
                    </div>
                    
                    <!-- Vùng Đích & Thẻ -->
                    <div id="interactive-area" class="space-y-6"></div>

                    <!-- Gợi ý -->
                    <div id="hints-area" class="hidden space-y-3 mt-6 border-t border-slate-700 pt-4"></div>

                    <!-- Vùng phản hồi -->
                    <div id="feedback-area" class="hidden mt-4 p-4 rounded-lg flex items-start gap-3 text-sm sm:text-base font-medium transition-all"></div>

                    <!-- Lời giải -->
                    <div id="solution-area" class="hidden mt-6 bg-slate-800 border border-slate-600 rounded-lg p-4 sm:p-5">
                        <h4 class="font-bold text-green-400 mb-3 flex items-center gap-2"><svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" /></svg> Lời giải chi tiết:</h4>
                        <div id="solution-text" class="text-slate-200 space-y-2 leading-relaxed"></div>
                    </div>

                    <!-- Action Buttons -->
                    <div id="action-buttons" class="mt-6 pt-5 border-t border-slate-700 flex flex-col sm:flex-row gap-3">
                        <button id="btn-submit" onclick="submitAnswer()" class="flex-1 bg-yellow-500 hover:bg-yellow-400 text-slate-900 font-bold btn-touch rounded-lg transition-colors shadow-sm text-lg">Nộp bài</button>
                        <button id="btn-hint" onclick="requestHint()" class="sm:w-auto bg-slate-700 hover:bg-slate-600 text-yellow-400 border border-slate-500 font-semibold btn-touch px-4 rounded-lg transition-colors flex gap-2 items-center">
                            Xin gợi ý (<span id="hint-count">0/3</span>)
                        </button>
                        <button id="btn-show-solution" onclick="confirmShowSolution()" class="hidden sm:w-auto bg-slate-800 border border-slate-600 text-slate-300 font-semibold btn-touch px-4 rounded-lg hover:bg-slate-700 transition-colors">Xem lời giải</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- MÀN HÌNH TỔNG KẾT -->
        <div id="screen-result" class="hidden w-full bg-white rounded-xl shadow-sm border border-slate-200 p-6 md:p-10 self-start mt-4">
            <div class="text-center mb-8">
                <h2 class="text-3xl font-bold text-slate-800 uppercase tracking-tight mb-2">Kết quả lượt chơi</h2>
                <p id="res-student" class="text-slate-600 font-medium"></p>
                
                <div class="mt-6 flex flex-wrap justify-center gap-4">
                    <div class="bg-yellow-50 border border-yellow-200 px-6 py-3 rounded-lg text-center shadow-sm min-w-[120px]">
                        <div class="text-xs font-bold text-yellow-600 uppercase mb-1">Điểm số</div>
                        <div class="text-2xl font-black text-yellow-700"><span id="res-score">0</span>/30</div>
                    </div>
                    <div class="bg-green-50 border border-green-200 px-6 py-3 rounded-lg text-center shadow-sm min-w-[120px]">
                        <div class="text-xs font-bold text-green-600 uppercase mb-1">Đã giải đúng</div>
                        <div class="text-2xl font-black text-green-700"><span id="res-correct-final">0</span>/3</div>
                    </div>
                </div>
            </div>

            <!-- Visual Doors -->
            <div class="flex justify-center gap-4 mb-8">
                <div id="door-1" class="w-16 h-24 rounded-t-md border-2 border-slate-300 bg-slate-100 flex items-center justify-center opacity-50"><svg class="h-6 w-6 text-slate-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" /></svg></div>
                <div id="door-2" class="w-16 h-24 rounded-t-md border-2 border-slate-300 bg-slate-100 flex items-center justify-center opacity-50"><svg class="h-6 w-6 text-slate-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" /></svg></div>
                <div id="door-3" class="w-16 h-24 rounded-t-md border-2 border-slate-300 bg-slate-100 flex items-center justify-center opacity-50"><svg class="h-6 w-6 text-slate-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" /></svg></div>
            </div>

            <div id="badges-area" class="flex flex-wrap justify-center gap-3 mb-8"></div>

            <h3 class="font-bold text-slate-800 text-lg border-b pb-2 mb-4">Chi tiết nhiệm vụ</h3>
            <div class="overflow-x-auto mb-8 border border-slate-200 rounded-lg">
                <table class="w-full text-left text-sm whitespace-nowrap">
                    <thead class="bg-slate-50 text-slate-600">
                        <tr>
                            <th class="p-3 font-semibold border-b">Nhiệm vụ</th>
                            <th class="p-3 font-semibold border-b text-center">Số lần nộp</th>
                            <th class="p-3 font-semibold border-b text-center">Gợi ý</th>
                            <th class="p-3 font-semibold border-b">Trạng thái</th>
                        </tr>
                    </thead>
                    <tbody id="res-table-body" class="divide-y divide-slate-100"></tbody>
                </table>
            </div>

            <div class="bg-slate-50 border border-slate-200 rounded-lg p-5 mb-6">
                <h4 class="font-bold text-slate-800 mb-2">Nhận xét:</h4>
                <div id="res-feedback" class="text-slate-700 text-sm mb-4 space-y-1"></div>
                
                <hr class="border-slate-200 my-4">
                <h4 class="font-bold text-slate-800 mb-2 text-sm">Phiếu tự phản hồi (Chưa gửi giáo viên):</h4>
                <p class="text-xs text-slate-500 mb-3 italic">Quay lại bài khảo sát, kiểm tra điều kiện có nghiệm, công thức nghiệm và điều kiện xác định ở những câu liên quan.</p>
                <label class="block text-sm font-medium text-slate-700 mb-1">Trước khi kết luận một giá trị là nghiệm, em sẽ kiểm tra những gì?</label>
                <textarea id="self-reflection" rows="3" class="w-full p-3 border border-slate-300 rounded-md focus:ring-2 focus:ring-slate-800 outline-none text-sm"></textarea>
            </div>

            <p class="text-xs text-center text-slate-400 mb-6">Đây là kết quả luyện tập. Bài kiểm tra tổng hợp 45 phút được thực hiện riêng.</p>

            <div class="flex flex-col sm:flex-row gap-3">
                <button onclick="copyResult()" class="flex-1 bg-slate-900 hover:bg-slate-800 text-white font-bold btn-touch rounded-lg transition-colors flex items-center justify-center gap-2"><svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 5H6a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2v-1M8 5a2 2 0 002 2h2a2 2 0 002-2M8 5a2 2 0 012-2h2a2 2 0 012 2m0 0h2a2 2 0 012 2v3m2 4H10m0 0l3-3m-3 3l3 3" /></svg> Sao chép kết quả</button>
                <button onclick="requestRestart()" class="sm:w-1/3 bg-white border-2 border-slate-300 text-slate-700 font-bold btn-touch rounded-lg hover:bg-slate-50 transition-colors">Chơi lại</button>
            </div>
            
            <div id="copy-msg" class="hidden mt-4 p-3 rounded-lg text-sm text-center font-medium"></div>
            <textarea id="fallback-copy" class="hidden w-full mt-2 p-2 border rounded h-32 text-xs bg-slate-50" readonly></textarea>
        </div>

    </main>

    <!-- Modal Hướng Dẫn -->
    <div id="modal-help" class="fixed inset-0 modal-overlay hidden z-50 flex items-center justify-center p-4 opacity-0 transition-opacity">
        <div class="bg-white rounded-xl shadow-xl w-full max-w-md overflow-hidden transform scale-95 transition-transform flex flex-col max-h-[90vh]" id="modal-help-content">
            <div class="flex justify-between items-center p-4 border-b border-slate-100 bg-slate-50">
                <h3 class="font-bold text-lg text-slate-800">Hướng dẫn cách chơi</h3>
                <button onclick="closeHelp()" class="text-slate-400 hover:text-slate-600 bg-slate-200 rounded-full p-1 transition-colors"><svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            <div class="p-5 text-sm text-slate-700 space-y-3 overflow-y-auto">
                <p><strong>1.</strong> Đọc từng yêu cầu trên bộ khóa.</p>
                <p><strong>2.</strong> Chọn câu trả lời cho tất cả các ý.</p>
                <p><strong>3.</strong> Nhấn <span class="bg-slate-800 text-white px-1.5 rounded font-semibold">Nộp bài</span> để mở khóa.</p>
                <p><strong>4.</strong> Nếu chưa đúng, tự sửa hoặc mở gợi ý lần lượt.</p>
                <p><strong>5.</strong> Nhấn <span class="bg-yellow-100 text-yellow-800 px-1.5 rounded font-semibold">Tiếp tục</span> để sang cánh cửa tiếp theo.</p>
                <hr class="border-slate-100 my-2">
                <ul class="list-disc pl-5 space-y-1 text-slate-600">
                    <li>Có 3 nhiệm vụ chính và tối đa 3 nhiệm vụ luyện thêm.</li>
                    <li>Điểm trò chơi dùng để ghi nhận hoạt động luyện tập, không thay thế điểm kiểm tra môn học.</li>
                    <li>Mỗi ý chỉ có một đáp án đúng. Các thẻ được xáo trộn ngẫu nhiên một lần.</li>
                </ul>
            </div>
            <div class="p-4 border-t border-slate-100 text-right bg-slate-50">
                <button onclick="closeHelp()" class="bg-slate-800 text-white font-semibold px-5 py-2 rounded-lg hover:bg-slate-700 transition">Đã hiểu</button>
            </div>
        </div>
    </div>

    <!-- Modal Xác nhận -->
    <div id="modal-confirm" class="fixed inset-0 modal-overlay hidden z-50 flex items-center justify-center p-4 opacity-0 transition-opacity">
        <div class="bg-white rounded-xl shadow-xl w-full max-w-sm overflow-hidden transform scale-95 transition-transform" id="modal-confirm-content">
            <div class="p-6 text-center">
                <div id="modal-icon" class="mx-auto flex items-center justify-center h-12 w-12 rounded-full bg-amber-100 mb-4">
                    <svg class="h-6 w-6 text-amber-600" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" /></svg>
                </div>
                <h3 id="modal-title" class="text-lg font-bold text-slate-800 mb-2">Xác nhận</h3>
                <p id="modal-msg" class="text-sm text-slate-600">Nội dung</p>
            </div>
            <div class="flex border-t border-slate-100 bg-slate-50">
                <button id="btn-modal-cancel" class="flex-1 py-3 font-semibold text-slate-600 hover:bg-slate-100 border-r border-slate-100 transition-colors">Hủy</button>
                <button id="btn-modal-ok" class="flex-1 py-3 font-bold text-slate-900 hover:bg-slate-200 transition-colors">Đồng ý</button>
            </div>
        </div>
    </div>

    <script>
        // --- 1. DỮ LIỆU NHIỆM VỤ ---
        const pi = `<span class="math sym">π</span>`;
        const f = (n, d) => `<span class="frac"><span class="num">${n}</span><span class="den">${d}</span></span>`;
        const neq = `<span class="math sym">≠</span>`;
        const inn = `<span class="math sym">∈</span>`;
        const Z = `<span class="math font-bold">Z</span>`;
        const R = `<span class="math font-bold">R</span>`;

        const TASKS = [
            {
                id: "CN01", type: "main", stage: 1,
                title: "Cửa tồn tại",
                desc: "Với mỗi phương trình sau, hãy chọn <strong class='text-green-400'>Có nghiệm thực</strong> hoặc <strong class='text-red-400'>Vô nghiệm</strong>.",
                questions: [
                    { id: "A", q: `A. <span class="math">sin x = ${f(3,2)}</span>`, options: [{v: "Y", l: "Có nghiệm thực"}, {v: "N", l: "Vô nghiệm"}], ans: "N" },
                    { id: "B", q: `B. <span class="math">cos x = -1</span>`, options: [{v: "Y", l: "Có nghiệm thực"}, {v: "N", l: "Vô nghiệm"}], ans: "Y" },
                    { id: "C", q: `C. <span class="math">tan x = 2</span>`, options: [{v: "Y", l: "Có nghiệm thực"}, {v: "N", l: "Vô nghiệm"}], ans: "Y" },
                    { id: "D", q: `D. <span class="math">cot x = 0</span>`, options: [{v: "Y", l: "Có nghiệm thực"}, {v: "N", l: "Vô nghiệm"}], ans: "Y" }
                ],
                hints: [
                    "Giá trị của sin và cos luôn nằm trong đoạn từ −1 đến 1.",
                    "Giá trị −1 và 1 vẫn thuộc đoạn [−1; 1]. Trên miền xác định, tan và cot có thể nhận mọi giá trị thực.",
                    `Ở ý A, so sánh 3/2 với 1. Ở ý B, kiểm tra giá trị cos ${pi}. Ở ý D, kiểm tra giá trị cot(${f(pi,2)}).`
                ],
                solution: `A. Vô nghiệm vì 3/2 > 1.<br>B. Có nghiệm, chẳng hạn x = ${pi}.<br>C. Có nghiệm vì tan nhận mọi giá trị thực trên miền xác định; chẳng hạn x = arctan 2.<br>D. Có nghiệm, chẳng hạn x = ${f(pi,2)}.<br><span class="text-sm text-slate-400 mt-2 block italic">*Lưu ý: "Tan và cot nhận mọi giá trị thực" không có nghĩa chúng xác định với mọi x.</span>`,
                evaluate: (selections, errors) => {
                    let ok = true, msg = [];
                    if(selections["A"] !== "N" || selections["B"] !== "Y") { ok = false; msg.push("Em hãy kiểm tra đoạn giá trị của sin và cos, kể cả hai đầu mút."); errors.add("L02"); }
                    if(selections["C"] !== "Y" || selections["D"] !== "Y") { ok = false; msg.push("Em hãy phân biệt tập giá trị của hàm số với tập xác định của hàm số."); errors.add("L02"); }
                    return { ok, msg: msg.join(" ") };
                }
            },
            {
                id: "CN02", type: "main", stage: 2,
                title: "Cửa họ nghiệm",
                desc: `Chọn công thức biểu diễn đầy đủ tập nghiệm của mỗi phương trình. Trong tất cả các phương án, k ${inn} ${Z}.`,
                questions: [
                    { id: "A", q: `A. <span class="math">tan x = 1</span>`, options: [{v: "A1", l: `<span class="math">x = ${f(pi,4)} + k${pi}</span>`}, {v: "A2", l: `<span class="math">x = ${f(pi,4)} + 2k${pi}</span>`}, {v: "A3", l: `<span class="math">x = ${f(pi,4)} + ${f(`k${pi}`,2)}</span>`}], ans: "A1" },
                    { id: "B", q: `B. <span class="math">cos x = 1</span>`, options: [{v: "B1", l: `<span class="math">x = k${pi}</span>`}, {v: "B2", l: `<span class="math">x = 2k${pi}</span>`}, {v: "B3", l: `<span class="math">x = ${pi} + 2k${pi}</span>`}], ans: "B2" },
                    { id: "C", q: `C. <span class="math">sin x = 0</span>`, options: [{v: "C1", l: `<span class="math">x = 2k${pi}</span>`}, {v: "C2", l: `<span class="math">x = ${pi} + 2k${pi}</span>`}, {v: "C3", l: `<span class="math">x = k${pi}</span>`}], ans: "C3" }
                ],
                hints: [
                    "Công thức cần chứa tất cả nghiệm và không đưa thêm giá trị không thỏa mãn.",
                    "Tan có chu kỳ π. Cos bằng 1 sau mỗi vòng 2π. Sin bằng 0 ở cả hai điểm trên trục hoành của vòng tròn lượng giác.",
                    `Với ý A, thử x = ${f(pi,4)} và x = ${f(`5${pi}`,4)}.<br>Với ý B, so sánh cos 0 và cos ${pi}.<br>Với ý C, kiểm tra x = 0 và x = ${pi}.`
                ],
                solution: `A. tan x = 1 ⇔ x = ${f(pi,4)} + k${pi}.<br>B. cos x = 1 ⇔ x = 2k${pi}.<br>C. sin x = 0 ⇔ x = k${pi}.<br><span class="text-sm text-slate-400 mt-2 block italic">*Giải thích: Hàm số sin có chu kỳ 2${pi}, nhưng tập nghiệm của phương trình sin x = 0 gồm các bội nguyên của ${pi}. Vì vậy x = k${pi} là công thức đúng.</span>`,
                evaluate: (selections, errors) => {
                    let ok = true, msg = [];
                    if(selections["A"] !== "A1") { 
                        ok = false; errors.add("L04");
                        if(selections["A"] === "A2") msg.push("Công thức ý A em chọn cho các nghiệm đúng nhưng chưa bao quát tất cả nghiệm.");
                        if(selections["A"] === "A3") msg.push("Công thức ý A em chọn đưa thêm giá trị không thỏa mãn; em hãy thử k = 1.");
                    }
                    if(selections["B"] !== "B2") {
                        ok = false; errors.add("L04");
                        if(selections["B"] === "B1") msg.push(`Ý B: Em hãy thử k = 1 rồi thay x vào phương trình.`);
                        if(selections["B"] === "B3") msg.push(`Ý B: Em hãy thay x = ${pi} vào phương trình.`);
                    }
                    if(selections["C"] !== "C3") {
                        ok = false; errors.add("L04"); errors.add("L03c");
                        msg.push("Công thức ý C em chọn mới bao quát một phần các nghiệm.");
                    }
                    return { ok, msg: msg.join(" ") };
                }
            },
            {
                id: "CN03", type: "main", stage: 3,
                title: "Cửa điều kiện",
                desc: "Chọn điều kiện xác định đầy đủ, sau đó kiểm tra giá trị được đề xuất.",
                questions: [
                    { id: "A", q: `A. Điều kiện xác định của <span class="math">tan x</span> là:`, options: [{v: "A1", l: `<span class="math">x ${neq} ${f(pi,2)} + n${pi}</span>, n ${inn} ${Z}`}, {v: "A2", l: `<span class="math">x ${neq} n${pi}</span>, n ${inn} ${Z}`}, {v: "A3", l: `<span class="math">x</span> thuộc ${R}`}], ans: "A1" },
                    { id: "B", q: `B. Điều kiện xác định của <span class="math">cot x</span> là:`, options: [{v: "B1", l: `<span class="math">x ${neq} ${f(pi,2)} + n${pi}</span>, n ${inn} ${Z}`}, {v: "B2", l: `<span class="math">x ${neq} n${pi}</span>, n ${inn} ${Z}`}, {v: "B3", l: `<span class="math">x</span> thuộc ${R}`}], ans: "B2" },
                    { id: "C", q: `C. Một bạn cho rằng <span class="math">x = ${f(pi,2)}</span> là nghiệm của <span class="math">tan x = 0</span>. Nhận xét nào đúng?`, options: [{v: "C1", l: `Đúng, vì <span class="math">cos(${f(pi,2)}) = 0</span>.`}, {v: "C2", l: `Sai, vì <span class="math">tan x</span> không xác định tại <span class="math">x = ${f(pi,2)}</span>.`}, {v: "C3", l: `Sai, vì <span class="math">tan(${f(pi,2)}) = 1</span>.`}], ans: "C2" }
                ],
                hints: [
                    "Viết tan và cot dưới dạng tỉ số của sin và cos.",
                    "tan x = sin x / cos x cần cos x ≠ 0.<br>cot x = cos x / sin x cần sin x ≠ 0.",
                    `cos x = 0 khi x = ${f(pi,2)} + n${pi}.<br>sin x = 0 khi x = n${pi}.<br>Hãy loại những giá trị làm mẫu số bằng 0.`
                ],
                solution: `A. tan x xác định khi x ≠ ${f(pi,2)} + n${pi}.<br>B. cot x xác định khi x ≠ n${pi}.<br>C. x = ${f(pi,2)} không là nghiệm của tan x = 0 vì tan không xác định tại đó.<br><span class="text-sm text-slate-400 mt-2 block italic">*Thông điệp: Một giá trị chỉ có thể là nghiệm khi biểu thức xác định tại giá trị ấy.</span>`,
                evaluate: (selections, errors) => {
                    let ok = true, msg = [];
                    if(selections["A"] !== "A1" || selections["B"] !== "B2") { ok = false; msg.push("Ý A/B: Em hãy kiểm tra hàm lượng giác nào nằm ở mẫu số."); errors.add("L06"); }
                    if(selections["C"] !== "C2") { ok = false; msg.push("Ý C: Trước khi tính giá trị tan, em cần kiểm tra mẫu số có bằng 0 không."); errors.add("L06"); }
                    return { ok, msg: msg.join(" ") };
                }
            },
            {
                id: "CN04", type: "backup", stage: 1,
                title: "Kiểm tra quyền vào cửa",
                desc: "Phân loại các phương trình sau: <strong class='text-green-400'>Có nghiệm thực</strong> hoặc <strong class='text-red-400'>Vô nghiệm</strong>.<br><span class='text-slate-400 text-xs mt-1 block uppercase tracking-wider'>Luyện thêm - Chặng 1</span>",
                questions: [
                    { id: "A", q: `A. <span class="math">cos x = -${f(5,4)}</span>`, options: [{v: "Y", l: "Có nghiệm thực"}, {v: "N", l: "Vô nghiệm"}], ans: "N" },
                    { id: "B", q: `B. <span class="math">sin x = 1</span>`, options: [{v: "Y", l: "Có nghiệm thực"}, {v: "N", l: "Vô nghiệm"}], ans: "Y" },
                    { id: "C", q: `C. <span class="math">tan x = -3</span>`, options: [{v: "Y", l: "Có nghiệm thực"}, {v: "N", l: "Vô nghiệm"}], ans: "Y" },
                    { id: "D", q: `D. <span class="math">cot x = 2</span>`, options: [{v: "Y", l: "Có nghiệm thực"}, {v: "N", l: "Vô nghiệm"}], ans: "Y" }
                ],
                hints: [
                    "Nhớ giới hạn giá trị của sin và cos.",
                    "−5/4 nhỏ hơn −1, còn 1 nằm trong đoạn [−1; 1].",
                    "Sin có thể bằng 1. Tan và cot có thể nhận các giá trị thực −3 và 2 trên miền xác định."
                ],
                solution: `A. Vô nghiệm vì −5/4 < −1.<br>B. Có nghiệm, chẳng hạn x = ${f(pi,2)}.<br>C. Có nghiệm, chẳng hạn x = −arctan 3.<br>D. Có nghiệm, chẳng hạn x = arctan(1/2).`,
                evaluate: (selections, errors) => {
                    let ok = true, msg = [];
                    if(selections["A"] !== "N" || selections["B"] !== "Y") { ok = false; msg.push("Kiểm tra lại điều kiện có nghiệm của sin và cos."); errors.add("L02"); }
                    if(selections["C"] !== "Y" || selections["D"] !== "Y") { ok = false; msg.push("Tan và cot nhận mọi giá trị thực trên miền xác định."); errors.add("L02"); }
                    return { ok, msg: msg.join(" ") };
                }
            },
            {
                id: "CN05", type: "backup", stage: 2,
                title: "Chọn chìa khóa đủ nghiệm",
                desc: `Chọn công thức biểu diễn đầy đủ tập nghiệm. Trong tất cả các phương án, k ${inn} ${Z}.<br><span class='text-slate-400 text-xs mt-1 block uppercase tracking-wider'>Luyện thêm - Chặng 2</span>`,
                questions: [
                    { id: "A", q: `A. <span class="math">cot x = 1</span>`, options: [{v: "A1", l: `<span class="math">x = ${f(pi,4)} + 2k${pi}</span>`}, {v: "A2", l: `<span class="math">x = ${f(pi,4)} + k${pi}</span>`}, {v: "A3", l: `<span class="math">x = ${f(pi,4)} + ${f(`k${pi}`,2)}</span>`}], ans: "A2" },
                    { id: "B", q: `B. <span class="math">sin x = 1</span>`, options: [{v: "B1", l: `<span class="math">x = ${f(pi,2)} + k${pi}</span>`}, {v: "B2", l: `<span class="math">x = k${pi}</span>`}, {v: "B3", l: `<span class="math">x = ${f(pi,2)} + 2k${pi}</span>`}], ans: "B3" },
                    { id: "C", q: `C. <span class="math">cos x = 0</span>`, options: [{v: "C1", l: `<span class="math">x = ${f(pi,2)} + k${pi}</span>`}, {v: "C2", l: `<span class="math">x = ${f(pi,2)} + 2k${pi}</span>`}, {v: "C3", l: `<span class="math">x = k${pi}</span>`}], ans: "C1" }
                ],
                hints: [
                    "Công thức cần vừa đủ nghiệm, không bỏ sót và không thêm nghiệm sai.",
                    "Cot có chu kỳ π. Sin bằng 1 tại điểm cao nhất của vòng tròn. Cos bằng 0 ở cả hai điểm trên trục tung.",
                    `Ý A: so sánh cot(${f(pi,4)}) và cot(${f(`5${pi}`,4)}).<br>Ý B: so sánh sin(${f(pi,2)}) và sin(${f(`3${pi}`,2)}).<br>Ý C: kiểm tra cos(${f(pi,2)}) và cos(${f(`3${pi}`,2)}).`
                ],
                solution: `A. x = ${f(pi,4)} + k${pi}.<br>B. x = ${f(pi,2)} + 2k${pi}.<br>C. x = ${f(pi,2)} + k${pi}.<br><span class="text-sm text-slate-400 mt-2 block italic">*Giải thích: Hàm số cos có chu kỳ 2${pi}, nhưng các nghiệm liên tiếp của cos x = 0 cách nhau ${pi}.</span>`,
                evaluate: (selections, errors) => {
                    let ok = true, msg = [];
                    if(selections["A"] !== "A2" || selections["B"] !== "B3" || selections["C"] !== "C1") { 
                        ok = false; errors.add("L04");
                        if(selections["A"] === "A1" || selections["C"] === "C2") msg.push("Công thức em chọn chưa bao quát tất cả nghiệm.");
                        if(["A3","B1","B2","C3"].includes(selections["A"]) || ["A3","B1","B2","C3"].includes(selections["B"]) || ["A3","B1","B2","C3"].includes(selections["C"])) {
                            msg.push("Em hãy thử một giá trị k đơn giản và thay vào phương trình để kiểm tra.");
                        }
                        if(selections["C"] !== "C1") errors.add("L03c");
                    }
                    return { ok, msg: msg.join(" ") };
                }
            },
            {
                id: "CN06", type: "backup", stage: 3,
                title: "Kiểm tra trước khi mở cửa",
                desc: "Chọn điều kiện xác định của biểu thức và đánh giá một giá trị được đề xuất.<br><span class='text-slate-400 text-xs mt-1 block uppercase tracking-wider'>Luyện thêm - Chặng 3</span>",
                questions: [
                    { id: "A", q: `A. Điều kiện xác định của <span class="math">tan(x - ${f(pi,4)})</span> là:`, options: [{v: "A1", l: `<span class="math">x ${neq} ${f(`3${pi}`,4)} + n${pi}</span>, n ${inn} ${Z}`}, {v: "A2", l: `<span class="math">x ${neq} ${f(pi,4)} + n${pi}</span>, n ${inn} ${Z}`}, {v: "A3", l: `<span class="math">x</span> thuộc ${R}`}], ans: "A1" },
                    { id: "B", q: `B. Điều kiện xác định của <span class="math">cot(x + ${f(pi,6)})</span> là:`, options: [{v: "B1", l: `<span class="math">x ${neq} ${f(pi,3)} + n${pi}</span>, n ${inn} ${Z}`}, {v: "B2", l: `<span class="math">x ${neq} -${f(pi,6)} + n${pi}</span>, n ${inn} ${Z}`}, {v: "B3", l: `<span class="math">x</span> thuộc ${R}`}], ans: "B2" },
                    { id: "C", q: `C. Có thể nhận <span class="math">x = ${f(`3${pi}`,4)}</span> là nghiệm của phương trình <span class="math">tan(x - ${f(pi,4)}) = 1</span> không?`, options: [{v: "C1", l: `Có, vì <span class="math">tan(${f(pi,2)}) = 1</span>.`}, {v: "C2", l: `Không, vì khi đó biểu thức tan không xác định.`}, {v: "C3", l: `Không, vì khi đó <span class="math">tan(x - ${f(pi,4)}) = 0</span>.`}], ans: "C2" }
                ],
                hints: [
                    "Đặt u bằng biểu thức nằm trong tan hoặc cot, rồi xét điều kiện của u.",
                    `Tan u cần u ≠ ${f(pi,2)} + n${pi}.<br>Cot u cần u ≠ n${pi}.`,
                    `Ý A: x − ${f(pi,4)} ≠ ${f(pi,2)} + n${pi}.<br>Ý B: x + ${f(pi,6)} ≠ n${pi}.<br>Ý C: Thay x = ${f(`3${pi}`,4)} vào x − ${f(pi,4)} trước.`
                ],
                solution: `A. x − ${f(pi,4)} ≠ ${f(pi,2)} + n${pi} ⇔ x ≠ ${f(`3${pi}`,4)} + n${pi}.<br>B. x + ${f(pi,6)} ≠ n${pi} ⇔ x ≠ −${f(pi,6)} + n${pi}.<br>C. Nếu x = ${f(`3${pi}`,4)} thì x − ${f(pi,4)} = ${f(pi,2)}. Tan không xác định tại ${f(pi,2)}. Vì vậy không thể nhận giá trị x này là nghiệm.`,
                evaluate: (selections, errors) => {
                    let ok = true, msg = [];
                    if(selections["A"] !== "A1" || selections["B"] !== "B2") { ok = false; msg.push("Ý A/B: Em hãy kiểm tra điều kiện của góc bên trong trước khi biến đổi về x."); errors.add("L06"); }
                    if(selections["C"] !== "C2") { ok = false; msg.push("Ý C: Em hãy thay giá trị x vào góc rồi kiểm tra mẫu số của tan."); errors.add("L06"); }
                    return { ok, msg: msg.join(" ") };
                }
            }
        ];

        // --- 2. QUẢN LÝ TRẠNG THÁI ---
        let state = {
            studentId: "",
            score: 0,
            correctMainTasks: 0,
            currentTaskIndex: 0,
            audioEnabled: false,
            history: {}, // { taskId: { attempts, hintLevel, status, points, errors: Set, solutionViewed, firstAns, lastAns } }
            currentSelections: {} // { questionId: optionValue }
        };

        // --- 3. ÂM THANH (Tone.js) ---
        let toneInit = false;
        let sfx;
        
        async function initAudio() {
            if (toneInit) return;
            await Tone.start();
            sfx = new Tone.PolySynth(Tone.Synth, { oscillator: { type: "sine" }, envelope: { attack: 0.05, decay: 0.2, sustain: 0.1, release: 1 } }).toDestination();
            sfx.volume.value = -10;
            toneInit = true;
        }

        async function toggleAudio() {
            state.audioEnabled = !state.audioEnabled;
            if (state.audioEnabled && !toneInit) await initAudio();
            document.querySelectorAll('.icon-audio-on').forEach(el => el.classList.toggle('hidden', !state.audioEnabled));
            document.querySelectorAll('.icon-audio-off').forEach(el => el.classList.toggle('hidden', state.audioEnabled));
            document.querySelectorAll('.btn-audio').forEach(btn => {
                if(state.audioEnabled) btn.classList.replace('text-slate-300', 'text-yellow-400');
                else btn.classList.replace('text-yellow-400', 'text-slate-300');
            });
        }

        function playSfx(type) {
            if(!state.audioEnabled || !sfx) return;
            const now = Tone.now();
            if(type === 'select') sfx.triggerAttackRelease("C5", "32n", now);
            else if(type === 'correct') { sfx.triggerAttackRelease(["C5", "E5"], "8n", now); sfx.triggerAttackRelease("G5", "8n", now + 0.15); }
            else if(type === 'wrong') sfx.triggerAttackRelease("C4", "8n", now);
            else if(type === 'finish') { sfx.triggerAttackRelease("C4", "8n", now); sfx.triggerAttackRelease("E4", "8n", now + 0.15); sfx.triggerAttackRelease("G4", "8n", now + 0.3); sfx.triggerAttackRelease("C5", "2n", now + 0.45); }
        }

        // --- 4. TIỆN ÍCH MODAL ---
        function openHelp() { let m = document.getElementById('modal-help'), c = document.getElementById('modal-help-content'); m.classList.remove('hidden'); void m.offsetWidth; m.classList.remove('opacity-0'); c.classList.remove('scale-95'); }
        function closeHelp() { let m = document.getElementById('modal-help'), c = document.getElementById('modal-help-content'); m.classList.add('opacity-0'); c.classList.add('scale-95'); setTimeout(() => m.classList.add('hidden'), 200); }
        
        let pendingConfirm = null;
        function showConfirm(title, msg, onOk) {
            document.getElementById('modal-title').innerText = title; document.getElementById('modal-msg').innerText = msg;
            pendingConfirm = onOk;
            let m = document.getElementById('modal-confirm'), c = document.getElementById('modal-confirm-content');
            m.classList.remove('hidden'); void m.offsetWidth; m.classList.remove('opacity-0'); c.classList.remove('scale-95');
        }
        document.getElementById('btn-modal-cancel').onclick = () => { let m = document.getElementById('modal-confirm'), c = document.getElementById('modal-confirm-content'); m.classList.add('opacity-0'); c.classList.add('scale-95'); setTimeout(() => { m.classList.add('hidden'); pendingConfirm = null; }, 200); };
        document.getElementById('btn-modal-ok').onclick = () => { if(pendingConfirm) pendingConfirm(); document.getElementById('btn-modal-cancel').click(); };

        function shuffle(array) {
            let arr = [...array];
            for (let i = arr.length - 1; i > 0; i--) { const j = Math.floor(Math.random() * (i + 1)); [arr[i], arr[j]] = [arr[j], arr[i]]; }
            return arr;
        }

        // --- 5. LOGIC TRÒ CHƠI ---
        function startGame() {
            state.studentId = document.getElementById('student-id').value.trim();
            TASKS.forEach(t => { state.history[t.id] = { attempts: 0, hintLevel: 0, status: 'Chưa thực hiện', points: 0, errors: new Set(), solutionViewed: false, firstAns: null, lastAns: null }; });
            
            document.getElementById('screen-intro').classList.add('hidden');
            document.getElementById('stats-bar').classList.remove('hidden'); document.getElementById('stats-bar').classList.add('flex');
            document.getElementById('btn-header-restart').classList.remove('hidden');
            document.getElementById('screen-game').classList.remove('hidden');
            
            if(state.audioEnabled) initAudio();
            loadTask(0);
        }

        function loadTask(idx) {
            state.currentTaskIndex = idx;
            const task = TASKS[idx];
            const hist = state.history[task.id];
            
            document.getElementById('stat-stage').innerText = task.stage;
            document.getElementById('task-id').innerText = task.id;
            document.getElementById('task-badge').innerText = task.type === 'main' ? 'Chính' : 'Luyện thêm';
            document.getElementById('task-badge').className = task.type === 'main' ? 'bg-yellow-500 text-slate-900 text-xs font-bold px-2 py-1 rounded uppercase' : 'bg-slate-600 text-white text-xs font-bold px-2 py-1 rounded uppercase';
            document.getElementById('task-title').innerText = task.title;
            document.getElementById('task-question').innerHTML = task.desc;
            
            state.currentSelections = {};
            let area = document.getElementById('interactive-area');
            area.innerHTML = '';
            
            task.questions.forEach(q => {
                let qDiv = document.createElement('div');
                qDiv.className = "bg-slate-800/50 border border-slate-600 rounded-lg p-4";
                qDiv.innerHTML = `<div class="font-bold text-slate-200 mb-3 text-lg">${q.q}</div>`;
                
                let optsDiv = document.createElement('div');
                optsDiv.className = "grid grid-cols-1 sm:grid-cols-2 gap-3";
                
                // Trộn thẻ 1 lần khi chưa làm
                let opts = [...q.options];
                if(hist.attempts === 0) opts = shuffle(opts);
                
                opts.forEach(opt => {
                    let btn = document.createElement('div');
                    btn.id = `opt-${q.id}-${opt.v}`;
                    btn.className = "option-card flex items-center justify-between";
                    btn.innerHTML = `<span>${opt.l}</span><svg class="status-mark success h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7" /></svg><svg class="status-mark error h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg>`;
                    btn.onclick = () => selectOption(q.id, opt.v);
                    optsDiv.appendChild(btn);
                });
                qDiv.appendChild(optsDiv);
                area.appendChild(qDiv);
            });

            resetUIState(task);
            window.scrollTo({top: 0, behavior: 'smooth'});
        }

        function resetUIState(task) {
            document.getElementById('hints-area').innerHTML = ''; document.getElementById('hints-area').classList.add('hidden');
            document.getElementById('feedback-area').classList.add('hidden');
            document.getElementById('solution-area').classList.add('hidden');
            document.getElementById('task-locked-overlay').classList.add('hidden');
            
            let btnSubmit = document.getElementById('btn-submit');
            btnSubmit.disabled = false; btnSubmit.classList.remove('opacity-50');
            
            let btnHint = document.getElementById('btn-hint');
            btnHint.disabled = false; btnHint.classList.remove('hidden', 'opacity-50');
            document.getElementById('hint-count').innerText = `${state.history[task.id].hintLevel}/3`;
            
            document.getElementById('btn-show-solution').classList.add('hidden');
        }

        function selectOption(qId, val) {
            if (isTaskLocked()) return;
            playSfx('select');
            state.currentSelections[qId] = val;
            
            const task = TASKS[state.currentTaskIndex];
            const q = task.questions.find(x => x.id === qId);
            q.options.forEach(o => {
                let el = document.getElementById(`opt-${qId}-${o.v}`);
                el.classList.remove('selected', 'error-mark');
            });
            document.getElementById(`opt-${qId}-${val}`).classList.add('selected');
            document.getElementById('feedback-area').classList.add('hidden');
        }

        function isTaskLocked() {
            const t = TASKS[state.currentTaskIndex];
            const h = state.history[t.id];
            return h.status.includes('Đúng') || h.status === 'Đã xem lời giải' || h.status === 'Cần luyện thêm';
        }

        function submitAnswer() {
            if (isTaskLocked()) return;
            const task = TASKS[state.currentTaskIndex];
            const hist = state.history[task.id];
            
            if (Object.keys(state.currentSelections).length < task.questions.length) {
                showFeedback("Em hãy trả lời đầy đủ các ý trước khi nộp.", "warning");
                return;
            }

            hist.attempts++;
            hist.status = "Đang thực hiện";
            let currentCopy = JSON.parse(JSON.stringify(state.currentSelections));
            hist.lastAns = currentCopy;
            if (hist.attempts === 1) hist.firstAns = currentCopy;

            let evalRes = task.evaluate(state.currentSelections, hist.errors);

            if (evalRes.ok) {
                playSfx('correct');
                lockTaskSuccess(task, hist);
                showFeedback("Chính xác! Cánh cửa đã được mở.", "success");
            } else {
                playSfx('wrong');
                markErrors(task);
                showFeedback(`Chưa chính xác.<br>${evalRes.msg}`, "error");
                if (hist.attempts >= 3) document.getElementById('btn-show-solution').classList.remove('hidden');
            }
        }

        function markErrors(task) {
            task.questions.forEach(q => {
                let sel = state.currentSelections[q.id];
                if(sel && sel !== q.ans) {
                    document.getElementById(`opt-${q.id}-${sel}`).classList.add('error-mark');
                }
            });
        }

        function showFeedback(msg, type) {
            let area = document.getElementById('feedback-area');
            area.className = "mt-4 p-4 rounded flex items-start gap-3 text-sm sm:text-base font-medium border";
            let icon = '';
            if (type === 'success') { area.classList.add('bg-green-900/30', 'border-green-800', 'text-green-400'); icon = `<svg class="h-6 w-6 shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" /></svg>`; }
            else if (type === 'error') { area.classList.add('bg-red-900/30', 'border-red-800', 'text-red-400'); icon = `<svg class="h-6 w-6 shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z" /></svg>`; }
            else { area.classList.add('bg-yellow-900/30', 'border-yellow-800', 'text-yellow-400'); icon = `<svg class="h-6 w-6 shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" /></svg>`; }
            area.innerHTML = `${icon} <div>${msg}</div>`;
            area.classList.remove('hidden');
        }

        function calculatePoints(task, hist) {
            if (task.type === 'backup') { hist.status = hist.hintLevel > 0 ? "Đúng có gợi ý" : "Đúng sau tự sửa"; return; }
            let pts = 0;
            if (hist.attempts === 1 && hist.hintLevel === 0) { pts = 10; hist.status = "Đúng lần đầu không gợi ý"; }
            else if (hist.hintLevel === 0) { pts = 8; hist.status = "Đúng sau tự sửa"; }
            else if (hist.hintLevel === 1) { pts = 6; hist.status = "Đúng có gợi ý"; }
            else if (hist.hintLevel === 2) { pts = 4; hist.status = "Đúng có gợi ý"; }
            else { pts = 2; hist.status = "Đúng có gợi ý"; }
            hist.points = pts; state.score += pts; state.correctMainTasks++;
            document.getElementById('stat-score').innerText = state.score;
            document.getElementById('stat-correct').innerText = state.correctMainTasks;
        }

        function lockTaskSuccess(task, hist) {
            calculatePoints(task, hist);
            task.questions.forEach(q => {
                q.options.forEach(o => document.getElementById(`opt-${q.id}-${o.v}`).classList.add('locked'));
                document.getElementById(`opt-${q.id}-${q.ans}`).classList.add('success-mark');
            });
            document.getElementById('task-locked-overlay').classList.remove('hidden');
            document.getElementById('btn-submit').disabled = true;
        }

        function requestHint() {
            const task = TASKS[state.currentTaskIndex];
            const hist = state.history[task.id];
            if (hist.hintLevel < task.hints.length) {
                let txt = task.hints[hist.hintLevel];
                hist.hintLevel++;
                let d = document.createElement('div'); d.className = "bg-slate-700/50 border border-yellow-700/50 text-yellow-100 p-3 rounded text-sm flex gap-2";
                d.innerHTML = `<span class="font-bold text-yellow-500 whitespace-nowrap">Gợi ý ${hist.hintLevel}:</span> <span>${txt}</span>`;
                document.getElementById('hints-area').appendChild(d); document.getElementById('hints-area').classList.remove('hidden');
                document.getElementById('hint-count').innerText = `${hist.hintLevel}/3`;
                if (hist.hintLevel === task.hints.length) document.getElementById('btn-hint').classList.add('opacity-50');
            }
        }

        function confirmShowSolution() {
            showConfirm("Xem lời giải", "Nhiệm vụ sẽ ghi nhận là đã xem lời giải và không được tính điểm. Em chắc chắn muốn xem?", () => {
                const task = TASKS[state.currentTaskIndex]; const hist = state.history[task.id];
                hist.solutionViewed = true; hist.status = "Đã xem lời giải"; hist.points = 0;
                document.getElementById('solution-text').innerHTML = task.solution;
                document.getElementById('solution-area').classList.remove('hidden');
                document.getElementById('task-locked-overlay').classList.remove('hidden');
                document.getElementById('task-locked-overlay').querySelector('h3').innerText = "Cửa đang khóa";
                document.getElementById('task-locked-overlay').querySelector('svg').outerHTML = `<svg class="h-16 w-16 text-slate-400 mb-2" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z" /></svg>`;
                document.getElementById('btn-next').className = "w-full bg-slate-200 hover:bg-slate-300 text-slate-800 font-bold btn-touch rounded-lg transition-all shadow-lg text-lg uppercase";
                if (task.type === 'backup') { hist.status = "Cần luyện thêm"; document.getElementById('btn-end-early-container').classList.remove('hidden'); }
            });
        }

        function confirmSkipBackup() {
            showConfirm("Chuyển chặng", "Nhiệm vụ sẽ được ghi là chưa tự hoàn thành. Em muốn chuyển sang chặng tiếp theo?", () => {
                state.history[TASKS[state.currentTaskIndex].id].status = "Cần luyện thêm"; nextTask(true);
            });
        }

        function nextTask(skipLogic = false) {
            const task = TASKS[state.currentTaskIndex]; const hist = state.history[task.id];
            let nextId = "END";
            let isPerfect = hist.status.includes('Đúng') && !hist.solutionViewed;

            if (task.id === "CN01") nextId = isPerfect ? "CN02" : "CN04";
            else if (task.id === "CN04") nextId = "CN02";
            else if (task.id === "CN02") nextId = isPerfect ? "CN03" : "CN05";
            else if (task.id === "CN05") nextId = "CN03";
            else if (task.id === "CN03") nextId = isPerfect ? "END" : "CN06";
            else if (task.id === "CN06") nextId = "END";

            if (nextId === "END") showResult();
            else loadTask(TASKS.findIndex(t => t.id === nextId));
        }

        // --- 6. TỔNG KẾT & XUẤT ---
        function showResult() {
            playSfx('finish');
            document.getElementById('screen-game').classList.add('hidden'); document.getElementById('btn-header-restart').classList.add('hidden');
            document.getElementById('screen-result').classList.remove('hidden');
            document.getElementById('res-student').innerText = state.studentId ? `Mã HS: ${state.studentId}` : "";
            document.getElementById('res-score').innerText = state.score; document.getElementById('res-correct-final').innerText = state.correctMainTasks;
            
            TASKS.forEach(t => { if(state.history[t.id].status === 'Chưa thực hiện' && t.type === 'backup') state.history[t.id].status = "Không cần thực hiện"; });
            updateVisualDoors(); generateBadges(); generateTable(); generateSummary();
            window.scrollTo({top: 0, behavior: 'smooth'});
        }

        function updateVisualDoors() {
            let s1 = state.history['CN01'].status.includes('Đúng') || (state.history['CN04'] && state.history['CN04'].status.includes('Đúng'));
            let s2 = state.history['CN02'].status.includes('Đúng') || (state.history['CN05'] && state.history['CN05'].status.includes('Đúng'));
            let s3 = state.history['CN03'].status.includes('Đúng') || (state.history['CN06'] && state.history['CN06'].status.includes('Đúng'));
            let unlock = `<svg class="h-6 w-6 text-green-600" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 11V7a4 4 0 118 0m-4 8v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2z" /></svg>`;
            if(s1) { document.getElementById('door-1').classList.replace('opacity-50', 'opacity-100'); document.getElementById('door-1').classList.replace('border-slate-300', 'border-green-400'); document.getElementById('door-1').classList.replace('bg-slate-100', 'bg-green-50'); document.getElementById('door-1').innerHTML = unlock; }
            if(s2) { document.getElementById('door-2').classList.replace('opacity-50', 'opacity-100'); document.getElementById('door-2').classList.replace('border-slate-300', 'border-green-400'); document.getElementById('door-2').classList.replace('bg-slate-100', 'bg-green-50'); document.getElementById('door-2').innerHTML = unlock; }
            if(s3) { document.getElementById('door-3').classList.replace('opacity-50', 'opacity-100'); document.getElementById('door-3').classList.replace('border-slate-300', 'border-green-400'); document.getElementById('door-3').classList.replace('bg-slate-100', 'bg-green-50'); document.getElementById('door-3').innerHTML = unlock; }
        }

        function generateBadges() {
            let bArea = document.getElementById('badges-area'); bArea.innerHTML = '';
            let addB = (title, taskRef, status) => { bArea.innerHTML += `<div class="bg-white border-2 border-yellow-400 rounded-lg p-3 text-center shadow-sm flex flex-col items-center"><svg class="h-8 w-8 text-yellow-500 mb-1" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z" /></svg><span class="font-bold text-sm text-slate-800">${title}</span><span class="text-[10px] text-slate-500 font-medium">${taskRef}: ${status}</span></div>`; };
            
            let c1 = [state.history['CN01'], state.history['CN04']].find(h => h && h.status.includes('Đúng') && !h.solutionViewed);
            if(c1) addB("Nhận đúng điều kiện", c1===state.history['CN01']?'CN01':'CN04', c1.status);
            let c2 = [state.history['CN02'], state.history['CN05']].find(h => h && h.status.includes('Đúng') && !h.solutionViewed);
            if(c2) addB("Chọn đủ họ nghiệm", c2===state.history['CN02']?'CN02':'CN05', c2.status);
            let c3 = [state.history['CN03'], state.history['CN06']].find(h => h && h.status.includes('Đúng') && !h.solutionViewed);
            if(c3) addB("Kiểm tra trước khi nhận", c3===state.history['CN03']?'CN03':'CN06', c3.status);
        }

        function generateTable() {
            let tbody = document.getElementById('res-table-body'); tbody.innerHTML = '';
            TASKS.forEach(t => {
                let h = state.history[t.id]; if(h.status === 'Chưa thực hiện') return;
                let sCls = "text-slate-600";
                if(h.status.includes('Đúng')) sCls = "text-green-600 font-bold";
                else if(h.status === 'Không cần thực hiện') sCls = "text-slate-400 italic";
                else if(h.status === 'Đã xem lời giải' || h.status === 'Cần luyện thêm') sCls = "text-red-500 font-bold";
                tbody.innerHTML += `<tr><td class="p-3"><div class="font-bold text-slate-800">${t.id}</div><div class="text-xs text-slate-500">${t.type==='main'?'Chính':'Luyện thêm'} ${h.errors.size>0? '| Lỗi: '+Array.from(h.errors).join(','):''}</div></td><td class="p-3 text-center">${h.status==='Không cần thực hiện'?'-':h.attempts}</td><td class="p-3 text-center">${h.status==='Không cần thực hiện'?'-':(h.hintLevel>0?`H${h.hintLevel}`:'0')}</td><td class="p-3 ${sCls}">${h.status}</td></tr>`;
            });
        }

        function generateSummary() {
            let fb = [];
            let h4 = state.history['CN04'], h5 = state.history['CN05'], h6 = state.history['CN06'];
            if(h4 && (h4.status === 'Cần luyện thêm' || h4.status === 'Đã xem lời giải')) fb.push("Chặng 1 còn khó: Em cần phân biệt điều kiện có nghiệm của sin, cos với tan, cot.");
            if(h5 && (h5.status === 'Cần luyện thêm' || h5.status === 'Đã xem lời giải')) fb.push("Chặng 2 còn khó: Em cần kiểm tra công thức có đủ nghiệm và có đưa thêm giá trị sai không.");
            if(h6 && (h6.status === 'Cần luyện thêm' || h6.status === 'Đã xem lời giải')) fb.push("Chặng 3 còn khó: Em cần kiểm tra mẫu số trước khi nhận một giá trị là nghiệm.");
            if(fb.length === 0) fb.push("Em đã hoàn thành các nhiệm vụ được giao. Hãy áp dụng ba bước kiểm tra vào bài khảo sát.");
            document.getElementById('res-feedback').innerHTML = fb.map(i => `<div>- ${i}</div>`).join('');
        }

        function copyResult() {
            let txt = `KẾT QUẢ: CÁNH CỬA NGHIỆM\n`;
            if (state.studentId) txt += `Mã HS: ${state.studentId}\n`;
            txt += `Thời gian: ${new Date().toLocaleString('vi-VN')}\n`;
            txt += `Điểm: ${state.score}/30 | Đúng chính: ${state.correctMainTasks}/3\n\n`;
            TASKS.forEach(t => {
                let h = state.history[t.id]; if(h.status !== 'Không cần thực hiện') {
                    txt += `[${t.id}] ${t.title} (${t.type}): ${h.status}\n`;
                    txt += `  Nộp: ${h.attempts} | Gợi ý: H${h.hintLevel} | Lỗi: ${Array.from(h.errors).join(',')||'Không'}\n`;
                }
            });
            txt += `\nTự phản hồi: ${document.getElementById('self-reflection').value}\n`;
            let msg = document.getElementById('copy-msg'), fbArea = document.getElementById('fallback-copy');
            try { navigator.clipboard.writeText(txt).then(() => { msg.innerText = "Đã sao chép! Hãy dán vào nơi giáo viên yêu cầu."; msg.className = "mt-4 p-3 text-sm text-center font-medium rounded bg-green-50 text-green-700 block"; }).catch(() => fallbackCpy(txt, msg, fbArea));
            } catch(e) { fallbackCpy(txt, msg, fbArea); }
        }
        function fallbackCpy(txt, msg, fbArea) { fbArea.value = txt; fbArea.classList.remove('hidden'); fbArea.select(); document.execCommand('copy'); msg.innerText = "Đã sao chép (hoặc tự bôi đen ở ô trên)."; msg.className = "mt-2 p-3 text-sm text-center font-medium rounded bg-yellow-50 text-yellow-700 block"; }
        function requestRestart() { showConfirm("Chơi lại từ đầu?", "Chơi lại sẽ xóa kết quả lượt này. Nhớ sao chép kết quả trước. Em muốn tiếp tục không?", () => location.reload()); }
    </script>
</body>
</html>
