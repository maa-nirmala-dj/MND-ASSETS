<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#f8fafc">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="default">
    <title>MND | Premium Logistics Tracker</title>

    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;500;600;700;800;900&family=Space+Grotesk:wght@500;700;900&family=Cinzel:wght@700;900&family=Dancing+Script:wght@700&family=Orbitron:wght@500;700;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">

    <!-- Firebase SDKs -->
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/10.10.0/firebase-database-compat.js"></script>
    
    <!-- HTML2Canvas & jsPDF for Receipt Generation -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>

    <!-- Google Translate API -->
    <script type="text/javascript" src="https://translate.google.com/translate_a/element.js?cb=googleTranslateElementInit"></script>

    <style>
        /* =========================================================================
           ULTRA-PREMIUM LIGHT THEME (DYNAMIC COLORS)
           ========================================================================= */
        :root {
            --primary: #4f46e5; --primary-glow: rgba(79, 70, 229, 0.15);
            --accent: #0284c7; --accent-glow: rgba(2, 132, 199, 0.15);
            --danger: #ef4444; --danger-glow: rgba(239, 68, 68, 0.15);
            --gold: #b45309; --gold-glow: rgba(180, 83, 9, 0.15);
            --success: #059669;
            --purple-accent: #6366f1;
            
            /* Professional Light Palette */
            --bg-base: #f1f5f9; 
            --bg-nav: rgba(255, 255, 255, 0.95); 
            --bg-card: rgba(255, 255, 255, 0.85);
            --text-main: #0f172a; 
            --text-muted: #64748b; 
            --border-light: rgba(0, 0, 0, 0.08);
            
            --chat-admin: #3b82f6; --chat-client: #f1f5f9;
            
            --nav-height: 70px; --bottom-nav-height: 80px;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Outfit', sans-serif; -webkit-tap-highlight-color: transparent; outline: none; }
        
        html { background: var(--bg-base); }
        body { 
            width: 100%; min-height: 100vh; margin: 0; padding: 0; 
            background: transparent; color: var(--text-main); 
            overflow-x: hidden;
            padding-top: calc(var(--nav-height) + 15px);
            padding-bottom: calc(var(--bottom-nav-height) + 15px);
        }

        /* Subtle Light Mesh Background */
        .bg-mesh {
            position: fixed; inset: 0; z-index: -2; opacity: 0.6;
            background-image: radial-gradient(circle at 10% 20%, var(--primary-glow) 0%, transparent 45%), radial-gradient(circle at 90% 80%, var(--accent-glow) 0%, transparent 45%);
            animation: pulseBg 12s infinite alternate ease-in-out;
        }
        .grid-overlay {
            position: fixed; inset: 0; z-index: -1; opacity: 0.05;
            background-image: linear-gradient(var(--text-main) 1px, transparent 1px), linear-gradient(90deg, var(--text-main) 1px, transparent 1px);
            background-size: 35px 35px; mask-image: radial-gradient(circle at center, black 40%, transparent 100%);
            -webkit-mask-image: radial-gradient(circle at center, black 40%, transparent 100%);
        }
        @keyframes pulseBg { 0% { transform: scale(1); opacity: 0.4; } 100% { transform: scale(1.1); opacity: 0.8; } }

        /* Navigation */
        .top-nav {
            position: fixed; top: 0; left: 0; width: 100%; height: var(--nav-height);
            background: var(--bg-nav); backdrop-filter: blur(25px); -webkit-backdrop-filter: blur(25px);
            border-bottom: 1px solid var(--border-light); z-index: 1000;
            display: flex; justify-content: space-between; align-items: center; padding: 0 20px;
            transform: translateY(-100%); transition: transform 0.5s cubic-bezier(0.4, 0, 0.2, 1); 
            box-shadow: 0 4px 20px rgba(0,0,0,0.05);
        }
        .top-nav.visible { transform: translateY(0); }
        .brand-logo { display: flex; align-items: center; gap: 10px; font-family: 'Space Grotesk', sans-serif; font-size: 20px; font-weight: 900; color: var(--gold); letter-spacing: 0.5px; }
        .brand-logo i { color: var(--gold); font-size: 24px; filter: drop-shadow(0 0 8px var(--gold-glow)); }
        
        .nav-right { display: flex; align-items: center; gap: 10px; }
        .nav-btn { width: 40px; height: 40px; border-radius: 12px; background: #fff; border: 1px solid var(--border-light); color: var(--text-main); font-size: 16px; display: flex; justify-content: center; align-items: center; cursor: pointer; transition: 0.3s; box-shadow: 0 2px 10px rgba(0,0,0,0.05); }
        .nav-btn:hover { background: var(--bg-base); transform: translateY(-2px); border-color: var(--primary); color: var(--primary); }

        .bottom-nav {
            position: fixed; bottom: 0; left: 0; width: 100%; height: var(--bottom-nav-height);
            background: var(--bg-nav); backdrop-filter: blur(25px); -webkit-backdrop-filter: blur(25px);
            border-top: 1px solid var(--border-light); z-index: 1000;
            display: flex; justify-content: space-around; align-items: center; padding: 0 5px; padding-bottom: env(safe-area-inset-bottom);
            transform: translateY(100%); transition: transform 0.5s cubic-bezier(0.4, 0, 0.2, 1);
            box-shadow: 0 -4px 20px rgba(0,0,0,0.05);
        }
        .bottom-nav.visible { transform: translateY(0); }
        .b-nav-item { display: flex; flex-direction: column; align-items: center; gap: 6px; color: var(--text-muted); cursor: pointer; transition: 0.3s; width: 65px; position: relative; }
        .b-nav-item i { font-size: 22px; transition: 0.3s; z-index: 2; }
        .b-nav-item span { font-size: 10px; font-weight: 700; z-index: 2; text-transform: uppercase; letter-spacing: 0.5px; }
        .b-nav-item.active { color: var(--primary); }
        .b-nav-item.active i { transform: translateY(-4px); filter: drop-shadow(0 0 8px var(--primary-glow)); }

        /* Views Structure */
        .view-wrapper { width: 100%; display: block; z-index: 1; }
        .view-container { 
            display: none; width: 100%; max-width: 650px; margin: 0 auto;
            padding: 0 15px; animation: fadeSlideUp 0.4s cubic-bezier(0.2, 0.8, 0.2, 1) forwards;
        }
        .active-view { display: block !important; }
        #view-auth.active-view { display: flex !important; flex-direction: column; justify-content: center; min-height: calc(100vh - var(--nav-height) - var(--bottom-nav-height) - 30px); }
        @keyframes fadeSlideUp { from { opacity: 0; transform: translateY(25px); } to { opacity: 1; transform: translateY(0); } }
        .flex-center-wrapper { display: flex; flex-direction: column; justify-content: center; width: 100%; }
        
        /* Premium Light Forms & Cards */
        .glass-card { width: 100%; background: var(--bg-card); backdrop-filter: blur(25px); -webkit-backdrop-filter: blur(25px); border: 1px solid var(--border-light); border-radius: 24px; padding: 25px; box-shadow: 0 15px 35px rgba(0, 0, 0, 0.06), 0 5px 15px rgba(0,0,0,0.03); margin-bottom: 20px; flex-shrink: 0; position: relative; overflow: hidden; }
        .card-header { display: flex; justify-content: space-between; align-items: center; border-bottom: 1px dashed var(--border-light); padding-bottom: 12px; margin-bottom: 15px; }
        .section-title { font-family: 'Space Grotesk'; font-size: 17px; color: var(--text-main); font-weight: 800; display: flex; align-items: center; gap: 10px; margin: 0; }

        .input-group { position: relative; margin-bottom: 15px; width: 100%; }
        .input-icon { position: absolute; left: 18px; top: 50%; transform: translateY(-50%); color: var(--text-muted); font-size: 18px; }
        .mn-input {
            width: 100%; padding: 16px 16px 16px 50px; background: #fff;
            border: 1px solid #cbd5e1; border-radius: 14px; color: var(--text-main); font-size: 15px; font-weight: 500; transition: 0.3s; box-shadow: inset 0 2px 4px rgba(0,0,0,0.02);
        }
        .mn-input:focus { border-color: var(--primary); box-shadow: 0 0 0 3px rgba(79, 70, 229, 0.2); }
        .mn-input::placeholder { color: #94a3b8; font-weight: 400; }
        
        .pin-input { font-family: 'Orbitron'; letter-spacing: 12px; font-size: 20px; font-weight: 700; text-align: center; padding-left: 20px; color: var(--primary); }
        .pin-input + .input-icon { display: none; }
        .pin-input::placeholder { letter-spacing: 3px; font-family: 'Outfit'; font-weight: 400; font-size: 14px; color: #94a3b8; }

        /* Buttons */
        .mn-btn {
            width: 100%; padding: 16px; border-radius: 14px; font-weight: 800; font-size: 13px; cursor: pointer;
            display: flex; justify-content: center; align-items: center; gap: 10px; border: none;
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1); text-transform: uppercase; letter-spacing: 1px; flex-shrink: 0;
        }
        .mn-btn:active { transform: scale(0.95); }
        .btn-primary { background: linear-gradient(135deg, var(--primary) 0%, #3730a3 100%); color: #fff; box-shadow: 0 10px 20px var(--primary-glow); }
        .btn-accent { background: linear-gradient(135deg, var(--accent) 0%, #0369a1 100%); color: #fff; box-shadow: 0 10px 20px var(--accent-glow); }
        .btn-danger { background: linear-gradient(135deg, var(--danger) 0%, #b91c1c 100%); color: #fff; box-shadow: 0 10px 20px var(--danger-glow); }
        .btn-gold { background: linear-gradient(135deg, var(--gold) 0%, #9a3412 100%); color: #fff; box-shadow: 0 10px 20px var(--gold-glow); }
        .btn-success { background: linear-gradient(135deg, var(--success) 0%, #047857 100%); color: #fff; box-shadow: 0 10px 20px rgba(5, 150, 105, 0.3); }
        .btn-outline { background: #fff; border: 1px solid var(--border-light); color: var(--text-main); box-shadow: 0 4px 10px rgba(0,0,0,0.05); }
        .btn-outline:hover { background: #f1f5f9; border-color: #cbd5e1; }
        .btn-cyan-outline { background: transparent; border: 1px solid var(--accent); color: var(--accent); }
        .btn-purple-solid { background: var(--primary); color: #fff; border: none; box-shadow: 0 5px 15px var(--primary-glow); }

        /* Asset Grid */
        .asset-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 15px; width: 100%; margin-top: 15px; }
        .asset-card { background: #fff; border: 1px solid var(--border-light); border-radius: 18px; padding: 15px; display: flex; flex-direction: column; align-items: center; gap: 10px; text-align: center; box-shadow: 0 5px 15px rgba(0,0,0,0.05); transition: 0.3s; }
        .asset-card.hidden { display: none !important; }
        .asset-icon-box { width: 50px; height: 50px; border-radius: 14px; display: flex; justify-content: center; align-items: center; font-size: 24px; margin-bottom: 5px; box-shadow: 0 5px 15px rgba(0,0,0,0.1); }
        .asset-jug { background: linear-gradient(135deg, #3b82f6 0%, #1d4ed8 100%); color: #fff; }
        .asset-drum { background: linear-gradient(135deg, #10b981 0%, #047857 100%); color: #fff; }
        .asset-dj { background: linear-gradient(135deg, #f59e0b 0%, #b45309 100%); color: #fff; }
        .asset-mic { background: linear-gradient(135deg, #ec4899 0%, #be185d 100%); color: #fff; }
        .asset-chair { background: linear-gradient(135deg, #8b5cf6 0%, #5b21b6 100%); color: #fff; }
        .asset-table { background: linear-gradient(135deg, #ef4444 0%, #b91c1c 100%); color: #fff; }
        .asset-name { font-family: 'Space Grotesk'; font-size: 13px; color: var(--text-main); font-weight: 700; letter-spacing: 0.5px; }
        .asset-qty-display { font-family: 'Orbitron'; font-size: 22px; color: var(--primary); font-weight: 900; }
        .qty-controls { display: flex; align-items: center; gap: 10px; width: 100%; justify-content: center; margin-top: 5px; }
        .qty-btn { width: 32px; height: 32px; border-radius: 8px; background: #f1f5f9; border: 1px solid #cbd5e1; color: var(--text-main); font-size: 14px; cursor: pointer; transition: 0.2s; }
        .qty-btn:active { background: #e2e8f0; transform: scale(0.9); }
        .qty-input { width: 55px; background: #fff; border: 1px solid #cbd5e1; color: var(--text-main); text-align: center; border-radius: 8px; padding: 6px; font-family: 'Orbitron'; font-weight: bold; font-size: 14px; }

        /* Clean List View */
        .simple-asset-list { display: flex; flex-direction: column; gap: 10px; width: 100%; }
        .simple-asset-item { display: flex; justify-content: space-between; align-items: center; padding: 18px 20px; background: #fff; border: 1px solid var(--border-light); border-radius: 16px; box-shadow: 0 4px 10px rgba(0,0,0,0.04); }
        .simple-asset-item span:first-child { font-weight: 700; color: var(--text-main); font-size: 15px; display: flex; align-items: center; gap: 12px; }
        .simple-asset-item span:last-child { font-family: 'Orbitron'; font-weight: bold; color: var(--primary); font-size: 18px; }

        /* Directory List */
        .data-list { display: flex; flex-direction: column; gap: 12px; max-height: 500px; overflow-y: auto; padding-right: 5px; }
        .dir-item { background: #fff; border: 1px solid var(--border-light); border-left: 5px solid var(--accent); border-radius: 16px; padding: 18px; display: flex; justify-content: space-between; align-items: center; transition: 0.3s; cursor: pointer; box-shadow: 0 5px 15px rgba(0,0,0,0.05); }
        .dir-item:hover { transform: translateY(-3px); box-shadow: 0 8px 25px rgba(0,0,0,0.08); border-left-color: var(--primary); }
        .dir-info h4 { font-family: 'Space Grotesk'; font-size: 16px; color: var(--text-main); margin-bottom: 4px; font-weight: 800; }
        .dir-info p { font-size: 12px; color: var(--text-muted); font-weight: 500; }
        .dir-actions { display: flex; gap: 10px; align-items: center; }
        .action-icon-btn { background: #f1f5f9; border: 1px solid #cbd5e1; color: var(--text-muted); width: 36px; height: 36px; border-radius: 10px; display: flex; justify-content: center; align-items: center; font-size: 14px; cursor: pointer; transition: 0.3s; }
        .action-icon-btn:hover { background: #fff; color: var(--primary); border-color: var(--primary); transform: scale(1.05); }
        .action-icon-btn.trash { color: var(--danger); }
        .action-icon-btn.trash:hover { border-color: var(--danger); background: rgba(239,68,68,0.1); }

        /* Light Chat System */
        .chat-container { display: flex; flex-direction: column; width: 100%; height: 450px; background: #f8fafc; border: 1px solid var(--border-light); border-radius: 20px; overflow: hidden; margin-top: 15px; box-shadow: inset 0 2px 10px rgba(0,0,0,0.02); }
        .chat-messages { flex-grow: 1; padding: 15px; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; }
        .chat-bubble { max-width: 80%; padding: 12px 16px; border-radius: 16px; font-size: 14px; line-height: 1.5; position: relative; word-wrap: break-word; box-shadow: 0 2px 5px rgba(0,0,0,0.05); font-weight: 500; }
        .chat-bubble.admin { align-self: flex-end; background: var(--chat-admin); color: #fff; border-bottom-right-radius: 4px; }
        .chat-bubble.client { align-self: flex-start; background: var(--chat-client); color: var(--text-main); border: 1px solid #e2e8f0; border-bottom-left-radius: 4px; }
        .chat-media-preview { max-width: 100%; border-radius: 10px; margin-bottom: 8px; border: 1px solid rgba(0,0,0,0.1); }
        .chat-doc-preview { display: flex; align-items: center; gap: 10px; background: rgba(0,0,0,0.05); padding: 12px; border-radius: 10px; margin-bottom: 8px; color: var(--text-main); font-size: 13px; font-weight: 600; border: 1px solid rgba(0,0,0,0.1); }
        .chat-time { display: block; font-size: 10px; color: inherit; opacity: 0.7; text-align: right; margin-top: 6px; }
        .chat-del-btn { position: absolute; top: -10px; right: -10px; background: #fff; border: 1px solid var(--danger); color: var(--danger); width: 26px; height: 26px; border-radius: 50%; display: flex; justify-content: center; align-items: center; font-size: 12px; cursor: pointer; opacity: 0; transition: 0.3s; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .chat-bubble:hover .chat-del-btn { opacity: 1; }
        .chat-input-area { display: flex; align-items: center; gap: 10px; padding: 15px; background: #fff; border-top: 1px solid #e2e8f0; }
        .chat-attach-btn { background: #f1f5f9; border: 1px solid #cbd5e1; border-radius: 50%; width: 45px; height: 45px; display: flex; justify-content: center; align-items: center; color: var(--text-muted); font-size: 18px; cursor: pointer; transition: 0.3s; }
        .chat-attach-btn:hover { color: var(--primary); border-color: var(--primary); }
        .chat-text-input { flex-grow: 1; background: #f1f5f9; border: 1px solid #cbd5e1; border-radius: 25px; padding: 14px 20px; color: var(--text-main); outline: none; font-size: 14px; transition: 0.3s; }
        .chat-text-input:focus { border-color: var(--primary); background: #fff; }
        .chat-send-btn { background: var(--primary); color: #fff; border: none; border-radius: 50%; width: 45px; height: 45px; display: flex; justify-content: center; align-items: center; cursor: pointer; box-shadow: 0 4px 10px var(--primary-glow); transition: 0.3s; }
        .chat-send-btn:active { transform: scale(0.9); }

        /* Toasts & Modals */
        #toast-container { position: fixed; top: 90px; left: 50%; transform: translateX(-50%); z-index: 99999; display: flex; flex-direction: column; gap: 10px; pointer-events: none; width: 100%; align-items: center; }
        .toast {
            background: #fff; border-left: 5px solid var(--primary);
            color: var(--text-main); padding: 16px 20px; border-radius: 12px; font-weight: 700; font-size: 14px;
            box-shadow: 0 15px 40px rgba(0,0,0,0.15); display: flex; align-items: center; gap: 12px; max-width: 90%;
            animation: dropDown 0.4s cubic-bezier(0.2, 0.8, 0.2, 1) forwards, fadeUp 0.4s 4s forwards;
        }
        .toast.error { border-color: var(--danger); } .toast.success { border-color: var(--success); }
        @keyframes dropDown { from { opacity: 0; transform: translateY(-40px); } to { opacity: 1; transform: translateY(0); } }
        @keyframes fadeUp { from { opacity: 1; transform: translateY(0); } to { opacity: 0; transform: translateY(-40px); } }

        .modal-overlay { display: none; position: fixed; inset: 0; background: rgba(15, 23, 42, 0.8); backdrop-filter: blur(8px); z-index: 99999; justify-content: center; align-items: center; padding: 20px; }
        .modal-overlay.active { display: flex; animation: fadeSlideUp 0.3s ease forwards; }
        .modal-content { background: var(--bg-card); border: 1px solid var(--border-light); border-radius: 24px; width: 100%; max-width: 420px; padding: 30px; box-shadow: 0 30px 60px rgba(0,0,0,0.2); max-height: 90vh; overflow-y: auto; position: relative; }
        
        .action-sheet-btn { width: 100%; background: #fff; border: 1px solid #cbd5e1; color: var(--text-main); padding: 16px 20px; border-radius: 14px; margin-bottom: 12px; font-size: 15px; font-weight: 700; display: flex; align-items: center; gap: 15px; cursor: pointer; transition: 0.3s; box-shadow: 0 4px 10px rgba(0,0,0,0.03); }
        .action-sheet-btn:hover { background: #f8fafc; border-color: var(--primary); color: var(--primary); transform: translateX(5px); }

        /* Social Badges */
        .social-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px; margin-top: 15px; }
        .social-btn { background: #fff; border: 1px solid #cbd5e1; color: var(--text-main); padding: 12px; border-radius: 12px; display: flex; align-items: center; justify-content: center; font-size: 20px; text-decoration: none; transition: 0.3s; box-shadow: 0 4px 10px rgba(0,0,0,0.03); }
        .social-btn:hover { background: #f8fafc; transform: translateY(-3px); }
        .s-ig { color: #E1306C; } .s-fb { color: #1877F2; } .s-wa { color: #25D366; } .s-x { color: #000; } .s-yt { color: #FF0000; } .s-in { color: #0A66C2; }

        /* Google Translate Hacks */
        .goog-te-banner-frame { display: none !important; }
        body { top: 0px !important; }

        /* =========================================================================
           ADVANCED A4 PRINT & PDF GENERATION ENGINE (PERFECT MATCH)
           ========================================================================= */
        #print-area {
            display: none; background: #fff; color: #111827; width: 210mm; min-height: 297mm; max-height: 297mm;
            padding: 8mm; box-sizing: border-box; font-family: 'Outfit', sans-serif; position: relative; overflow: hidden;
        }
        
        .print-border-wrapper { border: 2px solid #111827; border-radius: 16px; height: 100%; width: 100%; padding: 8mm; display: flex; flex-direction: column; position: relative; box-sizing: border-box; }
        .print-corner-tl { position: absolute; top: 0; left: 0; width: 40mm; height: 40mm; background: linear-gradient(135deg, #1e3a8a 0%, #0f172a 100%); border-radius: 14px 0 100% 0; z-index: 0; }
        .print-corner-tl::after { content:''; position:absolute; top:5px; left:5px; width:35mm; height:35mm; border-right: 2px solid #D4AF37; border-bottom: 2px solid #D4AF37; border-radius: 10px 0 100% 0; }
        .print-corner-br { position: absolute; bottom: 0; left: 0; width: 100%; height: 25mm; background: #1e3a8a; border-radius: 0 0 14px 14px; z-index: 0; clip-path: polygon(0 100%, 100% 100%, 100% 0, 40% 100%); }
        .print-corner-br::before { content:''; position: absolute; bottom: 0; left: 0; width: 100%; height: 30mm; background: #0f172a; z-index: -1; clip-path: polygon(0 100%, 100% 100%, 100% 0, 30% 100%); }

        .print-header { display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 4mm; position: relative; z-index: 2; }
        .print-logo { width: 40mm; background: #fff; padding: 5px; border-radius: 8px; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
        .print-logo img { width: 100%; height: auto; object-fit: contain; }
        .print-company-info { text-align: right; }
        .print-company-info h1 { font-family: 'Cinzel', serif; color: #0f172a; font-size: 20px; margin: 0 0 3px 0; font-weight: 900; letter-spacing: 0.5px; }
        .print-company-info p { font-size: 9px; margin: 2px 0; color: #374151; font-weight: 500; }
        .print-divider-thick { width: 100%; height: 2px; background: #111827; margin-bottom: 5mm; position: relative; z-index: 2; }

        .print-meta-bar { background: #f3f4f6; border: 1px solid #e5e7eb; border-radius: 8px; padding: 8px 15px; display: flex; justify-content: space-between; font-size: 10px; color: #374151; margin-bottom: 6mm; position: relative; z-index: 2; }
        .print-meta-bar strong { color: #111827; font-family: 'Space Grotesk'; font-size: 11px; }

        .print-section-wrap { position: relative; margin-bottom: 5mm; z-index: 2; }
        .print-section-line { position: absolute; top: 50%; left: 0; width: 100%; height: 2px; background: #e5e7eb; z-index: 1; }
        .print-section-title { position: relative; z-index: 2; background: #1e3a8a; color: #fff; padding: 6px 15px; border-radius: 20px; display: inline-block; font-family: 'Space Grotesk'; font-weight: bold; font-size: 11px; letter-spacing: 0.5px; }
        
        .print-client-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 4mm 10mm; margin-bottom: 6mm; padding: 0 5mm; position: relative; z-index: 2; }
        .client-field { display: flex; align-items: flex-start; gap: 8px; }
        .client-icon { width: 24px; height: 24px; border-radius: 50%; background: #f3f4f6; display: flex; justify-content: center; align-items: center; color: #1e3a8a; font-size: 11px; flex-shrink: 0; }
        .client-text-box { display: flex; flex-direction: column; }
        .client-text-box .label { font-size: 8px; color: #6b7280; margin-bottom: 1px; text-transform: uppercase; }
        .client-text-box .value { font-size: 11px; color: #111827; font-weight: 700; }
        .client-text-box .value.pin { color: #dc2626; font-family: 'Orbitron', monospace; letter-spacing: 2px; font-size: 13px; }

        .print-table-container { position: relative; margin-bottom: 5mm; flex-grow: 1; z-index: 2; }
        .print-watermark { position: absolute; inset: 0; display: flex; justify-content: center; align-items: center; font-family: 'Cinzel'; font-size: 55px; color: rgba(15, 23, 42, 0.03); z-index: 0; pointer-events: none; transform: rotate(-15deg); font-weight: 900; white-space: nowrap; }
        .print-table-new { width: 100%; border-collapse: collapse; position: relative; z-index: 1; border: 1px solid #e5e7eb; border-radius: 8px; overflow: hidden; }
        .print-table-new th { background: #0f172a; color: #fff; padding: 8px 10px; font-size: 9px; text-align: left; text-transform: uppercase; letter-spacing: 0.5px; border-right: 1px solid #334155; }
        .print-table-new td { padding: 8px 10px; border-bottom: 1px solid #e5e7eb; font-size: 11px; font-weight: 600; color: #1f2937; border-right: 1px solid #e5e7eb; background: #fff; }
        .print-table-new th:first-child, .print-table-new td:first-child { text-align: center; width: 12%; }
        .print-table-new th:last-child, .print-table-new td:last-child { text-align: center; width: 25%; }
        .asset-desc-flex { display: flex; align-items: center; gap: 8px; }
        .asset-table-icon { width: 22px; height: 22px; border-radius: 5px; background: #0f172a; color: #fff; display: flex; justify-content: center; align-items: center; font-size: 10px; }

        .print-bottom-section { display: flex; gap: 10mm; margin-bottom: 5mm; align-items: stretch; position: relative; z-index: 2; }
        .print-instructions-box { flex: 1; background: #fffbeb; border: 1px solid #fcd34d; border-radius: 8px; padding: 10px 15px; }
        .print-instructions-box h4 { color: #d97706; margin: 0 0 6px 0; font-size: 11px; display: flex; align-items: center; gap: 6px; font-family: 'Space Grotesk'; text-transform: uppercase; }
        .print-instructions-box ul { margin: 0; padding-left: 15px; font-size: 9px; color: #92400e; line-height: 1.5; font-weight: 500; }
        .print-qr-container { display: flex; flex-direction: column; align-items: center; justify-content: center; background: #fff; border: 1px solid #e5e7eb; border-radius: 8px; padding: 5px; width: 35mm; }
        .print-qr-container img { width: 25mm; height: 25mm; margin-bottom: 4px; }
        .print-qr-container span { font-size: 7px; color: #6b7280; font-weight: bold; text-transform: uppercase; text-align: center; line-height: 1.1; }

        .print-absolute-footer { display: flex; justify-content: space-between; align-items: flex-end; position: relative; z-index: 2; margin-top: auto; }
        .sys-gen-text { font-size: 7px; color: #111827; line-height: 1.4; font-style: italic; margin-bottom: 2mm; font-weight: 500;}
        .signatures { display: flex; align-items: center; gap: 20px; background: #fff; padding: 10px; border-radius: 12px; box-shadow: 0 -5px 15px rgba(0,0,0,0.05); }
        .print-stamp { width: 25mm; height: 25mm; border: 2px solid #dc2626; border-radius: 50%; display: flex; flex-direction: column; align-items: center; justify-content: center; color: #dc2626; transform: rotate(-15deg); background: #fff; }
        .stamp-inner { border: 1px dashed #dc2626; border-radius: 50%; width: 21mm; height: 21mm; display: flex; flex-direction: column; align-items: center; justify-content: center; text-align: center; }
        .stamp-t1 { font-size: 6px; font-weight: 900; letter-spacing: 0.5px; line-height: 1.1; }
        .stamp-t2 { font-size: 9px; font-weight: 900; margin: 1px 0; letter-spacing: 1px; }
        .stamp-t3 { font-size: 6px; font-weight: bold; }
        .print-sig-box { text-align: center; width: 40mm; }
        .sig-text { font-family: 'Dancing Script', cursive; font-size: 26px; color: #1e3a8a; line-height: 0.8; transform: rotate(-5deg); margin-bottom: 4px; }
        .sig-line { width: 100%; height: 1px; background: #0f172a; margin: 4px 0; }
        .sig-title { font-size: 8px; font-weight: bold; color: #4b5563; text-transform: uppercase; letter-spacing: 1px; }

        @media print {
            @page { size: A4; margin: 0; }
            body * { visibility: hidden; }
            body { background: #fff !important; margin: 0 !important; padding: 0 !important; position: static; overflow: visible; }
            #print-area { display: block !important; visibility: visible; position: absolute; left: 0; top: 0; page-break-inside: avoid; }
            #print-area * { visibility: visible; }
        }
    </style>
</head>
<body>

    <div class="bg-mesh"></div>
    <div class="grid-overlay"></div>
    <div id="toast-container"></div>

    <header class="top-nav" id="main-top-nav">
        <div class="nav-left">
            <div class="brand-logo"><i class="fas fa-boxes"></i> <span style="font-family: 'Outfit'; font-weight: 900; letter-spacing: 1px;">MND ASSETS</span></div>
        </div>
        <div class="nav-right" style="display: flex; gap: 10px;">
            <button class="nav-btn" onclick="openModal('modal-language')" title="Translate Language" style="color: var(--gold); border-color: var(--gold);"><i class="fas fa-language" style="font-size: 18px;"></i></button>
            <button class="nav-btn" onclick="systemLogout()" title="Secure Logout" style="color: var(--danger); border-color: var(--danger);"><i class="fas fa-power-off"></i></button>
        </div>
    </header>

    <div class="view-wrapper">
        
        <!-- VIEW 1: AUTHENTICATION -->
        <div id="view-auth" class="view-container active-view">
            <div class="flex-center-wrapper">
                <div class="glass-card" style="border-color: var(--primary); box-shadow: 0 20px 60px rgba(0,0,0,0.1); padding: 40px 25px;">
                    <div style="text-align: center; margin-bottom: 35px;">
                        <div style="width: 80px; height: 80px; background: rgba(79, 70, 229, 0.1); border-radius: 24px; display: inline-flex; justify-content: center; align-items: center; margin-bottom: 20px; border: 1px solid rgba(79, 70, 229, 0.2); box-shadow: 0 10px 25px var(--primary-glow);">
                            <i class="fas fa-boxes" style="font-size: 38px; color: var(--primary);"></i>
                        </div>
                        <h2 style="font-family: 'Space Grotesk'; font-size: 26px; font-weight: 900; color: var(--text-main); letter-spacing: 1px;">Logistics Hub</h2>
                        <p style="font-size: 13px; color: var(--text-muted); margin-top: 8px; font-weight: 500;">Premium Inventory & Asset Tracker</p>
                    </div>

                    <div class="input-group">
                        <i class="fas fa-id-badge input-icon"></i>
                        <input type="tel" id="login-phone" class="mn-input" placeholder="Registration ID (Mobile)" autocomplete="off">
                    </div>
                    <div class="input-group">
                        <input type="password" id="login-pin" class="mn-input pin-input" placeholder="6 DIGIT PIN" maxlength="6" autocomplete="off">
                    </div>
                    
                    <button class="mn-btn btn-primary" onclick="processAuth()" id="btn-login" style="margin-top: 15px;">
                        <i class="fas fa-fingerprint"></i> SECURE LOGIN
                    </button>
                    <div style="text-align: center; margin-top: 25px; font-size: 11px; color: var(--text-muted); font-family: 'Cinzel'; letter-spacing: 2px; font-weight: bold;">POWERED BY MAA NIRMALA DJ</div>
                </div>
            </div>
        </div>

        <!-- VIEW 2: ADMIN DIRECTORY -->
        <div id="view-admin-directory" class="view-container">
            <div style="width: 100%; display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; background: #fff; padding: 20px; border-radius: 16px; border: 1px solid var(--border-light); box-shadow: 0 10px 30px rgba(0,0,0,0.05);">
                <div>
                    <h2 style="font-family: 'Cinzel'; font-size: 22px; color: var(--text-main); margin-bottom: 4px; font-weight: 900;">Admin Dash</h2>
                    <span style="font-size: 12px; color: var(--primary); font-weight: 600;">Asset Allocation Hub</span>
                </div>
                <div style="width:45px; height:45px; background:var(--primary-glow); border-radius:12px; display:flex; justify-content:center; align-items:center; color:var(--primary); font-size:20px;"><i class="fas fa-chart-pie"></i></div>
            </div>

            <div class="glass-card flex-grow" style="margin-bottom: 0;">
                <div class="card-header" style="border-bottom: none;">
                    <div class="section-title"><i class="fas fa-users" style="color: var(--primary);"></i> Client Directory</div>
                </div>
                <div class="input-group" style="margin-bottom: 20px;">
                    <i class="fas fa-search input-icon" style="color: var(--text-muted);"></i>
                    <input type="text" id="admin-search" class="mn-input" placeholder="Search client by name or ID..." onkeyup="filterUsers()" style="border-radius: 12px; padding-left: 45px;">
                </div>
                <div class="data-list" id="admin-users-list">
                    <div style="text-align:center; padding: 30px; color: var(--text-muted); font-size: 14px; font-weight: 500;"><i class="fas fa-circle-notch fa-spin"></i> Establishing secure connection...</div>
                </div>
            </div>
        </div>

        <!-- VIEW 3: ADMIN ASSETS MANAGER -->
        <div id="view-admin-assets" class="view-container">
            <div style="width: 100%; display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; background: #fff; padding: 18px 20px; border-radius: 16px; border: 1px solid var(--border-light); box-shadow: 0 10px 30px rgba(0,0,0,0.05);">
                <div style="overflow: hidden; flex-grow:1;">
                    <h2 style="font-family: 'Space Grotesk'; font-size: 20px; color: var(--text-main); font-weight: 800; margin-bottom: 4px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis;" id="target-name">Target Name</h2>
                    <span style="font-size: 12px; color: var(--accent); font-weight: 600;" id="target-phone">Reg ID</span>
                </div>
                <div style="display:flex; gap:10px;">
                    <button class="nav-btn" onclick="openEditProfile()" style="color:var(--gold); border-color:var(--gold);"><i class="fas fa-user-edit"></i></button>
                    <button class="nav-btn" onclick="switchView('view-admin-directory')"><i class="fas fa-arrow-left"></i></button>
                </div>
            </div>

            <div style="display: flex; gap: 12px; width: 100%; margin-bottom: 20px;">
                <button class="mn-btn btn-outline" style="flex: 1; font-size: 11px; padding: 14px;" onclick="openReceiptOptions('admin')">
                    <i class="fas fa-file-export"></i> EXPORT OPTIONS
                </button>
                <button class="mn-btn btn-danger" style="flex: 1; font-size: 11px; padding: 14px;" onclick="revokeTargetAssets()">
                    <i class="fas fa-trash-alt"></i> REVOKE ASSETS
                </button>
            </div>

            <div class="glass-card" style="border-top: 4px solid var(--primary); margin-bottom: 0;">
                <div class="card-header">
                    <div class="section-title"><i class="fas fa-box-open" style="color: var(--primary);"></i> Manage Assets</div>
                </div>
                
                <div class="input-group" style="margin-bottom: 15px;">
                    <i class="fas fa-search input-icon" style="color: var(--text-muted);"></i>
                    <input type="text" id="asset-search-input" class="mn-input" placeholder="Search specific asset (e.g. Drum)..." onkeyup="filterAssets()" style="padding: 14px 14px 14px 45px; font-size: 14px; border-radius: 12px;">
                </div>

                <div class="asset-grid" id="admin-asset-grid">
                    <div class="asset-card" data-name="water jugs"><div class="asset-icon-box asset-jug"><i class="fas fa-glass-water"></i></div><div class="asset-name">Water Jugs</div><div class="qty-controls"><button class="qty-btn" onclick="updateQty('jug', -1)"><i class="fas fa-minus"></i></button><input type="number" id="qty-jug" class="qty-input" value="0" onchange="markDirty()"><button class="qty-btn" onclick="updateQty('jug', 1)"><i class="fas fa-plus"></i></button></div></div>
                    <div class="asset-card" data-name="blue drums"><div class="asset-icon-box asset-drum"><i class="fas fa-drum-steelpan"></i></div><div class="asset-name">Blue Drums</div><div class="qty-controls"><button class="qty-btn" onclick="updateQty('drum', -1)"><i class="fas fa-minus"></i></button><input type="number" id="qty-drum" class="qty-input" value="0" onchange="markDirty()"><button class="qty-btn" onclick="updateQty('drum', 1)"><i class="fas fa-plus"></i></button></div></div>
                    <div class="asset-card" data-name="dj boxes"><div class="asset-icon-box asset-dj"><i class="fas fa-speaker"></i></div><div class="asset-name">DJ Boxes</div><div class="qty-controls"><button class="qty-btn" onclick="updateQty('djbox', -1)"><i class="fas fa-minus"></i></button><input type="number" id="qty-djbox" class="qty-input" value="0" onchange="markDirty()"><button class="qty-btn" onclick="updateQty('djbox', 1)"><i class="fas fa-plus"></i></button></div></div>
                    <div class="asset-card" data-name="microphones"><div class="asset-icon-box asset-mic"><i class="fas fa-microphone-alt"></i></div><div class="asset-name">Microphones</div><div class="qty-controls"><button class="qty-btn" onclick="updateQty('mic', -1)"><i class="fas fa-minus"></i></button><input type="number" id="qty-mic" class="qty-input" value="0" onchange="markDirty()"><button class="qty-btn" onclick="updateQty('mic', 1)"><i class="fas fa-plus"></i></button></div></div>
                    <div class="asset-card" data-name="chairs"><div class="asset-icon-box asset-chair"><i class="fas fa-chair"></i></div><div class="asset-name">Chairs</div><div class="qty-controls"><button class="qty-btn" onclick="updateQty('chair', -10)"><i class="fas fa-angle-double-down" style="font-size:10px;"></i></button><input type="number" id="qty-chair" class="qty-input" value="0" onchange="markDirty()"><button class="qty-btn" onclick="updateQty('chair', 10)"><i class="fas fa-angle-double-up" style="font-size:10px;"></i></button></div></div>
                    <div class="asset-card" data-name="tables"><div class="asset-icon-box asset-table"><i class="fas fa-table"></i></div><div class="asset-name">Tables</div><div class="qty-controls"><button class="qty-btn" onclick="updateQty('table', -5)"><i class="fas fa-angle-double-down" style="font-size:10px;"></i></button><input type="number" id="qty-table" class="qty-input" value="0" onchange="markDirty()"><button class="qty-btn" onclick="updateQty('table', 5)"><i class="fas fa-angle-double-up" style="font-size:10px;"></i></button></div></div>
                </div>

                <button class="mn-btn btn-primary" id="btn-save-assets" onclick="saveTargetAssets()" style="margin-top: 25px;">
                    <i class="fas fa-cloud-upload-alt"></i> DISPATCH & SAVE ALLOCATION
                </button>
            </div>
        </div>

        <!-- VIEW 4: ADMIN HELP LIST -->
        <div id="view-admin-help-list" class="view-container">
            <div style="width: 100%; display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; background: #fff; padding: 20px; border-radius: 16px; border: 1px solid var(--border-light); box-shadow: 0 10px 30px rgba(0,0,0,0.05);">
                <div>
                    <h2 style="font-family: 'Cinzel'; font-size: 22px; color: var(--success); margin-bottom: 4px; font-weight: 900;">Support Hub</h2>
                    <span style="font-size: 12px; color: var(--text-muted); font-weight: 600;">Client Communications</span>
                </div>
                <div style="width:45px; height:45px; background:rgba(5, 150, 105, 0.1); border-radius:12px; display:flex; justify-content:center; align-items:center; color:var(--success); font-size:24px;"><i class="fas fa-comments"></i></div>
            </div>
            <div class="glass-card flex-grow" style="margin-bottom: 0;">
                <div class="card-header" style="border-bottom: none;">
                    <div class="section-title"><i class="fas fa-headset" style="color: var(--success);"></i> Active Clients</div>
                </div>
                <div class="data-list" id="admin-chat-users-list">
                    <div style="text-align:center; padding: 30px; color: var(--text-muted); font-size: 14px; font-weight: 500;"><i class="fas fa-circle-notch fa-spin"></i> Loading directory...</div>
                </div>
            </div>
        </div>

        <!-- VIEW 5: ADMIN CHAT -->
        <div id="view-admin-chat" class="view-container" style="padding-bottom: 0;">
            <div style="width: 100%; display: flex; justify-content: space-between; align-items: center; margin-bottom: 15px; background: #fff; padding: 15px 20px; border-radius: 16px; border: 1px solid var(--border-light); box-shadow: 0 4px 15px rgba(0,0,0,0.05);">
                <div>
                    <h2 style="font-family: 'Space Grotesk'; font-size: 18px; color: var(--text-main); font-weight: 800; margin-bottom: 2px;" id="admin-chat-target-name">Target Name</h2>
                    <span style="font-size: 11px; color: var(--success); font-weight: 600;"><i class="fas fa-circle" style="font-size: 8px;"></i> Online Support</span>
                </div>
                <button class="nav-btn" onclick="switchView('view-admin-help-list')"><i class="fas fa-arrow-left"></i></button>
            </div>
            
            <div class="chat-container">
                <div class="chat-messages" id="admin-chat-messages"></div>
                <div class="chat-input-area">
                    <input type="file" id="admin-chat-file" accept="image/*, application/pdf, video/*, .doc, .docx" style="display:none;" onchange="handleChatFileUpload(event, 'admin')">
                    <button class="chat-attach-btn" onclick="document.getElementById('admin-chat-file').click()"><i class="fas fa-paperclip"></i></button>
                    <input type="text" id="admin-chat-input" class="chat-text-input" placeholder="Type a message...">
                    <button class="chat-send-btn" onclick="sendChatMsg('admin')"><i class="fas fa-paper-plane"></i></button>
                </div>
            </div>
        </div>

        <!-- VIEW 6: CLIENT HOME -->
        <div id="view-client-home" class="view-container">
            <div style="width: 100%; display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; background: #fff; padding: 20px; border-radius: 16px; border: 1px solid var(--border-light); box-shadow: 0 10px 30px rgba(0,0,0,0.05);">
                <div style="overflow: hidden;">
                    <h2 style="font-family: 'Space Grotesk'; font-size: 22px; color: var(--text-main); font-weight: 800; margin-bottom: 4px;">Hi, <span id="client-name-display" style="color: var(--primary);">User</span></h2>
                    <span style="font-size: 12px; color: var(--text-muted); font-weight: 500;">ID: <strong id="client-phone-display" style="color: var(--accent);">--</strong></span>
                </div>
                <button class="nav-btn" style="color: var(--gold); border-color: var(--gold); width: 45px; height: 45px; font-size: 20px;" onclick="openModal('modal-help')"><i class="fas fa-info-circle"></i></button>
            </div>

            <!-- About Card -->
            <div class="glass-card" style="background: #fff; border: 1px solid var(--border-light); border-left: 5px solid var(--gold); padding: 22px;">
                <div style="display: flex; gap: 15px; align-items: center; margin-bottom: 15px;">
                    <div style="background: #f8fafc; padding: 5px; border-radius: 12px; border: 1px solid #e2e8f0; display: flex; align-items: center; justify-content: center; width: 60px; height: 60px; box-shadow: 0 4px 10px rgba(0,0,0,0.05);">
                        <img src="https://i.postimg.cc/52vLtJBM/1000095487-(2).png" style="width: 100%; height: 100%; object-fit: contain;">
                    </div>
                    <div>
                        <h3 style="font-family: 'Cinzel'; color: var(--text-main); font-size: 17px; margin-bottom: 4px; font-weight: 900;">Maa Nirmala DJ & Tent House</h3>
                        <p style="font-size: 11px; color: var(--text-muted); font-weight: 500;"><i class="fas fa-map-marker-alt" style="margin-right: 4px; color: var(--gold);"></i> Tola Beltikri, Kaddhar, Banka (813106)</p>
                    </div>
                </div>
                <p style="font-size: 13px; color: var(--text-muted); line-height: 1.6; border-top: 1px dashed #cbd5e1; padding-top: 15px; font-weight: 500;">
                    Welcome to your live tracking portal. Review your allocated assets below or use the bottom navigation for more options.
                </p>
                <div style="display: flex; gap: 12px; margin-top: 20px;">
                    <button class="mn-btn btn-outline" style="flex:1; font-size: 11px; padding: 12px; border-radius: 10px; color: var(--primary); border-color: var(--primary);" onclick="openReceiptOptions('client')"><i class="fas fa-file-export"></i> EXPORT RECEIPT</button>
                    <a href="https://maa-nirmala-dj.github.io/-tent-house./" target="_blank" class="mn-btn btn-primary" style="flex:1; font-size: 11px; padding: 12px; border-radius: 10px; text-decoration: none;"><i class="fas fa-globe"></i> VISIT WEBSITE</a>
                </div>
            </div>

            <!-- Assets List View -->
            <div class="glass-card" style="border-top: 5px solid var(--purple-accent); padding-bottom: 25px; margin-bottom: 0; background: #fff;">
                <div class="card-header" style="border-bottom: none; padding-bottom: 0;">
                    <div class="section-title" style="color: var(--text-main);"><i class="fas fa-clipboard-list" style="color: var(--purple-accent);"></i> Assigned Assets Overview</div>
                </div>
                <div class="simple-asset-list" id="client-assets-list" style="margin-top:20px;">
                    <div style="text-align:center; padding: 20px; color: var(--text-muted); font-size: 13px; font-weight: 500;">Waiting for HQ to assign assets...</div>
                </div>
            </div>
        </div>

        <!-- VIEW 7: CLIENT ASSETS GRID -->
        <div id="view-client-assets" class="view-container">
            <div class="glass-card flex-grow" style="border-top: 5px solid var(--accent); margin-bottom: 0; background: #fff;">
                <div class="card-header" style="border-bottom: none; padding-bottom: 0;">
                    <div class="section-title" style="color: var(--text-main);"><i class="fas fa-cubes" style="color: var(--accent);"></i> Detailed Visual Grid</div>
                </div>
                <div class="asset-grid" id="client-assets-grid">
                    <div style="grid-column: span 2; text-align:center; padding: 50px 20px; color: var(--text-muted); font-size: 15px; font-weight: 500;">
                        <i class="fas fa-box-open" style="font-size: 48px; margin-bottom: 15px; opacity: 0.3; color: var(--accent);"></i><br>Waiting for HQ to assign assets...
                    </div>
                </div>
                <div id="client-last-updated" style="text-align: center; font-size: 11px; color: var(--text-muted); margin-top: 30px; font-family: 'Orbitron'; letter-spacing: 1px; font-weight: 700;">
                    STATUS: SYNCING...
                </div>
            </div>
        </div>

        <!-- VIEW 8: CLIENT HELP (Chat) -->
        <div id="view-client-help" class="view-container" style="padding-bottom: 0;">
            <div class="glass-card" style="margin-bottom: 15px; padding: 20px; background: #fff;">
                <h3 style="font-family: 'Space Grotesk'; font-size: 18px; color: var(--text-main); font-weight: 800; margin-bottom: 15px;"><i class="fas fa-headset" style="color: var(--primary);"></i> Contact Support</h3>
                <div style="display:flex; justify-content: space-around; gap: 10px;">
                    <a href="tel:7294969938" style="flex:1; display:flex; justify-content:center; align-items:center; gap:8px; color: var(--text-main); text-decoration: none; font-size: 14px; font-weight: 700; background: #f1f5f9; padding: 12px; border-radius: 12px; border: 1px solid #cbd5e1; transition: 0.3s;"><i class="fas fa-phone" style="color:var(--primary);"></i> 7294969938</a>
                    <a href="tel:8544341240" style="flex:1; display:flex; justify-content:center; align-items:center; gap:8px; color: var(--text-main); text-decoration: none; font-size: 14px; font-weight: 700; background: #f1f5f9; padding: 12px; border-radius: 12px; border: 1px solid #cbd5e1; transition: 0.3s;"><i class="fas fa-phone" style="color:var(--gold);"></i> 8544341240</a>
                </div>
            </div>

            <div class="chat-container">
                <div style="background:var(--bg-card); padding:15px; border-bottom:1px solid #e2e8f0; font-family:'Space Grotesk'; font-weight: 800; color:var(--text-main); font-size:15px; display:flex; align-items:center; gap:10px;"><i class="fas fa-comments" style="color: var(--primary); font-size: 20px;"></i> Direct Admin Chat</div>
                <div class="chat-messages" id="client-chat-messages"></div>
                <div class="chat-input-area">
                    <input type="file" id="client-chat-file" accept="image/*, application/pdf, video/*, .doc, .docx" style="display:none;" onchange="handleChatFileUpload(event, 'client')">
                    <button class="chat-attach-btn" onclick="document.getElementById('client-chat-file').click()"><i class="fas fa-paperclip"></i></button>
                    <input type="text" id="client-chat-input" class="chat-text-input" placeholder="Message Admin...">
                    <button class="chat-send-btn" onclick="sendChatMsg('client')"><i class="fas fa-paper-plane"></i></button>
                </div>
            </div>
        </div>
        
    </div>

    <!-- Bottom Nav -->
    <nav class="bottom-nav" id="main-bottom-nav">
        <div class="b-nav-item active" id="bnav-home" onclick="handleNavClick('home')"><i class="fas fa-home"></i><span>Home</span></div>
        <div class="b-nav-item" id="bnav-assets" onclick="handleNavClick('assets')"><i class="fas fa-cubes"></i><span>Assets</span></div>
        <div class="b-nav-item" id="bnav-help" onclick="handleNavClick('help')"><i class="fas fa-headset"></i><span>Help</span></div>
    </nav>

    <!-- ==========================================
         MODALS (Advanced Export & Edit)
         ========================================== -->
    <div id="modal-receipt-options" class="modal-overlay" onclick="closeModal('modal-receipt-options')">
        <div class="modal-content" style="padding: 30px;" onclick="event.stopPropagation()">
            <h3 style="color: var(--text-main); font-family: 'Space Grotesk'; font-size: 20px; font-weight: 800; margin-bottom: 25px; text-align: center;"><i class="fas fa-share-alt" style="color: var(--primary); margin-right: 8px;"></i> Export & Share</h3>
            
            <button class="action-sheet-btn" onclick="triggerPDF()"><i class="fas fa-file-pdf" style="color: var(--danger); font-size: 20px;"></i> Download PDF Receipt</button>
            <button class="action-sheet-btn" onclick="downloadReceiptImage()"><i class="fas fa-image" style="color: var(--accent); font-size: 20px;"></i> Save Image to Phone</button>
            <button class="action-sheet-btn" onclick="triggerPrint()"><i class="fas fa-print" style="color: var(--text-main); font-size: 20px;"></i> Direct Print Document</button>
            
            <div style="margin: 25px 0 10px; border-top: 1px dashed #cbd5e1; padding-top: 20px;">
                <p style="font-size: 11px; color: var(--text-muted); margin-bottom: 15px; text-transform: uppercase; font-weight: 800; text-align: center; letter-spacing: 1px;">Direct Share Integration</p>
                <div style="display:flex; flex-direction: column; gap: 10px;">
                    <button class="action-sheet-btn" style="background: rgba(37, 211, 102, 0.1); border-color: #25D366; color: #166534;" onclick="shareVia('text')"><i class="fab fa-whatsapp" style="font-size: 20px; color: #25D366;"></i> Share Info via WhatsApp</button>
                    <div style="display:flex; gap: 10px;">
                        <button class="action-sheet-btn" style="flex:1; justify-content:center; gap:8px;" onclick="shareVia('image')"><i class="fas fa-share-nodes" style="color: var(--primary);"></i> Share Image</button>
                        <button class="action-sheet-btn" style="flex:1; justify-content:center; gap:8px;" onclick="shareVia('pdf')"><i class="fas fa-share-nodes" style="color: var(--danger);"></i> Share PDF</button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Custom Confirm Modal -->
    <div id="modal-confirm" class="modal-overlay">
        <div class="modal-content" style="max-width: 340px; text-align: center; padding: 30px;">
            <i class="fas fa-exclamation-triangle" style="font-size: 48px; color: var(--danger); margin-bottom: 20px;"></i>
            <h3 id="confirm-title" style="color: var(--text-main); font-family: 'Space Grotesk'; font-size: 22px; font-weight: 800; margin-bottom: 10px;">Are you sure?</h3>
            <p id="confirm-msg" style="font-size: 14px; color: var(--text-muted); margin-bottom: 25px; line-height: 1.5; font-weight: 500;">This action cannot be undone.</p>
            <div style="display: flex; gap: 15px;">
                <button class="mn-btn btn-outline" style="flex: 1;" onclick="closeModal('modal-confirm')">Cancel</button>
                <button class="mn-btn btn-danger" id="confirm-yes-btn" style="flex: 1;">Confirm</button>
            </div>
        </div>
    </div>

    <div id="modal-help" class="modal-overlay" onclick="closeModal('modal-help')">
        <div class="modal-content" onclick="event.stopPropagation()">
            <div class="card-header">
                <h3 class="section-title" style="color: var(--primary);"><i class="fas fa-book-open"></i> Usage Info</h3>
                <i class="fas fa-times" style="cursor: pointer; color: var(--text-muted); font-size: 20px;" onclick="closeModal('modal-help')"></i>
            </div>
            <p style="font-size: 14px; color: var(--text-muted); line-height: 1.6; margin-bottom: 15px; font-weight: 500;">Your allocated assets are updated in real-time by the MND Admin team. You can view them as a list on the Home tab, or visually on the Assets tab.</p>
            <p style="font-size: 14px; color: var(--text-muted); line-height: 1.6; margin-bottom: 20px; font-weight: 500;">Use the <strong>EXPORT RECEIPT</strong> button to save your allocation as a PDF or Image, or to directly share it via WhatsApp and other apps.</p>
            <button class="mn-btn btn-primary" onclick="closeModal('modal-help')">UNDERSTOOD</button>
        </div>
    </div>

    <div id="modal-edit-profile" class="modal-overlay" onclick="closeModal('modal-edit-profile')">
        <div class="modal-content" onclick="event.stopPropagation()">
            <div class="card-header">
                <h3 class="section-title" style="color: var(--gold);"><i class="fas fa-user-edit"></i> Edit Client Profile</h3>
                <i class="fas fa-times" style="cursor: pointer; color: var(--text-muted); font-size: 20px;" onclick="closeModal('modal-edit-profile')"></i>
            </div>
            <div class="input-group"><i class="fas fa-user input-icon"></i><input type="text" id="edit-name" class="mn-input" placeholder="Client Name *"></div>
            <div class="input-group"><i class="fas fa-id-badge input-icon" style="color:#94a3b8;"></i><input type="text" id="edit-phone" class="mn-input" placeholder="Registration ID (Fixed)" disabled style="background:#f1f5f9; color:#64748b;"></div>
            <div class="input-group"><input type="text" id="edit-pin" class="mn-input pin-input" placeholder="6 DIGIT PIN *" maxlength="6"></div>
            <div class="input-group"><i class="fas fa-map-marker-alt input-icon"></i><textarea id="edit-address" class="mn-input" placeholder="Full Event Address *" rows="2" style="resize:none; padding-top:16px;"></textarea></div>
            <div class="input-group"><i class="fas fa-phone input-icon"></i><input type="tel" id="edit-alt-phone" class="mn-input" placeholder="Alt Mobile Number (Optional)"></div>
            <div class="input-group"><i class="fas fa-envelope input-icon"></i><input type="email" id="edit-email" class="mn-input" placeholder="Email ID (Optional)"></div>
            <button class="mn-btn btn-gold" id="btn-save-profile" style="margin-top: 15px;" onclick="saveUserProfile()"><i class="fas fa-cloud-upload-alt"></i> UPDATE RECORD</button>
        </div>
    </div>

    <!-- Language Translation Modal -->
    <div id="modal-language" class="modal-overlay" onclick="closeModal('modal-language')">
        <div class="modal-content" style="max-width: 350px;" onclick="event.stopPropagation()">
            <div class="card-header">
                <h3 class="section-title" style="margin:0; color: var(--gold); font-family: 'Space Grotesk'; font-weight: 800;"><i class="fas fa-language"></i> Select Language</h3>
                <i class="fas fa-times" style="cursor: pointer; color: var(--text-muted); font-size: 18px;" onclick="closeModal('modal-language')"></i>
            </div>
            <p style="font-size:13px; color:var(--text-muted); text-align:center; margin-bottom:20px; font-weight: 500;">Translate application to your preferred language.</p>
            <div id="google_translate_element"></div>
            <button class="mn-btn btn-outline" style="margin-top: 25px;" onclick="closeModal('modal-language')">DONE</button>
        </div>
    </div>

    <!-- =========================================================================
         ADVANCED A4 PRINT & PDF GENERATION ENGINE (PREMIUM MATCH)
         ========================================================================= -->
    <div id="print-area">
        <div class="print-border-wrapper">
            <div class="print-corner-tl"></div>
            <div class="print-corner-br"></div>

            <!-- Header -->
            <div class="print-header">
                <div class="print-logo"><img src="https://i.postimg.cc/52vLtJBM/1000095487-(2).png" alt="MND Logo"></div>
                <div class="print-company-info">
                    <h1>MAA NIRMALA DJ & TENT HOUSE</h1>
                    <p><i class="fas fa-map-marker-alt" style="color:#D4AF37;"></i> <strong>Address:</strong> Tola Beltikri, Kaddhar, Katoria, Banka Bihar (813106)</p>
                    <p><i class="fas fa-phone-alt" style="color:#D4AF37;"></i> <strong>Contact:</strong> +91 7294969938, +91 8544341240</p>
                    <p><i class="fas fa-envelope" style="color:#D4AF37;"></i> <strong>Email:</strong> maa.nirmala.dj.beltikri@gmail.com</p>
                </div>
            </div>
            <div class="print-divider-thick"></div>

            <!-- Meta Information -->
            <div class="print-meta-bar">
                <div style="display:flex; align-items:center; gap:8px;">
                    <div style="width:24px; height:24px; border-radius:50%; background:#1e3a8a; color:#fff; display:flex; justify-content:center; align-items:center;"><i class="fas fa-box"></i></div>
                    <span>DISPATCH / INVOICE ID<br><strong id="print-invoice-id">MND-000000</strong></span>
                </div>
                <div style="display:flex; align-items:center; gap:8px; text-align:right;">
                    <div style="width:24px; height:24px; border-radius:50%; background:#D4AF37; color:#fff; display:flex; justify-content:center; align-items:center;"><i class="far fa-calendar-alt"></i></div>
                    <span>GENERATED ON<br><strong id="print-date">Date Time</strong></span>
                </div>
            </div>

            <!-- Client Details Grid -->
            <div class="print-section-wrap">
                <div class="print-section-line"></div>
                <div class="print-section-title">CLIENT LOGISTICS DETAILS</div>
            </div>
            
            <div class="print-client-grid">
                <div class="client-field"><div class="client-icon"><i class="fas fa-user"></i></div><div class="client-text-box"><span class="label">Client Name</span><span class="value" id="print-c-name">Name</span></div></div>
                <div class="client-field"><div class="client-icon"><i class="fas fa-id-card"></i></div><div class="client-text-box"><span class="label">Registration ID</span><span class="value" id="print-c-phone">Phone</span></div></div>
                <div class="client-field"><div class="client-icon"><i class="fas fa-map-marker-alt"></i></div><div class="client-text-box"><span class="label">Event Address</span><span class="value" id="print-c-address">Address</span></div></div>
                <div class="client-field"><div class="client-icon"><i class="fas fa-phone"></i></div><div class="client-text-box"><span class="label">Alt Contact</span><span class="value" id="print-c-alt">N/A</span></div></div>
                <div class="client-field"><div class="client-icon"><i class="fas fa-barcode"></i></div><div class="client-text-box"><span class="label">Tracking PIN</span><span class="value pin" id="print-c-pin">PIN</span></div></div>
            </div>

            <!-- Asset Table -->
            <div class="print-table-container">
                <div class="print-watermark">MAA NIRMALA</div>
                <table class="print-table-new">
                    <thead><tr><th>SL. NO.</th><th>ASSET DESCRIPTION</th><th>QUANTITY ALLOCATED</th></tr></thead>
                    <tbody id="print-table-body">
                        <!-- Rows Injected by JS -->
                    </tbody>
                </table>
            </div>

            <!-- Instructions & QR Code -->
            <div class="print-bottom-section">
                <div class="print-instructions-box">
                    <h4><i class="fas fa-info-circle"></i> Tracking & Return Instructions</h4>
                    <ul>
                        <li>You can track these assigned assets live on your digital dashboard.</li>
                        <li>Go to official portal: <strong>https://maa-nirmala-dj.github.io/WELCOME-TO-MND-HUB/</strong></li>
                        <li>Login using your <strong>Registration Number</strong> and <strong>Tracking PIN</strong> printed above.</li>
                        <li>Do not share your PIN. Damages to physical assets will incur charges.</li>
                    </ul>
                </div>
                <div class="print-qr-container">
                    <img id="print-qr-img" src="" alt="Live Scanner">
                    <span>Scan For<br>Details</span>
                </div>
            </div>

            <!-- Footer (Stamp, Signature) -->
            <div class="print-absolute-footer">
                <div class="sys-gen-text">System Generated Document.<br>MND Premium Cloud Tracker.</div>
                <div class="signatures">
                    <div class="print-stamp"><div class="stamp-inner"><div class="stamp-t1">MAA NIRMALA</div><div class="stamp-t2">DJ</div><div class="stamp-t2">LEGAL</div><div class="stamp-t3">813106</div></div></div>
                    <div class="print-sig-box">
                        <div class="sig-text">Sildhar Kumar</div>
                        <div class="sig-line"></div>
                        <div class="sig-title">AUTHORIZED SIGNATORY</div>
                    </div>
                </div>
            </div>
            
            <!-- Follow Banner (Updated Links) -->
            <div style="text-align: center; margin-top: 10px; padding-top: 10px; border-top: 1px dashed #e5e7eb; position: relative; z-index: 2;">
                <p style="font-size: 8px; color: #4b5563; font-weight: bold; margin-bottom: 5px;"><i class="fas fa-link"></i> Follow & Connect With Us</p>
                <div style="display: flex; justify-content: center; gap: 8px; font-size: 10px; color: #1e3a8a;">
                    <i class="fab fa-instagram"></i> <i class="fab fa-facebook"></i> <i class="fab fa-youtube"></i> <i class="fab fa-whatsapp"></i> <i class="fab fa-linkedin"></i> <i class="fab fa-twitter"></i>
                </div>
            </div>
        </div>
    </div>

    <script>
        // --- Google Translate Init ---
        function googleTranslateElementInit() {
            new google.translate.TranslateElement({
                pageLanguage: 'en',
                includedLanguages: 'hi,en,bn,te,ta,mr,gu,ur,kn,ml,pa,or',
                layout: google.translate.TranslateElement.InlineLayout.SIMPLE
            }, 'google_translate_element');
        }

        // --- 1. Firebase Setup ---
        const firebaseConfig = {
            apiKey: "AIzaSyCZP-zuJNDW9S4sD_d4R_-nrTMjf0HD4MM",
            authDomain: "mnd-tracking.firebaseapp.com",
            databaseURL: "https://mnd-tracking-default-rtdb.asia-southeast1.firebasedatabase.app",
            projectId: "mnd-tracking",
            storageBucket: "mnd-tracking.firebasestorage.app"
        };
        if (!firebase.apps.length) { firebase.initializeApp(firebaseConfig); }
        const db = firebase.database();

        // --- 2. Core State Variables ---
        const ADMIN_NUMBERS = ["9771617808", "7294969938", "9153635378", "8544341240"];
        const MASTER_PIN = "121120";
        
        let sessionUser = null; 
        let currentRole = null; 
        let globalUsersMap = {};
        
        let currentAdminTargetPhone = ""; 
        let currentAdminTargetName = ""; 
        let currentAdminTargetPin = "";
        let hasUnsavedChanges = false;
        let clientFetchedAssets = {}; 
        let pendingReceiptRole = null;
        let currentConfirmCallback = null;

        const ASSET_CONFIG = {
            jug:   { name: 'Water Jugs', icon: 'fa-glass-water', class: 'asset-jug' }, drum:  { name: 'Blue Drums', icon: 'fa-drum-steelpan', class: 'asset-drum' }, 
            djbox: { name: 'DJ Boxes', icon: 'fa-speaker', class: 'asset-dj' }, mic:   { name: 'Microphones', icon: 'fa-microphone-alt', class: 'asset-mic' }, 
            chair: { name: 'Chairs', icon: 'fa-chair', class: 'asset-chair' }, table: { name: 'Tables', icon: 'fa-table', class: 'asset-table' }
        };

        // --- 3. UI Utility Functions ---
        window.addEventListener('DOMContentLoaded', () => {
            const savedPhone = localStorage.getItem('mnd_tracker_phone');
            const savedPin = localStorage.getItem('mnd_tracker_pin');
            if(savedPhone && savedPin) {
                document.getElementById('login-phone').value = savedPhone;
                document.getElementById('login-pin').value = savedPin;
                setTimeout(processAuth, 300);
            }
        });

        function showToast(msg, type = 'success') {
            const container = document.getElementById('toast-container');
            const toast = document.createElement('div'); toast.className = `toast ${type}`;
            const icon = type === 'success' ? 'fa-check-circle' : 'fa-exclamation-circle';
            toast.innerHTML = `<i class="fas ${icon}"></i> <span style="flex-grow:1;">${msg}</span>`;
            container.prepend(toast); setTimeout(() => toast.remove(), 4000);
        }

        function switchView(id) {
            document.querySelectorAll('.view-container').forEach(el => el.classList.remove('active-view'));
            document.getElementById(id).classList.add('active-view');
            const topNav = document.getElementById('main-top-nav'); const bottomNav = document.getElementById('main-bottom-nav');
            if(id === 'view-auth') { 
                topNav.classList.remove('visible'); bottomNav.classList.remove('visible');
                document.body.style.paddingTop = '0';
                document.body.style.paddingBottom = '0';
            } 
            else if (id === 'view-admin-assets' || id === 'view-admin-chat') { 
                topNav.classList.remove('visible'); bottomNav.classList.add('visible'); 
                document.body.style.paddingTop = '20px';
                document.body.style.paddingBottom = 'calc(var(--bottom-nav-height) + 15px)';
            } 
            else { 
                topNav.classList.add('visible'); bottomNav.classList.add('visible'); 
                document.body.style.paddingTop = 'calc(var(--nav-height) + 15px)';
                document.body.style.paddingBottom = 'calc(var(--bottom-nav-height) + 15px)';
            }
            window.scrollTo(0, 0);
        }

        function handleNavClick(tab) {
            document.querySelectorAll('.b-nav-item').forEach(el => el.classList.remove('active'));
            document.getElementById(`bnav-${tab}`).classList.add('active');
            if(currentRole === 'admin') { 
                if(tab === 'home') switchView('view-admin-directory'); 
                else if (tab === 'help') switchView('view-admin-help-list');
                else if (tab === 'assets') showToast("Select a user to manage assets.", "success");
            } 
            else if(currentRole === 'client') { 
                if(tab === 'home') switchView('view-client-home'); 
                else if (tab === 'assets') switchView('view-client-assets');
                else if (tab === 'help') switchView('view-client-help');
            }
        }

        function openModal(id) { document.getElementById(id).classList.add('active'); }
        function closeModal(id) { document.getElementById(id).classList.remove('active'); }
        function escapeHTML(str) { if(!str) return ''; return String(str).replace(/[&<>'"]/g, tag => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', "'": '&#39;', '"': '&quot;' }[tag] || tag)); }

        // Custom Confirm Logic
        function customConfirm(title, msg, callback) {
            document.getElementById('confirm-title').innerText = title;
            document.getElementById('confirm-msg').innerText = msg;
            currentConfirmCallback = callback; openModal('modal-confirm');
        }
        document.getElementById('confirm-yes-btn').addEventListener('click', () => {
            if(currentConfirmCallback) currentConfirmCallback();
            closeModal('modal-confirm');
        });

        // --- 4. Gatekeeper Auth System ---
        function processAuth() {
            const phone = document.getElementById('login-phone').value.trim(); const pin = document.getElementById('login-pin').value.trim(); 
            const btn = document.getElementById('btn-login');
            if(!phone || !pin) return showToast("Phone and PIN required.", "error");
            btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> CONNECTING...'; btn.disabled = true;

            if(ADMIN_NUMBERS.includes(phone) && pin === MASTER_PIN) {
                localStorage.setItem('mnd_tracker_phone', phone); localStorage.setItem('mnd_tracker_pin', pin);
                currentRole = 'admin'; sessionUser = { name: "System Admin", phone: phone };
                showToast("Admin Protocol Engaged", "success"); btn.innerHTML = '<i class="fas fa-fingerprint"></i> SECURE LOGIN'; btn.disabled = false;
                handleNavClick('home'); startAdminListeners(); return;
            }

            const handleSuccess = (userData) => {
                localStorage.setItem('mnd_tracker_phone', phone); localStorage.setItem('mnd_tracker_pin', pin);
                currentRole = 'client'; sessionUser = userData;
                document.getElementById('client-name-display').innerText = escapeHTML(userData.name); document.getElementById('client-phone-display').innerText = escapeHTML(userData.phone);
                showToast(`Welcome back, ${userData.name}`); handleNavClick('home'); startClientListeners(); 
                btn.innerHTML = '<i class="fas fa-fingerprint"></i> SECURE LOGIN'; btn.disabled = false;
            };

            db.ref(`mnd_pay_users/${phone}`).once('value').then(snap => {
                if(snap.exists() && snap.val().pin === pin) handleSuccess(snap.val());
                else return db.ref(`trackings/${phone}`).once('value').then(snap2 => {
                    if(snap2.exists() && snap2.val().pin === pin) handleSuccess(snap2.val()); else throw new Error("Invalid Security PIN or Not Found.");
                });
            }).catch(e => { showToast(e.message || "Network Error.", "error"); btn.innerHTML = '<i class="fas fa-fingerprint"></i> SECURE LOGIN'; btn.disabled = false; });
        }

        function systemLogout() {
            localStorage.removeItem('mnd_tracker_phone'); localStorage.removeItem('mnd_tracker_pin');
            sessionUser = null; currentRole = null; db.ref().off(); globalUsersMap = {}; currentAdminTargetPhone = ""; clientFetchedAssets = {};
            document.getElementById('login-phone').value = ''; document.getElementById('login-pin').value = '';
            switchView('view-auth'); showToast("Session Terminated Securely.");
        }

        // --- 5. Admin Dashboard Logic ---
        function startAdminListeners() {
            db.ref('trackings').on('value', snap2 => { if(snap2.exists()) snap2.forEach(c => { const d = c.val(); if(d.name && d.phone) globalUsersMap[d.phone] = { ...d, source: 'Logistics DB' }; }); renderAdminDirectory(); renderAdminHelpList(); });
            db.ref('mnd_pay_users').on('value', snap => { if(snap.exists()) snap.forEach(c => { globalUsersMap[c.key] = { ...c.val(), source: 'Pay DB' }; }); renderAdminDirectory(); renderAdminHelpList(); });
        }

        function renderAdminDirectory() {
            const list = document.getElementById('admin-users-list'); const searchQ = document.getElementById('admin-search').value.toLowerCase(); const users = Object.values(globalUsersMap);
            let html = ''; let count = 0; users.sort((a,b) => (b.registeredAt || b.timestamp || 0) - (a.registeredAt || a.timestamp || 0));
            users.forEach(u => {
                const searchStr = `${u.name} ${u.phone} ${u.event||''} ${u.address||''}`.toLowerCase();
                if(searchQ && !searchStr.includes(searchQ)) return;
                count++; const sourceColor = u.source === 'Logistics DB' ? 'var(--accent)' : 'var(--primary)';
                html += `
                    <div class="dir-item" style="border-left-color: ${sourceColor};" onclick="openAdminAssets('${u.phone}', '${escapeHTML(u.name)}', '${u.pin}')">
                        <div class="dir-info" style="flex-grow:1;">
                            <h4>${escapeHTML(u.name)}</h4>
                            <p>Reg ID: <strong>${escapeHTML(u.phone)}</strong> • PIN: <span style="color:var(--gold);">${u.pin}</span></p>
                            <p style="font-size:10px; margin-top:4px; color:var(--text-muted);"><i class="fas fa-map-marker-alt"></i> ${escapeHTML(u.address || 'Address not provided')}</p>
                        </div>
                        <div class="dir-actions">
                            <i class="fas fa-box-open action-icon-btn" style="color:var(--accent);" title="Manage Assets"></i>
                            <i class="fas fa-trash-alt action-icon-btn trash" onclick="event.stopPropagation(); deleteUser('${u.phone}')" title="Delete User"></i>
                        </div>
                    </div>
                `;
            });
            if(count === 0) list.innerHTML = '<div style="text-align:center; padding: 20px; color: #555; font-size: 14px;">No clients found.</div>'; else list.innerHTML = html;
        }

        function renderAdminHelpList() {
            const list = document.getElementById('admin-chat-users-list'); const users = Object.values(globalUsersMap); let html = '';
            users.forEach(u => {
                html += `
                    <div class="dir-item" onclick="openAdminChat('${u.phone}', '${escapeHTML(u.name)}')">
                        <div class="dir-info" style="flex-grow:1;"><h4>${escapeHTML(u.name)}</h4><p>${escapeHTML(u.phone)}</p></div>
                        <i class="fas fa-comments" style="color: var(--success); font-size:18px;"></i>
                    </div>
                `;
            });
            if(users.length === 0) list.innerHTML = '<div style="text-align:center; padding: 20px; color: #555; font-size: 14px;">No clients found.</div>'; else list.innerHTML = html;
        }

        function filterUsers() { renderAdminDirectory(); }
        
        function filterAssets() {
            const query = document.getElementById('asset-search-input').value.toLowerCase();
            document.querySelectorAll('#admin-asset-grid .asset-card').forEach(card => {
                if(card.getAttribute('data-name').includes(query)) card.classList.remove('hidden'); else card.classList.add('hidden');
            });
        }

        function deleteUser(phone) {
            customConfirm("Delete User", "Permanently delete this user from the entire system? All records will be lost.", () => {
                const promises = [db.ref(`trackings/${phone}`).remove(), db.ref(`mnd_pay_users/${phone}`).remove(), db.ref(`mnd_assets/${phone}`).remove(), db.ref(`mnd_logistics_chat/${phone}`).remove()];
                Promise.all(promises).then(() => {
                    delete globalUsersMap[phone]; renderAdminDirectory(); renderAdminHelpList(); showToast("User Completely Erased.", "success");
                }).catch(e => showToast("Error deleting user.", "error"));
            });
        }

        // --- 6. Admin Asset Management ---
        function openAdminAssets(phone, name, pin) {
            currentAdminTargetPhone = phone; currentAdminTargetName = name; currentAdminTargetPin = pin;
            document.getElementById('target-name').innerText = escapeHTML(name); document.getElementById('target-phone').innerText = phone;
            switchView('view-admin-assets');
            Object.keys(ASSET_CONFIG).forEach(id => document.getElementById(`qty-${id}`).value = 0);
            document.getElementById('asset-search-input').value = ''; filterAssets(); 
            const btn = document.getElementById('btn-save-assets'); btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> SYNCING...'; btn.classList.remove('btn-danger'); btn.classList.add('btn-primary');
            db.ref(`mnd_assets/${phone}/items`).once('value').then(snap => {
                if(snap.exists()) { const items = snap.val(); for(const key in items) { const el = document.getElementById(`qty-${key}`); if(el) el.value = items[key]; } }
                hasUnsavedChanges = false; btn.innerHTML = '<i class="fas fa-cloud-upload-alt"></i> DISPATCH & SAVE ALLOCATION';
            });
        }

        function updateQty(item, change) { const input = document.getElementById(`qty-${item}`); let val = parseInt(input.value) || 0; val += change; if(val < 0) val = 0; input.value = val; markDirty(); }
        function markDirty() { hasUnsavedChanges = true; const btn = document.getElementById('btn-save-assets'); btn.innerHTML = '<i class="fas fa-exclamation-circle"></i> UN-SAVED CHANGES (CLICK TO SAVE)'; btn.classList.remove('btn-primary'); btn.classList.add('btn-danger'); }

        function saveTargetAssets() {
            if(!currentAdminTargetPhone) return;
            const btn = document.getElementById('btn-save-assets'); btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> DISPATCHING...';
            const payload = {}; Object.keys(ASSET_CONFIG).forEach(id => { payload[id] = parseInt(document.getElementById(`qty-${id}`).value) || 0; });
            db.ref(`mnd_assets/${currentAdminTargetPhone}`).set({ items: payload, lastUpdated: Date.now(), lastUpdatedStr: new Date().toLocaleString('en-US', { day:'numeric', month:'short', hour:'2-digit', minute:'2-digit'}) }).then(() => {
                showToast("Assets dispatched to client!"); btn.innerHTML = '<i class="fas fa-check-circle"></i> ALLOCATION SAVED'; btn.classList.remove('btn-danger'); btn.classList.add('btn-primary'); hasUnsavedChanges = false;
            }).catch(e => { showToast("Failed to save.", "error"); btn.innerHTML = '<i class="fas fa-cloud-upload-alt"></i> DISPATCH & SAVE ALLOCATION'; });
        }

        function revokeTargetAssets() {
            if(!currentAdminTargetPhone) return;
            customConfirm("Revoke Assets", "Permanently revoke all assigned assets for this client?", () => {
                db.ref(`mnd_assets/${currentAdminTargetPhone}`).remove().then(() => {
                    Object.keys(ASSET_CONFIG).forEach(id => document.getElementById(`qty-${id}`).value = 0);
                    showToast("All assets revoked.", "success"); hasUnsavedChanges = false;
                    const btn = document.getElementById('btn-save-assets'); btn.innerHTML = '<i class="fas fa-cloud-upload-alt"></i> DISPATCH & SAVE ALLOCATION'; btn.classList.remove('btn-danger'); btn.classList.add('btn-primary');
                });
            });
        }

        // --- 7. Admin Profile Editor ---
        function openEditProfile() {
            if(!currentAdminTargetPhone) return; 
            document.getElementById('edit-phone').value = currentAdminTargetPhone; document.getElementById('edit-name').value = currentAdminTargetName; document.getElementById('edit-pin').value = currentAdminTargetPin;
            const userObj = globalUsersMap[currentAdminTargetPhone];
            if(userObj) { document.getElementById('edit-address').value = userObj.address || ''; document.getElementById('edit-alt-phone').value = userObj.altPhone || ''; document.getElementById('edit-email').value = userObj.email || ''; }
            openModal('modal-edit-profile');
        }

        function saveUserProfile() {
            if(!currentAdminTargetPhone) return;
            const name = document.getElementById('edit-name').value.trim(); const pin = document.getElementById('edit-pin').value.trim(); const address = document.getElementById('edit-address').value.trim(); const altPhone = document.getElementById('edit-alt-phone').value.trim(); const email = document.getElementById('edit-email').value.trim();
            if(!name || !pin || pin.length !== 6 || !address) return showToast("Name, Address and 6-digit PIN required.", "error");
            const btn = document.getElementById('btn-save-profile'); btn.innerHTML = '<i class="fas fa-spinner fa-spin"></i> SAVING...';
            const updates = { name, pin, address, altPhone, email };
            const promises = [db.ref(`trackings/${currentAdminTargetPhone}`).once('value').then(s => { if(s.exists()) return s.ref.update(updates); }), db.ref(`mnd_pay_users/${currentAdminTargetPhone}`).once('value').then(s => { if(s.exists()) return s.ref.update(updates); })];
            Promise.all(promises).then(() => {
                currentAdminTargetName = name; currentAdminTargetPin = pin;
                if(globalUsersMap[currentAdminTargetPhone]) Object.assign(globalUsersMap[currentAdminTargetPhone], updates);
                document.getElementById('target-name').innerText = escapeHTML(name); renderAdminDirectory(); closeModal('modal-edit-profile'); showToast("Profile Updated Successfully."); btn.innerHTML = '<i class="fas fa-cloud-upload-alt"></i> UPDATE RECORD';
            }).catch(e => { showToast("Error updating profile.", "error"); btn.innerHTML = '<i class="fas fa-cloud-upload-alt"></i> UPDATE RECORD'; });
        }

        // --- 8. Client Live Dashboard ---
        function startClientListeners() {
            if(!sessionUser) return;
            const grid = document.getElementById('client-assets-grid'); const list = document.getElementById('client-assets-list'); const statusEl = document.getElementById('client-last-updated');
            db.ref(`mnd_assets/${sessionUser.phone}`).on('value', snap => {
                if(snap.exists()) {
                    const data = snap.val(); const items = data.items; clientFetchedAssets = items; 
                    let gridHtml = ''; let listHtml = ''; let hasItems = false;
                    for(const key in items) {
                        if(items[key] > 0 && ASSET_CONFIG[key]) {
                            hasItems = true; const c = ASSET_CONFIG[key];
                            gridHtml += `<div class="asset-card"><div class="asset-icon-box ${c.class}"><i class="fas ${c.icon}"></i></div><div class="asset-name">${c.name}</div><div class="asset-qty-display">${items[key]}</div></div>`;
                            listHtml += `<div class="simple-asset-item"><span><i class="fas ${c.icon}" style="color: var(--gold); font-size: 18px;"></i> ${c.name}</span><span>${items[key]}</span></div>`;
                        }
                    }
                    if(hasItems) { grid.innerHTML = gridHtml; list.innerHTML = listHtml; statusEl.innerHTML = `<span style="color:var(--success);"><i class="fas fa-sync fa-spin" style="margin-right:5px; font-size:10px;"></i> LIVE SYNCED • ${data.lastUpdatedStr}</span>`; } 
                    else { grid.innerHTML = '<div style="grid-column: span 2; text-align:center; padding: 40px; color: var(--text-muted); font-size: 14px;"><i class="fas fa-box-open" style="font-size: 36px; margin-bottom: 12px; opacity: 0.5;"></i><br>No assets currently allocated.</div>'; list.innerHTML = '<div style="text-align:center; padding: 20px; color: var(--text-muted); font-size: 13px;">No assets assigned yet.</div>'; statusEl.innerHTML = `SYNCED • ZERO INVENTORY`; clientFetchedAssets = {}; }
                } else {
                    grid.innerHTML = '<div style="grid-column: span 2; text-align:center; padding: 40px; color: var(--text-muted); font-size: 14px;"><i class="fas fa-box-open" style="font-size: 36px; margin-bottom: 12px; opacity: 0.5;"></i><br>Waiting for HQ to assign assets...</div>'; list.innerHTML = '<div style="text-align:center; padding: 20px; color: var(--text-muted); font-size: 13px;">Waiting for HQ to assign assets...</div>'; statusEl.innerHTML = `WAITING FOR DISPATCH...`; clientFetchedAssets = {};
                }
            });
            
            db.ref(`mnd_logistics_chat/${sessionUser.phone}`).on('value', snap => {
                const area = document.getElementById('client-chat-messages');
                if(snap.exists()) {
                    let html = ''; snap.forEach(child => {
                        const msg = child.val(); const isMe = msg.sender === 'client'; let mediaHtml = '';
                        if(msg.fileType === 'image') mediaHtml = `<img src="${msg.media}" class="chat-media-preview" onclick="viewFullImage('${msg.media}')">`;
                        else if(msg.fileType) mediaHtml = `<div class="chat-doc-preview"><i class="fas fa-file-alt"></i> ${msg.fileName}</div>`;
                        const delBtn = isMe ? `<i class="fas fa-trash-alt chat-del-btn" onclick="deleteLogisticsChatMsg('${sessionUser.phone}', '${child.key}')"></i>` : '';
                        html += `<div class="chat-bubble ${isMe ? 'client' : 'admin'}">${delBtn}${mediaHtml}${escapeHTML(msg.text)}<span class="chat-time">${msg.time}</span></div>`;
                    }); area.innerHTML = html; area.scrollTop = area.scrollHeight;
                } else { area.innerHTML = '<div style="text-align:center; color:var(--text-muted); font-size:12px;">Start chat with HQ.</div>'; }
            });
        }

        // --- 9. Chat System (Admin & Client) ---
        function openAdminChat(phone, name) {
            currentAdminTargetPhone = phone; document.getElementById('admin-chat-target-name').innerText = escapeHTML(name); switchView('view-admin-chat');
            db.ref(`mnd_logistics_chat/${phone}`).on('value', snap => {
                const area = document.getElementById('admin-chat-messages');
                if(snap.exists()) {
                    let html = ''; snap.forEach(child => {
                        const msg = child.val(); const isMe = msg.sender === 'admin'; let mediaHtml = '';
                        if(msg.fileType === 'image') mediaHtml = `<img src="${msg.media}" class="chat-media-preview" onclick="viewFullImage('${msg.media}')">`;
                        else if(msg.fileType) mediaHtml = `<div class="chat-doc-preview"><i class="fas fa-file-alt"></i> ${msg.fileName}</div>`;
                        const delBtn = isMe ? `<i class="fas fa-trash-alt chat-del-btn" onclick="deleteLogisticsChatMsg('${phone}', '${child.key}')"></i>` : '';
                        html += `<div class="chat-bubble ${isMe ? 'admin' : 'client'}">${delBtn}${mediaHtml}${escapeHTML(msg.text)}<span class="chat-time">${msg.time}</span></div>`;
                    }); area.innerHTML = html; area.scrollTop = area.scrollHeight;
                } else { area.innerHTML = '<div style="text-align:center; color:var(--text-muted); font-size:12px;">Start secure chat.</div>'; }
            });
        }

        window.deleteLogisticsChatMsg = function(phone, key) { db.ref(`mnd_logistics_chat/${phone}/${key}`).remove(); }

        function sendChatMsg(role) {
            const targetPhone = role === 'admin' ? currentAdminTargetPhone : sessionUser?.phone; if(!targetPhone) return;
            const inputId = role === 'admin' ? 'admin-chat-input' : 'client-chat-input'; const input = document.getElementById(inputId); const text = input.value.trim(); if(!text) return;
            db.ref(`mnd_logistics_chat/${targetPhone}`).push({ sender: role, text: text, time: new Date().toLocaleTimeString('en-US', {hour:'2-digit', minute:'2-digit'}), timestamp: Date.now() });
            input.value = '';
        }

        function handleChatFileUpload(e, role) {
            const file = e.target.files[0]; if(!file) return;
            if(file.size > 5 * 1024 * 1024) { showToast("File too large. Limit 5MB.", "error"); e.target.value = ''; return; }
            const targetPhone = role === 'admin' ? currentAdminTargetPhone : sessionUser?.phone; if(!targetPhone) return;

            const reader = new FileReader(); let fileType = file.type.startsWith('image/') ? 'image' : 'document';
            if(file.type.startsWith('video/')) fileType = 'video'; if(file.type === 'application/pdf') fileType = 'pdf';

            reader.onload = function(event) {
                let mediaData = event.target.result;
                if(fileType === 'image') {
                    const img = new Image(); img.src = mediaData;
                    img.onload = function() {
                        const canvas = document.createElement('canvas'); const MAX_WIDTH = 500; let scale = 1; if(img.width > MAX_WIDTH) scale = MAX_WIDTH / img.width;
                        canvas.width = img.width * scale; canvas.height = img.height * scale;
                        const ctx = canvas.getContext('2d'); ctx.drawImage(img, 0, 0, canvas.width, canvas.height); mediaData = canvas.toDataURL('image/jpeg', 0.6);
                        db.ref(`mnd_logistics_chat/${targetPhone}`).push({ sender: role, text: '', media: mediaData, fileType: fileType, fileName: file.name, time: new Date().toLocaleTimeString('en-US', {hour:'2-digit', minute:'2-digit'}), timestamp: Date.now() });
                    }
                } else {
                    db.ref(`mnd_logistics_chat/${targetPhone}`).push({ sender: role, text: '', media: mediaData, fileType: fileType, fileName: file.name, time: new Date().toLocaleTimeString('en-US', {hour:'2-digit', minute:'2-digit'}), timestamp: Date.now() });
                }
            }; reader.readAsDataURL(file); e.target.value = '';
        }

        // --- 10. Advanced Print, PDF & Native Share Engine ---
        function openReceiptOptions(role) { pendingReceiptRole = role; openModal('modal-receipt-options'); }

        function populatePrintArea(role) {
            let name, phone, pin, items, address, email, altPhone, eventName;

            if (role === 'admin') {
                if(!currentAdminTargetPhone) { showToast("Select a user first.", "error"); return false; }
                const user = globalUsersMap[currentAdminTargetPhone] || {};
                name = currentAdminTargetName; phone = currentAdminTargetPhone; pin = currentAdminTargetPin || "N/A";
                address = user.address || "Address not provided"; email = user.email || "N/A"; altPhone = user.altPhone || "N/A";
                eventName = user.event || "Logistics Event";
                items = {}; Object.keys(ASSET_CONFIG).forEach(id => { let val = parseInt(document.getElementById(`qty-${id}`).value) || 0; if(val > 0) items[id] = val; });
            } else {
                if(!sessionUser) return false;
                name = sessionUser.name; phone = sessionUser.phone; pin = sessionUser.pin || "N/A";
                address = sessionUser.address || "Address not provided"; email = sessionUser.email || "N/A"; altPhone = sessionUser.altPhone || "N/A";
                eventName = sessionUser.event || "Logistics Event";
                items = clientFetchedAssets;
            }

            if(Object.keys(items).length === 0) { showToast("Cannot process empty inventory.", "error"); return false; }

            const dispatchId = "MND-" + Math.floor(Math.random() * 900000 + 100000);
            const dDate = new Date(); const days = ["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"];
            const currentDateTime = `${days[dDate.getDay()]}, ${dDate.toLocaleDateString('en-IN', { day: '2-digit', month: 'short', year: 'numeric' })} at ${dDate.toLocaleTimeString('en-IN', { hour: '2-digit', minute: '2-digit', hour12: true })}`;

            document.getElementById('print-invoice-id').innerText = dispatchId;
            document.getElementById('print-date').innerText = currentDateTime;
            document.getElementById('print-c-name').innerText = escapeHTML(name);
            document.getElementById('print-c-phone').innerText = escapeHTML(phone);
            document.getElementById('print-c-address').innerText = escapeHTML(address);
            document.getElementById('print-c-pin').innerText = escapeHTML(pin);
            document.getElementById('print-c-alt').innerText = escapeHTML(altPhone);

            let tableRows = ''; let slNo = 1; let assetStrForQR = '';
            for (const key in items) {
                if (items[key] > 0 && ASSET_CONFIG[key]) {
                    tableRows += `<tr><td>${slNo}</td><td><div class="asset-desc-flex"><div class="asset-table-icon"><i class="fas ${ASSET_CONFIG[key].icon}"></i></div><strong>${ASSET_CONFIG[key].name}</strong></div></td><td>${items[key]}</td></tr>`;
                    assetStrForQR += `${ASSET_CONFIG[key].name}: ${items[key]} | `;
                    slNo++;
                }
            }
            document.getElementById('print-table-body').innerHTML = tableRows;

            const qrData = `MND Logistics\nName: ${name}\nReg ID: ${phone}\nPIN: ${pin}\nAssets: ${assetStrForQR}`;
            document.getElementById('print-qr-img').src = `https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=${encodeURIComponent(qrData)}&color=111827`;

            return { targetPhone: phone, targetName: name, targetPin: pin, eventName: eventName };
        }

        function triggerPrint() {
            if(!pendingReceiptRole) return;
            if(populatePrintArea(pendingReceiptRole)) {
                closeModal('modal-receipt-options'); setTimeout(() => { window.print(); }, 500);
            }
        }
        
        function triggerPDF() {
            if(!pendingReceiptRole) return;
            const data = populatePrintArea(pendingReceiptRole);
            if(data) {
                closeModal('modal-receipt-options');
                const printArea = document.getElementById('print-area');
                printArea.style.display = 'block'; printArea.style.position = 'absolute'; printArea.style.left = '-9999px'; printArea.style.top = '-9999px';
                showToast("Generating PDF Document...");

                setTimeout(() => {
                    html2canvas(printArea, { scale: 2, useCORS: true, backgroundColor: "#ffffff" }).then(canvas => {
                        const imgData = canvas.toDataURL('image/jpeg', 0.95);
                        const { jsPDF } = window.jspdf; const pdf = new jsPDF('p', 'mm', 'a4');
                        const pdfWidth = pdf.internal.pageSize.getWidth(); const pdfHeight = (canvas.height * pdfWidth) / canvas.width;
                        pdf.addImage(imgData, 'JPEG', 0, 0, pdfWidth, Math.min(pdfHeight, 297));
                        pdf.save(`MND_Receipt_${data.targetPhone}.pdf`);
                        printArea.style.display = 'none'; printArea.style.position = ''; printArea.style.left = ''; printArea.style.top = '';
                        showToast("PDF Downloaded!", "success");
                    }).catch(e => { printArea.style.display = 'none'; showToast("Engine failed to render PDF.", "error"); });
                }, 800); 
            }
        }

        function downloadReceiptImage() {
            if(!pendingReceiptRole) return;
            const data = populatePrintArea(pendingReceiptRole);
            if(data) {
                closeModal('modal-receipt-options');
                const printArea = document.getElementById('print-area');
                printArea.style.display = 'block'; printArea.style.position = 'absolute'; printArea.style.left = '-9999px'; printArea.style.top = '-9999px';
                showToast("Rendering High-Quality Image...");

                setTimeout(() => {
                    html2canvas(printArea, { scale: 2, useCORS: true, backgroundColor: "#ffffff" }).then(canvas => {
                        const link = document.createElement('a');
                        link.download = `MND_Receipt_${data.targetPhone}.jpg`; link.href = canvas.toDataURL('image/jpeg', 0.9); link.click();
                        printArea.style.display = 'none'; printArea.style.position = ''; printArea.style.left = ''; printArea.style.top = '';
                        showToast("Image Saved to Gallery!", "success");
                    }).catch(e => { printArea.style.display = 'none'; showToast("Engine failed to render image.", "error"); });
                }, 800);
            }
        }

        // Comprehensive Direct Share Integration (Text, Image, PDF)
        async function shareVia(format) {
            if(!pendingReceiptRole) return;
            const data = populatePrintArea(pendingReceiptRole);
            if(!data) return;
            
            closeModal('modal-receipt-options');

            const msgText = `*👑 MAA NIRMALA DJ & TENT HOUSE BELTIKRI ( 813106 ) 👑*\n\n*📍 LIVE ASSET TRACKING 📍*\n\nHello ${data.targetName},\nYour logistics for *${data.eventName}* are active.\n----------------------------------------------------------\n*How to track:*\n1. Click the link and open the website.\n2. Click the 'Auth' button on the right corner side.\n3. Click the 'MND Asset & Logistic Track' button.\n4. Enter your registration number and 6-digit PIN.\n----------------------------------------------------------\n •••••••••••••••••••••••••••••••••••••••••••••••\n 🔗 *Link:* https://maa-nirmala-dj.github.io/WELCOME-TO-MND-HUB/\n📱 *Registration Number:* ${data.targetPhone}\n🔐 *Tracking PIN:* ${data.targetPin}\n•••••••••••••••••••••••••••••••••••••••••••••••\nThank You.`;
            const mediaNote = "Here is your official asset allocation document from Maa Nirmala DJ & Tent House (Tola Beltikri, Kaddhar, Banka 813106). Thank you for choosing our premium services!";

            if(format === 'text') {
                const waLink = `https://wa.me/?text=${encodeURIComponent(msgText)}`;
                window.open(waLink, '_blank');
                return;
            }

            const printArea = document.getElementById('print-area');
            printArea.style.display = 'block'; printArea.style.position = 'absolute'; printArea.style.left = '-9999px'; printArea.style.top = '-9999px';
            showToast(`Preparing ${format.toUpperCase()} for sharing...`);

            try {
                if(format === 'image') {
                    const canvas = await html2canvas(printArea, { scale: 2, useCORS: true, backgroundColor: "#ffffff" });
                    canvas.toBlob(async (blob) => {
                        const file = new File([blob], `MND_Receipt_${data.targetPhone}.jpg`, { type: 'image/jpeg' });
                        if (navigator.canShare && navigator.canShare({ files: [file] })) {
                            await navigator.share({ title: 'MND Logistics Receipt', text: mediaNote, files: [file] });
                        } else {
                            showToast("Native sharing not supported. Image downloaded.", "success");
                            const link = document.createElement('a'); link.download = file.name; link.href = URL.createObjectURL(blob); link.click();
                        }
                        printArea.style.display = 'none'; printArea.style.position = ''; printArea.style.left = ''; printArea.style.top = '';
                    }, 'image/jpeg', 0.9);
                } 
                else if(format === 'pdf') {
                    const canvas = await html2canvas(printArea, { scale: 2, useCORS: true, backgroundColor: "#ffffff" });
                    const imgData = canvas.toDataURL('image/jpeg', 0.95);
                    const { jsPDF } = window.jspdf; const pdf = new jsPDF('p', 'mm', 'a4');
                    const pdfWidth = pdf.internal.pageSize.getWidth(); const pdfHeight = (canvas.height * pdfWidth) / canvas.width;
                    pdf.addImage(imgData, 'JPEG', 0, 0, pdfWidth, Math.min(pdfHeight, 297));
                    const pdfBlob = pdf.output('blob');
                    const file = new File([pdfBlob], `MND_Receipt_${data.targetPhone}.pdf`, { type: 'application/pdf' });
                    
                    if (navigator.canShare && navigator.canShare({ files: [file] })) {
                        await navigator.share({ title: 'MND Logistics Receipt', text: mediaNote, files: [file] });
                    } else {
                        showToast("Native sharing not supported. PDF downloaded.", "success");
                        const link = document.createElement('a'); link.download = file.name; link.href = URL.createObjectURL(pdfBlob); link.click();
                    }
                    printArea.style.display = 'none'; printArea.style.position = ''; printArea.style.left = ''; printArea.style.top = '';
                }
            } catch (err) {
                printArea.style.display = 'none'; showToast("Failed to prepare share file.", "error");
            }
        }
    </script>
</body>
</html>
