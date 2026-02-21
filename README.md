# registre-d-appel-
Logiciel permettant de suivre l'assiduité des élèves 

<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Registre d’Appel - Lycée Moderne</title>

<style>
body {
    font-family: Arial, sans-serif;
    background: #f2f6fb;
    margin: 0;
}

header {
    background: #2c5282;
    color: white;
    padding: 20px;
    text-align: center;
}

.container {
    max-width: 1200px;
    margin: auto;
    padding: 20px;
}

.card {
    background: white;
    padding: 20px;
    border-radius: 10px;
    margin-bottom: 20px;
    box-shadow: 0 4px 12px rgba(0,0,0,0.08);
}

table {
    width: 100%;
    border-collapse: collapse;
}

th, td {
    padding: 8px;
    border: 1px solid #ddd;
    text-align: center;
}

th {
    background: #2c5282;
    color: white;
}

button {
    padding: 10px 15px;
    background: #2c5282;
    color: white;
    border: none;
    border-radius: 5px;
    cursor: pointer;
}

button:hover {
    background: #1e3a5f;
}

.alert {
    margin-top: 15px;
    font-weight: bold;
    color: #c53030;
}
</style>
</head>

<body>

<header>
<h1>📘 Registre d’Appel Hebdomadaire</h1>
<p>Lycée Moderne - Suivi de l’assiduité</p>
</header>

<div class="container">

<div class="card">
<label>Classe :
<input type="text" id="classe" placeholder="Ex: 3ème A">
</label>

<label style="margin-left:20px;">
Semaine :
<input type="week">
</label>

<br><br>

<label>Importer liste des élèves (CSV) :
<input type="file" id="fileInput" accept=".csv">
</label>

</div>

<div class="card">
<table id="tableAppel">
<thead>
<tr>
<th>N°</th>
<th>Nom</th>
<th>Téléphone Parent</th>
<th>Lun</th>
<th>Mar</th>
<th>Mer</th>
<th>Jeu</th>
<th>Ven</th>
<th>Taux (%)</th>
</tr>
</thead>
<tbody>
</tbody>
</table>

<br>
<button onclick="calculerPresence()">Calculer & Générer Alertes</button>

<div id="alertes" class="alert"></div>

</div>

</div>

<script>
document.getElementById('fileInput').addEventListener('change', function(event) {
    const file = event.target.files[0];
    const reader = new FileReader();

    reader.onload = function(e) {
        const lignes = e.target.result.split('\n');
        const tbody = document.querySelector("#tableAppel tbody");
        tbody.innerHTML = "";

        for(let i = 1; i < lignes.length; i++) {
            if(lignes[i].trim() === "") continue;

            const colonnes = lignes[i].split(",");
            const nom = colonnes[0];
            const telephone = colonnes[1];

            let row = `
            <tr>
                <td>${i}</td>
                <td>${nom}</td>
                <td>${telephone}</td>
                <td><input type="checkbox"></td>
                <td><input type="checkbox"></td>
                <td><input type="checkbox"></td>
                <td><input type="checkbox"></td>
                <td><input type="checkbox"></td>
                <td class="result"></td>
            </tr>
            `;
            tbody.innerHTML += row;
        }
    };

    reader.readAsText(file);
});

function calculerPresence() {
    let rows = document.querySelectorAll("#tableAppel tbody tr");
    let message = "";

    rows.forEach(row => {
        let checkboxes = row.querySelectorAll("input[type='checkbox']");
        let present = 0;

        checkboxes.forEach(box => {
            if(box.checked) present++;
        });

        let taux = (present / 5) * 100;
        row.querySelector(".result").innerText = taux + "%";

        let nom = row.cells[1].innerText;

        if (taux < 60) {
            message += "⚠️ " + nom + " a un taux faible (" + taux + "%).\n";
        }
    });

    document.getElementById("alertes").innerText = message;
}
</script>

</body>
</html>
