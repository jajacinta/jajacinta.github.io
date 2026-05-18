<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chicken Egg Genetic Dashboard</title>
    <!-- SEO Description for Search Engines -->
    <meta name="description" content="An interactive Punnett square calculator to predict chicken egg shell colors based on parent genetics. Supports blue, green, olive, white, tan, and brown traits.">
    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #121212; color: #e0e0e0; padding: 10px; margin: 0; }
        .container { max-width: 900px; margin: auto; background: #1e1e1e; padding: 20px; border-radius: 15px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        h2 { text-align: center; color: #f0e68c; letter-spacing: 1px; margin-bottom: 5px; }
        .subtitle { text-align: center; font-size: 0.8em; color: #888; margin-bottom: 15px; }
        /* Style for the new visible website description */
        .site-description { text-align: center; font-size: 0.95em; color: #b0b0b0; max-width: 700px; margin: 0 auto 25px auto; line-height: 1.5; }
        
        .inputs { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; background: #252525; padding: 15px; border-radius: 10px; margin-bottom: 20px; border: 1px solid #333; }
        label { color: #4A90E2; font-size: 11px; font-weight: bold; text-transform: uppercase; }
        input { width: 100%; padding: 12px; background: #121212; border: 1px solid #444; border-radius: 8px; color: #fff; font-size: 16px; box-sizing: border-box; margin-top: 5px; }
        button { width: 100%; padding: 15px; background: #4A90E2; color: white; border: none; border-radius: 8px; font-weight: bold; cursor: pointer; margin-top: 10px; font-size: 16px; box-shadow: 0 4px 0 #2a6db8; }

        .scroll-wrapper { overflow-x: auto; margin: 20px 0; border-radius: 10px; border: 1px solid #333; }
        table { border-collapse: collapse; width: 100%; min-width: 400px; }
        th, td { border: 1px solid #333; text-align: center; padding: 12px 8px; font-size: 12px; font-weight: bold; }
        th { background: #252525; color: #4A90E2; }
        
        .egg-visual-grid { 
            display: grid; 
            grid-template-columns: repeat(4, 1fr); 
            gap: 10px; 
            margin: 20px 0;
            background: #252525;
            padding: 20px;
            border-radius: 10px;
            border: 1px solid #333;
            max-width: 250px;
            margin-left: auto;
            margin-right: auto;
        }
        .egg-icon { 
            aspect-ratio: 1/1.3; 
            border-radius: 50% 50% 50% 50% / 65% 65% 35% 35%; 
            border: 1px solid rgba(255,255,255,0.05);
            box-shadow: inset -2px -4px 6px rgba(0,0,0,0.2);
        }

        .results-list { display: flex; flex-direction: column; gap: 10px; }
        .bar-container { background: #121212; border-radius: 20px; height: 30px; position: relative; overflow: hidden; display: flex; align-items: center; border: 1px solid #333; }
        .bar-fill { height: 100%; transition: width 0.8s ease-in-out; }
        .bar-label { position: absolute; left: 12px; font-size: 13px; font-weight: bold; }
        .bar-percent { position: absolute; right: 12px; font-size: 12px; font-weight: bold; }
    </style>
</head>
<body>

<div class="container">
    <h2>Egg Colour Calculator</h2>
    <div class="subtitle">W = Blue Shell | B = Brown Coating (1 Cap = Green, 2 Caps = Olive)</div>
    
    <!-- Visible description added directly to the page interface -->
    <p class="site-description">
        Welcome to the Chicken Egg Genetic Dashboard! This tool maps the genetic outcomes of breeding pairs to predict the shell colors of their offspring. Input the parent genotypes below to generate an interactive 4x4 Punnett square map, visual egg chart, and statistical probabilities.
    </p>
    
    <div class="inputs">
        <div>
            <label>Parent 1 Genotype</label>
            <input type="text" id="p1" value="WwBb" placeholder="e.g. WwBb">
        </div>
        <div>
            <label>Parent 2 Genotype</label>
            <input type="text" id="p2" value="WwBb" placeholder="e.g. WwBb">
        </div>
        <button style="grid-column: span 2;" onclick="calculate()">Hatch Visual Probabilities</button>
    </div>

    <div class="scroll-wrapper" id="square-output"></div>

    <div id="results-area">
        <h3 style="text-align: center; color: #f0e68c; font-size: 1.1em; margin-bottom: 5px;">4x4 Offspring Map</h3>
        <div class="egg-visual-grid" id="egg-grid"></div>
        
        <h3 style="color: #f0e68c; font-size: 1.1em; margin-bottom: 10px;"> Statistics</h3>
        <div class="results-list" id="stat-list"></div>
    </div>
</div>

<script>
    // Updated color settings: Using dark text (#121212) on light colors so text is visible!
    const COLORS = {
        blue: { name: "Blue", hex: "#AEDFF7", text: "#121212" },
        green: { name: "Green", hex: "#A7D49B", text: "#121212" },
        olive: { name: "Olive", hex: "#6B8E23", text: "#fff" },
        white: { name: "White", hex: "#FFFFFF", text: "#121212" },
        tan: { name: "Tan", hex: "#D2B48C", text: "#121212" },
        brown: { name: "Brown", hex: "#8B4513", text: "#fff" }
    };

    function getPheno(genotype) {
        const hasW = genotype.substring(0, 2).includes('W');
        const brownGenetics = genotype.substring(2);
        let brownCount = 0;
        for(let char of brownGenetics) {
            if(char === char.toUpperCase() && char !== char.toLowerCase()) brownCount++;
        }

        if (hasW) {
            if (brownCount === 0) return COLORS.blue;
            if (brownCount === 1) return COLORS.green; 
            return COLORS.olive;
        } else {
            if (brownCount === 0) return COLORS.white;
            if (brownCount === 1) return COLORS.tan;
            return COLORS.brown;
        }
    }

    function getGametes(g) {
        let genes = [];
        for(let i=0; i<g.length; i+=2) genes.push([g[i], g[i+1]]);
        return genes.reduce((a, b) => a.flatMap(d => b.map(e => d + e)));
    }

    function calculate() {
        const g1 = document.getElementById('p1').value.trim();
        const g2 = document.getElementById('p2').value.trim();
        
        const gam1 = getGametes(g1);
        const gam2 = getGametes(g2);
        const stats = {};
        const gridArray = [];

        let table = `<table><tr><th></th>`;
        gam2.forEach(g => table += `<th>${g}</th>`);
        table += `</tr>`;

        gam1.forEach(ga => {
            table += `<tr><th>${ga}</th>`;
            gam2.forEach(gb => {
                let combo = "";
                for(let i=0; i<ga.length; i++) {
                    let pair = [ga[i], gb[i]].sort().join('');
                    combo += pair;
                }
                const p = getPheno(combo);
                table += `<td style="background:${p.hex}; color:${p.text}">${combo}</td>`;
                stats[p.name] = (stats[p.name] || 0) + 1;
                gridArray.push(p);
            });
            table += `</tr>`;
        });
        document.getElementById('square-output').innerHTML = table + `</table>`;

        let gridHtml = "";
        gridArray.forEach(item => {
            gridHtml += `<div class="egg-icon" style="background:${item.hex}"></div>`;
        });
        document.getElementById('egg-grid').innerHTML = gridHtml;

        let total = gam1.length * gam2.length;
        let statHtml = "";
        Object.entries(stats).sort((a,b)=>b[1]-a[1]).forEach(([name, count]) => {
            const perc = (count/total * 100).toFixed(1);
            const colorObj = Object.values(COLORS).find(c => c.name === name);
            statHtml += `
                <div class="bar-container">
                    <div class="bar-fill" style="width:${perc}%; background:${colorObj.hex}"></div>
                    <span class="bar-label" style="color:${colorObj.text}">${name}</span>
                    <span class="bar-percent" style="color:${colorObj.text}">${perc}%</span>
                </div>`;
        });
        document.getElementById('stat-list').innerHTML = statHtml;
    }
    calculate();
</script>
</body>
</html>
