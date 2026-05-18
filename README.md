<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Locale — Community Events & Feed</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;600;700&family=DM+Sans:wght@300;400;500;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --ink: #0a0a0f;
            --ink-2: #13131a;
            --ink-3: #1c1c28;
            --ink-4: #252535;
            --glass: rgba(255,255,255,0.04);
            --glass-border: rgba(255,255,255,0.08);
            --glass-hover: rgba(255,255,255,0.07);
            --gold: #c9a96e;
            --gold-light: #e8c98a;
            --gold-dim: rgba(201,169,110,0.15);
            --sage: #7eb89a;
            --sage-dim: rgba(126,184,154,0.15);
            --rose: #c97e8a;
            --rose-dim: rgba(201, 126, 138, 0.15);
            --text-1: #f0ede8;
            --text-2: #a09890;
            --text-3: #5a5560;
            --r: 16px;
            --r-sm: 10px;
            --ease: cubic-bezier(0.4, 0, 0.2, 1);
            --spring: cubic-bezier(0.34, 1.56, 0.64, 1);
        }

        *, *::before, *::after {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            background: var(--ink);
            font-family: 'DM Sans', sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: flex-start;
            min-height: 100vh;
            padding: 12px;
            gap: 12px;
            overflow-y: auto;
        }

        body::before {
            content: '';
            position: fixed;
            top: -20%;
            left: 50%;
            transform: translateX(-50%);
            width: 600px;
            height: 600px;
            background: radial-gradient(ellipse, rgba(201,169,110,0.06) 0%, transparent 70%);
            pointer-events: none;
            z-index: 0;
        }

        /* ── DEV TOOLBAR ── */
        .toolbar {
            background: rgba(19,19,26,0.9);
            backdrop-filter: blur(20px);
            border: 1px solid var(--glass-border);
            padding: 6px;
            border-radius: 12px;
            display: flex;
            gap: 4px;
            max-width: 600px;
            width: 100%;
            justify-content: center;
            flex-wrap: wrap;
            position: relative;
            z-index: 10;
        }

        .tb-btn {
            background: var(--ink-3);
            color: var(--text-2);
            border: 1px solid transparent;
            padding: 5px 8px;
            border-radius: 7px;
            font-size: 0.72rem;
            font-weight: 600;
            font-family: 'DM Sans', sans-serif;
            letter-spacing: 0.02em;
            cursor: pointer;
            transition: all 0.2s var(--ease);
        }

        .tb-btn:hover {
            background: var(--ink-4);
            color: var(--text-1);
        }

        .tb-btn.active {
            background: var(--gold-dim);
            color: var(--gold);
            border-color: rgba(201,169,110,0.3);
        }

        .tb-btn:disabled {
            opacity: 0.4;
            cursor: not-allowed;
        }

        /* ── DEVICE FRAME ── */
        .device {
            width: 100%;
            max-width: 480px;
            flex: 1;
            height: 0; 
            background: var(--ink-2);
            border: 1.5px solid var(--glass-border);
            border-radius: 32px;
            box-shadow: 0 0 0 4px var(--ink), 0 20px 60px rgba(0,0,0,0.7), inset 0 1px 0 rgba(255,255,255,0.06);
            position: relative;
            display: flex;
            flex-direction: column;
            overflow: hidden;
            z-index: 1;
        }

        .device::before {
            content: '';
            position: absolute;
            top: 10px;
            left: 50%;
            transform: translateX(-50%);
            width: 80px;
            height: 4px;
            background: var(--ink-4);
            border-radius: 2px;
            z-index: 100;
        }

        /* ── HEADER ── */
        .app-header {
            padding: 24px 20px 14px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            position: relative;
            z-index: 10;
            background: linear-gradient(to bottom, var(--ink-2) 70%, transparent);
        }

        .app-logo {
            font-family: 'Playfair Display', serif;
            font-size: 1.5rem;
            font-weight: 700;
            color: var(--text-1);
            letter-spacing: -0.02em;
        }

        .app-logo span {
            color: var(--gold);
        }

        .header-pill {
            background: var(--glass);
            border: 1px solid var(--glass-border);
            color: var(--text-2);
            padding: 6px 12px;
            border-radius: 20px;
            font-size: 0.75rem;
            font-weight: 500;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .status-dot {
            width: 6px; height: 6px; background: var(--sage); border-radius: 50%; animation: pulse-dot 2s infinite;
        }

        @keyframes pulse-dot {
            0%, 100% { opacity: 1; transform: scale(1); }
            50% { opacity: 0.6; transform: scale(0.85); }
        }

        /* ── SCROLL AREA ── */
        .scroll-area {
            flex: 1;
            overflow-y: auto;
            padding: 8px 18px 100px;
            scroll-behavior: smooth;
            scrollbar-width: none;
        }

        .scroll-area::-webkit-scrollbar {
            display: none;
        }

        /* ── SCREENS ── */
        .screen {
            display: none;
        }

        .screen.active {
            display: block;
            animation: screenIn 0.4s var(--ease);
        }

        @keyframes screenIn {
            from { opacity: 0; transform: translateY(12px); }
            to { opacity: 1; transform: translateY(0); }
        }

        /* ── TOAST ── */
        .toast {
            position: absolute;
            top: 68px;
            left: 18px;
            right: 18px;
            background: var(--ink-4);
            border: 1px solid var(--glass-border);
            border-left: 3px solid var(--gold);
            color: var(--text-1);
            padding: 12px 16px;
            font-size: 0.82rem;
            font-weight: 500;
            border-radius: var(--r-sm);
            display: none;
            z-index: 50;
            backdrop-filter: blur(20px);
            animation: toastIn 0.3s var(--spring);
            box-shadow: 0 8px 32px rgba(0,0,0,0.4);
        }

        @keyframes toastIn {
            from { opacity: 0; transform: translateY(-8px) scale(0.97); }
            to { opacity: 1; transform: translateY(0) scale(1); }
        }

        /* ── BOTTOM NAV ── */
        .bottom-nav {
            position: absolute;
            bottom: 0;
            width: 100%;
            background: rgba(19,19,26,0.95);
            backdrop-filter: blur(24px);
            border-top: 1px solid var(--glass-border);
            display: flex;
            justify-content: space-around;
            align-items: center;
            padding: 10px 0 14px;
            z-index: 20;
        }

        .nav-locked {
            display: none !important;
        }

        .nav-btn {
            background: none;
            border: none;
            color: var(--text-3);
            font-size: 0.61rem;
            font-weight: 500;
            font-family: 'DM Sans', sans-serif;
            cursor: pointer;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 4px;
            transition: all 0.25s var(--ease);
            padding: 0 2px;
            letter-spacing: 0.01em;
            text-transform: uppercase;
            position: relative;
            flex: 1;
        }

        .nav-icon {
            width: 32px;
            height: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 10px;
            transition: all 0.25s var(--spring);
        }

        .nav-btn.active .nav-icon {
            background: var(--gold-dim);
        }

        .nav-btn svg {
            width: 16px;
            height: 16px;
            stroke: currentColor;
            fill: none;
            stroke-width: 1.8;
            transition: all 0.25s var(--ease);
        }

        .nav-btn.active {
            color: var(--gold);
        }

        .nav-btn.active svg {
            stroke: var(--gold);
        }

        .nav-add .nav-icon {
            background: var(--gold);
            color: var(--ink);
            width: 34px;
            height: 34px;
            border-radius: 11px;
            box-shadow: 0 4px 16px rgba(201,169,110,0.35);
        }

        .nav-add svg {
            stroke: var(--ink);
        }

        .nav-add {
            color: var(--gold-light);
        }

        .nav-add:hover .nav-icon {
            transform: rotate(90deg) scale(1.08);
        }

        /* ── SECTION LABEL ── */
        .eyebrow {
            font-size: 0.7rem;
            text-transform: uppercase;
            letter-spacing: 0.12em;
            color: var(--text-3);
            font-weight: 600;
            margin-bottom: 14px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .eyebrow::after {
            content: ''; flex: 1; height: 1px; background: var(--glass-border);
        }

        /* ── CARDS ── */
        .event-card {
            background: var(--ink-3);
            border: 1px solid var(--glass-border);
            border-radius: var(--r);
            padding: 18px;
            margin-bottom: 12px;
            cursor: pointer;
            position: relative;
            overflow: hidden;
            transition: all 0.3s var(--ease);
        }

        .event-card::before {
            content: ''; position: absolute; top: 0; left: 0; right: 0; height: 2px; background: linear-gradient(90deg, transparent, var(--gold), transparent); opacity: 0; transition: opacity 0.3s;
        }

        .event-card:hover {
            border-color: rgba(201,169,110,0.2); transform: translateY(-2px); box-shadow: 0 12px 40px rgba(0,0,0,0.4);
        }

        .event-card:hover::before {
            opacity: 1;
        }

        .event-card.joined-card {
            border-color: rgba(126,184,154,0.2);
            background: linear-gradient(135deg, var(--ink-3) 0%, rgba(126,184,154,0.04) 100%);
        }

        .card-header {
            display: flex; justify-content: space-between; align-items: flex-start; margin-bottom: 8px; gap: 10px;
        }

        .card-title {
            font-family: 'Playfair Display', serif; font-size: 1.05rem; font-weight: 600; color: var(--text-1); line-height: 1.3; flex: 1;
        }

        .edit-btn {
            background: var(--glass); border: 1px solid var(--glass-border); color: var(--text-3); width: 30px; height: 30px; border-radius: 8px; cursor: pointer; display: flex; align-items: center; justify-content: center; flex-shrink: 0; transition: all 0.2s var(--ease);
        }

        .edit-btn:hover {
            background: var(--gold-dim); color: var(--gold); border-color: rgba(201,169,110,0.3);
        }

        .card-desc {
            font-size: 0.83rem; color: var(--text-2); line-height: 1.55; margin-bottom: 14px;
        }

        .card-footer {
            display: flex; align-items: center; justify-content: space-between;
        }

        .event-date {
            font-size: 0.78rem; color: var(--gold); font-weight: 600; letter-spacing: 0.01em; display: flex; align-items: center; gap: 5px;
        }

        .event-date svg {
            width: 13px; height: 13px; stroke: currentColor; fill: none; stroke-width: 2;
        }

        .status-chip {
            font-size: 0.72rem; font-weight: 700; padding: 4px 10px; border-radius: 20px; letter-spacing: 0.04em; text-transform: uppercase; background: var(--glass); color: var(--text-3); border: 1px solid var(--glass-border); transition: all 0.2s;
        }

        .status-chip.going {
            background: var(--sage-dim); color: var(--sage); border-color: rgba(126,184,154,0.3);
        }

        /* ── FILTER TABS ── */
        .filter-row {
            display: flex; gap: 8px; margin-bottom: 20px; overflow-x: auto; scrollbar-width: none; padding-bottom: 2px;
        }

        .f-tab {
            background: var(--glass); color: var(--text-2); border: 1px solid var(--glass-border); padding: 7px 16px; border-radius: 20px; font-size: 0.78rem; font-weight: 600; font-family: 'DM Sans', sans-serif; white-space: nowrap; cursor: pointer; transition: all 0.2s var(--ease); letter-spacing: 0.02em;
        }

        .f-tab:hover {
            background: var(--glass-hover); color: var(--text-1);
        }

        .f-tab.active {
            background: var(--gold); color: var(--ink); border-color: transparent; box-shadow: 0 4px 16px rgba(201,169,110,0.3);
        }

        /* ── MEMBER ROWS ── */
        .member-row {
            display: flex; align-items: center; gap: 14px; background: var(--ink-3); border: 1px solid var(--glass-border); padding: 14px 16px; border-radius: var(--r); margin-bottom: 10px; transition: all 0.2s var(--ease);
        }

        .member-row:hover {
            border-color: rgba(201,169,110,0.15); background: var(--ink-4);
        }

        .member-avatar {
            width: 46px; height: 46px; border-radius: 14px; background: var(--ink-4); border: 1px solid var(--glass-border); display: flex; align-items: center; justify-content: center; flex-shrink: 0; overflow: hidden; font-family: 'Playfair Display', serif; font-size: 1.1rem; color: var(--gold);
        }

        .member-avatar img {
            width: 100%; height: 100%; object-fit: cover;
        }

        .member-meta {
            flex: 1; min-width: 0;
        }

        .member-name {
            font-size: 0.9rem; font-weight: 600; color: var(--text-1); white-space: nowrap; overflow: hidden; text-overflow: ellipsis;
        }

        .member-email {
            font-size: 0.75rem; color: var(--text-3); white-space: nowrap; overflow: hidden; text-overflow: ellipsis; margin-top: 2px;
        }

        .role-badge {
            font-size: 0.68rem; font-weight: 700; padding: 3px 9px; border-radius: 6px; background: var(--gold-dim); color: var(--gold); text-transform: uppercase; letter-spacing: 0.05em; border: 1px solid rgba(201,169,110,0.2); white-space: nowrap;
        }

        /* ── FORMS ── */
        .form-group {
            margin-bottom: 18px;
        }

        .form-label {
            display: block; font-size: 0.78rem; font-weight: 600; color: var(--text-2); margin-bottom: 8px; text-transform: uppercase; letter-spacing: 0.08em;
        }

        .form-input {
            width: 100%; background: var(--ink-3); border: 1px solid var(--glass-border); border-radius: var(--r-sm); padding: 13px 14px; font-size: 0.9rem; color: var(--text-1); font-family: 'DM Sans', sans-serif; transition: all 0.2s var(--ease); outline: none; -webkit-appearance: none; color-scheme: dark;
        }

        .form-input::placeholder {
            color: var(--text-3);
        }

        .form-input:focus {
            border-color: rgba(201,169,110,0.4); background: var(--ink-4); box-shadow: 0 0 0 3px rgba(201,169,110,0.08);
        }

        textarea.form-input {
            resize: none; line-height: 1.55;
        }

        /* ── BUTTONS ── */
        .btn-gold {
            width: 100%; background: var(--gold); color: var(--ink); border: none; padding: 15px; border-radius: var(--r); font-family: 'DM Sans', sans-serif; font-size: 0.9rem; font-weight: 700; letter-spacing: 0.04em; text-transform: uppercase; cursor: pointer; transition: all 0.25s var(--ease); display: flex; align-items: center; justify-content: center; gap: 8px; box-shadow: 0 6px 24px rgba(201,169,110,0.25);
        }

        .btn-gold:hover {
            background: var(--gold-light); transform: translateY(-1px); box-shadow: 0 10px 32px rgba(201,169,110,0.35);
        }

        .btn-ghost {
            width: 100%; background: transparent; color: var(--text-2); border: 1px solid var(--glass-border); padding: 14px; border-radius: var(--r); font-family: 'DM Sans', sans-serif; font-size: 0.9rem; font-weight: 600; cursor: pointer; transition: all 0.2s var(--ease); margin-top: 10px;
        }

        .btn-ghost:hover {
            background: var(--glass); color: var(--text-1); border-color: rgba(255,255,255,0.12);
        }

        /* ── SCREEN: GATE ── */
        .gate-hero {
            text-align: center; padding: 12px 0 28px;
        }

        .gate-wordmark {
            font-family: 'Playfair Display', serif; font-size: 2.6rem; font-weight: 700; color: var(--text-1); letter-spacing: -0.03em; line-height: 1; margin-bottom: 4px;
        }

        .gate-wordmark span {
            color: var(--gold);
        }

        .gate-tagline {
            font-size: 0.88rem; color: var(--text-2); font-weight: 300; letter-spacing: 0.01em; line-height: 1.5; margin-top: 8px;
        }

        .gate-divider {
            width: 40px; height: 2px; background: linear-gradient(90deg, transparent, var(--gold), transparent); margin: 18px auto; border-radius: 1px;
        }

        /* ── PROFILE AVATAR ── */
        .avatar-zone {
            text-align: center; margin-bottom: 28px;
        }

        .avatar-ring {
            width: 96px; height: 96px; border-radius: 28px; background: var(--ink-3); border: 1.5px dashed rgba(201,169,110,0.3); margin: 0 auto 12px; display: flex; align-items: center; justify-content: center; overflow: hidden; cursor: pointer; transition: all 0.3s var(--ease); position: relative;
        }

        .avatar-ring:hover {
            border-color: var(--gold); background: var(--gold-dim); transform: scale(1.04);
        }

        .avatar-ring svg {
            color: var(--text-3); transition: all 0.2s;
        }

        .avatar-ring:hover svg {
            color: var(--gold); transform: scale(1.15);
        }

        .avatar-ring img {
            width: 100%; height: 100%; object-fit: cover; display: none;
        }

        .avatar-hint {
            font-size: 0.78rem; color: var(--text-3); font-weight: 500; letter-spacing: 0.02em;
        }

        /* ── PROFILE HEADER ── */
        .profile-header-card {
            background: linear-gradient(135deg, var(--ink-3), var(--ink-4)); border: 1px solid var(--glass-border); border-radius: var(--r); padding: 24px; display: flex; align-items: center; gap: 16px; margin-bottom: 24px;
        }

        .profile-header-avatar {
            width: 64px; height: 64px; border-radius: 18px; background: var(--gold-dim); border: 1px solid rgba(201,169,110,0.3); overflow: hidden; display: flex; align-items: center; justify-content: center; flex-shrink: 0; font-family: 'Playfair Display', serif; font-size: 1.5rem; color: var(--gold);
        }

        .profile-header-avatar img {
            width: 100%; height: 100%; object-fit: cover; display: none;
        }

        .profile-header-info {
            flex: 1;
        }

        .profile-header-name {
            font-family: 'Playfair Display', serif; font-size: 1.1rem; color: var(--text-1); font-weight: 700; margin-bottom: 4px;
        }

        .profile-header-email {
            font-size: 0.78rem; color: var(--text-2);
        }

        /* ── EMPTY STATE ── */
        .empty {
            text-align: center; padding: 48px 20px; color: var(--text-3); font-size: 0.85rem; line-height: 1.6;
        }

        .empty-icon {
            width: 48px; height: 48px; margin: 0 auto 14px; background: var(--ink-3); border: 1px solid var(--glass-border); border-radius: 14px; display: flex; align-items: center; justify-content: center;
        }

        /* ── STATS ROW ── */
        .stats-row {
            display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 20px;
        }

        .stat-card {
            background: var(--ink-3); border: 1px solid var(--glass-border); border-radius: var(--r); padding: 16px; text-align: center;
        }

        .stat-num {
            font-family: 'Playfair Display', serif; font-size: 1.8rem; font-weight: 700; color: var(--gold); line-height: 1; margin-bottom: 4px;
        }

        .stat-label {
            font-size: 0.72rem; color: var(--text-3); text-transform: uppercase; letter-spacing: 0.08em; font-weight: 600;
        }

        .avatar-ring.drag-over {
            border-color: var(--gold); background: var(--gold-dim); transform: scale(1.06); box-shadow: 0 0 0 6px rgba(201,169,110,0.15);
        }

        .avatar-ring.drag-over svg, .avatar-ring.drag-over .hover-label {
            color: var(--gold); opacity: 1;
        }

        .url-row {
            display: flex; gap: 8px; margin-top: 10px; align-items: center;
        }

        .url-input {
            flex: 1; padding: 10px 12px !important; font-size: 0.8rem !important;
        }

        .url-go-btn {
            background: var(--gold); color: var(--ink); border: none; width: 38px; height: 38px; border-radius: var(--r-sm); font-size: 1.1rem; font-weight: 700; cursor: pointer; flex-shrink: 0; transition: all 0.2s var(--ease); display: flex; align-items: center; justify-content: center;
        }

        .url-go-btn:hover {
            background: var(--gold-light); transform: scale(1.05);
        }

        /* ── UPLOAD OVERLAY ── */
        .drop-overlay {
            position: absolute; inset: 0; background: rgba(10,10,15,0.85); backdrop-filter: blur(6px); display: none; align-items: center; justify-content: center; flex-direction: column; gap: 12px; z-index: 200; border-radius: 32px; border: 2px dashed var(--gold); pointer-events: none;
        }

        .drop-overlay.active {
            display: flex;
        }

        .drop-text {
            font-family: 'Playfair Display', serif; font-size: 1.1rem; color: var(--gold); font-weight: 600;
        }

        .drop-sub {
            font-size: 0.8rem; color: var(--text-2);
        }

        .avatar-ring.has-photo {
            border-style: solid; border-color: rgba(201,169,110,0.5);
        }

        .avatar-ring.uploading {
            border-color: var(--gold); animation: ring-pulse 0.9s var(--ease) infinite alternate;
        }

        @keyframes ring-pulse {
            from { box-shadow: 0 0 0 0 rgba(201,169,110,0.3); }
            to { box-shadow: 0 0 0 10px rgba(201,169,110,0); }
        }

        .remove-photo-btn {
            display: none; margin-top: 8px; background: none; border: 1px solid rgba(201,101,110,0.3); color: var(--rose); font-size: 0.74rem; font-family: 'DM Sans', sans-serif; font-weight: 600; padding: 5px 14px; border-radius: 20px; cursor: pointer; letter-spacing: 0.03em; transition: all 0.2s;
        }

        .remove-photo-btn:hover {
            background: rgba(201,101,110,0.1); border-color: var(--rose);
        }

        .upload-progress {
            height: 2px; background: var(--gold-dim); border-radius: 1px; margin-top: 10px; overflow: hidden; display: none; width: 96px; margin-left: auto; margin-right: auto;
        }

        .upload-progress-bar {
            height: 100%; background: linear-gradient(90deg, var(--gold), var(--gold-light)); border-radius: 1px; width: 0%; transition: width 0.4s var(--ease);
        }

        .avatar-ring .hover-label {
            position: absolute; inset: 0; background: rgba(10,10,15,0.65); display: flex; align-items: center; justify-content: center; border-radius: inherit; opacity: 0; transition: opacity 0.2s; font-size: 0.72rem; font-weight: 700; color: var(--gold); text-transform: uppercase; letter-spacing: 0.08em; pointer-events: none;
        }

        .avatar-ring.has-photo:hover .hover-label {
            opacity: 1;
        }

        /* ── FEED COMPOSER ── */
        .feed-composer {
            background: var(--ink-3);
            border: 1px solid var(--glass-border);
            border-radius: var(--r);
            padding: 14px;
            margin-bottom: 20px;
        }
        
        .feed-composer textarea {
            background: transparent;
            border: none;
            padding: 4px 0;
            font-size: 0.88rem;
            color: var(--text-1);
            resize: none;
            width: 100%;
            outline: none;
            font-family: 'DM Sans', sans-serif;
        }

        .composer-actions {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-top: 10px;
            padding-top: 10px;
            border-top: 1px solid rgba(255,255,255,0.04);
        }

        .composer-btn-small {
            background: none;
            border: none;
            color: var(--text-3);
            font-size: 0.75rem;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 5px;
            font-weight: 500;
        }

        .composer-btn-small:hover, .composer-btn-small.active {
            color: var(--gold);
        }

        .composer-btn-small svg {
            width: 15px; height: 15px; stroke: currentColor; fill: none; stroke-width: 2;
        }

        .btn-post-submit {
            background: var(--gold);
            color: var(--ink);
            border: none;
            padding: 6px 14px;
            border-radius: 8px;
            font-size: 0.75rem;
            font-weight: 700;
            cursor: pointer;
            text-transform: uppercase;
            letter-spacing: 0.02em;
        }

        .btn-post-submit:hover {
            background: var(--gold-light);
        }

        .feed-card {
            background: var(--ink-3);
            border: 1px solid var(--glass-border);
            border-radius: var(--r);
            padding: 16px;
            margin-bottom: 14px;
        }

        .feed-card-header {
            display: flex;
            align-items: center;
            gap: 10px;
            margin-bottom: 10px;
        }

        .feed-avatar {
            width: 34px; height: 34px; border-radius: 10px; background: var(--gold-dim); color: var(--gold); font-family: 'Playfair Display', serif; font-size: 0.9rem; font-weight: 700; display: flex; align-items: center; justify-content: center; overflow: hidden;
        }

        .feed-avatar img { width: 100%; height: 100%; object-fit: cover; }

        .feed-author-meta { flex: 1; }
        .feed-author-name { font-size: 0.82rem; font-weight: 600; color: var(--text-1); }
        .feed-post-time { font-size: 0.7rem; color: var(--text-3); margin-top: 1px; }

        .feed-body {
            font-size: 0.85rem; color: var(--text-2); line-height: 1.5; word-break: break-word;
        }

        .feed-attached-img {
            width: 100%; max-height: 160px; object-fit: cover; border-radius: var(--r-sm); border: 1px solid var(--glass-border); margin-top: 10px;
        }

        .feed-actions-bar {
            display: flex; gap: 16px; margin-top: 14px; padding-top: 10px; border-top: 1px solid rgba(255,255,255,0.03);
        }

        .action-tab {
            background: none; border: none; color: var(--text-3); font-size: 0.75rem; font-weight: 600; cursor: pointer; display: flex; align-items: center; gap: 5px; transition: color 0.2s;
        }

        .action-tab svg { width: 15px; height: 15px; stroke: currentColor; fill: none; stroke-width: 2; transition: all 0.2s; }
        
        .action-tab.liked { color: var(--rose); }
        .action-tab.liked svg { fill: var(--rose); stroke: var(--rose); transform: scale(1.1); }
        .action-tab:hover { color: var(--text-1); }

        .comments-section {
            margin-top: 12px; padding-top: 10px; border-top: 1px solid rgba(255,255,255,0.03); display: none;
        }
        
        .comments-section.open { display: block; }

        .comment-row {
            display: flex; gap: 8px; margin-bottom: 8px; background: rgba(0,0,0,0.1); padding: 8px; border-radius: 8px;
        }

        .comment-avatar {
            width: 24px; height: 24px; border-radius: 6px; background: var(--glass-hover); color: var(--gold); font-size: 0.7rem; font-weight: 700; display: flex; align-items: center; justify-content: center; overflow: hidden;
        }

        .comment-avatar img { width: 100%; height: 100%; object-fit: cover; }

        .comment-content { flex: 1; font-size: 0.78rem; line-height: 1.4; }
        .comment-user { font-weight: 600; color: var(--text-1); margin-right: 4px; }
        .comment-text { color: var(--text-2); }

        .comment-input-row {
            display: flex; gap: 6px; margin-top: 10px;
        }

        .comment-input-row input {
            flex: 1; background: var(--ink-4); border: 1px solid var(--glass-border); border-radius: 6px; padding: 6px 10px; font-size: 0.78rem; color: var(--text-1); outline: none; font-family: 'DM Sans', sans-serif;
        }

        .comment-submit-btn {
            background: var(--glass-hover); border: 1px solid var(--glass-border); color: var(--gold); border-radius: 6px; padding: 0 10px; font-size: 0.75rem; font-weight: 600; cursor: pointer;
        }
        
        .comment-submit-btn:hover { background: var(--gold-dim); }

        /* ── CALENDAR & CLOCK ── */
        .clock-container {
            background: linear-gradient(135deg, var(--ink-3), var(--ink-4));
            border: 1px solid var(--glass-border);
            border-radius: var(--r);
            padding: 20px;
            text-align: center;
            margin-bottom: 16px;
            box-shadow: 0 8px 24px rgba(0,0,0,0.2);
        }

        .clock-time {
            font-family: 'Playfair Display', serif;
            font-size: 2.4rem;
            font-weight: 700;
            color: var(--gold);
            letter-spacing: 0.02em;
            line-height: 1;
        }

        .clock-date {
            font-size: 0.8rem;
            color: var(--text-2);
            text-transform: uppercase;
            letter-spacing: 0.08em;
            margin-top: 6px;
            font-weight: 500;
        }

        .calendar-box {
            background: var(--ink-3);
            border: 1px solid var(--glass-border);
            border-radius: var(--r);
            padding: 16px;
        }

        .calendar-header {
            display: flex;
            align-items: center;
            justify-content: space-between;
            margin-bottom: 14px;
        }

        .calendar-month {
            font-family: 'Playfair Display', serif;
            font-size: 1.15rem;
            font-weight: 700;
            color: var(--text-1);
        }

        .calendar-nav-btn {
            background: var(--glass);
            border: 1px solid var(--glass-border);
            color: var(--text-2);
            width: 28px;
            height: 28px;
            border-radius: 6px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .calendar-nav-btn:hover {
            background: var(--glass-hover);
            color: var(--text-1);
        }

        .calendar-weekdays {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            text-align: center;
            margin-bottom: 8px;
        }

        .weekday-label {
            font-size: 0.65rem;
            font-weight: 700;
            color: var(--text-3);
            text-transform: uppercase;
        }

        .calendar-days {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 6px;
        }

        .cal-day {
            aspect-ratio: 1;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.8rem;
            color: var(--text-2);
            border-radius: 8px;
            font-weight: 500;
            cursor: pointer;
            position: relative;
            transition: all 0.2s;
        }

        .cal-day:hover {
            background: var(--glass-hover);
            color: var(--text-1);
        }

        .cal-day.empty-day {
            cursor: default;
            pointer-events: none;
        }

        .cal-day.today {
            background: var(--gold-dim);
            color: var(--gold);
            font-weight: 700;
            border: 1px solid rgba(201,169,110,0.3);
        }

        .cal-day.has-event::after {
            content: '';
            position: absolute;
            bottom: 4px;
            width: 4px;
            height: 4px;
            background: var(--sage);
            border-radius: 50%;
        }
    </style>
</head>
<body>

    <!-- Dev Toolbar -->
    <div class="toolbar">
        <button class="tb-btn active" id="tb-gate" onclick="devGo('gate')">① Gate</button>
        <button class="tb-btn" id="tb-dashboard" onclick="devGo('dashboard')">② Events</button>
        <button class="tb-btn" id="tb-feed" onclick="devGo('feed')">③ Feed</button>
        <button class="tb-btn" id="tb-time" onclick="devGo('time')">④ Calendar</button>
        <button class="tb-btn" id="tb-members" onclick="devGo('members')">⑤ Neighbors</button>
        <button class="tb-btn" id="tb-form" onclick="devGo('form')">⑥ New Event</button>
        <button class="tb-btn" id="tb-profile" onclick="devGo('profile')">⑦ Profile</button>
    </div>

    <!-- Device Container -->
    <div class="device" id="device">
        
        <div class="drop-overlay" id="drop-overlay">
            <svg width="36" height="36" fill="none" stroke="var(--gold)" stroke-width="1.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M3 16.5v2.25A2.25 2.25 0 005.25 21h13.5A2.25 2.25 0 0021 18.75V16.5m-13.5-9L12 3m0 0l4.5 4.5M12 3v13.5"/></svg>
            <div class="drop-text">Drop your photo</div>
            <div class="drop-sub">Release to upload</div>
        </div>

        <!-- Header -->
        <div class="app-header" id="app-header">
            <div class="app-logo">Loc<span>a</span>le</div>
            <div class="header-pill" id="header-pill">
                <span class="status-dot"></span>
                <span id="header-label">Registration Required</span>
            </div>
        </div>

        <div class="toast" id="toast"></div>

        <!-- Scroll Content Area -->
        <div class="scroll-area" id="scroll-area">

            <!-- SCREEN: GATE -->
            <div class="screen active" id="screen-gate">
                <div class="gate-hero">
                    <div class="gate-wordmark">Loc<span>a</span>le</div>
                    <div class="gate-tagline">Where neighbors discover,<br>create, and gather.</div>
                    <div class="gate-divider"></div>
                </div>
                <div class="form-group">
                    <label class="form-label" for="reg-name">Full Name</label>
                    <input class="form-input" type="text" id="reg-name" placeholder="Your name">
                </div>
                <div class="form-group">
                    <label class="form-label" for="reg-email">Email Address</label>
                    <input class="form-input" type="email" id="reg-email" placeholder="you@example.com">
                </div>
                <div class="form-group" style="margin-bottom: 24px">
                    <label class="form-label" for="reg-pass">Password</label>
                    <input class="form-input" type="password" id="reg-pass" placeholder="•••••••••">
                </div>
                <button class="btn-gold" onclick="handleSignup()">
                    Join the Community
                    <svg width="16" height="16" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M13.5 4.5L21 12m0 0l-7.5 7.5M21 12H3"/></svg>
                </button>
            </div>

            <!-- SCREEN: DASHBOARD (EVENTS) -->
            <div class="screen" id="screen-dashboard">
                <div class="stats-row" id="stats-row"></div>
                <div class="filter-row">
                    <button class="f-tab active" id="f-all" onclick="setFilter('all')">All Events</button>
                    <button class="f-tab" id="f-joined" onclick="setFilter('joined')">My Schedule</button>
                </div>
                <div class="eyebrow" id="events-eyebrow">Upcoming</div>
                <div id="events-list"></div>
            </div>

            <!-- SCREEN: COMMUNITY FEED -->
            <div class="screen" id="screen-feed">
                <div class="feed-composer">
                    <textarea id="feed-input" rows="2" placeholder="Share updates, links, or items with your neighbors..."></textarea>
                    <div class="form-group url-row" id="feed-img-row" style="display:none; margin: 8px 0 0 0;">
                        <input class="form-input url-input" id="feed-img-url" type="url" placeholder="Paste optional image URL...">
                    </div>
                    <div class="composer-actions">
                        <button class="composer-btn-small" id="feed-img-toggle" onclick="toggleFeedImageInput()">
                            <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M2.25 15.75l5.159-5.159a2.25 2.25 0 013.182 0l5.159 5.159m-1.5-1.5l1.409-1.409a2.25 2.25 0 013.182 0l2.909 2.909m-18 3.75h16.5a1.5 1.5 0 001.5-1.5V6a1.5 1.5 0 00-1.5-1.5H3.75A1.5 1.5 0 002.25 6v12a1.5 1.5 0 001.5 1.5zm10.5-11.25h.008v.008h-.008V8.25zm.375 0a.375.375 0 11-.75 0 .375.375 0 01.75 0z"/></svg>
                            Add Image URL
                        </button>
                        <button class="btn-post-submit" onclick="submitFeedPost()">Post</button>
                    </div>
                </div>
                <div class="eyebrow">Neighborhood Buzz</div>
                <div id="feed-container"></div>
            </div>

            <!-- SCREEN: CALENDAR & CLOCK -->
            <div class="screen" id="screen-time">
                <div class="clock-container">
                    <div class="clock-time" id="live-clock">00:00:00</div>
                    <div class="clock-date" id="live-date">Monday, January 1</div>
                </div>
                <div class="calendar-box">
                    <div class="calendar-header">
                        <div class="calendar-month" id="cal-month-title">Month Year</div>
                        <div style="display: flex; gap: 6px;">
                            <button class="calendar-nav-btn" onclick="changeMonth(-1)">←</button>
                            <button class="calendar-nav-btn" onclick="changeMonth(1)">→</button>
                        </div>
                    </div>
                    <div class="calendar-weekdays">
                        <div class="weekday-label">Su</div>
                        <div class="weekday-label">Mo</div>
                        <div class="weekday-label">Tu</div>
                        <div class="weekday-label">We</div>
                        <div class="weekday-label">Th</div>
                        <div class="weekday-label">Fr</div>
                        <div class="weekday-label">Sa</div>
                    </div>
                    <div class="calendar-days" id="cal-days-grid"></div>
                </div>
            </div>

            <!-- SCREEN: MEMBERS -->
            <div class="screen" id="screen-members">
                <div class="eyebrow" id="members-eyebrow">Directory</div>
                <div id="members-list"></div>
            </div>

            <!-- SCREEN: FORM -->
            <div class="screen" id="screen-form">
                <input type="hidden" id="edit-id">
                <div class="form-group">
                    <label class="form-label" for="evt-title">Event Name</label>
                    <input class="form-input" type="text" id="evt-title" placeholder="e.g. Riverside Cleanup">
                </div>
                <div class="form-group">
                    <label class="form-label" for="evt-date">Date & Time</label>
                    <input class="form-input" type="datetime-local" id="evt-date">
                </div>
                <div class="form-group" style="margin-bottom:24px">
                    <label class="form-label" for="evt-desc">Description</label>
                    <textarea class="form-input" id="evt-desc" rows="4" placeholder="What's happening…"></textarea>
                </div>
                <button class="btn-gold" id="form-cta" onclick="submitEvent()">Publish Event</button>
                <button class="btn-ghost" onclick="go('dashboard')">Cancel</button>
            </div>

            <!-- SCREEN: PROFILE -->
            <div class="screen" id="screen-profile">
                <div class="profile-header-card" id="profile-hero"></div>
                <div class="avatar-zone">
                    <div class="avatar-ring" id="avatar-ring" ondragover="event.preventDefault(); this.classList.add('drag-over')" ondragleave="this.classList.remove('drag-over')" ondrop="handleAvatarDrop(event)">
                        <svg width="28" height="28" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24" id="avatar-icon">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M3 16.5v2.25A2.25 2.25 0 005.25 21h13.5A2.25 2.25 0 0021 18.75V16.5m-13.5-9L12 3m0 0l4.5 4.5M12 3v13.5"/>
                        </svg>
                        <img id="avatar-preview" alt="Profile photo" style="width:100%;height:100%;object-fit:cover;display:none;border-radius:inherit;">
                        <div class="hover-label" id="hover-label">Drop photo</div>
                    </div>
                    <div class="upload-progress" id="upload-progress">
                        <div class="upload-progress-bar" id="upload-bar"></div>
                    </div>
                    <p class="avatar-hint" id="avatar-hint">
                        <svg width="14" height="14" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24" style="vertical-align:-2px;margin-right:4px"><path stroke-linecap="round" stroke-linejoin="round" d="M3 16.5v2.25A2.25 2.25 0 005.25 21h13.5A2.25 2.25 0 0021 18.75V16.5m-13.5-9L12 3m0 0l4.5 4.5M12 3v13.5"/></svg>
                        Drag &amp; drop an image onto the circle
                    </p>
                    <div class="url-row" id="url-row">
                        <input class="form-input url-input" id="url-input" type="url" placeholder="…or paste an image URL">
                        <button class="url-go-btn" onclick="applyImageUrl()">→</button>
                    </div>
                    <button class="remove-photo-btn" id="remove-btn" onclick="removeAvatar()">✕ Remove photo</button>
                </div>
                <div class="form-group">
                    <label class="form-label" for="prof-name">Display Name</label>
                    <input class="form-input" type="text" id="prof-name">
                </div>
                <div class="form-group" style="margin-bottom:24px">
                    <label class="form-label" for="prof-email">Email Address</label>
                    <input class="form-input" type="email" id="prof-email">
                </div>
                <button class="btn-gold" onclick="saveProfile()">Save Profile</button>
            </div>
        </div>

        <!-- Bottom Navigation Bar -->
        <nav class="bottom-nav nav-locked" id="bottom-nav">
            <button class="nav-btn" id="nav-dashboard" onclick="go('dashboard')">
                <div class="nav-icon">
                    <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M2.25 12l8.954-8.955c.44-.439 1.152-.439 1.591 0L21.75 12M4.5 9.75v10.125c0 .621.504 1.125 1.125 1.125H9.75v-4.875c0-.621.504-1.125 1.125-1.125h2.25c.621 0 1.125.504 1.125 1.125V21h4.125c.621 0 1.125-.504 1.125-1.125V9.75"/></svg>
                </div> Events
            </button>
            <button class="nav-btn" id="nav-feed" onclick="go('feed')">
                <div class="nav-icon">
                    <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M7.5 8.25h9m-9 3H12m-9.75 1.51c0 1.6 1.123 2.994 2.707 3.227 1.129.166 2.27.293 3.423.379.35.026.67.21.865.501L12 21l2.755-4.133a1.14 1.14 0 01.865-.501c1.153-.086 2.294-.213 3.423-.379 1.584-.233 2.707-1.626 2.707-3.228V6.741c0-1.602-1.123-2.995-2.707-3.228A48.394 48.394 0 0012 3c-2.392 0-4.744.175-7.043.513C3.373 3.746 2.25 5.14 2.25 6.741v6.018z"/></svg>
                </div> Feed
            </button>
            <button class="nav-btn nav-add" id="nav-form" onclick="openCreateForm()">
                <div class="nav-icon">
                    <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 4.5v15m7.5-7.5h-15"/></svg>
                </div> New
            </button>
            <button class="nav-btn" id="nav-time" onclick="go('time')">
                <div class="nav-icon">
                    <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M6.75 3v2.25M17.25 3v2.25M3 18.75V7.5a2.25 2.25 0 012.25-2.25h13.5A2.25 2.25 0 0121 7.5v11.25m-18 0A2.25 2.25 0 005.25 21h13.5A2.25 2.25 0 0021 18.75m-18 0v-7.5A2.25 2.25 0 015.25 9h13.5A2.25 2.25 0 0121 11.25v7.5m-9-6h.008v.008H12v-.008zM12 15h.008v.008H12V15zm0 2.25h.008v.008H12v-.008zM9.75 15h.008v.008H9.75V15zm0 2.25h.008v.008H9.75v-.008zM7.5 15h.008v.008H7.5V15zm0 2.25h.008v.008H7.5v-.008zm6.75-4.5h.008v.008h-.008v-.008zm0 2.25h.008v.008h-.008V15zm0 2.25h.008v.008h-.008v-.008zm2.25-4.5h.008v.008H16.5v-.008zm0 2.25h.008v.008H16.5V15z"/></svg>
                </div> Schedule
            </button>
            <button class="nav-btn" id="nav-members" onclick="go('members')">
                <div class="nav-icon">
                    <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15 19.128a9.38 9.38 0 002.625.372 9.337 9.337 0 004.121-.952 4.125 4.125 0 00-7.533-2.493M15 19.128v-.003c0-1.113-.285-2.16-.786-3.07M15 19.128v.106A12.318 12.318 0 018.624 21c-2.331 0-4.512-.645-6.374-1.766l-.001-.109a6.375 6.375 0 0111.964-3.07M12 6.375a3.375 3.375 0 11-6.75 0 3.375 3.375 0 016.75 0zm8.25 2.25a2.625 2.625 0 11-5.25 0 2.625 2.625 0 015.25 0z"/></svg>
                </div> Neighbors
            </button>
            <button class="nav-btn" id="nav-profile" onclick="go('profile')">
                <div class="nav-icon">
                    <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15.75 6a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0zM4.501 20.118a7.5 7.5 0 0114.998 0A17.933 17.933 0 0112 21.75c-2.676 0-5.216-.584-7.499-1.632z"/></svg>
                </div> Profile
            </button>
        </nav>
    </div>

    <script>
        /* ── STATE ── */
        const S = {
            screen: 'gate',
            filter: 'all',
            isRegistered: false,
            user: { name: 'Guest', email: '', avatar: null },
            members: [
                { name: 'Clara Jenkins', email: 'clara@locale.io', avatar: null, role: 'Neighbor' },
                { name: 'David Cross', email: 'david@locale.io', avatar: null, role: 'Neighbor' }
            ],
            currentCalDate: new Date(),
            events: [
                { id: 'e1', title: 'Downtown Farmers Market', date: '2026-06-05T09:00', desc: 'Support local growers and craft artisans. Bring reusable bags and an appetite for discovery.', joined: true },
                { id: 'e2', title: 'Community Tree Planting', date: '2026-06-12T10:30', desc: 'Help restore the northern park canopy. Gloves, shovels, and refreshments provided by the borough.', joined: false },
                { id: 'e3', title: 'Evening Jazz & Lights', date: '2026-07-04T19:00', desc: 'Open-air concert along the riverfront promenade. Bring a blanket and your best company.', joined: false }
            ],
            posts: [
                {
                    id: 'p1',
                    author: 'Clara Jenkins',
                    authorEmail: 'clara@locale.io',
                    avatar: null,
                    text: 'Does anyone have a tall ladder I could borrow tomorrow afternoon? Just trying to clean out some dead leaves from the side awning.',
                    time: '2 hours ago',
                    likes: 4,
                    likedByMe: false,
                    comments: [
                        { user: 'Marcus Vance', text: 'I have an 8ft A-frame extension ladder you can grab over on Elm St!' }
                    ]
                },
                {
                    id: 'p2',
                    author: 'David Cross',
                    authorEmail: 'david@locale.io',
                    avatar: null,
                    text: 'Spotted some gorgeous blossoms on our morning walk through the valley trails today! Highly recommend heading out before the weekend rain.',
                    time: '5 hours ago',
                    image: 'https://images.unsplash.com/photo-1526047932273-341f2a7631f9?w=500&auto=format&fit=crop&q=60',
                    likes: 12,
                    likedByMe: true,
                    comments: []
                },
                {
                    id: 'p3',
                    author: 'Unregistered Entity',
                    authorEmail: 'hidden@ghost.com',
                    avatar: null,
                    text: 'This post is hidden because the author email is not inside the registered members array.',
                    time: '1 day ago',
                    likes: 0,
                    likedByMe: false,
                    comments: []
                }
            ]
        };

        /* ── LOCALSTORAGE PERSISTENCE ENGINE ── */
        function saveStateToStorage() {
            localStorage.setItem('locale_app_state_v1', JSON.stringify({
                isRegistered: S.isRegistered,
                user: S.user,
                members: S.members,
                events: S.events,
                posts: S.posts
            }));
        }

        function loadStateFromStorage() {
            const data = localStorage.getItem('locale_app_state_v1');
            if (data) {
                try {
                    const parsed = JSON.parse(data);
                    S.isRegistered = parsed.isRegistered || false;
                    S.user = parsed.user || S.user;
                    S.members = parsed.members || S.members;
                    S.events = parsed.events || S.events;
                    S.posts = parsed.posts || S.posts;
                } catch (err) {
                    console.error("Failed parsing persistent state storage.", err);
                }
            }
        }

        /* ── LIVE CLOCK LOGIC ── */
        function initClock() {
            setInterval(() => {
                const now = new Date();
                const clockEl = document.getElementById('live-clock');
                const dateEl = document.getElementById('live-date');
                if (clockEl) {
                    clockEl.textContent = now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', second: '2-digit', hour12: false });
                }
                if (dateEl) {
                    dateEl.textContent = now.toLocaleDateString([], { weekday: 'long', month: 'long', day: 'numeric' });
                }
            }, 1000);
        }

        /* ── CALENDAR GRID BUILDER ── */
        function changeMonth(dir) {
            S.currentCalDate.setMonth(S.currentCalDate.getMonth() + dir);
            renderCalendarGrid();
        }

        function renderCalendarGrid() {
            const grid = document.getElementById('cal-days-grid');
            const title = document.getElementById('cal-month-title');
            if (!grid || !title) return;

            grid.innerHTML = '';
            const year = S.currentCalDate.getFullYear();
            const month = S.currentCalDate.getMonth();

            title.textContent = S.currentCalDate.toLocaleString([], { month: 'long', year: 'numeric' });

            const firstDayIndex = new Date(year, month, 1).getDay();
            const totalDays = new Date(year, month + 1, 0).getDate();
            const today = new Date();

            for (let i = 0; i < firstDayIndex; i++) {
                const empty = document.createElement('div');
                empty.className = 'cal-day empty-day';
                grid.appendChild(empty);
            }

            for (let day = 1; day <= totalDays; day++) {
                const dayEl = document.createElement('div');
                dayEl.className = 'cal-day';
                dayEl.textContent = day;

                if (day === today.getDate() && month === today.getMonth() && year === today.getFullYear()) {
                    dayEl.classList.add('today');
                }

                const currentIsodate = `${year}-${String(month + 1).padStart(2, '0')}-${String(day).padStart(2, '0')}`;
                const hasMatch = S.events.some(e => e.date.startsWith(currentIsodate));
                if (hasMatch) {
                    dayEl.classList.add('has-event');
                }

                dayEl.onclick = () => {
                    const foundEvents = S.events.filter(e => e.date.startsWith(currentIsodate));
                    if (foundEvents.length) {
                        toast(`Day has ${foundEvents.length} event(s). Checking Schedule tab!`, 'sage');
                        setFilter('all');
                        go('dashboard');
                    } else {
                        toast(`No events scheduled for ${S.currentCalDate.toLocaleString([], { month: 'short' })} ${day}.`);
                    }
                };

                grid.appendChild(dayEl);
            }
        }

        /* ── INTERCEPTIVE NAVIGATION GUARD ── */
        function go(id) {
            if (!S.isRegistered && id !== 'gate') {
                toast('Please complete registration to view other pages.');
                id = 'gate';
            }

            S.screen = id;
            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            document.getElementById('screen-' + id).classList.add('active');
            document.getElementById('scroll-area').scrollTop = 0;
            
            document.querySelectorAll('.tb-btn').forEach(b => b.classList.remove('active'));
            const tb = document.getElementById('tb-' + id);
            if (tb) tb.classList.add('active');

            const nav = document.getElementById('bottom-nav');
            const hpill = document.getElementById('header-pill');

            if (id === 'gate') {
                nav.classList.add('nav-locked');
                hpill.style.display = 'flex';
                document.getElementById('header-label').textContent = 'Registration Required';
            } else {
                nav.classList.remove('nav-locked');
                document.querySelectorAll('.nav-btn').forEach(n => n.classList.remove('active'));
                const nb = document.getElementById('nav-' + id);
                if (nb) nb.classList.add('active');
                
                document.getElementById('header-label').textContent = S.user.name.split(' ')[0] || 'Welcome';
                
                if (id === 'dashboard') renderDashboard();
                if (id === 'feed') renderFeed();
                if (id === 'time') renderCalendarGrid();
                if (id === 'members') renderMembers();
                if (id === 'profile') renderProfile();
            }
        }

        /* ── DEV SHORTCUT INTERCEPTOR ── */
        function devGo(id) {
            if (!S.isRegistered && id !== 'gate') {
                toast('Action locked. Please register inside the app interface first.');
                return;
            }
            if (id === 'form') {
                document.getElementById('edit-id').value = '';
                document.getElementById('evt-title').value = '';
                document.getElementById('evt-date').value = '';
                document.getElementById('evt-desc').value = '';
                document.getElementById('form-cta').textContent = 'Publish Event';
            }
            go(id);
        }

        /* ── TOAST ── */
        let toastTimer;
        function toast(msg, type = 'gold') {
            const el = document.getElementById('toast');
            el.textContent = msg;
            el.style.borderLeftColor = type === 'sage' ? 'var(--sage)' : 'var(--gold)';
            el.style.display = 'block';
            clearTimeout(toastTimer);
            toastTimer = setTimeout(() => { el.style.display = 'none'; }, 2800);
        }

        /* ── SIGNUP HANDLING ── */
        function handleSignup() {
            const n = document.getElementById('reg-name').value.trim();
            const e = document.getElementById('reg-email').value.trim();
            const p = document.getElementById('reg-pass').value.trim();

            if (!n || !e || !p) {
                toast('Please complete all fields to sign up.');
                return;
            }

            S.user = { name: n, email: e, avatar: null };
            S.isRegistered = true; 

            if (!S.members.find(m => m.email === e)) {
                S.members.push({ name: n, email: e, avatar: null, role: 'New Neighbor' });
            }

            document.getElementById('reg-name').value = '';
            document.getElementById('reg-email').value = '';
            document.getElementById('reg-pass').value = '';

            saveStateToStorage();
            toast('Welcome to Locale, ' + n.split(' ')[0] + '!', 'sage');
            go('dashboard');
        }

        /* ── DASHBOARD (EVENTS) ── */
        function setFilter(f) {
            S.filter = f;
            ['all','joined'].forEach(k => document.getElementById('f-'+k).classList.toggle('active', k === f));
            renderDashboard();
        }

        function renderDashboard() {
            const joined = S.events.filter(e => e.joined).length;
            document.getElementById('stats-row').innerHTML = `
                <div class="stat-card"><div class="stat-num">${S.events.length}</div><div class="stat-label">Events</div></div>
                <div class="stat-card"><div class="stat-num">${joined}</div><div class="stat-label">Attending</div></div>
            `;
            
            let list = S.filter === 'joined' ? S.events.filter(e => e.joined) : S.events;
            document.getElementById('events-eyebrow').textContent = S.filter === 'joined' ? `My Schedule · ${list.length}` : 'Upcoming';
            
            const el = document.getElementById('events-list');
            el.innerHTML = '';

            if (!list.length) {
                el.innerHTML = `<div class="empty"><div class="empty-icon"><svg width="22" height="22" fill="none" stroke="var(--text-3)" stroke-width="1.8" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M6.75 3v2.25M17.25 3v2.25M3 18.75V7.5a2.25 2.25 0 012.25-2.25h13.5A2.25 2.25 0 0121 7.5v11.25"/></svg></div>No events here yet.</div>`;
                return;
            }

            list.forEach((ev, i) => {
                const d = new Date(ev.date);
                const fmt = d.toLocaleString([], { month: 'short', day: 'numeric', hour: '2-digit', minute: '2-digit' });
                const card = document.createElement('div');
                card.className = 'event-card' + (ev.joined ? ' joined-card' : '');
                card.style.animationDelay = (i * 0.06) + 's';
                card.innerHTML = `
                    <div class="card-header">
                        <div class="card-title">${ev.title}</div>
                        <button class="edit-btn" onclick="editEvent(event,'${ev.id}')">
                            <svg width="13" height="13" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M16.862 4.487l1.687-1.688a1.875 1.875 0 112.652 2.652L6.832 19.82a4.5 4.5 0 01-1.897 1.13l-2.685.8.8-2.685a4.5 4.5 0 011.13-1.897L16.863 4.487z"/></svg>
                        </button>
                    </div>
                    <div class="card-desc">${ev.desc}</div>
                    <div class="card-footer">
                        <div class="event-date">
                            <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M6.75 3v2.25M17.25 3v2.25M3 18.75V7.5a2.25 2.25 0 012.25-2.25h13.5A2.25 2.25 0 0121 7.5v11.25"/></svg>
                            ${fmt}
                        </div>
                        <span class="status-chip ${ev.joined ? 'going' : ''}">${ev.joined ? '✓ Going' : 'Join'}</span>
                    </div>
                `;
                card.onclick = (e) => { if (!e.target.closest('.edit-btn')) toggleJoin(ev.id); };
                el.appendChild(card);
            });
        }

        function toggleJoin(id) {
            const ev = S.events.find(e => e.id === id);
            ev.joined = !ev.joined;
            saveStateToStorage();
            toast(ev.joined ? 'Added to your schedule!' : 'Removed from schedule.', ev.joined ? 'sage' : 'gold');
            renderDashboard();
        }

        /* ── COMMUNITY FEED LOGIC ── */
        function toggleFeedImageInput() {
            const row = document.getElementById('feed-img-row');
            const btn = document.getElementById('feed-img-toggle');
            if (row.style.display === 'none') {
                row.style.display = 'flex';
                btn.classList.add('active');
            } else {
                row.style.display = 'none';
                document.getElementById('feed-img-url').value = '';
                btn.classList.remove('active');
            }
        }

        function submitFeedPost() {
            const txt = document.getElementById('feed-input').value.trim();
            const imgUrl = document.getElementById('feed-img-url').value.trim();
            
            if (!txt) {
                toast('Please enter some text for your post.');
                return;
            }

            const newPost = {
                id: 'p_' + Date.now(),
                author: S.user.name,
                authorEmail: S.user.email,
                avatar: S.user.avatar,
                text: txt,
                time: 'Just now',
                image: imgUrl || null,
                likes: 0,
                likedByMe: false,
                comments: []
            };

            S.posts.unshift(newPost);
            document.getElementById('feed-input').value = '';
            document.getElementById('feed-img-url').value = '';
            document.getElementById('feed-img-row').style.display = 'none';
            document.getElementById('feed-img-toggle').classList.remove('active');
            
            saveStateToStorage();
            toast('Post shared to community feed!', 'sage');
            renderFeed();
        }

        function toggleLikePost(postId) {
            const post = S.posts.find(p => p.id === postId);
            if (!post) return;
            
            post.likedByMe = !post.likedByMe;
            post.likes += post.likedByMe ? 1 : -1;
            saveStateToStorage();
            renderFeed();
        }

        function toggleCommentsArea(postId) {
            const el = document.getElementById(`comments-${postId}`);
            el.classList.toggle('open');
        }

        function submitComment(postId) {
            const input = document.getElementById(`comment-in-${postId}`);
            const text = input.value.trim();
            if (!text) return;

            const post = S.posts.find(p => p.id === postId);
            if (!post) return;

            post.comments.push({
                user: S.user.name,
                text: text
            });

            input.value = '';
            saveStateToStorage();
            renderFeed();
            document.getElementById(`comments-${postId}`).classList.add('open');
        }

        function renderFeed() {
            const container = document.getElementById('feed-container');
            container.innerHTML = '';

            const visiblePosts = S.posts.filter(post => {
                return S.members.some(member => member.email === post.authorEmail);
            });

            if (!visiblePosts.length) {
                container.innerHTML = '<div class="empty">No conversations from registered members yet. Start the buzz!</div>';
                return;
            }

            visiblePosts.forEach(post => {
                const initial = post.author.charAt(0).toUpperCase();
                const avatarHTML = post.avatar 
                    ? `<div class="feed-avatar"><img src="${post.avatar}" alt="${post.author}"></div>`
                    : `<div class="feed-avatar">${initial}</div>`;

                const mediaHTML = post.image 
                    ? `<img class="feed-attached-img" src="${post.image}" alt="Attached content">` 
                    : '';

                let commentsHTML = '';
                post.comments.forEach(c => {
                    commentsHTML += `
                        <div class="comment-row">
                            <div class="comment-avatar">${c.user.charAt(0).toUpperCase()}</div>
                            <div class="comment-content">
                                <span class="comment-user">${c.user}</span>
                                <span class="comment-text">${c.text}</span>
                            </div>
                        </div>
                    `;
                });

                const card = document.createElement('div');
                card.className = 'feed-card';
                card.innerHTML = `
                    <div class="feed-card-header">
                        ${avatarHTML}
                        <div class="feed-author-meta">
                            <div class="feed-author-name">${post.author}</div>
                            <div class="feed-post-time">${post.time}</div>
                        </div>
                    </div>
                    <div class="feed-body">
                        ${post.text}
                        ${mediaHTML}
                    </div>
                    <div class="feed-actions-bar">
                        <button class="action-tab ${post.likedByMe ? 'liked' : ''}" onclick="toggleLikePost('${post.id}')">
                            <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M21 8.25c0-2.485-2.099-4.5-4.688-4.5-1.935 0-3.597 1.126-4.312 2.733-.715-1.607-2.377-2.733-4.313-2.733C5.1 3.75 3 5.765 3 8.25c0 7.22 9 12 9 12s9-4.78 9-12z"/></svg>
                            ${post.likes} ${post.likes === 1 ? 'Like' : 'Likes'}
                        </button>
                        <button class="action-tab" onclick="toggleCommentsArea('${post.id}')">
                            <svg viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 20.25c4.97 0 9-3.694 9-8.25s-4.03-8.25-9-8.25S3 7.444 3 12c0 2.104.859 4.023 2.273 5.48.432.447.74 1.04.586 1.641a4.483 4.483 0 01-.923 1.785A5.969 5.969 0 006 21c1.282 0 2.47-.402 3.445-1.087.081-.058.174-.088.27-.088.26 0 .518.062.75.175.488.238.994.38 1.535.38z"/></svg>
                            ${post.comments.length} ${post.comments.length === 1 ? 'Comment' : 'Comments'}
                        </button>
                    </div>
                    
                    <div class="comments-section" id="comments-${post.id}">
                        <div class="comments-list-wrapper">${commentsHTML}</div>
                        <div class="comment-input-row">
                            <input type="text" id="comment-in-${post.id}" placeholder="Write a reply...">
                            <button class="comment-submit-btn" onclick="submitComment('${post.id}')">Reply</button>
                        </div>
                    </div>
                `;
                container.appendChild(card);
            });
        }

        /* ── MEMBERS DIRECTORY ── */
        function renderMembers() {
            const el = document.getElementById('members-list');
            document.getElementById('members-eyebrow').textContent = `Registered · ${S.members.length}`;
            el.innerHTML = '';

            if (!S.members.length) {
                el.innerHTML = `<div class="empty"><div class="empty-icon"><svg width="22" height="22" fill="none" stroke="var(--text-3)" stroke-width="1.8" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M15 19.128a9.38 9.38 0 002.625.372 9.337 9.337 0 004.121-.952 4.125 4.125 0 00-7.533-2.493M15 19.128v-.003c0-1.113-.285-2.16-.786-3.07M15 19.128v.106A12.318 12.318 0 018.624 21"/></svg></div>No neighbors yet.</div>`;
                return;
            }

            S.members.forEach((m, i) => {
                const row = document.createElement('div');
                row.className = 'member-row';
                row.style.animationDelay = (i * 0.05) + 's';
                const initial = m.name.charAt(0).toUpperCase();
                const avatarHTML = m.avatar ? `<div class="member-avatar"><img src="${m.avatar}" alt="${m.name}"></div>` : `<div class="member-avatar">${initial}</div>`;
                
                row.innerHTML = `
                    ${avatarHTML}
                    <div class="member-meta">
                        <div class="member-name">${m.name}</div>
                        <div class="member-email">${m.email}</div>
                    </div>
                    <span class="role-badge">${m.role}</span>
                `;
                el.appendChild(row);
            });
        }

        /* ── EVENT CREATION / EDITING FORMS ── */
        function openCreateForm() {
            document.getElementById('edit-id').value = '';
            document.getElementById('evt-title').value = '';
            document.getElementById('evt-date').value = '';
            document.getElementById('evt-desc').value = '';
            document.getElementById('form-cta').textContent = 'Publish Event';
            go('form');
        }

        function editEvent(e, id) {
            e.stopPropagation();
            
            const pinAttempt = prompt("Enter password to edit this event:");
            if (pinAttempt !== "7094") {
                toast("Incorrect password. Edit denied.", "rose");
                return;
            }

            const ev = S.events.find(x => x.id === id);
            if (!ev) return;
            document.getElementById('edit-id').value = ev.id;
            document.getElementById('evt-title').value = ev.title;
            document.getElementById('evt-date').value = ev.date;
            document.getElementById('evt-desc').value = ev.desc;
            document.getElementById('form-cta').textContent = 'Save Changes';
            go('form');
        }

        function submitEvent() {
            const title = document.getElementById('evt-title').value.trim();
            const date = document.getElementById('evt-date').value;
            const desc = document.getElementById('evt-desc').value.trim();

            if (!title || !date || !desc) {
                toast('Please fill all fields.');
                return;
            }

            const eid = document.getElementById('edit-id').value;
            if (eid) {
                const ev = S.events.find(e => e.id === eid);
                Object.assign(ev, { title, date, desc });
                toast('Event updated!', 'sage');
            } else {
                S.events.unshift({ id: 'e' + Date.now(), title, date, desc, joined: true });
                toast('Event published!', 'sage');
            }
            saveStateToStorage();
            go('dashboard');
        }

        /* ── PROFILE SCREEN ── */
        function renderProfile() {
            const { name, email, avatar } = S.user;
            const initial = name.charAt(0).toUpperCase();
            const heroAv = avatar ? `<div class="profile-header-avatar"><img src="${avatar}" style="display:block;width:100%;height:100%;object-fit:cover;border-radius:inherit;"></div>` : `<div class="profile-header-avatar">${initial}</div>`;
            
            document.getElementById('profile-hero').innerHTML = `
                ${heroAv}
                <div class="profile-header-info">
                    <div class="profile-header-name">${name}</div>
                    <div class="profile-header-email">${email}</div>
                </div>
            `;
            document.getElementById('prof-name').value = name;
            document.getElementById('prof-email').value = email;
            syncAvatarUI();
        }

        /* ── AVATAR UPLOAD HANDLING ── */
        function handleAvatarDrop(e) {
            e.preventDefault();
            document.getElementById('avatar-ring').classList.remove('drag-over');
            const file = e.dataTransfer && e.dataTransfer.files && e.dataTransfer.files[0];
            if (!file) return;
            if (!file.type.startsWith('image/')) {
                toast('Drop an image file (JPG, PNG, GIF…)');
                return;
            }
            readFileIntoAvatar(file);
        }

        // Base64 Reader Engine
        function readFileIntoAvatar(file) {
            const ring = document.getElementById('avatar-ring');
            const prog = document.getElementById('upload-progress');
            const bar = document.getElementById('upload-bar');
            
            ring.classList.add('uploading');
            prog.style.display = 'block';
            bar.style.width = '0%';
            
            setTimeout(() => bar.style.width = '60%', 60);
            
            const reader = new FileReader();
            reader.onload = ev => {
                bar.style.width = '100%';
                setTimeout(() => {
                    S.user.avatar = ev.target.result;
                    ring.classList.remove('uploading');
                    prog.style.display = 'none';
                    bar.style.width = '0%';
                    syncAvatarUI();
                    toast('Photo set! Save profile to apply.', 'sage');
                }, 300);
            };
            reader.onerror = () => {
                ring.classList.remove('uploading');
                prog.style.display = 'none';
                toast('Could not read file.');
            };
            reader.readAsDataURL(file);
        }

        function applyImageUrl() {
            const url = document.getElementById('url-input').value.trim();
            if (!url) return;

            const img = new Image();
            img.crossOrigin = 'anonymous';
            img.onload = () => {
                try {
                    const c = document.createElement('canvas');
                    c.width = img.naturalWidth;
                    c.height = img.naturalHeight;
                    c.getContext('2d').drawImage(img, 0, 0);
                    S.user.avatar = c.toDataURL('image/jpeg', 0.9);
                } catch {
                    S.user.avatar = url;
                }
                document.getElementById('url-input').value = '';
                syncAvatarUI();
                toast('Photo set! Save profile to apply.', 'sage');
            };
            img.onerror = () => toast('Could not load URL. Use direct image link ending in .jpg/.png');
            img.src = url;
        }

        function removeAvatar() {
            S.user.avatar = null;
            syncAvatarUI();
            toast('Photo removed.');
        }

        function syncAvatarUI() {
            const img = document.getElementById('avatar-preview');
            const icon = document.getElementById('avatar-icon');
            const ring = document.getElementById('avatar-ring');
            const hint = document.getElementById('avatar-hint');
            const rmBtn = document.getElementById('remove-btn');
            const urlRow = document.getElementById('url-row');
            const hLabel = document.getElementById('hover-label');

            if (S.user.avatar) {
                img.src = S.user.avatar;
                img.style.display = 'block';
                icon.style.display = 'none';
                ring.classList.add('has-photo');
                hint.style.display = 'none';
                urlRow.style.display = 'none';
                rmBtn.style.display = 'inline-block';
                hLabel.textContent = 'Drop to change';
            } else {
                img.src = '';
                img.style.display = 'none';
                icon.style.display = 'block';
                ring.classList.remove('has-photo');
                hint.style.display = 'block';
                urlRow.style.display = 'flex';
                rmBtn.style.display = 'none';
                hLabel.textContent = 'Drop photo';
            }
        }

        (function setupDeviceDrop() {
            const device = document.getElementById('device');
            const overlay = document.getElementById('drop-overlay');
            let dc = 0;
            device.addEventListener('dragenter', e => {
                if ([...e.dataTransfer.types].includes('Files')) {
                    dc++; overlay.classList.add('active');
                }
            });
            device.addEventListener('dragleave', () => {
                if (--dc <= 0) { dc=0; overlay.classList.remove('active'); }
            });
            device.addEventListener('dragover', e => e.preventDefault());
            device.addEventListener('drop', e => {
                e.preventDefault();
                dc=0; overlay.classList.remove('active');
                const file = e.dataTransfer.files && e.dataTransfer.files[0];
                if (!file || !file.type.startsWith('image/')) return;
                if (S.screen !== 'profile') go('profile');
                readFileIntoAvatar(file);
            });
        })();

        function saveProfile() {
            const oldEmail = S.user.email;
            S.user.name = document.getElementById('prof-name').value.trim() || S.user.name;
            S.user.email = document.getElementById('prof-email').value.trim() || S.user.email;
            
            S.posts.forEach(p => {
                if (p.authorEmail === oldEmail) {
                    p.author = S.user.name;
                    p.avatar = S.user.avatar;
                }
            });

            S.members = S.members.map(m => m.email === oldEmail 
                ? { ...m, name: S.user.name, email: S.user.email, avatar: S.user.avatar } 
                : m
            );
            
            document.getElementById('header-label').textContent = S.user.name.split(' ')[0];
            saveStateToStorage();
            toast('Profile saved!', 'sage');
            go('dashboard');
        }

        window.addEventListener('DOMContentLoaded', () => {
            loadStateFromStorage();
            initClock();
            if (S.isRegistered) {
                go('dashboard');
            } else {
                go('gate');
            }
        });
    </script>
</body>
</html>
