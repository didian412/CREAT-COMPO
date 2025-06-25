# CREAT-COMPO<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Créateur de Compo - Ligue des Champions</title>
  <style>
    body {
      font-family: Arial;
      background: #eaf4fc;
      margin: 0;
      padding: 0;
    }
    .navbar {
      background: #003366;
      color: white;
      padding: 10px 0;
      text-align: center;
    }
    .navbar h1 {
      margin: 0;
      font-size: 24px;
    }
    .container {
      max-width: 1000px;
      margin: auto;
      padding: 20px;
      text-align: center;
    }
    select, input[type="text"], button {
      padding: 6px;
      font-size: 16px;
      margin: 5px;
    }
    #players input {
      margin-bottom: 5px;
    }
    .pitch {
      margin-top: 30px;
      background: #1a572f;
      height: 600px;
      width: 100%;
      border: 4px solid white;
      position: relative;
    }
    .playerOnField {
      position: absolute;
      color: white;
      font-size: 13px;
      text-align: center;
      transform: translate(-50%, -50%);
    }
    .playerOnField img {
      width: 42px;
      height: 42px;
      border-radius: 50%;
      border: 2px solid white;
      margin-bottom: 3px;
      object-fit: cover;
    }
  </style>
</head>
<body>
  <div class="navbar">
    <h1>⚽ Créateur de Compo - LDC Edition ⚽</h1>
  </div>
  <div class="container">
    <label>Choisis ton club : </label>
    <select id="clubSelect"></select>

    <label>Formation : </label>
    <select id="formationSelect"></select>

    <div id="players"></div>
    <button onclick="displayTeam()">Afficher l'équipe</button>
    <div id="pitch" class="pitch"></div>
  </div>

  <script>
    const formations = {
      "4-3-3": [[50,90],[20,70],[40,70],[60,70],[80,70],[25,50],[50,50],[75,50],[20,30],[50,20],[80,30]],
      "4-4-2": [[50,90],[20,70],[40,70],[60,70],[80,70],[20,50],[40,50],[60,50],[80,50],[35,30],[65,30]],
      "3-5-2": [[50,90],[30,70],[50,70],[70,70],[20,50],[40,50],[60,50],[50,30],[80,50],[40,30],[60,30]],
      "4-2-3-1": [[50,90],[20,70],[40,70],[60,70],[80,70],[30,55],[70,55],[20,40],[50,35],[80,40],[50,20]]
    };

    const formationRoles = {
      "4-3-3": ["GB", "DG", "DC", "DC", "DD", "MC", "MC", "MC", "AG", "AC", "AD"],
      "4-4-2": ["GB", "DG", "DC", "DC", "DD", "MG", "MC", "MC", "MD", "AC", "AC"],
      "3-5-2": ["GB", "DC", "DC", "DC", "MG", "MC", "MC", "MOC", "MD", "AC", "AC"],
      "4-2-3-1": ["GB", "DG", "DC", "DC", "DD", "MDF", "MDF", "MG", "MOC", "MD", "AC"]
    };

    const postLabels = {
      "GB": "Gardien",
      "DC": "Défenseur Central",
      "DG": "Latéral Gauche",
      "DD": "Latéral Droit",
      "MC": "Milieu Central",
      "MDF": "Milieu Défensif",
      "MOC": "Milieu Offensif",
      "MG": "Milieu Gauche",
      "MD": "Milieu Droit",
      "AG": "Ailier Gauche",
      "AD": "Ailier Droit",
      "AC": "Avant-Centre"
    };

    const allClubs = [
      "PSG", "Real Madrid", "Barcelone", "Manchester City", "Bayern Munich", "Liverpool", "Chelsea", "Arsenal", "Benfica", "Porto", "Sporting", "Ajax", "Feyenoord", "AC Milan", "Inter", "Juventus", "Napoli", "Roma", "Lazio", "Atletico Madrid", "Sevilla", "Valence", "Galatasaray", "Fenerbahce", "Besiktas", "Shakhtar Donetsk", "Dynamo Kiev", "Zenit", "Spartak Moscou", "CSKA Moscou", "Celtic", "Rangers", "Club Brugge", "Anderlecht", "Bâle", "Young Boys", "Red Star Belgrade", "Partizan", "Dinamo Zagreb", "Maribor", "Legia Varsovie", "Wisla Cracovie", "Slovan Bratislava", "Rapid Vienne", "Austria Vienne", "Malmo", "AIK", "IFK Göteborg", "Rosenborg", "Copenhague", "Helsinki", "Maccabi Haifa", "Hapoel Tel Aviv", "PAOK", "AEK Athènes", "Panathinaïkos", "Olympiakos", "Apollon Limassol", "Sheriff Tiraspol", "Dinamo Bucarest", "Steaua Bucarest", "Viktoria Plzen", "BATE Borisov", "Astana", "Al Ahly", "Zamalek", "Raja Casablanca", "Wydad Casablanca", "TP Mazembe", "AS Vita Club", "Esperance Tunis", "USM Alger", "JS Kabylie", "MC Alger", "Horoya", "Coton Sport", "ASEC Mimosas", "Enyimba", "Kano Pillars", "Orlando Pirates", "Kaizer Chiefs", "Mamelodi Sundowns", "Simba SC", "Young Africans", "Al Hilal", "Al Nassr", "Al Ittihad", "Al Sadd", "Al Duhail", "Persepolis", "Esteghlal", "Sepahan", "Pakhtakor", "Ulsan", "Jeonbuk", "Suwon", "Kashima Antlers", "Yokohama F. Marinos", "Urawa Reds", "Shanghai SIPG", "Guangzhou Evergrande", "Beijing Guoan", "Sydney FC", "Melbourne City", "Wellington Phoenix", "Boca Juniors", "River Plate", "Independiente", "Racing Club", "San Lorenzo", "Velez Sarsfield", "Palmeiras", "Flamengo", "Santos", "Sao Paulo", "Gremio", "Internacional", "Corinthians", "Cruzeiro", "Atletico Mineiro", "America", "Tigres", "Monterrey", "Chivas", "Cruz Azul", "Pumas", "Toluca", "Leon", "Alianza Lima", "Universitario", "Barcelona SC", "Emelec", "Liga de Quito", "Deportivo Cali", "Atletico Nacional", "Independiente del Valle", "Olimpia", "Cerro Porteno", "Bolivar", "The Strongest", "Auckland City"
    ];

    const formationSelect = document.getElementById("formationSelect");
    for (let f in formations) {
      const opt = document.createElement("option");
      opt.value = f;
      opt.textContent = f;
      formationSelect.appendChild(opt);
    }

    const clubSelect = document.getElementById("clubSelect");
    allClubs.forEach(club => {
      const opt = document.createElement("option");
      opt.textContent = club;
      clubSelect.appendChild(opt);
    });

    formationSelect.addEventListener("change", updateForm);
    clubSelect.addEventListener("change", updateForm);

    function updateForm() {
      const formationKey = formationSelect.value;
      const roles = formationRoles[formationKey];
      const playersDiv = document.getElementById("players");
      if (!roles) return;
      playersDiv.innerHTML = "<h3>Entrez vos joueurs</h3>";
      roles.forEach((pos, i) => {
        playersDiv.innerHTML += `
          <div>
            <strong>${postLabels[pos]}</strong> <br/>
            Nom : <input type="text" id="name${i}" placeholder="Nom du joueur"><br/>
            Photo (URL) : <input type="text" id="photo${i}" placeholder="https://..."><br/>
          </div><hr/>
        `;
      });
    }

    function displayTeam() {
      const pitch = document.getElementById("pitch");
      pitch.innerHTML = "";
      const formationKey = formationSelect.value;
      const positions = formations[formationKey];
      const roles = formationRoles[formationKey];
      if (!positions || !roles) return;

      roles.forEach((pos, i) => {
        const name = document.getElementById(`name${i}`).value || "Joueur";
        const photo = document.getElementById(`photo${i}`).value || "https://via.placeholder.com/40";
        const div = document.createElement("div");
        div.className = "playerOnField";
        const [x, y] = positions[i];
        div.style.left = `${x}%`;
        div.style.top = `${y}%`;
        div.innerHTML = `<img src="${photo}" onerror="this.src='https://via.placeholder.com/40'" /><br/>${name}<br/><small>${postLabels[pos]}</small>`;
        pitch.appendChild(div);
      });
    }
  </script>
</body>
</html>
