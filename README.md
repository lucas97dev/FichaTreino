<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>Treino Lucas Morais</title>
    <style>
        :root {
            --bg: #f2f2f7; --card-bg: #ffffff;
            --push: #007AFF; --pull: #5856D6; --legs: #34C759; --upper: #FF9500; --lower: #FF2D55;
            --radius: 20px;
        }

        body { font-family: -apple-system, system-ui, sans-serif; background-color: #e5e5ea; margin: 0; padding: 0; display: flex; justify-content: center; }

        .container {
            width: 100%; max-width: 450px; background-color: var(--bg); min-height: 100vh;
            padding: 20px; padding-bottom: 40px; box-shadow: 0 0 40px rgba(0,0,0,0.1); position: relative;
            display: flex; flex-direction: column;
        }

        .header-app { text-align: left; margin-bottom: 25px; padding-left: 5px; }
        h1 { font-size: 28px; font-weight: 800; margin: 5px 0; letter-spacing: -1px; }
        .subtitle { font-size: 11px; color: #8e8e93; text-transform: uppercase; font-weight: 700; letter-spacing: 1px; }

        .dia-secao { margin-bottom: 30px; border-radius: var(--radius); }
        .dia-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 12px; padding: 0 5px; }
        .dia-titulo { font-size: 20px; font-weight: 800; color: #1c1c1e; }
        .vol-badge { font-size: 10px; font-weight: 800; background: #fff; padding: 4px 10px; border-radius: 20px; box-shadow: 0 2px 4px rgba(0,0,0,0.05); }

        .card { background: var(--card-bg); border-radius: var(--radius); padding: 18px; margin-bottom: 15px; box-shadow: 0 4px 12px rgba(0,0,0,0.05); }
        
        .ex-header { display: flex; align-items: center; gap: 8px; margin-bottom: 15px; }
        .ex-nome { font-weight: 700; font-size: 16px; color: #1c1c1e; }
        .btn-edit-name { background: none; border: none; font-size: 14px; cursor: pointer; opacity: 0.4; transition: opacity 0.2s; padding: 0; }
        .btn-edit-name:hover { opacity: 1; }

        .serie-row { display: flex; gap: 8px; align-items: center; margin-bottom: 10px; background: #f8f8fa; padding: 8px; border-radius: 12px; }
        .serie-num { font-size: 10px; font-weight: 800; color: #aeaeb2; width: 22px; text-align: center; }
        .input-group { flex: 1; }
        label { font-size: 9px; text-transform: uppercase; color: #8e8e93; font-weight: 700; margin-bottom: 3px; display: block; }
        input { width: 100%; padding: 10px; border: 1px solid #e5e5ea; border-radius: 10px; font-size: 16px; text-align: center; }

        .controls { display: flex; gap: 10px; margin-top: 15px; }
        .btn { border: none; border-radius: 12px; padding: 10px; font-size: 12px; font-weight: 700; cursor: pointer; flex: 1; }
        .btn-add { background: #1c1c1e; color: #fff; }
        .btn-hist { background: #f2f2f7; color: #007AFF; }

        .vol-ex-txt { font-size: 11px; color: #8e8e93; margin-top: 10px; font-weight: 600; text-align: right; }
        .hist-box { display: none; margin-top: 15px; font-size: 11px; padding: 12px; background: #fff; border: 1px solid #eee; border-radius: 12px; }

        .seg .dia-titulo { color: var(--push); }
        .ter .dia-titulo { color: var(--pull); }
        .qui .dia-titulo { color: var(--legs); }
        .sex .dia-titulo { color: var(--upper); }
        .sab .dia-titulo { color: var(--lower); }

        /* Assinatura */
        footer {
            margin-top: auto;
            padding: 30px 0 10px 0;
            text-align: center;
            font-size: 10px;
            color: #aeaeb2;
            letter-spacing: 0.5px;
        }
    </style>
</head>
<body>

    <div class="container">
        <div class="header-app">
            <div class="subtitle">Chikorita Gym</div>
            <h1>Meu Treino</h1>
        </div>
        
        <div id="app"></div>

        <footer>Produced by Lucas Morais</footer>
    </div>

    <script>
        const fichaBase = {
            "seg": { t: "Segunda: Push", l: ["Supino Inclinado Máq", "Chest Press Máq", "Pec Deck", "Elevação Lateral Máq", "Tríceps Corda"]},
            "ter": { t: "Terça: Pull", l: ["Puxada Alta Frontal", "Remada Neutra", "Remada Unilateral", "Crucifixo Inverso Máq", "Rosca no Cabo"]},
            "qua": { t: "Quarta: Legs (Quad)", l: ["Hack ou Leg 45°", "Leg Horizontal", "Cadeira Extensora", "Cadeira Flexora", "Panturrilha no Leg"]},
            "qui": { t: "Quinta: Upper", l: ["Shoulder Press Máq", "Puxada Triângulo", "Chest Press Neutro", "Dip Machine", "Rosca Scott Máq"]},
            "sex": { t: "Sexta: Lower (Post)", l: ["Mesa Flexora", "Cadeira Flexora (Sent)", "Elevação Pélvica Máq", "Cadeira Abdutora", "Panturrilha Sentado"]}
        };

        const renderApp = () => {
            const container = document.getElementById('app');
            let html = '';

            Object.entries(fichaBase).forEach(([key, dia]) => {
                html += `
                <div class="dia-secao ${key}">
                    <div class="dia-header">
                        <div class="dia-titulo">${dia.t}</div>
                        <div class="vol-badge" id="vol-dia-${key}">Vol: 0kg</div>
                    </div>`;
                
                dia.l.forEach((exOriginal, idx) => {
                    const idUnico = `${key}-ex-${idx}`;
                    const exNome = localStorage.getItem(`${idUnico}-name`) || exOriginal;
                    const series = JSON.parse(localStorage.getItem(`${idUnico}-series`) || "[{}]");
                    
                    html += `
                    <div class="card">
                        <div class="ex-header">
                            <span class="ex-nome" id="label-${idUnico}">${exNome}</span>
                            <button class="btn-edit-name" onclick="editName('${idUnico}')">✏️</button>
                        </div>
                        <div id="cont-${idUnico}">${series.map((s, i) => renderSerie(idUnico, i, s.c, s.r)).join('')}</div>
                        <div class="vol-ex-txt" id="vol-ex-${idUnico}">Volume: 0kg</div>
                        <div class="controls">
                            <button class="btn btn-add" onclick="addS('${idUnico}')">+ Série</button>
                            <button class="btn btn-hist" onclick="toggleH('${idUnico}')">Histórico</button>
                        </div>
                        <div id="h-${idUnico}" class="hist-box"></div>
                    </div>`;
                });
                html += `</div>`;
            });
            container.innerHTML = html;
            calcAll();
        };

        window.renderSerie = (id, i, c, r) => `
            <div class="serie-row">
                <div class="serie-num">${i+1}º</div>
                <div class="input-group"><label>Carga</label><input type="number" placeholder="0" value="${c||''}" oninput="calcAll()"></div>
                <div class="input-group"><label>Reps</label><input type="number" placeholder="0" value="${r||''}" oninput="calcAll()"></div>
                <button style="border:none; background:none; color:#ff3b30; font-size:18px;" onclick="remS('${id}', this)">×</button>
            </div>`;

        window.editName = (id) => {
            const oldName = document.getElementById(`label-${id}`).innerText;
            const newName = prompt("Nome do exercício:", oldName);
            if (newName) {
                localStorage.setItem(`${id}-name`, newName);
                document.getElementById(`label-${id}`).innerText = newName;
            }
        };

        window.addS = (id) => {
            const cont = document.getElementById(`cont-${id}`);
            cont.insertAdjacentHTML('beforeend', renderSerie(id, cont.children.length, '', ''));
        };

        window.remS = (id, btn) => { btn.parentElement.remove(); calcAll(); };

        window.calcAll = () => {
            Object.keys(fichaBase).forEach(diaKey => {
                let volDia = 0;
                fichaBase[diaKey].l.forEach((_, idx) => {
                    const id = `${diaKey}-ex-${idx}`;
                    const cont = document.getElementById(`cont-${id}`);
                    if(!cont) return;
                    let volEx = 0, seriesData = [];
                    cont.querySelectorAll('.serie-row').forEach((row, i) => {
                        const c = parseFloat(row.querySelectorAll('input')[0].value) || 0;
                        const r = parseFloat(row.querySelectorAll('input')[1].value) || 0;
                        volEx += (c * r);
                        seriesData.push({c, r});
                        row.querySelector('.serie-num').innerText = `${i+1}º`;
                    });
                    document.getElementById(`vol-ex-${id}`).innerText = `Volume: ${volEx.toLocaleString()}kg`;
                    volDia += volEx;
                    localStorage.setItem(`${id}-series`, JSON.stringify(seriesData));
                    if(volEx > 0) {
                        const hoje = new Date().toLocaleDateString('pt-BR');
                        let h = JSON.parse(localStorage.getItem(`${id}-h`) || "[]");
                        if(h.length === 0 || h[0].d !== hoje) h.unshift({d: hoje, v: volEx});
                        else h[0] = {d: hoje, v: volEx};
                        localStorage.setItem(`${id}-h`, JSON.stringify(h.slice(0,5)));
                    }
                });
                document.getElementById(`vol-dia-${diaKey}`).innerText = `Vol: ${volDia.toLocaleString()}kg`;
            });
        };

        window.toggleH = (id) => {
            const d = document.getElementById(`h-${id}`);
            if(d.style.display === 'block') d.style.display = 'none';
            else {
                const h = JSON.parse(localStorage.getItem(`${id}-h`) || "[]");
                d.innerHTML = h.map(i => `<div style="display:flex; justify-content:space-between; margin-bottom:4px;"><strong>${i.d}</strong> <span>${i.v.toLocaleString()}kg</span></div>`).join('') || "Sem dados.";
                d.style.display = 'block';
            }
        };

        renderApp();
    </script>
</body>
</html>
