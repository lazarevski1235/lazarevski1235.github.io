<!DOCTYPE html>
<html lang="mk">
<head>
    <meta charset="UTF-8">
    <title>Поднеси материјали</title>
    <style>
        body { font-family: sans-serif; max-width: 520px; margin: 2rem auto; padding: 0 1rem; color: #111; }
        h2 { font-size: 20px; font-weight: 500; margin-bottom: 1.5rem; }
        .field-label { font-size: 13px; color: #666; display: block; margin: 1rem 0 6px; }

        .diff-group { display: flex; gap: 8px; }
        .diff-btn { flex: 1; padding: 9px 0; border: 1px solid #ccc; border-radius: 8px; background: white; font-size: 14px; color: #555; cursor: pointer; }
        .diff-btn:hover { background: #f5f5f5; }
        .diff-btn.active { border-color: #333; background: #f0f0f0; color: #111; font-weight: 500; }

        /* Syllabus section */
        .syllabus-box { border: 1px solid #e5e5e5; border-radius: 8px; padding: 10px 12px; background: #fafafa; margin-top: 6px; }
        .syllabus-hint { font-size: 12px; color: #999; margin: 0 0 8px; }
        .syllabus-row { display: flex; align-items: center; gap: 8px; background: white; border: 1px solid #e5e5e5; border-radius: 8px; padding: 7px 10px; margin-bottom: 8px; }
        .syllabus-row .file-name { flex: 1; font-size: 13px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .source-badge { font-size: 11px; background: #e8f0fe; color: #1a73e8; border-radius: 4px; padding: 2px 6px; white-space: nowrap; flex-shrink: 0; }
        .attach-row { display: flex; gap: 8px; }
        .attach-btn { flex: 1; padding: 7px 0; border: 1px dashed #ccc; border-radius: 8px; font-size: 13px; color: #555; background: white; cursor: pointer; }
        .attach-btn:hover { background: #f5f5f5; }
        .attach-btn-drive { border-color: #4285f4; color: #4285f4; }
        .attach-btn-drive:hover { background: #e8f0fe; }

        /* Files section */
        .file-list { display: flex; flex-direction: column; gap: 8px; margin-top: 6px; }
        .file-row { display: flex; align-items: center; gap: 8px; background: #f9f9f9; border: 1px solid #e5e5e5; border-radius: 8px; padding: 8px 10px; }
        .file-row .file-name { flex: 1; font-size: 13px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .file-row input[type="file"] { flex: 1; font-size: 13px; border: none; background: none; outline: none; }
        .remove-btn { background: none; border: none; font-size: 18px; color: #999; cursor: pointer; padding: 0 4px; line-height: 1; flex-shrink: 0; }
        .remove-btn:hover { color: #333; }
        .add-row { display: flex; gap: 8px; margin-top: 8px; }
        .add-local-btn { flex: 1; padding: 7px 0; border: 1px dashed #ccc; border-radius: 8px; font-size: 13px; color: #555; background: white; cursor: pointer; }
        .add-local-btn:hover { background: #f5f5f5; }
        .add-drive-btn { flex: 1; padding: 7px 0; border: 1px dashed #4285f4; border-radius: 8px; font-size: 13px; color: #4285f4; background: white; cursor: pointer; }
        .add-drive-btn:hover { background: #e8f0fe; }

        textarea { width: 100%; box-sizing: border-box; border: 1px solid #ddd; border-radius: 8px; padding: 9px 10px; font-size: 14px; font-family: inherit; resize: vertical; min-height: 90px; margin-top: 6px; }

        #submit-btn { width: 100%; margin-top: 1.25rem; padding: 11px; background: white; border: 1px solid #333; border-radius: 8px; font-size: 15px; font-weight: 500; cursor: pointer; }
        #submit-btn:hover { background: #f5f5f5; }

        #status { margin-top: 1rem; font-size: 14px; color: #555; text-align: center; }

        /* Google Drive picker modal */
        #drive-overlay { display: none; position: fixed; inset: 0; background: rgba(0,0,0,0.4); z-index: 999; align-items: center; justify-content: center; }
        #drive-overlay.open { display: flex; }
        .drive-modal { background: white; border-radius: 12px; width: 420px; max-width: 95vw; max-height: 520px; display: flex; flex-direction: column; overflow: hidden; box-shadow: 0 8px 32px rgba(0,0,0,0.18); }
        .drive-modal-header { display: flex; align-items: center; justify-content: space-between; padding: 14px 16px; border-bottom: 1px solid #eee; }
        .drive-modal-header span { font-weight: 500; font-size: 14px; display: flex; align-items: center; gap: 8px; }
        .drive-modal-body { flex: 1; overflow-y: auto; padding: 8px 10px; display: flex; flex-direction: column; gap: 4px; }
        .drive-file-row { display: flex; align-items: center; gap: 8px; padding: 8px 10px; border-radius: 8px; cursor: pointer; border: 1px solid transparent; font-size: 13px; }
        .drive-file-row:hover { background: #f5f5f5; border-color: #e5e5e5; }
        .drive-file-row .fname { flex: 1; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .drive-loading { font-size: 13px; color: #888; padding: 8px 2px; }
    </style>
</head>
<body>

<h2>Поднеси материјали</h2>

<span class="field-label">Ниво на тежина</span>
<div class="diff-group">
    <button class="diff-btn" onclick="selectDiff(this)" data-value="Почетно">Почетно</button>
    <button class="diff-btn" onclick="selectDiff(this)" data-value="Средно">Средно</button>
    <button class="diff-btn" onclick="selectDiff(this)" data-value="Напредно">Напредно</button>
</div>

<!-- SYLLABUS -->
<span class="field-label">Силабус <span style="font-size:11px;color:#aaa;">(референца за структурата на Jupyter тетратката)</span></span>
<div class="syllabus-box">
    <div id="syllabus-display"></div>
    <div class="attach-row">
        <button class="attach-btn" onclick="document.getElementById('syllabus-file-input').click()">+ Локална датотека</button>
        <button class="attach-btn attach-btn-drive" onclick="openDrivePicker('syllabus')">&#9650; Google Drive</button>
    </div>
    <input type="file" id="syllabus-file-input" style="display:none;" accept=".pdf,.doc,.docx,.txt,.ipynb">
</div>

<!-- MATERIALS -->
<span class="field-label">Датотеки</span>
<div class="file-list" id="file-list"></div>
<div class="add-row">
    <button class="add-local-btn" onclick="document.getElementById('files-input').click()">+ Локална датотека</button>
    <button class="add-drive-btn" onclick="openDrivePicker('files')">&#9650; Google Drive</button>
</div>
<input type="file" id="files-input" style="display:none;" multiple>

<span class="field-label">Дополнителни барања</span>
<textarea id="comments" placeholder="..."></textarea>

<button id="submit-btn" onclick="submitForm()">Испрати</button>
<p id="status"></p>

<!-- GOOGLE DRIVE PICKER MODAL -->
<div id="drive-overlay">
    <div class="drive-modal">
        <div class="drive-modal-header">
            <span>
                <svg width="16" height="16" viewBox="0 0 87.3 78" xmlns="http://www.w3.org/2000/svg">
                    <path d="m6.6 66.85 3.85 6.65c.8 1.4 1.95 2.5 3.3 3.3l13.75-23.8h-27.5c0 1.55.4 3.1 1.2 4.5z" fill="#0066da"/>
                    <path d="m43.65 25-13.75-23.8c-1.35.8-2.5 1.9-3.3 3.3l-25.4 44a9.06 9.06 0 0 0-1.2 4.5h27.5z" fill="#00ac47"/>
                    <path d="m73.55 76.8c1.35-.8 2.5-1.9 3.3-3.3l1.6-2.75 7.65-13.25c.8-1.4 1.2-2.95 1.2-4.5h-27.5l5.85 11.5z" fill="#ea4335"/>
                    <path d="m43.65 25 13.75-23.8c-1.35-.8-2.9-1.2-4.5-1.2h-18.5c-1.6 0-3.15.45-4.5 1.2z" fill="#00832d"/>
                    <path d="m59.8 53h-32.3l-13.75 23.8c1.35.8 2.9 1.2 4.5 1.2h50.8c1.6 0 3.15-.45 4.5-1.2z" fill="#2684fc"/>
                    <path d="m73.4 26.5-12.7-22c-.8-1.4-1.95-2.5-3.3-3.3l-13.75 23.8 16.15 27h27.45c0-1.55-.4-3.1-1.2-4.5z" fill="#ffba00"/>
                </svg>
                Избери од Google Drive
            </span>
            <button class="remove-btn" onclick="closeDrivePicker()">&#x2715;</button>
        </div>
        <div class="drive-modal-body" id="drive-file-list">
            <span class="drive-loading">Се вчитуваат датотеки...</span>
        </div>
    </div>
</div>

<script>
    // ✅ ЗАЛЕПИ ЈА ТВОЈАТА N8N WEBHOOK URL ОВДЕ
    const WEBHOOK_URL = "http://localhost:5678/webhook-test/post-form";

    // ✅ ЗАЛЕПИ ГО ТВОЈОТ GOOGLE DRIVE API KEY И CLIENT ID ОВДЕ
    // Добиј ги од: https://console.cloud.google.com/ → APIs & Services → Credentials
    const GOOGLE_API_KEY    = "YOUR_GOOGLE_API_KEY";
    const GOOGLE_CLIENT_ID  = "YOUR_GOOGLE_CLIENT_ID";

    let selectedDifficulty = "";
    let syllabusFile = null;   // { file, name, source: 'local'|'drive', driveId? }
    let materialFiles = [];    // [{ file?, name, source, driveId? }]
    let drivePickerTarget = null;
    let gapiReady = false;
    let tokenClient = null;

    /* ── Difficulty ── */
    function selectDiff(btn) {
        document.querySelectorAll(".diff-btn").forEach(b => b.classList.remove("active"));
        btn.classList.add("active");
        selectedDifficulty = btn.getAttribute("data-value");
    }

    /* ── Syllabus render ── */
    function renderSyllabus() {
        const d = document.getElementById("syllabus-display");
        if (!syllabusFile) { d.innerHTML = ""; return; }
        const badge = syllabusFile.source === "drive"
            ? `<span class="source-badge">Drive</span>` : "";
        d.innerHTML = `
            <div class="syllabus-row">
                <span class="file-name">📄 ${syllabusFile.name}</span>
                ${badge}
                <button class="remove-btn" onclick="clearSyllabus()">&#x2715;</button>
            </div>`;
    }
    function clearSyllabus() {
        syllabusFile = null;
        document.getElementById("syllabus-file-input").value = "";
        renderSyllabus();
    }

    document.getElementById("syllabus-file-input").addEventListener("change", function(e) {
        const f = e.target.files[0];
        if (f) { syllabusFile = { file: f, name: f.name, source: "local" }; renderSyllabus(); }
    });

    /* ── Material files render ── */
    function renderFiles() {
        const list = document.getElementById("file-list");
        list.innerHTML = "";
        materialFiles.forEach((item, idx) => {
            const badge = item.source === "drive"
                ? `<span class="source-badge">Drive</span>` : "";
            const row = document.createElement("div");
            row.className = "file-row";
            row.innerHTML = `
                <span class="file-name">📄 ${item.name}</span>
                ${badge}
                <button class="remove-btn" onclick="removeFile(${idx})">&#x2715;</button>`;
            list.appendChild(row);
        });
    }
    function removeFile(idx) {
        materialFiles.splice(idx, 1);
        renderFiles();
    }

    document.getElementById("files-input").addEventListener("change", function(e) {
        Array.from(e.target.files).forEach(f => {
            materialFiles.push({ file: f, name: f.name, source: "local" });
        });
        e.target.value = "";
        renderFiles();
    });

    /* ── Google Drive Picker ── */
    function loadGoogleApis() {
        return new Promise(resolve => {
            if (gapiReady) { resolve(); return; }
            const s1 = document.createElement("script");
            s1.src = "https://apis.google.com/js/api.js";
            s1.onload = () => {
                gapi.load("picker", () => {
                    gapiReady = true;
                    resolve();
                });
            };
            document.head.appendChild(s1);

            const s2 = document.createElement("script");
            s2.src = "https://accounts.google.com/gsi/client";
            document.head.appendChild(s2);
        });
    }

    async function openDrivePicker(target) {
        drivePickerTarget = target;
        await loadGoogleApis();

        tokenClient = google.accounts.oauth2.initTokenClient({
            client_id: GOOGLE_CLIENT_ID,
            scope: "https://www.googleapis.com/auth/drive.readonly",
            callback: (tokenResponse) => {
                if (tokenResponse.error) return;
                showPicker(tokenResponse.access_token);
            }
        });
        tokenClient.requestAccessToken({ prompt: "" });
    }

    function showPicker(accessToken) {
        const picker = new google.picker.PickerBuilder()
            .addView(google.picker.ViewId.DOCS)
            .setOAuthToken(accessToken)
            .setDeveloperKey(GOOGLE_API_KEY)
            .setCallback((data) => {
                if (data.action === google.picker.Action.PICKED) {
                    const doc = data.docs[0];
                    onDriveFilePicked({ id: doc.id, name: doc.name });
                }
            })
            .build();
        picker.setVisible(true);
    }

    function onDriveFilePicked(f) {
        const item = { name: f.name, source: "drive", driveId: f.id };
        if (drivePickerTarget === "syllabus") {
            syllabusFile = item;
            renderSyllabus();
        } else {
            materialFiles.push(item);
            renderFiles();
        }
    }

    /* ── Submit ── */
    async function submitForm() {
        const status = document.getElementById("status");

        if (!selectedDifficulty) {
            status.textContent = "Ве молиме изберете ниво на тежина.";
            return;
        }
        if (materialFiles.length === 0) {
            status.textContent = "Ве молиме прикачете барем една датотека.";
            return;
        }

        status.textContent = "Се испраќа...";
        const formData = new FormData();
        formData.append("difficulty", selectedDifficulty);
        formData.append("comments", document.getElementById("comments").value);

        // Syllabus
        if (syllabusFile) {
            if (syllabusFile.source === "drive") {
                formData.append("syllabus_drive_id", syllabusFile.driveId);
                formData.append("syllabus_name", syllabusFile.name);
            } else {
                formData.append("syllabus", syllabusFile.file, syllabusFile.name);
            }
        }

        // Material files
        materialFiles.forEach((item, i) => {
            if (item.source === "drive") {
                formData.append(`file_drive_id_${i}`, item.driveId);
                formData.append(`file_drive_name_${i}`, item.name);
            } else {
                formData.append(`file_${i}`, item.file, item.name);
            }
        });

        try {
            const res = await fetch(WEBHOOK_URL, { method: "POST", body: formData });
            if (res.ok) {
                status.textContent = "Успешно испратено!";
            } else {
                status.textContent = `Грешка: ${res.status}`;
            }
        } catch (err) {
            status.textContent = "Неуспешно испраќање. Проверете ја webhook URL-адресата.";
        }
    }
</script>

</body>
</html>
