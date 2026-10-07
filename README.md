[index.html](https://github.com/user-attachments/files/33151712/index.html)
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>솔빛 연구소 (V15 학부모 포털)</title>

    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-database.js"></script>
    <script src="https://www.gstatic.com/firebasejs/8.10.1/firebase-auth.js"></script>

    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard/dist/web/static/pretendard.css">

    <style>
        :root {
            --brand: #2c3e50;    /* 솔빛 네이비 */
            --brand-2: #34495e;
            --accent: #3498db;   /* 포인트 블루 */
            --bg: #f5f7fa;       /* 배경 회색 */
            --white: #ffffff;
            --line: #e1e4e8;
            --plan: #95a5a6;     /* 예정 */
            --present: #27ae60;  /* 출석 */
            --absent: #e74c3c;   /* 결석 */
            --makeup: #f39c12;   /* 보강 */
            --editc: #e67e22;
            --ph-a: #16707f;     /* 기초선 청록 */
            --ph-b: #2e6bd6;     /* 중재 블루 */
        }

        body { margin: 0; font-family: 'Pretendard', sans-serif; background: var(--bg); display: flex; height: 100vh; overflow: hidden; color: #333; }

        /* ===== 로그인 화면 ===== */
        .login-wrap { position: fixed; inset: 0; z-index: 5000; display: flex; background: var(--bg); }
        .login-brand { flex: 1.1; background: linear-gradient(155deg, #22303f 0%, #2c3e50 55%, #35506b 100%); color: #fff; padding: 56px 52px; display: flex; flex-direction: column; justify-content: space-between; }
        .login-brand h1 { font-size: 30px; font-weight: 800; letter-spacing: -0.5px; margin: 0 0 6px; }
        .login-brand .lb-sub { color: rgba(255,255,255,0.65); font-size: 14px; }
        .lb-feats { list-style: none; padding: 0; margin: 34px 0 0; }
        .lb-feats li { padding: 10px 0; font-size: 15px; color: rgba(255,255,255,0.92); border-top: 1px solid rgba(255,255,255,0.12); display: flex; gap: 10px; align-items: baseline; }
        .lb-feats b { color: #ffd97a; font-weight: 700; margin-right: 2px; }
        .lb-foot { font-size: 12px; color: rgba(255,255,255,0.4); }
        .login-side { flex: 1; display: flex; align-items: center; justify-content: center; padding: 30px; }
        .login-card { width: min(400px, 100%); background: #fff; border-radius: 16px; box-shadow: 0 12px 40px rgba(44,62,80,0.14); padding: 34px; }
        .login-card h2 { margin: 0 0 4px; font-size: 22px; color: var(--brand); }
        .login-card .lc-sub { font-size: 13px; color: #8a97a3; margin-bottom: 22px; }
        .login-card label { display: block; font-size: 13px; font-weight: 600; color: #5c6b7a; margin: 14px 0 5px; }
        .login-card input { width: 100%; box-sizing: border-box; }
        .lc-btn { width: 100%; margin-top: 20px; padding: 13px; font-size: 15px; }
        .lc-toggle { text-align: center; margin-top: 16px; font-size: 13px; color: #7f8c8d; }
        .lc-toggle a { color: var(--accent); cursor: pointer; font-weight: 600; text-decoration: none; }
        .lc-err { background: #fdedec; color: #c0392b; border: 1px solid #f5b7b1; font-size: 13px; border-radius: 8px; padding: 10px 12px; margin-top: 14px; line-height: 1.5; display: none; }
        .lc-note { margin-top: 18px; font-size: 12px; color: #95a5a6; line-height: 1.6; background: #f8f9fa; border-radius: 8px; padding: 10px 12px; }
        .setup-guide { display: none; margin-top: 14px; background: #f4f8fb; border: 1px solid #d6e6f4; border-radius: 10px; padding: 14px; font-size: 12.5px; line-height: 1.7; color: #46617a; }
        .setup-guide ol { margin: 6px 0; padding-left: 18px; }
        .setup-guide textarea { width: 100%; box-sizing: border-box; font-size: 11px; font-family: monospace; height: 150px; margin-top: 6px; }

        /* ===== 사이드바 ===== */
        aside { width: 260px; background: var(--brand); color: white; display: flex; flex-direction: column; padding: 20px; box-shadow: 4px 0 15px rgba(0,0,0,0.1); z-index: 100; flex-shrink: 0; }
        .logo { font-size: 20px; font-weight: 800; margin-bottom: 26px; cursor: pointer; color: #fff; text-decoration: none; }
        .add-box { display: flex; gap: 5px; margin-bottom: 20px; }
        .add-box input { flex: 1; padding: 10px; border-radius: 5px; border: none; font-size: 14px; min-width: 0; }
        .btn-add { background: var(--accent); color: white; border: none; padding: 0 15px; border-radius: 5px; cursor: pointer; font-weight: bold; }
        .student-list { flex: 1; overflow-y: auto; }
        .student-item { padding: 12px 15px; margin-bottom: 5px; border-radius: 8px; cursor: pointer; transition: 0.2s; display: flex; justify-content: space-between; align-items: center; font-size: 15px; }
        .student-item:hover { background: rgba(255,255,255,0.1); }
        .student-item.active { background: var(--accent); font-weight: bold; box-shadow: 0 2px 8px rgba(0,0,0,0.2); }
        .del-btn { opacity: 0.4; cursor: pointer; font-size: 12px; } .del-btn:hover { opacity: 1; color: #ffadad; }
        .user-box { margin-top: 14px; padding-top: 14px; border-top: 1px solid rgba(255,255,255,0.2); display: flex; align-items: center; gap: 10px; }
        .avatar { width: 36px; height: 36px; border-radius: 50%; background: var(--accent); display: flex; align-items: center; justify-content: center; font-weight: 800; flex-shrink: 0; }
        .ub-name { font-size: 13px; font-weight: 700; line-height: 1.3; }
        .ub-role { font-size: 11px; color: #ffd97a; font-weight: 600; }
        .ub-actions { margin-left: auto; display: flex; flex-direction: column; gap: 4px; }
        .ub-btn { background: rgba(255,255,255,0.12); border: none; color: #fff; font-size: 11px; padding: 4px 8px; border-radius: 5px; cursor: pointer; }
        .ub-btn:hover { background: rgba(255,255,255,0.25); }

        /* ===== 메인 ===== */
        main { flex: 1; display: flex; flex-direction: column; overflow: hidden; position: relative; min-width: 0; }
        header { background: white; padding: 15px 30px; border-bottom: 1px solid var(--line); display: flex; justify-content: space-between; align-items: center; min-height: 60px; box-sizing: border-box; }
        .page-title { font-size: 20px; font-weight: bold; color: var(--brand); }
        .sub-info { font-size: 13px; color: #7f8c8d; margin-left: 10px; }
        .sync-status { font-size: 12px; color: var(--present); font-weight: bold; display: flex; align-items: center; gap: 5px; }
        .hdr-right { display: flex; gap: 8px; align-items: center; }
        .lock-chip { font-size: 12px; font-weight: 700; padding: 6px 12px; border-radius: 20px; cursor: pointer; border: 1px solid transparent; }
        .lock-chip.locked { background: #fdecea; color: #c0392b; border-color: #f5b7b1; }
        .lock-chip.open { background: #eafaf1; color: #1e8449; border-color: #a9dfbf; }
        .eye-toggle { font-size: 12px; font-weight: 600; padding: 6px 12px; border-radius: 20px; border: 1px solid var(--line); background: #fff; color: #666; cursor: pointer; }
        .eye-toggle.on { background: var(--brand); color: #fff; border-color: var(--brand); }

        /* 탭 메뉴 */
        .tabs { background: white; padding: 0 30px; border-bottom: 1px solid var(--line); display: flex; gap: 18px; overflow-x: auto; }
        .tab-btn { padding: 15px 5px; cursor: pointer; color: #95a5a6; font-weight: 600; font-size: 15px; border-bottom: 3px solid transparent; transition: 0.2s; white-space: nowrap; }
        .tab-btn:hover { color: var(--accent); } .tab-btn.active { color: var(--brand); border-bottom-color: var(--brand); }
        .tab-btn.tab-ai.active { color: #6c3fc5; border-bottom-color: #6c3fc5; }

        /* 컨텐츠 */
        .content-area { flex: 1; padding: 30px; overflow-y: auto; }
        .view-section { display: none; animation: fadeIn 0.3s; }
        .view-section.active { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

        /* 카드 & 폼 */
        .card { background: white; border-radius: 12px; padding: 25px; box-shadow: 0 2px 12px rgba(0,0,0,0.04); margin-bottom: 25px; border: 1px solid #f0f0f0; }
        .card-title { font-size: 17px; font-weight: bold; color: var(--brand); border-bottom: 2px solid #f0f0f0; padding-bottom: 10px; margin-bottom: 20px; display: flex; justify-content: space-between; align-items: center; }
        .input-row { display: flex; gap: 10px; flex-wrap: wrap; margin-bottom: 10px; align-items: center; }
        input, select, textarea { padding: 10px; border: 1px solid #ddd; border-radius: 6px; font-family: inherit; font-size: 14px; }
        textarea { width: 100%; resize: vertical; min-height: 100px; box-sizing: border-box; }
        button { padding: 10px 18px; border: none; border-radius: 6px; cursor: pointer; font-weight: bold; color: white; transition: 0.2s; font-size: 14px; }
        .btn-primary { background: var(--brand); } .btn-blue { background: var(--accent); } .btn-green { background: var(--present); }
        .btn-purple { background: #6c3fc5; } .btn-orange { background: var(--editc); }
        .btn-gray { background: #95a5a6; } .btn-red { background: var(--absent); }
        .btn-sm { padding: 6px 12px; font-size: 12px; }
        .hint { font-size: 12px; color: #666; margin: 6px 0 0; line-height: 1.6; }

        /* 캘린더 스타일 */
        .calendar-ctrl { display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; flex-wrap: wrap; gap: 10px; }
        .cal-legend { display: flex; gap: 10px; font-size: 12px; flex-wrap: wrap; }
        .legend-item { display: flex; align-items: center; gap: 5px; }
        .dot { width: 10px; height: 10px; border-radius: 50%; }
        .calendar-grid { display: grid; grid-template-columns: repeat(7, 1fr); gap: 1px; background: var(--line); border: 1px solid var(--line); border-radius: 8px; overflow: hidden; }
        .cal-head { background: #f8f9fa; padding: 10px; text-align: center; font-weight: bold; font-size: 13px; color: #555; }
        .cal-cell { background: white; min-height: 120px; padding: 8px; font-size: 13px; position: relative; transition: background 0.2s; }
        .cal-cell.drag-over { background: #eafaf1; border: 2px dashed var(--present); }
        .cal-date { font-weight: bold; color: #333; margin-bottom: 5px; display: block; pointer-events: none; }
        .cal-cell .today-mark { color: var(--accent); }
        .event-chip { display: flex; justify-content: space-between; align-items: center; padding: 4px 6px; border-radius: 4px; margin-bottom: 4px; font-size: 12px; color: white; transition: 0.2s; cursor: grab; user-select: none; }
        .event-chip:active { cursor: grabbing; opacity: 0.7; }
        .event-chip:hover { filter: brightness(0.95); box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
        .event-info { flex: 1; display: flex; align-items: center; gap: 5px; cursor: pointer; padding: 2px 0; min-width: 0; }
        .event-info:hover { text-decoration: underline; }
        .event-actions { display: flex; gap: 3px; opacity: 0.6; transition: 0.2s; }
        .event-chip:hover .event-actions { opacity: 1; }
        .icon-btn { cursor: pointer; padding: 2px 4px; font-size: 11px; border-radius: 3px; }
        .icon-btn:hover { background: rgba(255,255,255,0.3); }
        .st-plan { background-color: var(--plan); } .st-present { background-color: var(--present); } .st-absent { background-color: var(--absent); } .st-makeup { background-color: var(--makeup); }

        /* 테이블 */
        table { width: 100%; border-collapse: collapse; margin-top: 10px; font-size: 14px; }
        th { background: #f8f9fa; padding: 12px; border-bottom: 2px solid #ddd; text-align: center; color: #555; }
        td { padding: 12px; border-bottom: 1px solid #eee; text-align: center; }
        .empty-row td { color: #aaa; padding: 30px; }

        /* 로그 & 분석 */
        .log-entry { background: #fff; border-left: 5px solid #ddd; padding: 15px; margin-bottom: 15px; border-radius: 0 8px 8px 0; box-shadow: 0 2px 5px rgba(0,0,0,0.05); }
        .log-header { display: flex; justify-content: space-between; margin-bottom: 10px; font-size: 13px; color: #888; border-bottom: 1px solid #f0f0f0; padding-bottom: 5px; gap: 8px; align-items: center; }
        .log-badge { padding: 3px 8px; border-radius: 12px; color: white; font-size: 11px; font-weight: bold; white-space: nowrap; }
        .log-content { line-height: 1.6; color: #444; white-space: pre-line; font-size: 15px; }
        .analysis-box p { margin: 8px 0; font-size: 15px; color: #444; }
        .strength-tag { color: var(--present); font-weight: bold; background: #eafaf1; padding: 2px 6px; border-radius: 4px; }
        .weakness-tag { color: var(--absent); font-weight: bold; background: #fdedec; padding: 2px 6px; border-radius: 4px; }

        /* 배너 / 안내 */
        .banner { border-radius: 10px; padding: 14px 18px; font-size: 13.5px; line-height: 1.6; margin-bottom: 20px; display: flex; gap: 12px; align-items: flex-start; }
        .banner b { display: block; margin-bottom: 2px; }
        .banner.warn { background: #fff8e6; border: 1px solid #f1c40f; color: #7d6608; }
        .banner.info { background: #eaf2f8; border: 1px solid #aed6f1; color: #1f618d; }
        .banner .bn-btn { flex-shrink: 0; }

        /* 스니펫(자주 쓰는 텍스트) */
        .snippet-bar { display: flex; gap: 8px; align-items: center; margin-bottom: 8px; flex-wrap: wrap; }
        .snippet-bar select { flex: 1; min-width: 220px; background: #f4f8fb; border-color: #aed6f1; font-weight: 600; color: var(--brand); }

        /* 행동중재 */
        .behavior-tap { display: flex; gap: 14px; flex-wrap: wrap; }
        .tap-card { flex: 1; min-width: 190px; background: #fff; border: 2px solid var(--line); border-radius: 14px; padding: 18px; text-align: center; }
        .tap-card .tc-name { font-size: 17px; font-weight: 800; color: var(--brand); }
        .tap-card .tc-def { font-size: 12px; color: #7f8c8d; margin: 6px 0 12px; min-height: 30px; line-height: 1.5; }
        .tap-btn { width: 100%; padding: 20px 10px; font-size: 19px; border-radius: 12px; }
        .tap-btn.phase-a { background: var(--ph-a); } .tap-btn.phase-b { background: var(--ph-b); }
        .tap-card .tc-today { margin-top: 10px; font-size: 13px; color: #555; }
        .tap-card .tc-today b { font-size: 19px; color: var(--brand); }
        .phase-chip { display: inline-block; padding: 3px 10px; border-radius: 12px; font-size: 11px; font-weight: 800; color: #fff; }
        .phase-chip.a { background: var(--ph-a); } .phase-chip.b { background: var(--ph-b); }
        .stat-grid { display: flex; gap: 12px; flex-wrap: wrap; margin-top: 12px; }
        .stat-item { flex: 1; min-width: 110px; background: #f8f9fa; border: 1px solid #eee; border-radius: 10px; padding: 12px 14px; }
        .stat-item .si-v { font-size: 21px; font-weight: 800; color: var(--brand); }
        .stat-item .si-l { font-size: 12px; color: #7f8c8d; margin-top: 2px; }
        .stat-item .si-note { font-size: 11px; color: #99a3ac; margin-top: 4px; line-height: 1.4; }

        /* AI 리포트 */
        .report { background: #fff; border: 1px solid var(--line); border-radius: 12px; padding: 40px 44px; box-shadow: 0 2px 12px rgba(0,0,0,0.04); max-width: 860px; margin: 0 auto; }
        .report .rp-head { text-align: center; border-bottom: 3px double var(--brand); padding-bottom: 18px; margin-bottom: 26px; }
        .report .rp-org { font-size: 13px; letter-spacing: 4px; color: #7f8c8d; font-weight: 700; }
        .report .rp-title { font-size: 24px; font-weight: 800; color: var(--brand); margin: 8px 0 6px; }
        .report .rp-meta { font-size: 13px; color: #7f8c8d; }
        .report h3 { font-size: 16px; color: var(--brand); border-left: 4px solid var(--accent); padding-left: 10px; margin: 26px 0 10px; }
        .report p, .report li { font-size: 14px; line-height: 1.85; color: #3d4852; }
        .report ul { padding-left: 20px; margin: 8px 0; }
        .report .rp-dis { margin-top: 30px; font-size: 11.5px; color: #9aa5ae; border-top: 1px solid #eee; padding-top: 12px; line-height: 1.6; }
        .ai-tag { display: inline-flex; align-items: center; gap: 6px; background: #f3ecfd; color: #6c3fc5; font-size: 12px; font-weight: 800; padding: 4px 12px; border-radius: 14px; }
        .ai-out { white-space: pre-line; background: #fbfafd; border: 1px solid #e8ddf9; border-radius: 10px; padding: 18px 20px; font-size: 14px; line-height: 1.9; color: #444; }
        .ai-out strong { color: #6c3fc5; }
        .an-block { border: 1px solid #eee; border-radius: 10px; padding: 14px 16px; margin-bottom: 10px; font-size: 14px; line-height: 1.75; color: #444; background: #fdfefe; }
        .an-block b { color: var(--brand); }

        /* 모달 */
        .mbk { position: fixed; inset: 0; background: rgba(24,34,46,0.55); display: none; align-items: center; justify-content: center; z-index: 2000; padding: 20px; }
        .mbk.open { display: flex; }
        .modal { background: #fff; border-radius: 14px; width: min(600px, 94vw); max-height: 90vh; overflow-y: auto; padding: 26px; box-shadow: 0 20px 60px rgba(0,0,0,0.3); }
        .modal h3 { margin: 0 0 16px; color: var(--brand); font-size: 18px; border-bottom: 2px solid #f0f0f0; padding-bottom: 10px; }
        .modal .mo-row { margin-bottom: 14px; }
        .modal label { display: block; font-size: 13px; font-weight: 600; color: #5c6b7a; margin-bottom: 5px; }
        .modal input, .modal select, .modal textarea { width: 100%; box-sizing: border-box; }
        .mo-foot { display: flex; justify-content: flex-end; gap: 8px; margin-top: 18px; }
        .setting-nav { display: flex; gap: 6px; flex-wrap: wrap; margin-bottom: 18px; }
        .setting-nav button { background: #f0f3f6; color: #555; font-size: 12.5px; padding: 8px 13px; }
        .setting-nav button.on { background: var(--brand); color: #fff; }
        .rules-box { width: 100%; box-sizing: border-box; font-family: monospace; font-size: 11.5px; height: 230px; background: #f8f9fa; border: 1px solid #ddd; border-radius: 8px; padding: 10px; color: #444; }

        /* 승인 대기 & 유저 테이블 */
        .pending-box { position: fixed; inset: 0; z-index: 4000; background: var(--bg); display: none; align-items: center; justify-content: center; flex-direction: column; gap: 14px; text-align: center; padding: 30px; }
        .pending-box .pb-card { background: #fff; border-radius: 16px; padding: 40px; box-shadow: 0 12px 40px rgba(44,62,80,0.14); max-width: 420px; }
        .pending-box h2 { color: var(--brand); margin: 0 0 10px; }
        .pending-box p { color: #7f8c8d; font-size: 14px; line-height: 1.7; }
        .user-row-bad { background: #fdecea; color: #c0392b; padding: 2px 8px; border-radius: 10px; font-size: 11px; font-weight: 700; }
        .user-row-ok { background: #eafaf1; color: #1e8449; padding: 2px 8px; border-radius: 10px; font-size: 11px; font-weight: 700; }

        /* 토스트 */
        #toast { position: fixed; bottom: 26px; left: 50%; transform: translateX(-50%) translateY(80px); background: #2c3e50; color: #fff; padding: 13px 22px; border-radius: 10px; font-size: 14px; font-weight: 600; box-shadow: 0 8px 30px rgba(0,0,0,0.25); opacity: 0; transition: 0.3s; z-index: 3000; pointer-events: none; max-width: 80vw; }
        #toast.show { transform: translateX(-50%) translateY(0); opacity: 1; }
        #toast.err { background: #c0392b; }

        /* 로딩 */
        #loader { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: white; z-index: 9999; display: flex; justify-content: center; align-items: center; flex-direction: column; }
        .app-root { display: none; width: 100%; height: 100vh; }
        .app-root.on { display: flex; }

        /* ===== 학부모 포털 ===== */
        .parent-top { background: var(--brand); color: #fff; padding: 14px 22px; display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 8px; }
        .parent-top .pt-title { font-size: 17px; font-weight: 800; }
        .parent-wrap { max-width: 900px; margin: 0 auto; padding: 26px 20px 60px; }
        .parent-child { display: flex; gap: 16px; align-items: center; background: #fff; border-radius: 14px; padding: 20px 24px; box-shadow: 0 2px 12px rgba(0,0,0,0.05); margin-bottom: 20px; flex-wrap: wrap; }
        .parent-child .pc-ava { width: 52px; height: 52px; border-radius: 50%; background: var(--accent); color: #fff; display: flex; align-items: center; justify-content: center; font-size: 22px; font-weight: 800; }
        .parent-sec { background: #fff; border-radius: 14px; padding: 22px 24px; box-shadow: 0 2px 12px rgba(0,0,0,0.05); margin-bottom: 20px; }
        .parent-sec h3 { margin: 0 0 12px; font-size: 16px; color: var(--brand); border-left: 4px solid var(--accent); padding-left: 10px; }
        .memo-item { border-left: 4px solid #27ae60; background: #f8fbf8; border-radius: 0 8px 8px 0; padding: 12px 14px; margin-bottom: 10px; }
        .memo-item .mi-date { font-size: 12px; color: #888; margin-bottom: 4px; }
        .memo-item .mi-body { font-size: 14px; line-height: 1.7; color: #444; white-space: pre-line; }
        .sched-row { display: flex; gap: 10px; align-items: center; padding: 9px 4px; border-bottom: 1px solid #f0f0f0; font-size: 14px; }
        .badge { padding: 3px 10px; border-radius: 12px; color: #fff; font-size: 11.5px; font-weight: 700; }
        .code-chip { font-family: monospace; font-size: 15px; font-weight: 800; letter-spacing: 2px; background: #f4f8fb; border: 1px dashed #aed6f1; color: var(--brand); padding: 4px 10px; border-radius: 8px; }

        @media print {
            aside, .tabs, .no-print, header button, .mbk, #toast, .banner { display: none !important; }
            main { background: white; }
            .card { box-shadow: none; border: 1px solid #ddd; break-inside: avoid; }
            .view-section { display: none !important; }
            .view-section.print-target { display: block !important; }
            body, .app-root { height: auto; overflow: visible; display: block; }
            .content-area { overflow: visible; padding: 0; }
            .report { box-shadow: none; border: none; padding: 0; max-width: 100%; }
        }
        @media (max-width: 900px) {
            body { flex-direction: column; height: auto; overflow: auto; }
            aside { width: 100%; box-sizing: border-box; }
            .student-list { max-height: 200px; }
            main { overflow: visible; }
            .login-wrap { flex-direction: column; overflow-y: auto; }
            .login-brand { padding: 30px; }
            .content-area { padding: 16px; }
        }
    </style>
</head>
<body>
<div id="loader">
    <div style="font-size: 24px; font-weight: bold; color: #2c3e50; margin-bottom: 10px;">☁️ 솔빛 연구소 V15</div>
    <div style="color: #7f8c8d;">학부모 포털 모듈 로딩 중...</div>
</div>

<!-- ===== 로그인 ===== -->
<div class="login-wrap" id="loginWrap" style="display:none;">
    <div class="login-brand">
        <div>
            <div style="font-size:15px; font-weight:700; color:#ffd97a; letter-spacing:2px; margin-bottom:10px;">솔빛언어심리연구소</div>
            <h1>아동 학습·심리 기록 관리</h1>
            <div class="lb-sub">훈련 데이터 · 검사 결과 · 상담 일지 · 행동중재 · AI 종합 리포트</div>
            <ul class="lb-feats">
                <li><b>주의력 훈련</b> 시간·오류 추이를 영역별 그래프로 추적합니다</li>
                <li><b>행동중재</b> 기초선→중재 단일대상설계로 중재 효과를 확인합니다</li>
                <li><b>AI 종합리포트</b> 훈련 기록과 상담 일지를 합쳐 소견을 자동 작성합니다</li>
                <li><b>실명 보호</b> 학생 이름은 암호화 저장되고 화면에서는 마스킹됩니다</li>
                <li><b>학부모 포털</b> 승인된 학부모가 우리 아이 리포트·출석·가정 연계 메모를 확인합니다</li>
            </ul>
        </div>
        <div class="lb-foot">V14 · 관리자 승인 후 이용할 수 있습니다</div>
    </div>
    <div class="login-side">
        <div class="login-card">
            <h2 id="lcTitle">로그인</h2>
            <div class="lc-sub" id="lcSub">연구소 계정으로 로그인해 주세요</div>
            <div id="lcNameWrap" style="display:none;">
                <label>이름 (실명)</label>
                <input type="text" id="lcName" placeholder="예: 김솔빛">
                <label style="margin-top:10px;">가입 유형</label>
                <div style="display:flex; gap:16px; font-size:14px; margin-top:2px;">
                    <label style="font-weight:500; display:flex; gap:5px; align-items:center; margin:0;"><input type="radio" name="lcRole" value="teacher" checked onchange="lcRoleChanged()">교사/연구자</label>
                    <label style="font-weight:500; display:flex; gap:5px; align-items:center; margin:0;"><input type="radio" name="lcRole" value="parent" onchange="lcRoleChanged()">학부모</label>
                </div>
                <div id="lcCodeWrap" style="display:none;">
                    <label>아이 연결 코드 (연구소에서 안내받은 6자리)</label>
                    <input type="text" id="lcCode" placeholder="예: 482913" maxlength="6" inputmode="numeric">
                </div>
            </div>
            <label>이메일</label>
            <input type="email" id="lcEmail" placeholder="email@example.com">
            <label>비밀번호 (6자 이상)</label>
            <input type="password" id="lcPass" placeholder="비밀번호" onkeydown="if(event.key==='Enter')lcSubmit()">
            <div class="lc-err" id="lcErr"></div>
            <button class="btn-blue lc-btn" id="lcBtn" onclick="lcSubmit()">로그인</button>
            <div class="lc-toggle" id="lcToggle">
                처음이신가요? <a onclick="lcMode()">계정 만들기</a>
            </div>
            <div class="lc-note">
                가입 즉시 이용되지 않고 <b>관리자 승인</b> 후 입장합니다. 승인 계정은 관리자에게 문의해 주세요.<br>
                <a href="?demo" style="color:#3498db; font-weight:600; cursor:pointer;" onclick="location.search='?demo'">▶ 데모: 관리자 화면</a> · <a href="?demo&as=parent" style="color:#3498db; font-weight:600; cursor:pointer;" onclick="location.search='?demo&as=parent'">▶ 데모: 학부모 포털</a>
            </div>
            <div class="setup-guide" id="setupGuide">
                <b>🛠 Firebase 설정이 필요합니다 (관리자 전용, 최초 1회)</b>
                <ol>
                    <li>Firebase 콘솔(<b>sobit-d4e76</b> 프로젝트) → <b>Authentication</b> → 시작하기</li>
                    <li>로그인 방법에서 <b>이메일/비밀번호</b> 사용 설정</li>
                    <li>Realtime Database → <b>규칙</b> 탭에 아래 규칙 붙여넣기 → 게시</li>
                </ol>
                <textarea id="rulesBox" readonly></textarea>
                <button class="btn-blue btn-sm" style="margin-top:6px;" onclick="copyRules()">📋 규칙 복사</button>
            </div>
        </div>
    </div>
</div>

<!-- ===== 승인 대기 ===== -->
<div class="pending-box" id="pendingBox">
    <div class="pb-card">
        <div style="font-size:44px;">⏳</div>
        <h2>승인 대기 중</h2>
        <p>계정이 만들어졌지만 아직 관리자 승인 전입니다.<br>승인되면 알림 없이 자동 입장할 수 있도록<br>잠시 후 다시 로그인해 주세요.</p>
        <button class="btn-gray" onclick="doLogout()">로그아웃</button>
    </div>
</div>

<!-- ===== 앱 ===== -->
<div class="app-root" id="appRoot">
<aside class="no-print">
    <div class="logo" onclick="goHome()">📅 솔빛 연구소</div>
    <div class="add-box">
        <input type="text" id="newStudentName" placeholder="학생 실명 입력">
        <button class="btn-add" onclick="addStudentUI()">+</button>
    </div>
    <div style="font-size: 12px; color: rgba(255,255,255,0.6); margin-bottom: 10px; font-weight: bold;" id="listLabel">STUDENT LIST (가나다순)</div>
    <div class="student-list" id="studentList"></div>
    <div style="margin-top:auto; padding-top:15px; border-top:1px solid rgba(255,255,255,0.2);">
        <div class="user-box">
            <div class="avatar" id="ubAvatar">?</div>
            <div>
                <div class="ub-name" id="ubName">-</div>
                <div class="ub-role" id="ubRole">-</div>
            </div>
            <div class="ub-actions">
                <button class="ub-btn" onclick="openSettings()">⚙️ 설정</button>
                <button class="ub-btn" onclick="doLogout()">로그아웃</button>
            </div>
        </div>
    </div>
</aside>

<main>
    <header>
        <div>
            <div style="display:flex; align-items:center; gap:10px;">
                <span class="page-title" id="headerTitle">전체 스케줄</span>
                <span class="sync-status" id="syncStatus">● 온라인</span>
            </div>
            <span class="sub-info" id="headerSub">공유 일정 · 학생 관리</span>
        </div>
        <div class="hdr-right no-print">
            <span class="lock-chip locked" id="lockChip" onclick="lockChipClick()">🔒 실명 잠김</span>
            <button class="eye-toggle" id="eyeToggle" onclick="toggleRealView()" title="실명 표시 전환">마스킹 보기</button>
            <button class="btn-primary" onclick="window.print()">🖨️ 출력</button>
        </div>
    </header>

    <div class="tabs no-print" id="moduleTabs" style="display:none;">
        <div class="tab-btn" onclick="switchTab('attention', this)">1. 주의력 훈련</div>
        <div class="tab-btn" onclick="switchTab('wisc', this)">2. WISC-V 분석</div>
        <div class="tab-btn" onclick="switchTab('counsel', this)">3. 상담/수업 일지</div>
        <div class="tab-btn" onclick="switchTab('behavior', this)">4. 행동중재</div>
        <div class="tab-btn" onclick="switchTab('dev', this)">5. 발달 종합평가</div>
        <div class="tab-btn tab-ai" onclick="switchTab('aireport', this)">🤖 AI 종합리포트</div>
    </div>

    <div class="content-area" id="contentArea">

        <!-- 전체 스케줄 -->
        <div id="view-schedule" class="view-section active">
            <div class="banner warn no-print" id="openRulesBanner" style="display:none;">
                <div><b>⚠️ 데이터 보안 점검 필요</b>현재 Firebase 규칙이 열려 있어 누구나 데이터에 접근할 수 있습니다. 설정 → 보안 탭의 안내에 따라 규칙을 잠가 주세요.</div>
                <button class="btn-orange bn-btn btn-sm" onclick="openSettings('sec')">설정 열기</button>
            </div>
            <div class="card">
                <div class="calendar-ctrl">
                    <div><button class="btn-primary" onclick="changeMonth(-1)">◀</button><strong id="calYearMonth" style="font-size:18px; margin:0 15px;"></strong><button class="btn-primary" onclick="changeMonth(1)">▶</button></div>
                    <div class="cal-legend"><div class="legend-item"><div class="dot st-plan"></div>예정</div><div class="legend-item"><div class="dot st-present"></div>출석</div><div class="legend-item"><div class="dot st-absent"></div>결석</div><div class="legend-item"><div class="dot st-makeup"></div>보강완료</div></div>
                    <div><button class="btn-green" onclick="addScheduleModal()">+ 일정</button></div>
                </div>
                <div class="calendar-grid" id="calendarGrid"></div>
                <p class="hint">* 상태 칩을 클릭하면 예정→출석→결석→보강으로 순환합니다. 드래그로 날짜 이동, <b>Ctrl+드래그</b>로 복사, 📄로 다른 날짜 복사.</p>
            </div>
            <div class="card" id="makeupAlertBox" style="display:none; border-left: 5px solid var(--absent);">
                <div class="card-title" style="margin-bottom:0; border:none; color: var(--absent);">⚠️ 보강 필요 수업</div>
                <div id="makeupList" style="margin-top:10px; color:#555;"></div>
            </div>
        </div>

        <!-- 1. 주의력 훈련 -->
        <div id="view-attention" class="view-section">
            <div class="card no-print">
                <div class="card-title">📉 훈련 데이터 입력 / 수정</div>
                <div class="input-row" id="attInputForm">
                    <select id="attCat"><option value="visual">시각주의력</option><option value="auditory">청각주의력</option><option value="speed">처리속도</option></select>
                    <input type="text" id="attSub" placeholder="세부활동 (예: 기호쓰기)" list="subListOptions" style="width: 150px;">
                    <datalist id="subListOptions"></datalist>
                    <input type="date" id="attDate">
                    <div style="display:flex; align-items:center; gap:5px; background:#f9f9f9; padding:2px 8px; border-radius:6px; border:1px solid #ddd;">
                        <input type="number" id="attMin" placeholder="분" style="width: 45px; border:none; text-align:right;"> <span style="font-size:12px; color:#666;">분</span>
                        <input type="number" id="attSec" placeholder="초" style="width: 45px; border:none; text-align:right;"> <span style="font-size:12px; color:#666;">초</span>
                    </div>
                    <input type="number" id="attError" placeholder="오류(개)" style="width: 80px;">
                    <input type="text" id="attNote" placeholder="메모" style="flex: 1; min-width:120px;">
                    <button id="btnAttSubmit" class="btn-primary" onclick="addAttention()">추가</button>
                    <button id="btnAttCancel" class="btn-gray" onclick="cancelEdit()" style="display:none;">취소</button>
                </div>
                <p class="hint">* 리스트의 '✎' 버튼을 누르면 기존 데이터를 수정할 수 있습니다.</p>
            </div>
            <div class="card no-print" style="padding: 15px; background: #eaf2f8; display:flex; align-items:center; gap:10px; border-left: 5px solid #3498db;">
                <span style="font-weight:bold; color:#2c3e50;">📊 그래프 필터:</span>
                <select id="graphFilter" onchange="renderAttention()" style="flex:1; max-width:300px; font-weight:bold; color:#2c3e50;">
                    <option value="all">전체 데이터 보기 (모든 활동)</option>
                </select>
            </div>
            <div class="card" id="attAiBox" style="border-left:5px solid #6c3fc5;">
                <div class="card-title" style="color:#6c3fc5;">🤖 AI 데이터 분석 <span id="attAiTag" class="ai-tag">규칙 기반 분석</span></div>
                <div id="attAiOut" class="an-block">데이터가 입력되면 자동으로 분석됩니다.</div>
            </div>
            <div class="card">
                <div class="card-title" style="color: #3498db;">👁️ 시각주의력 추이 (Visual)</div>
                <div style="height: 250px;"><canvas id="chartVisual"></canvas></div>
            </div>
            <div class="card">
                <div class="card-title" style="color: #27ae60;">👂 청각주의력 추이 (Auditory)</div>
                <div style="height: 250px;"><canvas id="chartAuditory"></canvas></div>
            </div>
            <div class="card">
                <div class="card-title" style="color: #e67e22;">⚡ 처리속도 추이 (Speed)</div>
                <div style="height: 250px;"><canvas id="chartSpeed"></canvas></div>
            </div>
            <div class="card">
                <div class="card-title">상세 기록</div>
                <table id="attTable">
                    <thead><tr><th>영역/활동</th><th>날짜</th><th>수행시간</th><th style="color:#d35400;">오류(개)</th><th>메모</th><th>관리</th></tr></thead>
                    <tbody></tbody>
                </table>
            </div>
        </div>

        <!-- 2. WISC-V -->
        <div id="view-wisc" class="view-section">
            <div class="card no-print">
                <div class="card-title">🧠 지능검사 결과 입력</div>
                <div class="input-row"><label>검사일:</label><input type="date" id="wDate"><label>FSIQ:</label><input type="number" id="wFsiq" style="width:55px" min="40" max="160"><label>언어(VCI):</label><input type="number" id="wVci" style="width:55px" min="40" max="160"><label>시공간(VSI):</label><input type="number" id="wVsi" style="width:55px" min="40" max="160"><label>유동(FRI):</label><input type="number" id="wFri" style="width:55px" min="40" max="160"><label>작업(WMI):</label><input type="number" id="wWmi" style="width:55px" min="40" max="160"><label>처리(PSI):</label><input type="number" id="wPsi" style="width:55px" min="40" max="160"><button class="btn-blue" onclick="saveWisc()">분석</button></div><textarea id="wComment" placeholder="종합 소견..." style="margin-top:10px;"></textarea>
            </div>
            <div class="card" style="display:flex; gap:30px; flex-wrap:wrap;"><div style="flex:1; min-width:300px; height:400px;"><canvas id="wiscChart"></canvas></div><div style="flex:1; min-width:260px; padding-top:20px;"><div class="card-title">📊 분석 요약</div><div id="wiscSummary" class="analysis-box">데이터 없음</div></div></div>
        </div>

        <!-- 3. 상담/수업일지 -->
        <div id="view-counsel" class="view-section">
            <div class="card no-print">
                <div class="card-title">📝 상담 및 관찰 일지</div>
                <div class="snippet-bar">
                    <span style="font-size:13px; font-weight:700; color:#2c3e50;">💬 자주 쓰는 문구:</span>
                    <select id="snippetSelect" onchange="insertSnippet(this)"><option value="">— 선택하면 입력창에 삽입됩니다 —</option></select>
                    <button class="btn-gray btn-sm" onclick="openSnippetManager()">✎ 관리</button>
                </div>
                <div class="input-row"><input type="date" id="cnsDate"><select id="cnsType"><option value="수업관찰">👀 수업 태도</option><option value="학부모상담">🗣️ 학부모 상담</option><option value="과제부여">🏠 가정 연계</option></select></div>
                <textarea id="cnsContent" placeholder="내용 입력... (위 '자주 쓰는 문구'에서 빠르게 입력 가능)"></textarea>
                <div style="display:flex; justify-content:space-between; align-items:center; margin-top:10px; gap:10px; flex-wrap:wrap;">
                    <label style="display:flex; gap:6px; align-items:center; font-size:13px; color:#555; font-weight:600; margin:0;"><input type="checkbox" id="cnsShared" style="width:auto;">👨‍👩‍👧 학부모 포털에 공유</label>
                    <button class="btn-primary" onclick="addCounsel()">저장</button>
                </div>
            </div>
            <div class="card"><div class="card-title">히스토리</div><div id="counselList"></div></div>
        </div>

        <!-- 4. 행동중재 -->
        <div id="view-behavior" class="view-section">
            <div class="card no-print">
                <div class="card-title">🎯 표적행동 등록
                    <span style="font-size:12px; color:#888; font-weight:400;">기초선(A)에서 중재(B)로 전환하며 중재 효과를 확인하는 단일대상설계</span>
                </div>
                <div class="input-row">
                    <input type="text" id="bhName" placeholder="표적행동 (예: 자리이탈)" style="width:180px;">
                    <input type="text" id="bhDef" placeholder="조작적 정의 (예: 수업 중 엉덩이가 의자에서 떨어진 상태)" style="flex:1; min-width:200px;">
                    <button class="btn-green" onclick="addBehavior()">등록</button>
                </div>
                <p class="hint">* 표적행동은 <b>관찰 가능하고 세어서 기록할 수 있는 행동</b>으로 정의해야 합니다. 기초선 회기는 5회 이상 권장(최소 3회).</p>
            </div>
            <div class="card">
                <div class="card-title">👆 빈도 기록 (오늘)</div>
                <div class="behavior-tap" id="behaviorTaps" style="text-align:center; color:#999; padding:10px;">등록된 표적행동이 없습니다.</div>
            </div>
            <div class="card" id="bhChartCard" style="display:none;">
                <div class="card-title" id="bhChartTitle">📈 추이 그래프</div>
                <div style="height: 300px;"><canvas id="bhChart"></canvas></div>
                <div class="stat-grid" id="bhStats"></div>
                <p class="hint" id="bhHint"></p>
            </div>
            <div class="card" id="bhListCard" style="display:none;">
                <div class="card-title">기록 히스토리</div>
                <div style="overflow-x:auto;"><table id="bhTable">
                    <thead><tr><th>날짜</th><th>단계</th><th>횟수</th><th>비고</th><th>관리</th></tr></thead>
                    <tbody></tbody>
                </table></div>
            </div>
        </div>

        <!-- 5. 발달 -->
        <div id="view-dev" class="view-section">
            <div class="card no-print">
                <div class="card-title">🌱 발달 영역별 평가</div>
                <div class="input-row" style="justify-content:space-between;"><div><label>언어</label> <select id="dLang"><option value="1">1</option><option value="2">2</option><option value="3">3</option><option value="4">4</option><option value="5">5</option></select></div><div><label>인지</label> <select id="dCog"><option value="1">1</option><option value="2">2</option><option value="3">3</option><option value="4">4</option><option value="5">5</option></select></div><div><label>사회성</label> <select id="dSoc"><option value="1">1</option><option value="2">2</option><option value="3">3</option><option value="4">4</option><option value="5">5</option></select></div><div><label>대근육</label> <select id="dGross"><option value="1">1</option><option value="2">2</option><option value="3">3</option><option value="4">4</option><option value="5">5</option></select></div><div><label>소근육</label> <select id="dFine"><option value="1">1</option><option value="2">2</option><option value="3">3</option><option value="4">4</option><option value="5">5</option></select></div><button class="btn-blue" onclick="saveDev()">저장</button></div><textarea id="dComment" placeholder="소견..." style="margin-top:10px;"></textarea>
            </div>
            <div class="card"><div class="card-title">발달 균형 그래프</div><div style="height: 350px;"><canvas id="devChart"></canvas></div></div>
            <div class="card" id="devHistoryCard" style="display:none;"><div class="card-title">발달 평가 기록 (시점별)</div><table id="devHistoryTable"><thead><tr><th>평가일</th><th>언어</th><th>인지</th><th>사회성</th><th>대근육</th><th>소근육</th><th>소견</th></tr></thead><tbody></tbody></table></div>
        </div>

        <!-- AI 종합리포트 -->
        <div id="view-aireport" class="view-section">
            <div class="card no-print" style="background:#f7f4fd; border-left:5px solid #6c3fc5;">
                <div class="card-title" style="color:#6c3fc5;">🤖 AI 종합리포트 생성</div>
                <div class="input-row">
                    <span style="font-size:13px; font-weight:700;">기간:</span>
                    <select id="aiPeriod" style="font-weight:600;">
                        <option value="30">최근 1개월</option>
                        <option value="90">최근 3개월</option>
                        <option value="all">전체 기간</option>
                    </select>
                    <button class="btn-purple" onclick="generateReport()">📊 리포트 생성</button>
                    <button class="btn-purple" style="opacity:0.9;" onclick="generateAiOpinion()" id="btnAiOp">✨ AI 소견 보강</button>
                    <button class="btn-primary" onclick="printReport()">🖨️ 인쇄 (학부모용)</button>
                </div>
                <p class="hint" id="aiKeyHint">* 'AI 소견 보강'은 설정 → AI 탭에서 무료 Gemini API 키를 등록하면 사용할 수 있습니다. 미등록 시 데이터 통계 기반 분석만 제공됩니다.</p>
            </div>
            <div id="reportWrap">
                <div style="text-align:center; color:#999; padding:60px 0;">학생을 선택한 뒤 리포트를 생성해 주세요.</div>
            </div>
        </div>

    </div>
</main>
</div>

<!-- ===== 학부모 포털 ===== -->
<div class="app-root" id="parentShell" style="flex-direction:column;">
    <div class="parent-top no-print">
        <div class="pt-title">🌻 솔빛언어심리연구소 학부모 포털</div>
        <div style="display:flex; gap:8px; align-items:center;">
            <span style="font-size:13px;" id="ptUser"></span>
            <button class="btn-gray btn-sm" onclick="window.print()">🖨️ 인쇄</button>
            <button class="btn-gray btn-sm" onclick="doLogout()">로그아웃</button>
        </div>
    </div>
    <div class="parent-wrap" id="parentWrap">
        <div style="text-align:center; color:#999; padding:60px 0;">자료를 불러오는 중...</div>
    </div>
</div>

<!-- ===== 공용 모달 ===== -->
<div class="mbk" id="mbk" onclick="if(event.target===this)closeModal()">
    <div class="modal" id="modalBody"></div>
</div>

<div id="toast"></div>
<script>
    // ==================== 설정 ====================
    const firebaseConfig = {
      apiKey: "AIzaSyCX14dInsyCfvmRu0nKWoCX9mE2pxLKGk8",
      authDomain: "sobit-d4e76.firebaseapp.com",
      databaseURL: "https://sobit-d4e76-default-rtdb.firebaseio.com",
      projectId: "sobit-d4e76",
      storageBucket: "sobit-d4e76.firebasestorage.app",
      messagingSenderId: "870330568420",
      appId: "1:870330568420:web:2f503a7a1df13a006326f1",
      measurementId: "G-4ER2M0RKGF"
    };

    const IS_DEMO = new URLSearchParams(location.search).has('demo') || location.hash === '#demo';
    const ORG_NAME = "솔빛언어심리연구소";

    // ==================== 데모용 Firebase/Auth 대체 모듈 ====================
    let DemoState = null;
    function demoLoad(){ try { return JSON.parse(localStorage.getItem('solbit_demo_db')) || null; } catch(e){ return null; } }
    function demoSaveUser(){ try { const u = DemoState ? (DemoState.users||{}) : {}; localStorage.setItem('solbit_demo_users', JSON.stringify(u)); } catch(e){} }
    function demoLoadUsers(){ try { return JSON.parse(localStorage.getItem('solbit_demo_users')) || {}; } catch(e){ return {}; } }

    const DemoDB = {
      users: {},
      init(onReady, onError){
        DemoState = demoLoad() || DemoSeed();
        this.users = demoLoadUsers();
        localStorage.setItem('solbit_demo_db', JSON.stringify(DemoState));
        setTimeout(()=>{ onReady(DemoState); this._usersCb && this._usersCb(this.users||{}); }, 60);
      },
      set(path, value){
        if(!path){ DemoState = JSON.parse(JSON.stringify(value)); }
        else { const segs = path.split('/'); let t = DemoState; for(let i=0;i<segs.length-1;i++){ t[segs[i]] = t[segs[i]]||{}; t = t[segs[i]]; } if(value===null) delete t[segs[segs.length-1]]; else t[segs[segs.length-1]] = value; }
        localStorage.setItem('solbit_demo_db', JSON.stringify(DemoState));
        this._h && this._h(DemoState);
        return Promise.resolve();
      },
      getUser(uid){ const users = demoLoadUsers(); if(!users[uid]){ users[uid] = {name:'데모 관리자', email:'demo@sobit.kr', role:'admin', approved:true}; try{ localStorage.setItem('solbit_demo_users', JSON.stringify(users)); }catch(e){} } return Promise.resolve(users[uid]); },
      setParentViews(map){ DemoState.parentViews = map; localStorage.setItem('solbit_demo_db', JSON.stringify(DemoState)); return Promise.resolve(); },
      getParentView(uid){ if(!DemoState) DemoState = demoLoad() || DemoSeed(); if(!Object.keys(db.students||{}).length){ db.students = DemoState.students; db.schedule = DemoState.schedule||[]; } const stored = (DemoState.parentViews||{})[uid]; if(stored) return Promise.resolve(stored);
        const users = demoLoadUsers(); const u = users[uid];
        if(!u || u.role!=='parent' || !u.childSid) return Promise.resolve(null);
        const st = DemoState.students[u.childSid]; if(!st) return Promise.resolve(null);
        const nm = st.namePlain || st.maskName || '아이';
        const schedule = (DemoState.schedule||[]).filter(s=>s.sid===u.childSid).sort((a,b)=>(b.date||'').localeCompare(a.date||'')).slice(0,20).map(s=>({date:s.date,time:s.time,status:s.status}));
        const memos = (st.counsel||[]).filter(c=>c.shared).sort((a,b)=>(b.date||'').localeCompare(a.date||'')).slice(0,20).map(c=>({date:c.date,type:c.type,content:c.content}));
        return Promise.resolve({ childName:nm, childMask:st.maskName||'', reportHtml: st.aiReport ? buildReportHtml(u.childSid, st.aiReport.period, {childName:nm}) : '', schedule, memos, updatedAt: Date.now() });
      },
      setUsersAll(allUsers){ this.users = allUsers; demoSaveUser(); return Promise.resolve(); },
      subscribeUsers(cb){ this._usersCb = cb; return Promise.resolve(); },
      onUserChange(uid, profile){ this.users = this.users||{}; this.users[uid] = profile; demoSaveUser(); }
    };

    function DemoSeed(){
      const d = new Date(); const fmt = (off)=>{ const x=new Date(d); x.setDate(x.getDate()-off); return x.toISOString().slice(0,10); };
      const now = Date.now();
      const mk = (id, namePlain, att) => ({ id, namePlain, maskName: namePlain[0]+'**', parentCode: id==='s1'?'482913':'195748', attention: att, wisc:{scores:["98","105","112","102","88","90"], comment:"언어·시공간 대비 작업기억과 처리속도가 상대적으로 약함. 주의력 훈련 병행 권장.", date:fmt(120)}, counsel:[], dev:{scores:[4,3,3,4,3], comment:"", history:[]}, behaviors:[] });
      const att = (cat, sub, times, errs) => times.map((t,i)=>({ id: now+i+Math.floor(Math.random()*1000), cat, sub, date: fmt((times.length-1-i)*7), time: t, error: errs[i], note: "" }));
      const s1 = mk('s1','김하은', att('visual','기호쓰기',[320,300,285,260,240,215,200,182],[8,7,7,6,5,4,3,2])
        .concat(att('auditory','음운변별',[300,295,290,285,283,280,278,276],[9,9,8,8,8,7,8,7])));
      s1.counsel = [
        { id:now+1, date:fmt(28), type:'수업관찰', content:'수업 초반 10분간 집중 양호. 후반부로 갈수록 산만 행동 증가하나 자발적 복귀함.' },
        { id:now+2, date:fmt(14), type:'학부모상담', content:'가정 연습 기록 공유. 주 3회 이상 가정 학습 권고드림. 보호자 협조 양호.', shared:true },
        { id:now+3, date:fmt(3), type:'과제부여', content:'주간 과제: 기호쓰기 활동지 3회 배부. 다음 수업 시 제출 확인 필요.', shared:true }
      ];
      s1.behaviors = [{ id:'b1', name:'자리이탈', definition:'수업 중 엉덩이가 의자에서 완전히 떨어진 상태', phase:'B',
        records: (()=>{ const rec={}; const a=[4,3,4,3,5], b=[3,2,2,1,1,1]; a.forEach((c,i)=>rec[fmt(6*10-i*7)]={count:c, phase:'A', note:''}); b.forEach((c,i)=>rec[fmt(56-i*7)]={count:c, phase:'B', note:''}); return rec; })()
      }];
      s1.aiReport = { date: fmt(0), period:'30', text:'최근 1개월간 기호쓰기 수행시간이 꾸준히 감소하고 오류도 함께 줄어 활동 자동화가 진행 중입니다. 청각주의력은 개선 폭이 작아 청각 과제 난이도 조정과 반복 노출을 병행할 것을 제안드립니다. 자리이탈 행동은 중재 단계 진입 후 뚜렷하게 감소해 중재가 효과적인 것으로 보입니다.' };
      const s2 = mk('s2','박도윤', att('visual','따라그리기',[280,262,240,225,210],[6,5,4,3,3]).concat(att('speed','기호찾기',[200,190,175,160,150],[5,4,4,3,2])));
      s2.counsel = [{ id:now+4, date:fmt(7), type:'수업관찰', content:'지시 이해가 빠르고 과제 착수 즉시적. 소근육 활동에서 협응 오류 다소 관찰됨.' }];
      return {
        v: 15,
        crypto: { enabled: true, salt: 'ZGVtbw==', check: 'DEMO_CHECK' },
        students: { s1, s2 },
        schedule: [
          { id: now+10, sid:'s1', date: fmt(0), time:'14:00', status:'present' },
          { id: now+11, sid:'s1', date: fmt(7), time:'14:00', status:'present' },
          { id: now+12, sid:'s1', date: fmt(14), time:'14:00', status:'absent' },
          { id: now+13, sid:'s2', date: fmt(1), time:'16:00', status:'plan' },
          { id: now+14, sid:'s2', date: fmt(8), time:'16:00', status:'present' }
        ],
        snippets: [
          { id:now+20, cat:'수업관찰', text:'수업 초반 10분간 집중 상태 양호. 자리 이탈 없음.' },
          { id:now+21, cat:'수업관찰', text:'지시를 즉시 이해하고 과제를 착수함.' },
          { id:now+22, cat:'수업관찰', text:'시간 흐름 후반부 피로로 오류 증가 → 중간 휴식 필요해 보임.' },
          { id:now+23, cat:'학부모상담', text:'가정에서의 수행 시간과 오류 개수를 공유함. 주 3회 이상 가정 연습 권고.' },
          { id:now+24, cat:'학부모상담', text:'상담 내용 요약과 앞으로의 목표를 문자로 재안내드림.' },
          { id:now+25, cat:'과제부여', text:'주간 과제: 기호쓰기 활동지 3회 (1회당 10분) 배부함.' },
          { id:now+26, cat:'과제부여', text:'가정 연습 기록지 배부 — 다음 수업 시 제출 확인.' }
        ],
        users: {}
      };
    }

    const DemoAuth = {
      _cb: null, _user: null,
      onChange(cb){ this._cb = cb; const uid = localStorage.getItem('solbit_demo_session'); if(uid){ this._user = {uid, email:'demo@sobit.kr'}; } setTimeout(()=>cb(this._user), 80); },
      signup(email, pw, name, role='teacher', childCode=''){
        const users = demoLoadUsers();
        if(Object.keys(users).length === 0){ const uid='demo-admin'; users[uid]={name, email, role:'admin', approved:true}; this._finishSignup(uid, users); return Promise.resolve(); }
        if(Object.values(users).some(u=>u.email===email)) return Promise.reject({code:'auth/email-already-in-use'});
        const uid = 'demo-' + Date.now();
        users[uid] = { name, email, requestedRole: role, approved:false, ...(role==='parent'&&childCode?{childCode}:{}) };
        this._finishSignup(uid, users); return Promise.resolve();
      },
      _finishSignup(uid, users){ try{ localStorage.setItem('solbit_demo_users', JSON.stringify(users)); }catch(e){} localStorage.setItem('solbit_demo_session', uid); this._user={uid, email:'demo@sobit.kr'}; this._cb(this._user); },
      login(email, pw){ if(!localStorage.getItem('solbit_demo_session')) return Promise.reject({code:'auth/invalid-credential'}); const uid=localStorage.getItem('solbit_demo_session'); this._user={uid, email}; this._cb(this._user); return Promise.resolve(); },
      logout(){ localStorage.removeItem('solbit_demo_session'); this._user=null; this._cb(null); return Promise.resolve(); }
    };

    // ==================== 실제 Firebase 모듈 ====================
    const RealDB = {
      _root: null, _usersRoot: null,
      init(onReady, onError){
        if(!firebase.apps.length) firebase.initializeApp(firebaseConfig);
        this._root = firebase.database().ref('solbit_data');
        this._usersRoot = firebase.database().ref('users');
        this._root.on('value', snap=>onReady(snap.val()), err=>onError(err));
      },
      set(path, value){ let r = this._root; if(path) path.split('/').forEach(s=>r=r.child(s)); return r.set(value); },
      getUser(uid){ return this._usersRoot.child(uid).once('value').then(s=>s.val()); },
      setUsersAll(all){ return this._usersRoot.set(all); },
      subscribeUsers(cb){ this._usersRoot.on('value', s=>cb(s.val()||{})); },
      onUserChange(uid, profile){ return this._usersRoot.child(uid).update(profile); },
      setParentViews(map){ return firebase.database().ref('parent_view').set(map); },
      getParentView(uid){ return firebase.database().ref('parent_view/'+uid).once('value').then(s=>s.val()); }
    };

    const RealAuth = {
      _cb: null,
      onChange(cb){ this._cb = cb; firebase.auth().onAuthStateChanged(u=>cb(u ? {uid:u.uid, email:u.email} : null)); },
      signup(email, pw, name, role='teacher', childCode=''){
        return firebase.auth().createUserWithEmailAndPassword(email, pw).then(cred=>{
          const adminProfile = { name, email, role:'admin', approved:true, createdAt: Date.now() };
          const profile = role==='parent'
            ? { name, email, requestedRole:'parent', childCode, createdAt: Date.now() }
            : { name, email, createdAt: Date.now() };
          // 1차 시도: 첫 관리자(users 노드가 비어 있을 때만 규칙이 허용) → 실패 시 일반 가입
          return firebase.database().ref('users/'+cred.user.uid).set(adminProfile).catch(()=>{
            return firebase.database().ref('users/'+cred.user.uid).set(profile);
          });
        });
      },
      login(email, pw){ return firebase.auth().signInWithEmailAndPassword(email, pw); },
      logout(){ return firebase.auth().signOut(); }
    };

    const DB = IS_DEMO ? DemoDB : RealDB;
    const AUTH = IS_DEMO ? DemoAuth : RealAuth;

    // ==================== 공용 상태 ====================
    let db = { v:15, students:{}, schedule:[], snippets:[] };
    let usersMap = {};
    let curStudent = null;
    let curDate = new Date();
    let charts = {};
    let editingId = null;
    let editingBhId = null;
    let me = null;              // {uid, email, profile}
    let sessionKey = null;      // CryptoKey (데이터 잠금 해제 시)
    let nameCache = {};         // sid -> 실명 (복호화 성공 시)
    let VIEW_REAL = false;      // 실명 표시 모드

    // ==================== 암호화 (AES-GCM) ====================
    const CRYPTO = {
      enc: new TextEncoder(), dec: new TextDecoder(),
      b64(buf){ return btoa(Array.from(new Uint8Array(buf)).map(c=>String.fromCharCode(c)).join('')); },
      unb64(s){ return Uint8Array.from(atob(s), c=>c.charCodeAt(0)); },
      async deriveKey(pass, saltB64){
        const salt = this.unb64(saltB64);
        const km = await crypto.subtle.importKey('raw', this.enc.encode(pass), 'PBKDF2', false, ['deriveKey']);
        return crypto.subtle.deriveKey({name:'PBKDF2', salt, iterations:120000, hash:'SHA-256'}, km, {name:'AES-GCM', length:256}, false, ['encrypt','decrypt']);
      },
      async encrypt(text, key){ const iv = crypto.getRandomValues(new Uint8Array(12)); const ct = await crypto.subtle.encrypt({name:'AES-GCM', iv}, key, this.enc.encode(text)); return this.b64(iv)+'.'+this.b64(ct); },
      async decrypt(token, key){ try { const parts = token.split('.'); const iv = this.unb64(parts[0]); const pt = await crypto.subtle.decrypt({name:'AES-GCM', iv}, key, this.unb64(parts[1])); return this.dec.decode(pt); } catch(e){ return null; } }
    };
    function maskName(n){ if(!n) return '??'; return n.length<=2 ? n[0]+'*' : n[0]+'*'.repeat(n.length-1); }

    // ==================== UI 유틸 ====================
    function toast(msg, isErr){ const t=document.getElementById('toast'); t.innerText=msg; t.className=isErr?'show err':'show'; clearTimeout(t._h); t._h=setTimeout(()=>t.className='', 3200); }
    function openModal(html){ document.getElementById('modalBody').innerHTML = html; document.getElementById('mbk').classList.add('open'); }
    function closeModal(){ document.getElementById('mbk').classList.remove('open'); }
    function esc(s){ return String(s==null?'':s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;'); }
    function todayStr(){ return new Date().toISOString().slice(0,10); }
    function dstr(d){ return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`; }
    function myRole(){ return (me && me.profile && (me.profile.role || me.profile.requestedRole)) || 'teacher'; }

    // ==================== 데이터 접근 헬퍼 ====================
    function saveAll(){ return DB.set('', db); }
    function saveStudent(sid){ publishParentViewsSoon(); return DB.set('students/'+sid, db.students[sid]); }
    function saveSchedule(){ return DB.set('schedule', db.schedule); }
    function saveSnippets(){ return DB.set('snippets', db.snippets||[]); }

    function getStudent(sid){ return db.students[sid] || null; }
    function getStudentName(sid, real){
      const s = getStudent(sid); if(!s) return '(삭제된 학생)';
      if(real && VIEW_REAL && nameCache[sid]) return nameCache[sid];
      if(s.namePlain) return s.namePlain;                    // 미암호화 마이그레이션 데이터
      if(real && VIEW_REAL && s.nameEnc && sessionKey){
        // 동기 캐시: 복호화는 비동기지만 unlock 시 미리 채워둠
        return nameCache[sid] || s.maskName || '??';
      }
      return s.maskName || '??';
    }
    async function unlockNames(){
      nameCache = {};
      for(const sid of Object.keys(db.students)){
        const s = db.students[sid];
        if(s.nameEnc && sessionKey){ const n = await CRYPTO.decrypt(s.nameEnc, sessionKey); if(n) nameCache[sid] = n; }
        else if(s.namePlain) nameCache[sid] = s.namePlain;
      }
    }

    // ==================== 인증 플로우 ====================
    let lcIsSignup = false;
    function lcMode(){ lcIsSignup = !lcIsSignup; document.getElementById('lcTitle').innerText = lcIsSignup?'계정 만들기':'로그인'; document.getElementById('lcBtn').innerText = lcIsSignup?'가입 요청':'로그인'; document.getElementById('lcSub').innerText = lcIsSignup?'가입 후 관리자 승인이 필요합니다':'연구소 계정으로 로그인해 주세요'; document.getElementById('lcNameWrap').style.display = lcIsSignup?'block':'none'; document.getElementById('lcCodeWrap').style.display='none'; document.getElementById('lcErr').style.display='none'; }
    function lcError(msg, showGuide){ const e=document.getElementById('lcErr'); e.innerHTML = esc(msg) + (showGuide?' <a style="color:#1f618d; cursor:pointer; font-weight:700;" onclick="document.getElementById(\'setupGuide\').style.display=\'block\'">→ 설정 방법 보기</a>':''); e.style.display='block'; }
    async function lcSubmit(){
      const email = document.getElementById('lcEmail').value.trim();
      const pw = document.getElementById('lcPass').value;
      const name = document.getElementById('lcName').value.trim();
      const btn = document.getElementById('lcBtn'); btn.disabled = true; btn.innerText = '처리 중...';
      try {
        if(lcIsSignup){
          if(!name) throw {msg:'이름을 입력해 주세요.'};
          const role = (document.querySelector('input[name="lcRole"]:checked')||{}).value || 'teacher';
          const childCode = (document.getElementById('lcCode').value||'').trim();
          if(role==='parent' && !/^[0-9]{6}$/.test(childCode)) throw {msg:'학부모 가입에는 6자리 연결 코드가 필요합니다. 연구소에 문의해 주세요.'};
          await AUTH.signup(email, pw, name, role, childCode);
        } else {
          await AUTH.login(email, pw);
        }
      } catch(err){
        const code = err && err.code ? err.code : '';
        const map = {
          'auth/email-already-in-use':'이미 가입된 이메일입니다. 로그인해 주세요.',
          'auth/invalid-email':'이메일 형식이 올바르지 않습니다.',
          'auth/weak-password':'비밀번호는 6자 이상이어야 합니다.',
          'auth/invalid-credential':'이메일 또는 비밀번호가 올바르지 않습니다.',
          'auth/user-not-found':'등록되지 않은 이메일입니다.',
          'auth/wrong-password':'비밀번호가 올바르지 않습니다.',
          'auth/too-many-requests':'시도가 너무 많습니다. 잠시 후 다시 시도해 주세요.',
          'auth/configuration-not-found':'Firebase에서 이메일/비밀번호 로그인이 아직 사용 설정되지 않았습니다.',
          'auth/operation-not-allowed':'Firebase에서 이메일/비밀번호 로그인이 아직 사용 설정되지 않았습니다.',
          'auth/admin-restricted-operation':'Firebase Authentication이 아직 설정되지 않았습니다.',
          'auth/network-request-failed':'네트워크 오류입니다. 연결을 확인해 주세요.'
        };
        const showGuide = ['auth/configuration-not-found','auth/operation-not-allowed','auth/admin-restricted-operation'].includes(code);
        lcError(map[code] || (err&&err.msg) || '오류가 발생했습니다: '+(code||err), showGuide || code==='' );
      } finally { btn.disabled=false; btn.innerText = lcIsSignup?'가입 요청':'로그인'; }
    }
    function doLogout(){ AUTH.logout().catch(()=>{}); if(IS_DEMO){ location.hash=''; } location.reload(); }


    function showScreen(name){
      document.getElementById('loader').style.display = 'none';
      document.getElementById('loginWrap').style.display = name==='login'?'flex':'none';
      document.getElementById('pendingBox').style.display = name==='pending'?'flex':'none';
      document.getElementById('appRoot').classList.toggle('on', name==='app');
      document.getElementById('parentShell').classList.toggle('on', name==='parent');
    }

    // ==================== 앱 시작 ====================
    function startApp(){
      showScreen('app');
      document.getElementById('ubName').innerText = me.profile.name || me.email;
      document.getElementById('ubRole').innerText = me.profile.role==='admin' ? '관리자(원장/센터장)' : '교사/연구자';
      document.getElementById('ubAvatar').innerText = (me.profile.name||'?')[0];
      DB.subscribeUsers(u => { usersMap = u||{}; if(currentUserView()==='pendingList') renderUsersTable(); publishParentViewsSoon(); });
      DB.init(onData, onDataError);
    }
    function onDataError(err){
      if(err && (err.code==='PERMISSION_DENIED' || String(err.message||'').includes('permission'))){ showScreen('pending'); }
      else { document.getElementById('loader').innerHTML = '<div style="color:#c0392b; font-weight:700;">데이터 로드 실패<br><span style="font-size:13px; color:#888;">'+esc(String(err&&err.message||err))+'</span></div>'; }
    }
    function onData(val){
      if(!val){ db = { v:15, students:{}, schedule:[], snippets:[] }; } else { db = val; }
      db.students = db.students || {}; db.schedule = db.schedule || []; db.snippets = db.snippets || [];
      document.getElementById('loader').style.display = 'none';
      if(IS_DEMO){ db.crypto = db.crypto || {enabled:false}; }
      if(!db.v || db.v < 15){ needMigration().then(()=>{ afterDataReady(); }); }
      else { afterDataReady(); }
    }
    function afterDataReady(){
      checkRulesOpen();
      // 세션 키 복원(데모) 또는 잠금 상태 반영
      if(db.crypto && db.crypto.enabled){
        if(IS_DEMO && !sessionKey && db.crypto.check!=='DEMO_CHECK'){ /* pass */ }
        document.getElementById('lockChip').className = 'lock-chip ' + (sessionKey?'open':'locked');
        document.getElementById('lockChip').innerText = sessionKey ? '🔓 실명 열람 가능' : '🔒 실명 잠김';
      } else {
        document.getElementById('lockChip').className = 'lock-chip locked';
        document.getElementById('lockChip').innerText = '🔒 미설정';
      }
      // 마이그레이션 데이터에 마스크명이 없으면 생성
      Object.keys(db.students).forEach(sid=>{ const s=db.students[sid]; if(!s.maskName){ s.maskName = s.namePlain ? maskName(s.namePlain) : '??'; } });
      renderStudentList(); renderCalendar(); renderSnippetSelect();
      if(curStudent && !db.students[curStudent]) curStudent = null;
      if(curStudent){ const t=document.querySelector('.tab-btn'); if(t) t.click(); }
      // 데모: 첫 학생 자동 선택 + 해시 탭
      if(IS_DEMO && !curStudent){ const first = Object.keys(db.students)[0]; if(first){ selectStudent(first, false); const h = location.hash.replace('#view-',''); const map=['attention','wisc','counsel','behavior','dev','aireport']; if(map.includes(h)){ const btn=[...document.querySelectorAll('.tab-btn')].find(b=>b.getAttribute('onclick')&&b.getAttribute('onclick').includes(h)); if(btn) btn.click(); } else if(h==='schedule'){ goHome(); } else { document.querySelector('.tab-btn').click(); } } }
      if(db.crypto && db.crypto.enabled && !sessionKey && !IS_DEMO){ /* 잠금 유지 */ }
      if(db.crypto && !db.crypto.enabled && Object.values(db.students).some(s=>s.namePlain)){ toast('⚠️ 실명 암호화가 설정되지 않았습니다. 설정 → 보안 탭에서 설정하세요.', true); }
      publishParentViewsSoon();
    }

    // 규칙 개방 여부 감지: 비관리자가 users 전체를 읽을 수 있으면 열린 규칙
    function checkRulesOpen(){
      if(IS_DEMO) return;
      if(me.profile.role==='admin') return; // 관리자는 읽을 수 있으므로 판별 불가 → 미표시
      firebase.database().ref('users').once('value').then(()=>{ document.getElementById('openRulesBanner').style.display='flex'; }).catch(()=>{});
    }

    // ==================== 마이그레이션 (V13.5 → V14) ====================
    function needMigration(){
      return new Promise(resolve=>{
        openModal(`<h3>🔄 데이터 구조 업그레이드 (V13.5 → V14)</h3>
          <p style="font-size:14px; line-height:1.8; color:#444;">
          V14에서는 학생을 <b>고유 ID</b>로 관리하고, <b>실명 암호화</b>·<b>행동중재</b>·<b>AI 리포트</b> 기능이 추가됩니다.<br>
          진행하면 기존 데이터 전체가 <b>backup_v135</b> 경로에 자동 백업된 뒤 변환됩니다.</p>
          <div class="mo-foot"><button class="btn-gray" onclick="location.reload()">취소</button><button class="btn-primary" onclick="doMigration().then(()=>{closeModal();resolve();})">업그레이드 진행</button></div>`);
      });
    }
    async function doMigration(){
      const old = JSON.parse(JSON.stringify(db));
      try { if(!IS_DEMO) await DB.set('backup_pre_v15/'+Date.now(), old); } catch(e){ toast('백업 실패: '+e.message, true); return; }
      const students = {}; const nameToSid = {};
      let i = 0;
      for(const [name, s] of Object.entries(old.students||{})){
        const sid = 's' + Date.now() + (i++);
        nameToSid[name] = sid;
        students[sid] = {
          id: sid, namePlain: name, maskName: maskName(name),
          attention: (s.attention||[]).map(x=>({ ...x, sub: x.sub || '', cat: x.cat || 'visual' })),
          wisc: { scores: (s.wisc&&s.wisc.scores)||[0,0,0,0,0,0], comment:(s.wisc&&s.wisc.comment)||'', date:(s.wisc&&s.wisc.date)||'' },
          counsel: s.counsel||[],
          dev: { scores:(s.dev&&s.dev.scores)||[3,3,3,3,3], comment:(s.dev&&s.dev.comment)||'', history:[] },
          behaviors: []
        };
      }
      const schedule = (old.schedule||[]).map(e=>({ ...e, sid: nameToSid[e.name] || '' }));
      db = { v:15, students, schedule, snippets: defaultSnippets(), crypto:{ enabled:false } };
      Object.keys(db.students).forEach(sid=>{ db.students[sid].parentCode = genParentCode(); });
      await saveAll();
      toast('업그레이드 완료. 이제 보안 설정에서 실명 암호화를 켜주세요.');
    }
    function defaultSnippets(){ const n=Date.now(); return [
      { id:n+1, cat:'수업관찰', text:'수업 초반 10분간 집중 상태 양호. 자리 이탈 없음.' },
      { id:n+2, cat:'수업관찰', text:'지시를 즉시 이해하고 과제를 착수함.' },
      { id:n+3, cat:'수업관찰', text:'시간 흐름 후반부 피로로 오류 증가 → 중간 휴식 필요해 보임.' },
      { id:n+4, cat:'수업관찰', text:'과제 중 산만한 행동 관찰되나 자발적으로 복귀함.' },
      { id:n+5, cat:'학부모상담', text:'가정에서의 수행 시간과 오류 개수를 공유함. 주 3회 이상 가정 연습 권고.' },
      { id:n+6, cat:'학부모상담', text:'상담 내용 요약과 앞으로의 목표를 문자로 재안내드림.' },
      { id:n+7, cat:'과제부여', text:'주간 과제: 기호쓰기 활동지 3회 (1회당 10분) 배부함.' },
      { id:n+8, cat:'과제부여', text:'가정 연습 기록지 배부 — 다음 수업 시 제출 확인.' }
    ]; }

    // ==================== 실명 보호 (잠금/해제) ====================
    function lockChipClick(){
      if(db.crypto && db.crypto.enabled && !sessionKey){ openUnlockModal(); }
      else if(db.crypto && db.crypto.enabled && sessionKey){ toggleRealView(); }
      else { openSettings('sec'); }
    }
    function openUnlockModal(){
      openModal(`<h3>🔒 데이터 잠금 해제</h3>
        <p class="hint">실명 데이터는 암호화되어 저장됩니다. 화면에서 실명을 보려면 연구소가 정한 <b>데이터 잠금 암호</b>를 입력하세요. (세션 동안만 유지)</p>
        <div class="mo-row"><label>데이터 잠금 암호</label><input type="password" id="ulPass"></div>
        <div class="mo-foot"><button class="btn-gray" onclick="closeModal()">마스킹 상태로 두기</button><button class="btn-primary" onclick="doUnlock()">해제</button></div>`);
    }
    async function doUnlock(){
      const pass = document.getElementById('ulPass').value;
      if(!pass) return toast('암호를 입력하세요', true);
      try {
        const key = await CRYPTO.deriveKey(pass, db.crypto.salt);
        const check = await CRYPTO.decrypt(db.crypto.check, key);
        if(check !== 'solbit-ok'){ toast('암호가 올바르지 않습니다', true); return; }
        sessionKey = key;
        await unlockNames();
        closeModal(); renderStudentList(); renderCalendar();
        const chip = document.getElementById('lockChip'); chip.className='lock-chip open'; chip.innerText='🔓 실명 열람 가능';
        toast('실명 데이터가 열렸습니다. (세션 동안 유지)');
      } catch(e){ toast('잠금 해제 실패: '+e.message, true); }
    }
    function toggleRealView(){
      if(db.crypto && db.crypto.enabled && !sessionKey){ openUnlockModal(); return; }
      VIEW_REAL = !VIEW_REAL;
      const btn = document.getElementById('eyeToggle');
      btn.innerText = VIEW_REAL ? '실명 보기 중' : '마스킹 보기';
      btn.classList.toggle('on', VIEW_REAL);
      renderStudentList(); renderCalendar();
      if(curStudent) reRenderCurrentTab();
    }

    // ==================== 학생 관리 ====================
    async function addStudentUI(){
      const n = document.getElementById('newStudentName').value.trim();
      if(!n) return;
      if(db.crypto && db.crypto.enabled && !sessionKey){ openUnlockModal(); return; }
      const dup = Object.values(db.students).find(s=>(s.namePlain===n) || (nameCache[Object.keys(db.students).find(k=>db.students[k]===s)]===n));
      if(dup) return alert('이미 등록된 학생입니다.');
      const sid = 's' + Date.now();
      const pCode = genParentCode();
      let nameEnc = null;
      if(db.crypto && db.crypto.enabled && sessionKey){
        nameEnc = await CRYPTO.encrypt(n, sessionKey);
      } else if(db.crypto && db.crypto.enabled && !sessionKey){
        // 여기 올 일 없음(위에서 막음)
        return;
      }
      db.students[sid] = {
        id: sid, namePlain: (!db.crypto || !db.crypto.enabled) ? n : null, nameEnc,
        maskName: maskName(n), parentCode: pCode,
        attention: [], wisc: {scores:[0,0,0,0,0,0], comment:'', date:''}, counsel: [], dev:{scores:[3,3,3,3,3], comment:'', history:[]}, behaviors: []
      };
      if(db.crypto && db.crypto.enabled) nameCache[sid] = n;
      document.getElementById('newStudentName').value='';
      saveStudent(sid);
    }
    function renderStudentList(){
      const list = document.getElementById('studentList'); list.innerHTML='';
      const ids = Object.keys(db.students).sort((a,b)=>{
        const na = getStudentName(a).localeCompare(getStudentName(b),'ko'); return na;
      });
      ids.forEach(sid=>{
        const div = document.createElement('div');
        div.className = `student-item ${sid===curStudent?'active':''}`;
        div.innerHTML = `<span>${esc(getStudentName(sid, true))}</span><span class="del-btn" onclick="deleteStudent('${sid}',event)">✕</span>`;
        div.onclick = (e)=>{ if(e.target.className!=='del-btn') selectStudent(sid); };
        list.appendChild(div);
      });
    }
    function deleteStudent(sid, e){
      e.stopPropagation();
      const nm = getStudentName(sid, true);
      if(confirm(`[${nm}] 학생의 모든 기록이 삭제됩니다. 정말 삭제할까요?`)){
        if(confirm('한 번 삭제하면 되돌릴 수 없습니다. 백업이 필요하면 설정→백업에서 먼저 내려받으세요. 삭제할까요?')){
          delete db.students[sid];
          db.schedule = db.schedule.filter(s=>s.sid!==sid);
          DB.set('students/'+sid, null).then(()=>saveSchedule());
          if(curStudent===sid){ curStudent=null; goHome(); }
        }
      }
    }
    function selectStudent(name, refresh=true){
      curStudent = name;
      document.getElementById('headerTitle').innerText = `${getStudentName(name, true)} 관리`;
      document.getElementById('moduleTabs').style.display = 'flex';
      document.getElementById('headerSub').innerText = '실명 마스킹 기본 · 인쇄 시 마스킹 유지';
      renderStudentList();
      if(refresh) document.querySelector('.tab-btn').click();
      else reRenderCurrentTab();
    }
    function currentTabName(){
      const active = document.querySelector('.tab-btn.active');
      if(!active) return 'attention';
      const oc = active.getAttribute('onclick')||'';
      const m = oc.match(/switchTab\('([^']+)'/); return m ? m[1] : 'attention';
    }
    function currentUserView(){ return currentTabName(); }
    function reRenderCurrentTab(){
      const t = currentTabName();
      if(t==='attention') renderAttention();
      if(t==='wisc') renderWisc();
      if(t==='counsel') renderCounsel();
      if(t==='behavior') renderBehavior();
      if(t==='dev') renderDev();
      if(t==='aireport') renderReportView();
    }
    function goHome(refresh = true){
      curStudent = null;
      document.getElementById('headerTitle').innerText = '전체 스케줄';
      document.getElementById('moduleTabs').style.display = 'none';
      document.getElementById('headerSub').innerText = '공유 일정 · 학생 관리';
      document.querySelectorAll('.view-section').forEach(el => el.classList.remove('active'));
      document.getElementById('view-schedule').classList.add('active');
      renderStudentList(); renderCalendar();
    }
    // ==================== V15: 역할 / 학부모 포털 ====================
    function roleLabel(u){ return u.role==='admin'?'관리자' : u.role==='parent'?'학부모' : (u.requestedRole==='parent'?'학부모(요청)':'교사'); }
    function genParentCode(){ let c; do { c = String(Math.floor(100000+Math.random()*900000)); } while(Object.values(db.students).some(s=>s.parentCode===c)); return c; }
    function realNameOf(sid){ const s=getStudent(sid); if(!s) return null; return nameCache[sid] || s.namePlain || null; }
    let _pvTimer=null;
    function publishParentViewsSoon(){ clearTimeout(_pvTimer); _pvTimer=setTimeout(publishParentViews, 1200); }
    async function publishParentViews(){
      try {
        if(!me || myRole()==='parent') return;
        const parents = Object.entries(usersMap||{}).filter(([uid,u])=>u.role==='parent' && u.approved && u.childSid);
        if(!parents.length) return;
        const pv = {};
        parents.forEach(([uid,u])=>{
          const st = db.students[u.childSid]; if(!st) return;
          const nm = realNameOf(u.childSid) || st.maskName || '아이';
          const sched = (db.schedule||[]).filter(s=>s.sid===u.childSid).sort((a,b)=>(b.date||'').localeCompare(a.date||'')).slice(0,20).map(s=>({date:s.date,time:s.time,status:s.status}));
          const memos = (st.counsel||[]).filter(c=>c.shared).sort((a,b)=>(b.date||'').localeCompare(a.date||'')).slice(0,20).map(c=>({date:c.date,type:c.type,content:c.content}));
          pv[uid] = { childName: nm, childMask: st.maskName||'', reportHtml: st.aiReport ? buildReportHtml(u.childSid, st.aiReport.period, {childName:nm, forParent:true}) : '', schedule: sched, memos, updatedAt: Date.now() };
        });
        await DB.setParentViews(pv);
      } catch(e){ /* 학부모 공유 실패는 무음 */ }
    }
    function lcRoleChanged(){ document.getElementById('lcCodeWrap').style.display = ((document.querySelector('input[name="lcRole"]:checked')||{}).value==='parent') ? 'block':'none'; }
    function stBadgeKo(st){ return {plan:['예정','#95a5a6'],present:['출석','#27ae60'],absent:['결석','#e74c3c'],makeup:['보강','#f39c12']}[st]||[st||'예정','#999']; }
    function startParentApp(){
      showScreen('parent');
      document.getElementById('ptUser').innerText = (me.profile.name||'학부모')+' 님';
      renderParentPortal();
    }
    function renderParentPortal(){
      DB.getParentView(me.uid).then(pv=>{
        const w=document.getElementById('parentWrap');
        if(!pv){ w.innerHTML = '<div class="parent-sec" style="text-align:center; color:#888;">아직 공유된 자료가 없습니다.<br><span style="font-size:13px;">연구소에서 자료를 등록하면 이곳에 표시됩니다.</span></div>'; return; }
        const counts = (pv.schedule||[]).reduce((a,s)=>{ a[s.status]=(a[s.status]||0)+1; return a; },{});
        const reportSec = pv.reportHtml ? pv.reportHtml : '<p style="color:#888;">아직 생성된 리포트가 없습니다. 리포트가 게시되면 이곳에서 확인할 수 있습니다.</p>';
        const memoSec = (pv.memos&&pv.memos.length) ? pv.memos.map(m=>`<div class="memo-item"><div class="mi-date">📅 ${esc(m.date)} · ${esc(m.type||'가정연계')}</div><div class="mi-body">${esc(m.content)}</div></div>`).join('') : '<p style="color:#888;">공유된 가정 연계 메모가 아직 없습니다.</p>';
        const schedSec = (pv.schedule&&pv.schedule.length) ? pv.schedule.map(s=>{ const t=stBadgeKo(s.status); return `<div class="sched-row"><span style="width:110px;">📅 ${esc(s.date)}</span><span style="width:70px;">${esc(s.time||'')}</span><span class="badge" style="background:${t[1]};">${t[0]}</span></div>`; }).join('') : '<p style="color:#888;">수업 기록이 없습니다.</p>';
        w.innerHTML = `
          <div class="parent-child">
            <div class="pc-ava">${esc((pv.childName||'아이')[0])}</div>
            <div style="flex:1; min-width:180px;">
              <div style="font-size:19px; font-weight:800; color:var(--brand);">${esc(pv.childName||'아이')} 학생</div>
              <div style="font-size:13px; color:#7f8c8d; margin-top:3px;">최근 업데이트: ${pv.updatedAt?new Date(pv.updatedAt).toLocaleDateString('ko-KR'):'-'}</div>
            </div>
            <div style="display:flex; gap:8px; flex-wrap:wrap;">
              <span class="badge" style="background:#27ae60;">출석 ${counts.present||0}</span>
              <span class="badge" style="background:#f39c12;">보강 ${counts.makeup||0}</span>
              <span class="badge" style="background:#e74c3c;">결석 ${counts.absent||0}</span>
            </div>
          </div>
          <div class="parent-sec"><h3>📊 우리 아이 통합 리포트</h3>${reportSec}</div>
          <div class="parent-sec"><h3>📩 가정 연계 메모</h3>${memoSec}</div>
          <div class="parent-sec"><h3>📅 수업 일정 · 출석</h3>${schedSec}</div>`;
      }).catch(()=>{ document.getElementById('parentWrap').innerHTML = '<div class="parent-sec" style="text-align:center; color:#c0392b;">자료를 불러오지 못했습니다.</div>'; });
    }
    function toggleShare(id){ const l=db.students[curStudent].counsel; const x=l.find(c=>c.id===id); if(x){ x.shared=!x.shared; saveStudent(curStudent); } }
    function approveParent(uid){ const sel=document.getElementById('sel_'+uid); if(!sel||!sel.value) return toast('연결할 학생을 선택하세요', true); DB.onUserChange(uid,{approved:true, role:'parent', childSid:sel.value}).then(()=>{ toast('학부모 계정을 승인했습니다'); renderUsersTable(); publishParentViewsSoon(); }); }
    function renderParentLinkTab(){
        const body=document.getElementById('setBody'); if(!body) return;
        const needCode = Object.keys(db.students).filter(sid=>!db.students[sid].parentCode);
        needCode.forEach(sid=>{ db.students[sid].parentCode = genParentCode(); DB.set('students/'+sid, db.students[sid]); });
        const parentUsers = Object.entries(usersMap||{}).filter(([uid,u])=>u.role==='parent');
        const students = Object.keys(db.students);
        body.innerHTML = `<p class="hint" style="margin-bottom:12px;">학부모가 가입할 때 이 <b>연결 코드</b>를 입력하면, 관리자 승인 후 해당 아이의 포털이 열립니다. 코드를 학부모에게 안내해 주세요. 승인은 '승인 관리' 탭에서 합니다.<br>* 실명 열람(잠금 해제) 상태에서 저장되면 포털에 실명이 반영되고, 잠긴 상태면 마스킹 이름으로 표시됩니다.</p>
        <table><thead><tr><th>학생</th><th>연결 코드</th><th>연결된 학부모</th><th>관리</th></tr></thead><tbody>
        ${students.length? students.map(sid=>{
            const linked = parentUsers.filter(([uid,u])=>u.childSid===sid);
            return `<tr><td>${esc(getStudentName(sid,true))}</td>
            <td><span class="code-chip">${db.students[sid].parentCode||'-'}</span></td>
            <td style="font-size:12px;">${linked.map(([uid,u])=>esc((u.name||u.email||uid)+' ('+(u.approved?'승인':'대기')+')')).join('<br>')||'<span style="color:#aaa;">없음</span>'}</td>
            <td><button class="btn-gray btn-sm" onclick="regenCode('${sid}')">재발급</button></td></tr>`;
        }).join('') : '<tr class="empty-row"><td colspan="4">학생이 없습니다.</td></tr>'}
        </tbody></table>`;
    }
    function regenCode(sid){ db.students[sid].parentCode = genParentCode(); saveStudent(sid); renderParentLinkTab(); toast('코드가 재발급되었습니다'); }

    function switchTab(tabName, btn){
      if(!curStudent) return;
      document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
      btn.classList.add('active');
      document.querySelectorAll('.view-section').forEach(el => el.classList.remove('active'));
      document.getElementById(`view-${tabName}`).classList.add('active');
      cancelEdit(); editingBhId = null;
      reRenderCurrentTab();
    }

    // ==================== 캘린더 ====================
    const stMap={'plan':'st-plan','present':'st-present','absent':'st-absent','makeup':'st-makeup'};
    const stKo = {plan:'예정', present:'출석', absent:'결석', makeup:'보강'};
    function changeMonth(d){ curDate.setMonth(curDate.getMonth()+d); renderCalendar(); }
    function renderCalendar(){
      const y=curDate.getFullYear(), m=curDate.getMonth();
      document.getElementById('calYearMonth').innerText=`${y}.${String(m+1).padStart(2,'0')}`;
      const grid=document.getElementById('calendarGrid');
      grid.innerHTML=`<div class="cal-head" style="color:#e74c3c">일</div><div class="cal-head">월</div><div class="cal-head">화</div><div class="cal-head">수</div><div class="cal-head">목</div><div class="cal-head">금</div><div class="cal-head" style="color:#3498db">토</div>`;
      for(let i=0; i<new Date(y,m,1).getDay(); i++) grid.innerHTML+=`<div class="cal-cell" style="background:#f9f9f9"></div>`;
      let mkList=[];
      const today = todayStr();
      for(let d=1; d<=new Date(y,m+1,0).getDate(); d++){
        const dStr=`${y}-${String(m+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`;
        const evs = db.schedule.filter(s => s.date === dStr).sort((a, b) => (a.time||'').localeCompare(b.time||''));
        let h=`<div class="cal-cell" ondrop="dropEvent(event, '${dStr}')" ondragover="allowDrop(event)" ondragleave="leaveDrop(event)"><span class="cal-date ${dStr===today?'today-mark':''}">${d}${dStr===today?' ●':''}</span>`;
        evs.forEach(e=>{
          if(e.status==='absent' && e.sid) mkList.push(`${getStudentName(e.sid, true)} (${e.date})`);
          h+=`<div class="event-chip ${stMap[e.status]}" draggable="true" ondragstart="dragEvent(event, ${e.id})"><div class="event-info" onclick="toggleStatus(${e.id}, event)"><span>${esc(getStudentName(e.sid, true))}</span><span style="font-size:10px; opacity:0.8; margin-left:3px;">${esc(e.time||'')}</span></div><div class="event-actions"><span class="icon-btn" onclick="editScheduleModal(${e.id}, event)" title="수정">✎</span><span class="icon-btn" onclick="delSchedule(${e.id}, event)" title="삭제">✕</span></div></div>`;
        });
        grid.innerHTML+=h+`</div>`;
      }
      document.getElementById('makeupAlertBox').style.display=mkList.length>0?'block':'none';
      document.getElementById('makeupList').innerText=mkList.join(', ');
    }
    function allowDrop(ev) { ev.preventDefault(); ev.currentTarget.classList.add('drag-over'); }
    function leaveDrop(ev) { ev.currentTarget.classList.remove('drag-over'); }
    function dragEvent(ev, id) { ev.dataTransfer.setData("text/plain", id); ev.effectAllowed = ev.ctrlKey ? "copy" : "move"; }
    function dropEvent(ev, tDate) {
      ev.preventDefault(); ev.currentTarget.classList.remove('drag-over');
      const id = ev.dataTransfer.getData("text/plain");
      const o = db.schedule.find(x => x.id == id);
      if (o) { if (ev.ctrlKey) { db.schedule.push({ id: Date.now(), sid: o.sid, date: tDate, time: o.time, status: 'plan' }); } else { if (o.date !== tDate) o.date = tDate; } saveSchedule(); }
    }
    function addScheduleModal(){
      if(Object.keys(db.students).length===0) return alert('먼저 학생을 등록해 주세요.');
      openModal(`<h3>＋ 수업 일정 추가</h3>
        <div class="mo-row"><label>학생</label><select id="mSchedStudent">${Object.keys(db.students).map(sid=>`<option value="${sid}">${esc(getStudentName(sid, true))}</option>`).join('')}</select></div>
        <div class="mo-row"><label>날짜</label><input type="date" id="mSchedDate" value="${todayStr()}"></div>
        <div class="mo-row"><label>시간</label><input type="time" id="mSchedTime" value="14:00"></div>
        <div class="mo-foot"><button class="btn-gray" onclick="closeModal()">취소</button><button class="btn-green" onclick="saveScheduleItem(null)">추가</button></div>`);
    }
    function editScheduleModal(id, e){
      e.stopPropagation();
      const s = db.schedule.find(x=>x.id===id); if(!s) return;
      openModal(`<h3>✎ 일정 수정</h3>
        <div class="mo-row"><label>학생</label><select id="mSchedStudent">${Object.keys(db.students).map(sid=>`<option value="${sid}" ${sid===s.sid?'selected':''}>${esc(getStudentName(sid, true))}</option>`).join('')}</select></div>
        <div class="mo-row"><label>날짜</label><input type="date" id="mSchedDate" value="${s.date}"></div>
        <div class="mo-row"><label>시간</label><input type="time" id="mSchedTime" value="${esc(s.time||'14:00')}"></div>
        <div class="mo-row"><label>상태 (현재: ${stKo[s.status]||'예정'})</label><select id="mSchedStatus"><option value="plan" ${s.status==='plan'?'selected':''}>예정</option><option value="present" ${s.status==='present'?'selected':''}>출석</option><option value="absent" ${s.status==='absent'?'selected':''}>결석</option><option value="makeup" ${s.status==='makeup'?'selected':''}>보강완료</option></select></div>
        <div class="mo-foot"><button class="btn-gray" onclick="closeModal()">취소</button><button class="btn-primary" onclick="saveScheduleItem(${id})">저장</button></div>`);
    }
    function saveScheduleItem(id){
      const sid = document.getElementById('mSchedStudent').value;
      const date = document.getElementById('mSchedDate').value;
      const time = document.getElementById('mSchedTime').value || '14:00';
      if(!date) return toast('날짜를 선택하세요', true);
      if(id===null){ db.schedule.push({ id: Date.now(), sid, date, time, status:'plan' }); }
      else {
        const s = db.schedule.find(x=>x.id===id); if(!s) return;
        Object.assign(s, { sid, date, time, status: document.getElementById('mSchedStatus').value });
      }
      closeModal(); saveSchedule();
    }
    function toggleStatus(id, e){ if(e) e.stopPropagation(); const s = db.schedule.find(x => x.id === id); if(s){ const nxt={'plan':'present','present':'absent','absent':'makeup','makeup':'plan'}; s.status=nxt[s.status]; saveSchedule(); } }
    function delSchedule(id, e) { e.stopPropagation(); if(confirm("삭제?")) { db.schedule = db.schedule.filter(x => x.id !== id); saveSchedule(); } }
    // ==================== 통계 헬퍼 ====================
    function mean(a){ return a.length ? a.reduce((x,y)=>x+y,0)/a.length : 0; }
    function median(a){ if(!a.length) return 0; const s=[...a].sort((x,y)=>x-y); const m=Math.floor(s.length/2); return s.length%2 ? s[m] : (s[m-1]+s[m])/2; }
    function linSlope(ys){ const n=ys.length; if(n<2) return 0; const mx=(n-1)/2, my=mean(ys); let num=0,den=0; for(let i=0;i<n;i++){ num+=(i-mx)*(ys[i]-my); den+=(i-mx)**2; } return den? num/den : 0; }
    function pctChange(a,b){ if(!a || a===0) return null; return Math.round((b-a)/Math.abs(a)*100); }
    function inPeriod(dateStr, days){ if(!dateStr) return false; if(days==='all') return true; const cut=new Date(); cut.setDate(cut.getDate()-Number(days)); return new Date(dateStr) >= cut; }
    function koDate(s){ if(!s) return '-'; const d=new Date(s); if(isNaN(d)) return s; return `${d.getMonth()+1}/${d.getDate()}`; }

    // ==================== 1. 주의력 훈련 ====================
    function addAttention(){
        const c=document.getElementById('attCat').value;
        const s=document.getElementById('attSub').value.trim();
        const d=document.getElementById('attDate').value;
        const minVal = parseInt(document.getElementById('attMin').value) || 0;
        const secVal = parseInt(document.getElementById('attSec').value) || 0;
        const t = (minVal * 60) + secVal;
        const e=document.getElementById('attError').value;
        const n=document.getElementById('attNote').value;
        if(!d || t <= 0) return toast('날짜와 정확한 시간을 입력해주세요.', true);
        const subName = s || '기본';
        if(!db.students[curStudent].attention) db.students[curStudent].attention=[];
        if (editingId) {
            const idx = db.students[curStudent].attention.findIndex(x => x.id === editingId);
            if (idx !== -1) db.students[curStudent].attention[idx] = { id: editingId, cat:c, sub:subName, date:d, time:t, error:e, note:n };
            cancelEdit();
        } else {
            db.students[curStudent].attention.push({ id:Date.now(), cat:c, sub:subName, date:d, time:t, error:e, note:n });
            ['attMin','attSec','attError','attNote'].forEach(id=>document.getElementById(id).value='');
        }
        saveStudent(curStudent);
    }
    function editAtt(id) {
        const item = db.students[curStudent].attention.find(x => x.id === id);
        if (!item) return;
        const totalSec = parseInt(item.time) || 0;
        document.getElementById('attMin').value = Math.floor(totalSec / 60) || '';
        document.getElementById('attSec').value = (totalSec % 60) || '0';
        document.getElementById('attCat').value = item.cat;
        document.getElementById('attSub').value = item.sub || '';
        document.getElementById('attDate').value = item.date;
        document.getElementById('attError').value = item.error || '';
        document.getElementById('attNote').value = item.note || '';
        editingId = id;
        const btn = document.getElementById('btnAttSubmit');
        btn.innerText = "수정 저장"; btn.classList.remove('btn-primary'); btn.classList.add('btn-orange');
        document.getElementById('btnAttCancel').style.display = 'inline-block';
        document.getElementById('view-attention').scrollTop = 0;
    }
    function cancelEdit() {
        editingId = null;
        ['attMin','attSec','attError','attNote','attSub'].forEach(id=>{ const el=document.getElementById(id); if(el) el.value=''; });
        const btn = document.getElementById('btnAttSubmit'); if(!btn) return;
        btn.innerText = "추가"; btn.classList.add('btn-primary'); btn.classList.remove('btn-orange');
        const c = document.getElementById('btnAttCancel'); if(c) c.style.display = 'none';
    }
    function renderAttention(){
        if(!curStudent) return;
        const allData = db.students[curStudent].attention || [];
        const subSet = new Set(allData.map(d => d.sub || '기본'));
        const filterSelect = document.getElementById('graphFilter');
        const currentFilter = filterSelect.value;
        filterSelect.innerHTML = '<option value="all">전체 데이터 보기 (모든 활동)</option>';
        const dataList = document.getElementById('subListOptions'); dataList.innerHTML = '';
        subSet.forEach(sub => {
            const opt = document.createElement('option'); opt.value = sub; opt.innerText = sub;
            if(sub === currentFilter) opt.selected = true;
            filterSelect.appendChild(opt);
            const dOpt = document.createElement('option'); dOpt.value = sub; dataList.appendChild(dOpt);
        });
        const selectedSub = filterSelect.value;
        const filteredData = (selectedSub === 'all') ? allData : allData.filter(d => (d.sub || '기본') === selectedSub);
        const tb=document.querySelector("#attTable tbody"); tb.innerHTML='';
        const rows = filteredData.slice().sort((a,b)=>new Date(a.date)-new Date(b.date));
        if(!rows.length) tb.innerHTML = '<tr class="empty-row"><td colspan="6">아직 기록이 없습니다. 위 입력창에서 첫 기록을 추가해 보세요.</td></tr>';
        rows.forEach(x=>{
            const errD = x.error !== undefined && x.error !== '' ? x.error : (x.score || '-');
            const catKo = {'visual':'시각','auditory':'청각','speed':'속도'}[x.cat] || x.cat;
            const t = parseInt(x.time); const displayTime = `${Math.floor(t/60)}분 ${t%60}초`;
            tb.innerHTML+=`<tr>
                <td><span style="font-size:11px; color:#999;">${catKo}</span><br><strong>${esc(x.sub||'기본')}</strong></td>
                <td>${esc(x.date)}</td>
                <td><strong>${displayTime}</strong></td>
                <td style="color:#d35400; font-weight:bold;">${esc(errD)}개</td>
                <td style="text-align:left; font-size:12px;">${esc(x.note)}</td>
                <td><span onclick="editAtt(${x.id})" style="cursor:pointer; font-size:16px; margin-right:8px;" title="수정">✎</span><span onclick="delAtt(${x.id})" style="color:red; cursor:pointer; font-size:16px;" title="삭제">✕</span></td>
            </tr>`;
        });
        drawOneChart('chartVisual', filteredData.filter(d => d.cat === 'visual'), '#3498db');
        drawOneChart('chartAuditory', filteredData.filter(d => d.cat === 'auditory'), '#27ae60');
        drawOneChart('chartSpeed', filteredData.filter(d => d.cat === 'speed'), '#e67e22');
        document.getElementById('attAiOut').innerHTML = buildAttentionAnalysis(curStudent, 'all') || '<span style="color:#888;">데이터가 입력되면 자동으로 분석됩니다.</span>';
        const hasKey = !!localStorage.getItem('solbit_gemini_key');
        document.getElementById('attAiTag').innerText = hasKey ? '규칙 기반 + AI 연동 가능' : '규칙 기반 분석';
    }
    function drawOneChart(canvasId, data, color) {
        const ctx = document.getElementById(canvasId);
        if(charts[canvasId]) charts[canvasId].destroy();
        charts[canvasId] = new Chart(ctx, {
            data: {
                labels: data.map(d => d.date),
                datasets: [
                    { type: 'line', label: '수행시간(초)', data: data.map(d => d.time), borderColor: '#e74c3c', borderWidth: 2, yAxisID: 'y', tension: 0.25 },
                    { type: 'bar', label: '오류(개)', data: data.map(d => Number(d.error) || 0), backgroundColor: color, yAxisID: 'y1' }
                ]
            },
            options: {
                responsive: true, maintainAspectRatio: false,
                plugins: { legend: { display: true, position: 'top' } },
                scales: {
                    y: { position: 'left', title: {display:true, text:'초(sec)'} },
                    y1: { position: 'right', grid: {drawOnChartArea:false}, title:{display:true, text:'오류'}, beginAtZero:true }
                }
            }
        });
    }
    function delAtt(id){ if(confirm('정말 삭제하시겠습니까?')) { db.students[curStudent].attention=db.students[curStudent].attention.filter(x=>x.id!==id); saveStudent(curStudent); } }

    // ==================== AI 분석 엔진 (규칙 기반) ====================
    function collectAttStats(sid, days){
        const s = getStudent(sid); if(!s) return [];
        const all = (s.attention||[]).filter(x=>inPeriod(x.date, days) && x.date);
        const groups = {};
        all.forEach(x=>{ const k=`${x.cat}|${x.sub||'기본'}`; (groups[k]=groups[k]||[]).push(x); });
        return Object.entries(groups).map(([k, arr])=>{
            const [cat, sub] = k.split('|');
            arr.sort((a,b)=>new Date(a.date)-new Date(b.date));
            const times = arr.map(x=>Number(x.time)||0), errs = arr.map(x=>Number(x.error)||0);
            const half = Math.ceil(arr.length/2);
            return { cat, sub, n: arr.length, first: arr[0].date, last: arr[arr.length-1].date,
                t1: mean(times.slice(0,half)), t2: mean(times.slice(half)),
                e1: mean(errs.slice(0,half)), e2: mean(errs.slice(half)),
                times, errs, meanErr: mean(errs), meanTime: mean(times) };
        });
    }
    function attVerdictText(g){
        if(g.n < 3) return `<b>「${esc(g.sub)}」</b> 기록 ${g.n}회 — 분석에 충분하지 않습니다(회기당 3회 이상 권장).`;
        const tc = pctChange(g.t1, g.t2), ec = pctChange(g.e1, g.e2);
        const tcT = tc===null?'-':(tc>0?`+${tc}`:tc), ecT = ec===null?'-':(ec>0?`+${ec}`:ec);
        let v;
        if(tc!==null && tc<=-15 && (ec===null || ec<=15))
            v = `✅ <b>효율 향상</b> — 수행시간 <b>${tcT}%</b> 단축, 오류 <b>${ecT}%</b> 유지·감소. 과제가 자동화되고 있으므로 다음 단계로 <b>난이도 상향</b>을 준비할 시점입니다.`;
        else if(tc!==null && tc<=-15 && ec>15)
            v = `⚠️ <b>속도만 빨라짐</b> — 시간 ${tcT}% 단축됐지만 오류 <b>+${ec}%</b> 증가. 성급한 수행(부주의한 단축) 경향이므로 <b>정확도 목표를 병행</b>해야 합니다.`;
        else if(ec!==null && ec<=-20)
            v = `🎯 <b>정확도 향상 단계</b> — 오류 <b>${ecT}%</b> 감소(시간 ${tcT}%). 신중한 처리가 잡힌 단계이므로, 안정이 유지되면 <b>시간 단축 목표로 전환</b>할 수 있습니다.`;
        else if((tc!==null && tc>10) && (ec!==null && ec>10))
            v = `🌀 <b>수행 저하</b> — 시간 +${tcT}%, 오류 +${ecT}%. 피로 누적, 수업 시간대, 활동 난이도 점검이 필요합니다.`;
        else
            v = `➖ <b>안정(플래토)</b> — 시간 ${tcT}%, 오류 ${ecT}%로 현재 난이도에서 학습된 수준에 도달. 활동 방식·난이도 조정의 적기입니다.`;
        return `<b>「${esc(g.sub)}」</b> (${g.cat==='visual'?'시각':g.cat==='auditory'?'청각':'속도'} · ${g.n}회, ${koDate(g.first)}~${koDate(g.last)})<br>${v}<span style="color:#888; font-size:12px;"> 전반 시간 ${Math.round(g.t1)}→${Math.round(g.t2)}초 · 오류 ${g.e1.toFixed(1)}→${g.e2.toFixed(1)}개</span>`;
    }
    function buildAttentionAnalysis(sid, days){
        const stats = collectAttStats(sid, days);
        if(!stats.length) return '';
        const blocks = [];
        stats.forEach(g=>{ blocks.push(`<div class="an-block">${attVerdictText(g)}</div>`); });
        // 영역 간 비교
        const byCat = {};
        stats.forEach(g=>{ (byCat[g.cat]=byCat[g.cat]||[]).push(g); });
        const catStats = Object.entries(byCat).map(([cat, gs])=>({ cat, meanErr: mean(gs.map(g=>g.meanErr)), meanTime: mean(gs.map(g=>g.meanTime)), n: gs.reduce((a,g)=>a+g.n,0) })).filter(c=>c.n>=3);
        if(catStats.length >= 2){
            catStats.sort((a,b)=>b.meanErr-a.meanErr);
            const weakest = catStats[0], best = catStats[catStats.length-1];
            const catKo = c=>({'visual':'시각주의력','auditory':'청각주의력','speed':'처리속도'}[c]||c);
            if(weakest.meanErr > 0 && best.meanErr !== weakest.meanErr){
                const ratio = best.meanErr ? (weakest.meanErr/best.meanErr).toFixed(1) : '-';
                blocks.push(`<div class="an-block">🔎 <b>영역 간 비교</b> — 평균 오류 기준 <b>${catKo(weakest.cat)}</b>이(${weakest.meanErr.toFixed(1)}개) 가장 약점이고, <b>${catKo(best.cat)}</b>이(${best.meanErr.toFixed(1)}개) 가장 강점입니다(약 ${ratio}배 차이). ${catKo(weakest.cat)} 활동의 난이도를 한 단계 낮추고 <b>성공 경험을 먼저 쌓은 뒤</b> 점진적으로 올리는 것을 권장합니다.</div>`);
            } else {
                blocks.push(`<div class="an-block">🔎 <b>영역 간 비교</b> — 세 영역의 오류 수준이 비슷합니다(${catStats.map(c=>`${c.meanErr.toFixed(1)}개`).join(' · ')}). 특정 영역 약점보다 <b>전반적 주의 유지</b>가 과제입니다.</div>`);
            }
        }
        return blocks.join('');
    }

    // ==================== 2. WISC-V ====================
    function saveWisc(){
        const ids=['wFsiq','wVci','wVsi','wFri','wWmi','wPsi'];
        const vals = ids.map(id=>document.getElementById(id).value);
        const bad = vals.find(v=>v!=='' && (Number(v)<40 || Number(v)>160));
        if(bad!==undefined && bad!=='') { if(!confirm('지수 점수는 보통 40~160입니다. 그래도 저장할까요?')) return; }
        db.students[curStudent].wisc = { scores: vals, comment: document.getElementById('wComment').value, date: document.getElementById('wDate').value };
        saveStudent(curStudent); toast('저장되었습니다');
    }
    function renderWisc(){
        const st=getStudent(curStudent); if(!st) return;
        const s=st.wisc.scores||[0,0,0,0,0,0];
        ['wFsiq','wVci','wVsi','wFri','wWmi','wPsi'].forEach((id,i)=>document.getElementById(id).value=s[i]||'');
        document.getElementById('wDate').value = st.wisc.date||'';
        document.getElementById('wComment').value = st.wisc.comment||'';
        const lbs=["FSIQ","언어","시공간","유동","작업","처리"];
        const nums=[1,2,3,4,5].map(i=>Number(s[i])||0);
        const max=Math.max(...nums), minV=Math.min(...nums.filter(v=>v>0));
        const significant = (max - minV) >= 10;
        let str=[],weak=[];
        if(significant && max>0){ nums.forEach((v,i)=>{ if(v===max) str.push(lbs[i+1]); }); nums.forEach((v,i)=>{ if(v===minV) weak.push(lbs[i+1]); }); }
        const note = significant ? '' : '<p style="font-size:12.5px; color:#888;">* 지수 간 차이가 10점 미만이어서 유의미한 강점/약점으로 해석하지 않습니다(전반적으로 고른 프로파일).</p>';
        document.getElementById('wiscSummary').innerHTML = `<p><strong>🔹 FSIQ:</strong> ${esc(s[0]||'-')} ${st.wisc.date?`<span style="color:#888; font-size:12px;">(검사일 ${esc(st.wisc.date)})</span>`:''}</p><p><strong>📈 강점:</strong> <span class="strength-tag">${str.join(', ') || '-'}</span>${significant?` (${max})`:''}</p><p><strong>📉 약점:</strong> <span class="weakness-tag">${weak.join(', ') || '-'}</span>${significant?` (${minV})`:''}</p>${note}<hr><p>${esc(st.wisc.comment||'').replace(/\n/g,'<br>')}</p>`;
        const ctx=document.getElementById('wiscChart'); if(charts.wisc)charts.wisc.destroy();
        charts.wisc=new Chart(ctx,{type:'radar',data:{labels:lbs.slice(1),datasets:[{label:'지수 프로파일',data:nums,backgroundColor:'rgba(52,152,219,0.2)',borderColor:'#3498db'}]},options:{scales:{r:{min:40,max:160}}}});
    }

    // ==================== 3. 상담/수업일지 + 스니펫 ====================
    function renderSnippetSelect(){
        const sel = document.getElementById('snippetSelect'); if(!sel) return;
        const items = db.snippets||[];
        const cats = [...new Set(items.map(s=>s.cat))];
        sel.innerHTML = '<option value="">— 선택하면 입력창에 삽입됩니다 —</option>' + cats.map(c=>`<optgroup label="${esc(c)}">${items.filter(s=>s.cat===c).map(s=>`<option value="${s.id}">${esc(s.text.length>26?s.text.slice(0,26)+'…':s.text)}</option>`).join('')}</optgroup>`).join('');
    }
    function insertSnippet(sel){
        if(!sel.value) return;
        const snip = (db.snippets||[]).find(s=>String(s.id)===sel.value);
        sel.value='';
        if(!snip) return;
        const ta = document.getElementById('cnsContent');
        const pos = ta.selectionStart!=null ? ta.selectionStart : ta.value.length;
        const pre = (pos>0 && ta.value[pos-1]!=='\n') ? '\n' : '';
        ta.value = ta.value.slice(0,pos) + pre + snip.text + ta.value.slice(ta.selectionEnd||pos);
        ta.focus();
    }
    function addCounsel(){
        const d=document.getElementById('cnsDate').value, t=document.getElementById('cnsType').value, c=document.getElementById('cnsContent').value;
        if(d&&c){ if(!db.students[curStudent].counsel) db.students[curStudent].counsel=[];
            db.students[curStudent].counsel.push({id:Date.now(),date:d,type:t,content:c,shared:document.getElementById('cnsShared').checked}); saveStudent(curStudent); document.getElementById('cnsContent').value=''; document.getElementById('cnsShared').checked=false; }
        else toast('날짜와 내용을 입력하세요', true);
    }
    function renderCounsel(){
        const l=document.getElementById('counselList'); l.innerHTML='';
        const d=(getStudent(curStudent).counsel||[]).slice();
        const colorMap={'학부모상담':'#e74c3c','수업관찰':'#3498db','과제부여':'#27ae60'};
        d.sort((a,b)=>new Date(b.date)-new Date(a.date));
        if(!d.length){ l.innerHTML='<div style="text-align:center; color:#999; padding:30px;">아직 기록이 없습니다.</div>'; return; }
        d.forEach(x=>{ const c=colorMap[x.type]||'#999';
            l.innerHTML+=`<div class="log-entry" style="border-left-color:${c}"><div class="log-header"><span>📅 ${esc(x.date)}</span><span class="log-badge" style="background:${c}">${esc(x.type)}</span></div><div class="log-content">${esc(x.content)}</div><div style="text-align:right"><span style="cursor:pointer;font-size:12px;color:${x.shared?'#27ae60':'#ccc'};font-weight:${x.shared?'700':'400'}" onclick="toggleShare(${x.id})">${x.shared?'🔗 가정공유중':'🔒 미공유'}</span> · <span style="cursor:pointer;font-size:12px;color:#ccc" onclick="delCns(${x.id})">삭제</span></div></div>`; });
    }
    function delCns(id){ if(confirm('삭제?')){ db.students[curStudent].counsel=db.students[curStudent].counsel.filter(x=>x.id!==id); saveStudent(curStudent); } }
    function openSnippetManager(){ openSettings('snippetMgmt'); }

    // ==================== 4. 행동중재 ====================
    function addBehavior(){
        const n=document.getElementById('bhName').value.trim(), d=document.getElementById('bhDef').value.trim();
        if(!n) return toast('표적행동 이름을 입력하세요', true);
        const st=getStudent(curStudent);
        st.behaviors = st.behaviors||[];
        st.behaviors.push({ id:'b'+Date.now(), name:n, definition:d, phase:'A', records:{} });
        document.getElementById('bhName').value=''; document.getElementById('bhDef').value='';
        saveStudent(curStudent);
    }
    function tapBehavior(bid){
        const b = getStudent(curStudent).behaviors.find(x=>x.id===bid); if(!b) return;
        const key = todayStr();
        const rec = b.records[key] || { count:0, phase:b.phase, note:'' };
        rec.count++; b.records[key] = rec;
        saveStudent(curStudent); toast(`${b.name} +1 (오늘 ${rec.count}회)`);
    }
    function undoTap(bid){
        const b = getStudent(curStudent).behaviors.find(x=>x.id===bid); if(!b) return;
        const key = todayStr(); const rec = b.records[key];
        if(rec && rec.count>0){ rec.count--; if(rec.count===0) delete b.records[key]; saveStudent(curStudent); }
    }
    function switchPhase(bid){
        const b = getStudent(curStudent).behaviors.find(x=>x.id===bid); if(!b) return;
        if(b.phase==='A'){
            if(confirm(`「${b.name}」을(를) 중재(B) 단계로 전환할까요?\n이제부터 기록되는 횟수가 '중재' 구간으로 계산됩니다.\n※ 기초선은 3회 이상(권장 5회) 확보를 권장합니다.`)){ b.phase='B'; saveStudent(curStudent); }
        } else if(confirm(`「${b.name}」을(를) 기초선(A)으로 되돌릴까요? (이후 기록이 A로 계산됨)`)){ b.phase='A'; saveStudent(curStudent); }
    }
    function renderBehavior(){
        const st=getStudent(curStudent); if(!st) return;
        st.behaviors = st.behaviors||[];
        const taps=document.getElementById('behaviorTaps');
        if(!st.behaviors.length){ taps.innerHTML='<span style="color:#999;">등록된 표적행동이 없습니다. 위에서 등록해 주세요.</span>'; document.getElementById('bhChartCard').style.display='none'; document.getElementById('bhListCard').style.display='none'; return; }
        taps.innerHTML = st.behaviors.map(b=>{
            const today = b.records[todayStr()];
            return `<div class="tap-card">
                <div class="tc-name">${esc(b.name)}</div>
                <div class="tc-def">${esc(b.definition||'정의 미입력')}</div>
                <button class="tap-btn ${b.phase==='A'?'phase-a':'phase-b'}" onclick="tapBehavior('${b.id}')">+1 기록<br><span style="font-size:11px; font-weight:400;">한 번 누르면 1회</span></button>
                <div class="tc-today">오늘 <b>${today?today.count:0}</b>회 · <span class="phase-chip ${b.phase==='A'?'a':'b'}">${b.phase==='A'?'기초선 A':'중재 B'}</span></div>
                <div style="margin-top:8px; display:flex; gap:6px; justify-content:center;">
                    <button class="btn-gray btn-sm" onclick="undoTap('${b.id}')">−1 취소</button>
                    <button class="btn-blue btn-sm" onclick="switchPhase('${b.id}')">${b.phase==='A'?'중재(B) 전환':'기초선(A)으로'}</button>
                    <button class="btn-red btn-sm" onclick="delBehavior('${b.id}')">삭제</button>
                </div>
            </div>`;
        }).join('');
        // 첫 번째(또는 선택된) 행동 그래프
        const b = st.behaviors[0];
        document.getElementById('bhChartCard').style.display='block';
        document.getElementById('bhListCard').style.display='block';
        document.getElementById('bhChartTitle').innerHTML = `📈 ${esc(b.name)} — 빈도 추이 <span class="phase-chip ${b.phase==='A'?'a':'b'}" style="margin-left:8px;">현재 ${b.phase==='A'?'기초선':'중재'}</span>`;
        const dates = Object.keys(b.records).sort();
        const aData = dates.map(d=> b.records[d].phase==='A' ? b.records[d].count : null);
        const bData = dates.map(d=> b.records[d].phase==='B' ? b.records[d].count : null);
        const aVals = aData.filter(v=>v!==null), bVals = bData.filter(v=>v!==null);
        const aMean = mean(aVals);
        if(charts.bh) charts.bh.destroy();
        charts.bh = new Chart(document.getElementById('bhChart'), {
            type:'line',
            data:{ labels: dates.map(koDate),
                datasets:[
                    { label:'기초선(A)', data:aData, borderColor:'#16707f', backgroundColor:'rgba(22,112,127,0.08)', fill:'origin', tension:0, spanGaps:false, pointRadius:4 },
                    { label:'중재(B)', data:bData, borderColor:'#2e6bd6', tension:0, spanGaps:false, pointRadius:4 },
                    ...(aVals.length>=2 ? [{ label:'기초선 평균', data:dates.map((d,i)=> aData[i]!==null ? aMean.toFixed(1) : null), borderColor:'#95a5a6', borderDash:[6,5], pointRadius:0 }] : [])
                ]},
            options:{ responsive:true, maintainAspectRatio:false, scales:{ y:{ beginAtZero:true, title:{display:true, text:'회/일'} } } }
        });
        // 통계
        const statHtml = (label, vals)=>{ if(!vals.length) return ''; const med=median(vals); const stab = Math.round(vals.filter(v=>Math.abs(v-med)<=med*0.2).length/vals.length*100);
            return `<div class="stat-item"><div class="si-v">${vals.length}회기</div><div class="si-l">${label} 회기 수</div></div>
            <div class="stat-item"><div class="si-v">${mean(vals).toFixed(1)}회</div><div class="si-l">${label} 평균</div><div class="si-note">중앙값 ${med}</div></div>
            <div class="stat-item"><div class="si-v">${Math.min(...vals)}~${Math.max(...vals)}</div><div class="si-l">범위</div></div>
            <div class="stat-item"><div class="si-v">${label.includes('기초선')?stab+'%':'-'}</div><div class="si-l">안정성(참고)</div>${label.includes('기촌선')?'':'<div class="si-note">'+(label.includes('기초선')?'중앙값 ±20% 내 회기 비율':'')+'</div>'}</div>`; };
        let stats = statHtml('기초선', aVals) + statHtml('중재', bVals);
        if(aVals.length && bVals.length){
            const chg = pctChange(aMean, mean(bVals));
            stats += `<div class="stat-item" style="background:#eaf2f8; border-color:#aed6f1;"><div class="si-v">${chg>0?'+':''}${chg}%</div><div class="si-l">중재 vs 기초선</div><div class="si-note">${chg<0?'중재 후 행동이 감소했습니다':chg>0?'중재 후 행동이 증가했습니다':'변화 없음'}</div></div>`;
        }
        document.getElementById('bhStats').innerHTML = stats;
        document.getElementById('bhHint').innerHTML = '* 안정성 판정은 그래프와 함께 교사/연구자가 최종 판단합니다. 자료점 기준은 단일대상설계 관행(단계당 3개 이상, 5개 이상 권장)을 따릅니다.';
        // 히스토리 테이블
        const tb=document.querySelector('#bhTable tbody'); tb.innerHTML='';
        if(!dates.length){ tb.innerHTML='<tr class="empty-row"><td colspan="5">기록이 없습니다. 위 버튼으로 오늘 기록을 시작하세요.</td></tr>'; return; }
        dates.slice().reverse().forEach(d=>{ const r=b.records[d];
            tb.innerHTML+=`<tr><td>${esc(d)}</td><td><span class="phase-chip ${r.phase==='A'?'a':'b'}">${r.phase==='A'?'기초선':'중재'}</span></td><td><strong>${r.count}회</strong></td><td>${esc(r.note||'')}</td><td><span onclick="editBhRecord('${b.id}','${d}')" style="cursor:pointer; margin-right:8px;" title="수정">✎</span><span onclick="delBhRecord('${b.id}','${d}')" style="color:red; cursor:pointer;" title="삭제">✕</span></td></tr>`; });
    }
    function editBhRecord(bid, date){
        const b = getStudent(curStudent).behaviors.find(x=>x.id===bid); if(!b) return;
        const r = b.records[date]; if(!r) return;
        openModal(`<h3>✎ 기록 수정 — ${esc(b.name)} (${esc(date)})</h3>
            <div class="mo-row"><label>횟수</label><input type="number" id="mBhCount" value="${r.count}" min="0"></div>
            <div class="mo-row"><label>단계</label><select id="mBhPhase"><option value="A" ${r.phase==='A'?'selected':''}>기초선(A)</option><option value="B" ${r.phase==='B'?'selected':''}>중재(B)</option></select></div>
            <div class="mo-row"><label>비고</label><input type="text" id="mBhNote" value="${esc(r.note||'')}"></div>
            <div class="mo-foot"><button class="btn-gray" onclick="closeModal()">취소</button><button class="btn-primary" onclick="saveBhRecord('${bid}','${date}')">저장</button></div>`);
    }
    function saveBhRecord(bid, date){
        const b = getStudent(curStudent).behaviors.find(x=>x.id===bid); if(!b) return;
        b.records[date] = { count: Number(document.getElementById('mBhCount').value)||0, phase: document.getElementById('mBhPhase').value, note: document.getElementById('mBhNote').value };
        closeModal(); saveStudent(curStudent);
    }
    function delBhRecord(bid, date){ if(confirm('삭제?')){ const b=getStudent(curStudent).behaviors.find(x=>x.id===bid); delete b.records[date]; saveStudent(curStudent); } }
    function delBehavior(bid){ if(confirm('표적행동과 모든 기록이 삭제됩니다. 계속할까요?')){ const st=getStudent(curStudent); st.behaviors=st.behaviors.filter(x=>x.id!==bid); saveStudent(curStudent); } }

    // ==================== 5. 발달 ====================
    function saveDev(){
        const st=getStudent(curStudent);
        const scores=['dLang','dCog','dSoc','dGross','dFine'].map(id=>document.getElementById(id).value);
        const comment=document.getElementById('dComment').value;
        st.dev = st.dev||{scores:[3,3,3,3,3], comment:'', history:[]};
        st.dev.history = st.dev.history||[];
        st.dev.history.push({ date: todayStr(), scores, comment });
        if(st.dev.history.length>30) st.dev.history = st.dev.history.slice(-30);
        st.dev.scores = scores; st.dev.comment = comment;
        saveStudent(curStudent); toast('저장되었습니다 (기록 이력에 저장 시점이 남습니다)');
    }
    function renderDev(){
        const st=getStudent(curStudent); if(!st) return;
        const s=st.dev.scores||[3,3,3,3,3];
        ['dLang','dCog','dSoc','dGross','dFine'].forEach((id,i)=>document.getElementById(id).value=s[i]);
        document.getElementById('dComment').value=st.dev.comment||'';
        const ctx=document.getElementById('devChart'); if(charts.dev)charts.dev.destroy();
        charts.dev=new Chart(ctx,{type:'radar',data:{labels:['언어','인지','사회성','대근육','소근육'],datasets:[{label:'발달수준',data:s.map(Number),backgroundColor:'rgba(243,156,18,0.2)',borderColor:'#f39c12'}]},options:{scales:{r:{min:0,max:5,ticks:{stepSize:1}}}}});
        const hist = st.dev.history||[];
        const card = document.getElementById('devHistoryCard');
        if(hist.length){ card.style.display='block';
            document.querySelector('#devHistoryTable tbody').innerHTML = hist.slice().reverse().map(h=>`<tr><td>${esc(h.date)}</td><td>${h.scores[0]}</td><td>${h.scores[1]}</td><td>${h.scores[2]}</td><td>${h.scores[3]}</td><td>${h.scores[4]}</td><td style="text-align:left; font-size:12px;">${esc((h.comment||'').slice(0,40))}</td></tr>`).join('');
        } else card.style.display='none';
    }

    // ==================== AI 종합리포트 ====================
    function collectBehaviorStats(sid, days){
        const st=getStudent(sid); const out=[];
        ((st.behaviors)||[]).forEach(b=>{
            const dates=Object.keys(b.records||{}).filter(d=>inPeriod(d,days)).sort();
            if(!dates.length) return;
            const aVals=dates.filter(d=>b.records[d].phase==='A').map(d=>b.records[d].count);
            const bVals=dates.filter(d=>b.records[d].phase==='B').map(d=>b.records[d].count);
            out.push({ name:b.name, phase:b.phase, aN:aVals.length, bN:bVals.length, aMean:aVals.length?mean(aVals):null, bMean:bVals.length?mean(bVals):null, chg:(aVals.length&&bVals.length)?pctChange(mean(aVals),mean(bVals)):null });
        });
        return out;
    }
    function collectCounselSummary(sid, days){
        const st=getStudent(sid);
        return (st.counsel||[]).filter(x=>inPeriod(x.date,days)).sort((a,b)=>new Date(b.date)-new Date(a.date));
    }
    function collectAttendance(sid, days){
        const list = db.schedule.filter(s=>s.sid===sid && inPeriod(s.date, days));
        const total=list.length;
        const present=list.filter(s=>s.status==='present').length, makeup=list.filter(s=>s.status==='makeup').length, absent=list.filter(s=>s.status==='absent').length;
        return { total, present, makeup, absent, rate: total? Math.round((present+makeup)/total*100) : null };
    }
    function buildAiPrompt(sid, days){
        const st=getStudent(sid);
        const nm = VIEW_REAL ? getStudentName(sid, true) : st.maskName;
        const att = collectAttStats(sid, days).map(g=>({활동:g.sub, 영역:g.cat, 회기수:g.n, '전반평균시간초':Math.round(g.t1), '후반평균시간초':Math.round(g.t2), '전반평균오류':+g.e1.toFixed(1), '후반평균오류':+g.e2.toFixed(1)}));
        const bh = collectBehaviorStats(sid, days);
        const wisc = st.wisc||{};
        const dev = st.dev||{};
        const counsel = collectCounselSummary(sid, days).slice(0,10).map(c=>({날짜:c.date, 유형:c.type, 내용:c.content}));
        return `당신은 발달심리·학습심리 전문가입니다. 아래는 학습센터의 한 아동 기록입니다. 데이터에 근거해서만 서술하고, 없는 수치를 만들지 마세요.
[아동] ${nm} / 기간: ${days==='all'?'전체':'최근 '+days+'일'} / 출석률: ${JSON.stringify(collectAttendance(sid,days))}
[주의력 훈련 통계] ${JSON.stringify(att)}
[행동중재] ${JSON.stringify(bh)}
[WISC 지수] ${JSON.stringify(wisc.scores||[])} (순서: FSIQ,언어,시공간,유동,작업,처리) 소견: ${wisc.comment||'없음'}
[발달평가] ${JSON.stringify(dev.scores||[])} (언어,인지,사회성,대근육,소근육 / 5점 만점)
[상담·수업일지 발췌] ${JSON.stringify(counsel)}

다음 형식으로 한국어로 작성하세요(마크다운 금지, 일반 텍스트):
[종합 소견] 4~6문장. 시간 단축/오류 변화의 의미, 영역 간 강점·약점, 행동중재 변화를 연결해서 해석.
[강점] 2개.
[유의·우려] 2개.
[다음 단계 제안] 3개.
문체는 학부모에게 보여줄 수 있는 정중한 존댓말로.`;
    }
    async function callGemini(prompt, key){
        const model = localStorage.getItem('solbit_gemini_model') || 'gemini-2.0-flash';
        const res = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/${encodeURIComponent(model)}:generateContent?key=${encodeURIComponent(key)}`, {
            method:'POST', headers:{'Content-Type':'application/json'},
            body: JSON.stringify({ contents:[{ parts:[{text: prompt}] }] })
        });
        if(!res.ok){ const t = await res.text(); throw new Error('API 오류 '+res.status+' '+t.slice(0,120)); }
        const j = await res.json();
        return (j.candidates && j.candidates[0] && j.candidates[0].content && j.candidates[0].content.parts.map(p=>p.text).join('')) || '응답이 비어 있습니다.';
    }
    function renderReportView(){ if(curStudent && document.getElementById('reportWrap').dataset.done==='1') generateReport(false); }
    function buildReportHtml(sid, days, opts={}){
        const st = getStudent(sid); if(!st) return '';
        const nm = opts.childName || (VIEW_REAL ? getStudentName(sid, true) : st.maskName);
        const att = collectAttStats(sid, days);
        const attendance = collectAttendance(sid, days);
        const bh = collectBehaviorStats(sid, days);
        const counsel = collectCounselSummary(sid, days);
        const attBlocks = buildAttentionAnalysis(sid, days);
        const totalSessions = att.reduce((a,g)=>a+g.n,0);
        const attStatsHtml = `<div class="stat-grid">
            <div class="stat-item"><div class="si-v">${totalSessions}</div><div class="si-l">훈련 회기</div></div>
            <div class="stat-item"><div class="si-v">${attendance.rate===null?'-':attendance.rate+'%'}</div><div class="si-l">출석률</div><div class="si-note">출석 ${attendance.present} · 보강 ${attendance.makeup} · 결석 ${attendance.absent}</div></div>
            ${att.map(g=>`<div class="stat-item"><div class="si-v">${Math.round(g.meanTime)}초</div><div class="si-l">${esc(g.sub)} 평균시간</div><div class="si-note">평균 오류 ${g.meanErr.toFixed(1)}개 · ${g.n}회</div></div>`).join('')}
        </div>`;
        const bhHtml = bh.length ? bh.map(b=>`<p>· <b>${esc(b.name)}</b>: 기초선 ${b.aN||0}회기 평균 ${b.aMean===null?'-':b.aMean.toFixed(1)}회 → 중재 ${b.bN||0}회기 평균 ${b.bMean===null?'-':b.bMean.toFixed(1)}회 ${b.chg===null?'':`(중재 후 <b>${b.chg>0?'+':''}${b.chg}%</b> ${b.chg<0?'감소':b.chg>0?'증가':'유지'})`} — 현재 ${b.phase==='A'?'기초선':'중재'} 단계</p>`).join('') : '<p>기간 내 행동중재 기록이 없습니다.</p>';
        const cnCounts = {}; counsel.forEach(c=>cnCounts[c.type]=(cnCounts[c.type]||0)+1);
        const cnHtml = counsel.length ? `<p>기간 내 일지 ${counsel.length}건 (${Object.entries(cnCounts).map(([k,v])=>`${k} ${v}건`).join(', ')})</p><ul>${counsel.slice(0,6).map(c=>`<li>${esc(c.date)} [${esc(c.type)}] ${esc(c.content.length>70?c.content.slice(0,70)+'…':c.content)}</li>`).join('')}</ul>` : '<p>기간 내 상담·수업 일지가 없습니다.</p>';
        const ai = st.aiReport;
        const aiHtml = ai ? `<div class="ai-out">${esc(ai.text)}</div><p class="hint" style="margin-top:8px;">AI 생성일 ${esc(ai.date)} · 기간 설정: ${ai.period==='all'?'전체':'최근 '+ai.period+'일'}</p>` : (opts.forParent ? '<p style="color:#888;">아직 AI 소견이 등록되지 않았습니다.</p>' : `<p style="color:#888;">'✨ AI 소견 보강' 버튼을 누르면 위 데이터를 종합한 소견이 여기에 추가됩니다. (설정 → AI 탭에서 무료 Gemini API 키 등록 시)</p>`);
        return `
        <div class="report">
            <div class="rp-head">
                <div class="rp-org">${ORG_NAME}</div>
                <div class="rp-title">학생 통합 리포트</div>
                <div class="rp-meta">${esc(nm)} · 기간: ${days==='all'?'전체':'최근 '+days+'일'} · 생성일 ${todayStr()}${opts.forParent?'':(VIEW_REAL?'':' (실명 마스킹)')}</div>
            </div>
            <h3>1. 훈련 참여 요약</h3>
            ${attStatsHtml}
            <h3>2. 주의력 훈련 분석 <span class="ai-tag" style="font-size:11px;">자동 분석</span></h3>
            ${attBlocks || '<p>기간 내 훈련 데이터가 없습니다.</p>'}
            <h3>3. 행동중재 현황</h3>
            ${bhHtml}
            <h3>4. 상담 · 수업 일지</h3>
            ${cnHtml}
            <h3>5. AI 종합 소견</h3>
            ${aiHtml}
            <div class="rp-dis">본 리포트는 기록 데이터 기반 자동 분석 결과이며, 전문가의 임상적 판단을 대체하지 않습니다. 검사 결과 해석은 전문가와 함께 확인하시기 바랍니다.</div>
        </div>`;
    }
    function generateReport(scroll=true){
        if(!curStudent) return toast('먼저 학생을 선택하세요', true);
        const days = document.getElementById('aiPeriod').value;
        document.getElementById('reportWrap').dataset.done='1';
        document.getElementById('reportWrap').innerHTML = buildReportHtml(curStudent, days);
        if(scroll) document.getElementById('view-aireport').scrollTop = 0;
    }
    async function generateAiOpinion(){
        if(!curStudent) return toast('먼저 학생을 선택하세요', true);
        const key = localStorage.getItem('solbit_gemini_key');
        if(!key){ openSettings('ai'); return toast('Gemini API 키를 먼저 등록해 주세요 (설정 → AI)', true); }
        const btn=document.getElementById('btnAiOp'); btn.disabled=true; btn.innerText='분석 중...';
        try {
            const days = document.getElementById('aiPeriod').value;
            const text = await callGemini(buildAiPrompt(curStudent, days), key);
            const st = getStudent(curStudent);
            st.aiReport = { date: todayStr(), period: days, text };
            await saveStudent(curStudent);
            generateReport(false);
            toast('AI 소견이 생성되어 리포트에 반영되었습니다');
        } catch(e){ toast('AI 호출 실패: '+e.message, true); }
        finally { btn.disabled=false; btn.innerText='✨ AI 소견 보강'; }
    }
    function printReport(){
        const v=document.getElementById('view-aireport');
        v.classList.add('print-target'); window.print();
        setTimeout(()=>v.classList.remove('print-target'), 800);
    }

    // ==================== 설정 (관리자) ====================
    let settingsTab = null;
    const RULES = `{
  "rules": {
    "users": {
      ".read": "auth != null && root.child('users/'+auth.uid+'/role').val() === 'admin'",
      "$uid": {
        ".read": "auth != null && (auth.uid === $uid || root.child('users/'+auth.uid+'/role').val() === 'admin')",
        ".write": "auth != null && (!root.child('users').hasChildren() || auth.uid === $uid || root.child('users/'+auth.uid+'/role').val() === 'admin')",
        "approved": { ".write": "auth != null && (!root.child('users').hasChildren() || root.child('users/'+auth.uid+'/role').val() === 'admin')" },
        "role": { ".write": "auth != null && (!root.child('users').hasChildren() || root.child('users/'+auth.uid+'/role').val() === 'admin')" },
        "childSid": { ".write": "auth != null && (!root.child('users').hasChildren() || root.child('users/'+auth.uid+'/role').val() === 'admin')" }
      }
    },
    "solbit_data": {
      ".read": "auth != null && root.child('users/'+auth.uid+'/approved').val() === true && root.child('users/'+auth.uid+'/role').val() !== 'parent'",
      ".write": "auth != null && root.child('users/'+auth.uid+'/approved').val() === true && root.child('users/'+auth.uid+'/role').val() !== 'parent'"
    },
    "parent_view": {
      "$uid": {
        ".read": "auth != null && auth.uid === $uid && root.child('users/'+auth.uid+'/approved').val() === true",
        ".write": "auth != null && root.child('users/'+auth.uid+'/approved').val() === true && root.child('users/'+auth.uid+'/role').val() !== 'parent'"
      }
    }
  }
}`;
    function copyRules(){
        const t = document.getElementById('rulesBox') || document.getElementById('rulesBox2');
        t.select(); document.execCommand('copy');
        if(navigator.clipboard) navigator.clipboard.writeText(RULES).catch(()=>{});
        toast('규칙이 복사되었습니다');
    }
    const _startApp = startApp; let appBooted = false;
    startApp = function(){ if(appBooted) return; appBooted = true; _startApp(); };
    const _cuv = currentUserView;
    currentUserView = function(){ const mbk=document.getElementById('mbk'); if(mbk.classList.contains('open') && settingsTab) return settingsTab; return _cuv(); };
    if(IS_DEMO){
        const _demoInit = DemoDB.init.bind(DemoDB);
        DemoDB.init = function(onReady, onError){ this._h = onReady; _demoInit(onReady, onError); };
        const _demoOnChange = DemoAuth.onChange.bind(DemoAuth);
        DemoAuth.onChange = function(cb){ const asParent = location.search.includes('as=parent'); const wantUid = asParent ? 'demo-parent' : 'demo-admin'; localStorage.setItem('solbit_demo_session', wantUid); const users = demoLoadUsers(); if(!users[wantUid]){ users[wantUid] = wantUid==='demo-parent' ? {name:'김하은 학부모', email:'parent@demo.kr', role:'parent', approved:true, childSid:'s1'} : {name:'데모 관리자', email:'demo@sobit.kr', role:'admin', approved:true}; try{ localStorage.setItem('solbit_demo_users', JSON.stringify(users)); }catch(e){} } _demoOnChange(cb); };
        const _demoSetU = DemoDB.onUserChange.bind(DemoDB);
        DemoDB.onUserChange = function(uid, profile){ this.users = this.users||{}; if(profile===null) delete this.users[uid]; else this.users[uid] = {...(this.users[uid]||{}), ...profile}; demoSaveUser(); if(this._usersCb) this._usersCb(this.users); return Promise.resolve(); };
        const _realSetU = RealDB.onUserChange.bind(RealDB);
        RealDB.onUserChange = function(uid, profile){ if(profile===null) return this._usersRoot.child(uid).remove(); return this._usersRoot.child(uid).update(profile); };
        const _after = afterDataReady;
        afterDataReady = async function(){ await demoCryptoInit(); _after(); };
    }
    async function demoCryptoInit(){
        if(!db.crypto || !db.crypto.enabled || !db.crypto.salt || !db.crypto.check){
            db.crypto = { enabled:true, salt: CRYPTO.b64(crypto.getRandomValues(new Uint8Array(16))), check:null };
        }
        const key = await CRYPTO.deriveKey('demo1234', db.crypto.salt);
        for(const sid of Object.keys(db.students)){
            const s = db.students[sid];
            if(s.namePlain && !s.nameEnc){ s.nameEnc = await CRYPTO.encrypt(s.namePlain, key); if(!IS_DEMO) delete s.namePlain; }
        }
        if(!db.crypto.check) db.crypto.check = await CRYPTO.encrypt('solbit-ok', key);
        sessionKey = key; VIEW_REAL = true;
        await unlockNames();
        const et=document.getElementById('eyeToggle'); et.innerText='실명 보기 중'; et.classList.add('on');
    }
    function authHandler(user){
        if(!user){ showScreen('login'); return; }
        DB.getUser(user.uid).then(profile => {
            if(!profile){ showScreen('pending'); return; }
            me = { uid: user.uid, email: user.email, profile };
            if(profile.approved){ if(myRole()==='parent') startParentApp(); else startApp(); } else { showScreen('pending'); }
        }).catch(()=>{ showScreen('pending'); });
    }
    AUTH.onChange(authHandler);
    const _rb = document.getElementById('rulesBox'); if(_rb) _rb.value = RULES;

    function openSettings(tab){
        settingsTab = tab || (me.profile.role==='admin' ? 'pendingList' : 'sec');
        openModal(`<h3>⚙️ 설정</h3><div class="setting-nav" id="setNav"></div><div id="setBody"></div><div class="mo-foot"><button class="btn-gray" onclick="closeModal()">닫기</button></div>`);
        renderSettingsTab();
    }
    function renderSettingsTab(){
        const nav=document.getElementById('setNav'); if(!nav) return;
        const items=[ ['pendingList','👤 승인 관리'], ['parentLink','👨‍👩‍👧 학부모 연결'], ['sec','🔒 보안·암호'], ['ai','🤖 AI 설정'], ['backup','💾 백업/복원'], ['snippetMgmt','💬 문구 관리'] ];
        nav.innerHTML = items.filter(([k])=> !['pendingList','sec','parentLink'].includes(k) || myRole()==='admin')
            .map(([k,l])=>`<button class="${settingsTab===k?'on':''}" onclick="settingsTab='${k}';renderSettingsTab()">${l}</button>`).join('');
        const body=document.getElementById('setBody');
        if(settingsTab==='pendingList') return renderUsersTable();
        if(settingsTab==='sec') return renderSecTab();
        if(settingsTab==='ai') return renderAiTab();
        if(settingsTab==='backup') return renderBackupTab();
        if(settingsTab==='snippetMgmt') return renderSnippetTab();
        if(settingsTab==='parentLink') return renderParentLinkTab();
    }
    function renderUsersTable(){
        const body=document.getElementById('setBody'); if(!body) return;
        const entries = Object.entries(usersMap||{});
        body.innerHTML = `<p class="hint" style="margin-bottom:12px;">가입 계정을 승인하면 데이터에 접근할 수 있습니다. 첫 번째 가입 계정은 자동으로 관리자가 됩니다.</p>
        <table><thead><tr><th>이름</th><th>이메일</th><th>역할</th><th>상태</th><th>관리</th></tr></thead><tbody>
        ${entries.length ? entries.map(([uid,u])=>`<tr>
            <td>${esc(u.name||'-')}</td><td style="font-size:12px;">${esc(u.email||'')}</td>
            <td>${roleLabel(u)}</td>
            <td>${u.approved?'<span class="user-row-ok">승인</span>':'<span class="user-row-bad">대기</span>'}</td>
            <td>
                ${!u.approved ? (u.requestedRole==='parent'
                    ? `<span style="font-size:11px; color:#666;">코드 <b>${esc(u.childCode||'-')}</b></span><select id="sel_${uid}" style="padding:5px; border:1px solid #ddd; border-radius:5px; font-size:12px;">${Object.keys(db.students).map(sid=>`<option value="${sid}" ${db.students[sid].parentCode===u.childCode?'selected':''}>${esc(getStudentName(sid,true))}</option>`).join('')}</select><button class="btn-green btn-sm" onclick="approveParent('${uid}')">승인</button>`
                    : `<button class="btn-green btn-sm" onclick="approveUser('${uid}')">승인</button>`) : ''}
                ${u.role!=='parent' && u.requestedRole!=='parent' ?`<button class="btn-gray btn-sm" onclick="setRole('${uid}','${u.role==='admin'?'teacher':'admin'}')">${u.role==='admin'?'교사로':'관리자로'}</button>`:''}
                ${uid!==me.uid?`<button class="btn-red btn-sm" onclick="removeUser('${uid}')">삭제</button>`:'<span style="font-size:11px; color:#999;">(본인)</span>'}
            </td></tr>`).join('') : '<tr class="empty-row"><td colspan="5">등록된 계정이 없습니다.</td></tr>'}
        </tbody></table>`;
    }
    function approveUser(uid){ DB.onUserChange(uid, {approved:true, role:'teacher'}).then(()=>{ toast('승인되었습니다'); renderUsersTable(); }); }
    function setRole(uid, role){ DB.onUserChange(uid, {role}).then(()=>{ toast('역할이 변경되었습니다'); renderUsersTable(); }); }
    function removeUser(uid){ if(confirm('이 계정을 삭제할까요?')){ DB.onUserChange(uid, null).then(()=>renderUsersTable()); } }
    function renderSecTab(){
        const body=document.getElementById('setBody'); if(!body) return;
        const enabled = db.crypto && db.crypto.enabled;
        const plainCount = Object.values(db.students).filter(s=>s.namePlain).length;
        body.innerHTML = `
        <div class="banner ${enabled?'info':'warn'}" style="margin-bottom:14px;">
            <div><b>${enabled?'🔒 실명 암호화 사용 중':'⚠️ 실명 암호화 미설정'}</b>
            ${enabled ? `학생 실명은 AES-256으로 암호화되어 저장됩니다.${plainCount?` <b>미암호화 실명 ${plainCount}건</b>이 남아 있습니다.`:''}` : '설정 즉시 모든 학생 실명이 암호화되고, 화면 표시는 마스킹(예: 김**)로 전환됩니다.'}
            <br>* 암호를 잊으면 실명 복구가 불가능합니다. 반드시 안전한 곳에 기록해 두세요.</div>
            ${!enabled?'<button class="btn-orange bn-btn btn-sm" onclick="openCryptoSetup()">암호화 시작</button>':plainCount?'<button class="btn-orange bn-btn btn-sm" onclick="reEncryptPlain()">잔여 실명 암호화</button>':''}
        </div>
        ${enabled?`<div class="mo-row"><button class="btn-gray btn-sm" onclick="openCryptoChange()">암호 변경 (전체 재암호화)</button> <button class="btn-gray btn-sm" onclick="sessionKey=null;nameCache={};closeModal();toast('잠겼습니다');">지금 잠그기</button></div>`:''}
        <div class="card" style="box-shadow:none; border:1px solid #eee; margin-bottom:12px;">
            <div style="font-weight:700; margin-bottom:8px;">🛡 Firebase 보안 규칙 (필수)</div>
            <p class="hint" style="margin-bottom:8px;">1) Firebase 콘솔 → Authentication → 로그인 방법 → <b>이메일/비밀번호 사용 설정</b><br>2) Realtime Database → 규칙 탭 → 아래 내용 붙여넣고 <b>게시</b><br>3) 이후 가입 계정은 '승인 관리'에서 승인해야 입장 가능합니다.</p>
            <textarea id="rulesBox2" class="rules-box" readonly>${esc(RULES)}</textarea>
            <button class="btn-blue btn-sm" style="margin-top:8px;" onclick="copyRules2()">📋 규칙 복사</button>
        </div>`;
    }
    function copyRules2(){ const t=document.getElementById('rulesBox2'); t.select(); document.execCommand('copy'); if(navigator.clipboard) navigator.clipboard.writeText(RULES).catch(()=>{}); toast('규칙이 복사되었습니다'); }
    function openCryptoSetup(){
        openModal(`<h3>🔒 데이터 잠금 암호 설정</h3>
        <p class="hint">설정하면 모든 학생 실명이 이 암호로 암호화됩니다. <b>암호 분실 시 실명 복구가 불가능</b>합니다.</p>
        <div class="mo-row"><label>암호 (8자 이상 권장)</label><input type="password" id="cp1"></div>
        <div class="mo-row"><label>암호 확인</label><input type="password" id="cp2"></div>
        <div class="mo-foot"><button class="btn-gray" onclick="openSettings('sec')">취소</button><button class="btn-primary" onclick="setCryptoPass()">설정</button></div>`);
    }
    async function setCryptoPass(){
        const p1=document.getElementById('cp1').value, p2=document.getElementById('cp2').value;
        if(p1.length<8) return toast('8자 이상 권장합니다', true);
        if(p1!==p2) return toast('암호가 일치하지 않습니다', true);
        const salt = CRYPTO.b64(crypto.getRandomValues(new Uint8Array(16)));
        const key = await CRYPTO.deriveKey(p1, salt);
        for(const sid of Object.keys(db.students)){
            const s = db.students[sid];
            const plain = s.namePlain || (s.nameEnc ? await CRYPTO.decrypt(s.nameEnc, sessionKey) : null) || nameCache[sid];
            if(plain){ s.nameEnc = await CRYPTO.encrypt(plain, key); delete s.namePlain; s.maskName = maskName(plain); nameCache[sid]=plain; }
        }
        db.crypto = { enabled:true, salt, check: await CRYPTO.encrypt('solbit-ok', key) };
        sessionKey = key;
        await saveAll();
        openSettings('sec'); renderStudentList(); renderCalendar();
        const chip=document.getElementById('lockChip'); chip.className='lock-chip open'; chip.innerText='🔓 실명 열람 가능';
        toast('실명 암호화가 설정되었습니다');
    }
    async function reEncryptPlain(){
        if(!sessionKey) return openUnlockModal();
        for(const sid of Object.keys(db.students)){
            const s = db.students[sid];
            if(s.namePlain){ s.nameEnc = await CRYPTO.encrypt(s.namePlain, sessionKey); delete s.namePlain; }
        }
        await saveAll(); renderSecTab(); toast('잔여 실명이 암호화되었습니다');
    }
    function openCryptoChange(){
        if(!sessionKey) return openUnlockModal();
        openModal(`<h3>🔑 암호 변경</h3><p class="hint">기존 암호로 열린 상태에서 새 암호로 전체 실명을 다시 암호화합니다.</p>
        <div class="mo-row"><label>새 암호</label><input type="password" id="cn1"></div>
        <div class="mo-row"><label>새 암호 확인</label><input type="password" id="cn2"></div>
        <div class="mo-foot"><button class="btn-gray" onclick="openSettings('sec')">취소</button><button class="btn-primary" onclick="doCryptoChange()">변경</button></div>`);
    }
    async function doCryptoChange(){
        const p1=document.getElementById('cn1').value, p2=document.getElementById('cn2').value;
        if(p1.length<8) return toast('8자 이상 권장합니다', true);
        if(p1!==p2) return toast('암호가 일치하지 않습니다', true);
        const salt = CRYPTO.b64(crypto.getRandomValues(new Uint8Array(16)));
        const key = await CRYPTO.deriveKey(p1, salt);
        for(const sid of Object.keys(db.students)){
            const s = db.students[sid];
            let plain = nameCache[sid];
            if(!plain && s.nameEnc) plain = await CRYPTO.decrypt(s.nameEnc, sessionKey);
            if(plain){ s.nameEnc = await CRYPTO.encrypt(plain, key); s.maskName = maskName(plain); }
        }
        db.crypto.salt = salt; db.crypto.check = await CRYPTO.encrypt('solbit-ok', key);
        sessionKey = key;
        await saveAll(); openSettings('sec'); toast('암호가 변경되었습니다');
    }
    function renderAiTab(){
        const body=document.getElementById('setBody'); if(!body) return;
        body.innerHTML = `
        <p class="hint" style="margin-bottom:12px;">'AI 소견 보강'과 AI 종합리포트는 Google Gemini API(무료 사용량 있음)를 사용합니다. 키는 <b>이 브라우저에만</b> 저장되며 서버로 전송되지 않습니다(호출 시 Gemini API로 직접 전송됨).</p>
        <div class="mo-row"><label>Gemini API 키 (aistudio.google.com에서 무료 발급)</label><input type="password" id="aiKey" value="${esc(localStorage.getItem('solbit_gemini_key')||'')}" placeholder="AIza..."></div>
        <div class="mo-row"><label>모델</label><input type="text" id="aiModel" value="${esc(localStorage.getItem('solbit_gemini_model')||'gemini-2.0-flash')}"></div>
        <div class="mo-foot"><button class="btn-gray" onclick="testAi()">연결 테스트</button><button class="btn-primary" onclick="saveAiKey()">저장</button></div>`;
    }
    function saveAiKey(){
        localStorage.setItem('solbit_gemini_key', document.getElementById('aiKey').value.trim());
        localStorage.setItem('solbit_gemini_model', document.getElementById('aiModel').value.trim()||'gemini-2.0-flash');
        toast('저장되었습니다');
    }
    async function testAi(){
        const key=document.getElementById('aiKey').value.trim(); if(!key) return toast('키를 입력하세요', true);
        try { const r = await callGemini('한국어로 "연결 성공"만 출력하세요.', key); toast('AI 연결 성공: '+r.slice(0,40)); }
        catch(e){ toast('연결 실패: '+e.message, true); }
    }
    function renderBackupTab(){
        const body=document.getElementById('setBody'); if(!body) return;
        body.innerHTML = `
        <p class="hint" style="margin-bottom:12px;">전체 데이터(학생·일정·문구)를 JSON 파일로 내려받거나 복원합니다. 실명은 암호화 상태 그대로 저장되므로, 복원 시에도 <b>데이터 잠금 암호</b>가 필요합니다.</p>
        <div class="mo-row" style="display:flex; gap:10px;">
            <button class="btn-primary" onclick="exportBackup()">💾 백업 내려받기</button>
            <label class="btn-blue btn-sm" style="display:inline-flex; align-items:center; cursor:pointer;">📂 백업 복원<input type="file" id="importFile" accept=".json" style="display:none;" onchange="importBackup(this)"></label>
        </div>
        <p class="hint">* 복원은 현재 데이터를 백업 파일 내용으로 <b>덮어씁니다</b>. 신중히 실행하세요.</p>`;
    }
    function exportBackup(){
        const blob = new Blob([JSON.stringify(db, null, 1)], {type:'application/json'});
        const a = document.createElement('a');
        a.href = URL.createObjectURL(blob);
        a.download = `solbit_backup_${todayStr()}.json`;
        a.click(); URL.revokeObjectURL(a.href);
    }
    function importBackup(input){
        const f = input.files[0]; if(!f) return;
        const reader = new FileReader();
        reader.onload = ()=>{
            try {
                const data = JSON.parse(reader.result);
                if(!data || !data.students) throw new Error('형식이 올바르지 않습니다');
                if(confirm('현재 데이터를 백업 파일로 덮어쓸까요?')){
                    db = data; saveAll(); closeModal(); toast('복원이 완료되었습니다');
                }
            } catch(e){ toast('복원 실패: '+e.message, true); }
        };
        reader.readAsText(f);
    }
    function renderSnippetTab(){
        const body=document.getElementById('setBody'); if(!body) return;
        if(!db.snippets || !db.snippets.length){ db.snippets = defaultSnippets(); saveSnippets(); }
        const items = db.snippets;
        body.innerHTML = `<p class="hint" style="margin-bottom:12px;">상담/수업 일지 입력창에서 드롭다운으로 빠르게 삽입되는 문구입니다. 모든 교사 계정에 공유됩니다.</p>
        <div class="input-row">
            <select id="snCat" style="width:130px;"><option>수업관찰</option><option>학부모상담</option><option>과제부여</option><option>기타</option></select>
            <input type="text" id="snText" placeholder="추가할 문구…" style="flex:1; min-width:200px;">
            <button class="btn-green btn-sm" onclick="addSnippet()">추가</button>
        </div>
        <table><thead><tr><th>분류</th><th>문구</th><th>관리</th></tr></thead><tbody>
        ${items.map(s=>`<tr><td style="font-size:12px;">${esc(s.cat)}</td><td style="text-align:left; font-size:13px;">${esc(s.text)}</td><td><button class="btn-red btn-sm" onclick="delSnippet(${s.id})">삭제</button></td></tr>`).join('')}
        </tbody></table>`;
    }
    function addSnippet(){
        const cat=document.getElementById('snCat').value, text=document.getElementById('snText').value.trim();
        if(!text) return toast('문구를 입력하세요', true);
        db.snippets.push({id:Date.now(), cat, text}); saveSnippets(); renderSnippetTab(); renderSnippetSelect();
    }
    function delSnippet(id){ db.snippets=db.snippets.filter(s=>s.id!==id); saveSnippets(); renderSnippetTab(); renderSnippetSelect(); }

    // ==================== 데모 시드 진입 ====================
    // (AUTH.onChange 등록은 위에서 완료 — 로더는 화면 전환 시 자동 숨김)
</script>
<script type="module" src="https://static.cloudflareinsights.com/beacon.min.js/v31edd6df95cf4e85bb4c19e7a9bdbcba1788362987495" integrity="sha512-iIg7k2xntmwu6/uSb5tpc/hySgZc4eoL31yB29W6tJFo2akwjPWcEqnCEdJvGexCL0KEQwVYv5BlowfhVz26hg==" data-cf-beacon='{"version":"2024.11.0","token":"4edd5f8ec12a48cfa682ab8261b80a79","spa":2}' crossorigin="anonymous"></script>
</body>
</html>
