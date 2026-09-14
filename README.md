
<!DOCTYPE html>
<html lang="el">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Ελλάδα Weather</title>

<style>
*{box-sizing:border-box}
body{margin:0;font-family:Arial,sans-serif;background:#eaf6ff;color:#123047}
header{background:linear-gradient(135deg,#0879c9,#064b91);color:white;text-align:center;padding:28px 15px}
main{max-width:1100px;margin:auto;padding:20px}
.panel{background:white;border-radius:16px;padding:20px;margin-bottom:20px;box-shadow:0 4px 18px #0001}
select{width:100%;padding:13px;border:1px solid #aac5d8;border-radius:10px;font-size:16px}
#status{margin-top:12px;color:#527084}
.temp{font-size:58px;font-weight:bold;color:#0879c9}
.details,.forecast{display:grid;grid-template-columns:repeat(auto-fit,minmax(145px,1fr));gap:12px}
.detail,.day{background:#edf8ff;border-radius:12px;padding:14px}
.day{text-align:center;background:white;box-shadow:0 3px 12px #0000000d}
.icon{font-size:30px}
.max{color:#df4b35;font-weight:bold}
.min{color:#0879c9;font-weight:bold}
.agreement{font-size:12px;font-weight:bold;margin-top:8px}
.high{color:green}.medium{color:#b57900}.low{color:#c33}
.note{background:#fff8df;border-left:5px solid #d9a51e;padding:14px;border-radius:10px;line-height:1.5}
footer{text-align:center;padding:25px;color:#527084;font-size:13px}
</style>
</head>

<body>

<header>
<h1>🌦️ Ελλάδα Weather</h1>
<p>Πρόγνωση 15 ημερών από πολλά μετεωρολογικά μοντέλα</p>
</header>

<main>

<div class="panel">
<label for="city"><strong>Επίλεξε πόλη:</strong></label>

<select id="city">
<option value="40.6401,22.9444,Θεσσαλονίκη">Θεσσαλονίκη</option>
<option value="37.9838,23.7275,Αθήνα">Αθήνα</option>
<option value="38.2466,21.7346,Πάτρα">Πάτρα</option>
<option value="39.639,22.4191,Λάρισα">Λάρισα</option>
<option value="35.3387,25.1442,Ηράκλειο">Ηράκλειο</option>
<option value="39.665,20.8537,Ιωάννινα">Ιωάννινα</option>
<option value="40.5244,22.2053,Βέροια">Βέροια</option>
<option value="40.9396,24.4018,Καβάλα">Καβάλα</option>
<option value="40.2686,22.5061,Κατερίνη">Κατερίνη</option>
<option value="38.6214,21.4078,Αγρίνιο">Αγρίνιο</option>
<option value="36.4349,28.2176,Ρόδος">Ρόδος</option>
<option value="35.5138,24.018,Χανιά">Χανιά</option>
</select>

<div id="status">Φόρτωση δεδομένων...</div>
</div>

<div class="panel">
<h2 id="cityName">Θεσσαλονίκη</h2>
<p id="description">--</p>
<div class="temp" id="currentTemp">--°C</div>

<div class="details">
<div class="detail">🌡️ Μέση μέγιστη: <strong id="avgMax">--</strong></div>
<div class="detail">🌡️ Μέση ελάχιστη: <strong id="avgMin">--</strong></div>
<div class="detail">🌧️ Πιθανότητα βροχής: <strong id="rain">--</strong></div>
<div class="detail">🧮 Μοντέλα: <strong id="modelCount">--</strong></div>
</div>
</div>

<div class="panel">
<h2>📅 Πρόγνωση 15 ημερών</h2>
<div class="forecast" id="forecast"></div>
</div>

<div class="note">
<strong>Σημαντικό:</strong> Οι θερμοκρασίες είναι πολυμοντελικός μέσος όρος.
Η πρόγνωση μετά την 7η–10η ημέρα έχει μεγαλύτερη αβεβαιότητα.
Αν τα μοντέλα διαφωνούν, η ένδειξη συμφωνίας γίνεται χαμηλότερη.
</div>

</main>

<footer>
Δεδομένα μέσω Open-Meteo • ECMWF • GFS • ICON • UKMO • ARPEGE
</footer>

<script>
const citySelect = document.getElementById("city");
const statusBox = document.getElementById("status");

const models = [
  {name:"ECMWF", id:"ecmwf_ifs025"},
  {name:"GFS", id:"ncep_gfs_global"},
  {name:"ICON", id:"icon_global"},
  {name:"UKMO", id:"ukmo_global_deterministic_10km"},
  {name:"ARPEGE", id:"meteofrance_arpege_world"}
];

function weatherText(code) {
  const texts = {
    0:"☀️ Αίθριος",
    1:"🌤️ Κυρίως αίθριος",
    2:"⛅ Μερική συννεφιά",
    3:"☁️ Συννεφιά",
    45:"🌫️ Ομίχλη",
    48:"🌫️ Παγωμένη ομίχλη",
    51:"🌦️ Ψιλόβροχο",
    53:"🌦️ Ψιλόβροχο",
    55:"🌧️ Έντονο ψιλόβροχο",
    61:"🌦️ Ασθενής βροχή",
    63:"🌧️ Βροχή",
    65:"🌧️ Ισχυρή βροχή",
    71:"🌨️ Ασθενές χιόνι",
    73:"🌨️ Χιονόπτωση",
    75:"❄️ Ισχυρή χιονόπτωση",
    80:"🌦️ Μπόρες",
    81:"🌧️ Ισχυρές μπόρες",
    82:"⛈️ Πολύ ισχυρές μπόρες",
    95:"⛈️ Καταιγίδα",
    96:"⛈️ Καταιγίδα με χαλάζι",
    99:"⛈️ Ισχυρή καταιγίδα με χαλάζι"
  };

  return texts[code] || "Άγνωστος καιρός";
}

function formatDate(dateString) {
  return new Date(dateString + "T12:00:00").toLocaleDateString("el-GR", {
    weekday:"short",
    day:"numeric",
    month:"short"
  });
}

function agreement(spread) {
  if (spread <= 2) return '<span class="high">🟢 Υψηλή συμφωνία</span>';
  if (spread <= 5) return '<span class="medium">🟡 Μέτρια συμφωνία</span>';
  return '<span class="low">🔴 Χαμηλή συμφωνία</span>';
}

async function getModelForecast(lat, lon, model) {
  const params = new URLSearchParams({
    latitude:lat,
    longitude:lon,
    daily:"temperature_2m_max,temperature_2m_min,precipitation_probability_max,weather_code",
    forecast_days:"15",
    timezone:"auto",
    models:model.id
  });

  const response = await fetch(
    "https://api.open-meteo.com/v1/forecast?" + params.toString()
  );

  if (!response.ok) {
    throw new Error(model.name + " δεν είναι διαθέσιμο");
  }

  const data = await response.json();

  if (!data.daily || !data.daily.time) {
    throw new Error("Μη έγκυρα δεδομένα από " + model.name);
  }

  return {
    name:model.name,
    daily:data.daily
  };
}

async function loadWeather() {
  const [lat, lon, cityName] = citySelect.value.split(",");

  document.getElementById("cityName").textContent = cityName;
  statusBox.textContent = "Λήψη δεδομένων από πολλά μοντέλα...";

  const results = await Promise.allSettled(
    models.map(model => getModelForecast(lat, lon, model))
  );

  const successful = results
    .filter(result => result.status === "fulfilled")
    .map(result => result.value);

  if (successful.length === 0) {
    statusBox.textContent =
      "Δεν ήταν δυνατή η λήψη δεδομένων. Δοκίμασε ξανά αργότερα.";
    return;
  }

  const days = successful[0].daily.time.length;
  const forecastBox = document.getElementById("forecast");
  forecastBox.innerHTML = "";

  const average = (values) =>
    values.reduce((sum, value) => sum + value, 0) / values.length;

  for (let day = 0; day < days; day++) {
    const maxValues = successful
      .map(item => item.daily.temperature_2m_max[day])
      .filter(value => typeof value === "number");

    const minValues = successful
      .map(item => item.daily.temperature_2m_min[day])
      .filter(value => typeof value === "number");

    const rainValues = successful
      .map(item => item.daily.precipitation_probability_max?.[day])
      .filter(value => typeof value === "number");

    const codeValues = successful
      .map(item => item.daily.weather_code[day])
      .filter(value => typeof value === "number");

    if (!maxValues.length || !minValues.length) continue;

    const maxAverage = average(maxValues);
    const minAverage = average(minValues);
    const rainAverage = rainValues.length ? average(rainValues) : null;

    const spread = Math.max(...maxValues) - Math.min(...maxValues);
    const representativeCode = codeValues[0];

    const card = document.createElement("div");
    card.className = "day";

    card.innerHTML = `
      <strong>${formatDate(successful[0].daily.time[day])}</strong>
      <div class="icon">${weatherText(representativeCode).split(" ")[0]}</div>
      <div>${weatherText(representativeCode).substring(2)}</div>
      <p class="max">⬆️ ${maxAverage.toFixed(1)}°C</p>
      <p class="min">⬇️ ${minAverage.toFixed(1)}°C</p>
      <p>🌧️ ${rainAverage === null ? "—" : rainAverage.toFixed(0) + "%"}</p>
      <div class="agreement">${agreement(spread)}</div>
    `;

    forecastBox.appendChild(card);
  }

  const todayMax = successful
    .map(item => item.daily.temperature_2m_max[0])
    .filter(value => typeof value === "number");

  const todayMin = successful
    .map(item => item.daily.temperature_2m_min[0])
    .filter(value => typeof value === "number");

  document.getElementById("currentTemp").textContent =
    ((average(todayMax) + average(todayMin)) / 2).toFixed(1) + "°C";

  document.getElementById("avgMax").textContent =
    average(todayMax).toFixed(1) + "°C";

  document.getElementById("avgMin").textContent =
    average(todayMin).toFixed(1) + "°C";

  const todayRain = successful
    .map(item => item.daily.precipitation_probability_max?.[0])
    .filter(value => typeof value === "number");

  document.getElementById("rain").textContent =
    todayRain.length ? average(todayRain).toFixed(0) + "%" : "—";

  document.getElementById("modelCount").textContent =
    successful.length + "/" + models.length;

  document.getElementById("description").textContent =
    "Μέσος όρος των διαθέσιμων μοντέλων για " + cityName;

  statusBox.textContent =
    "Επιτυχής φόρτωση. Χρησιμοποιήθηκαν " +
    successful.length +
    " από τα " +
    models.length +
    " μοντέλα.";
}

citySelect.addEventListener("change", loadWeather);
loadWeather();
</script>

</body>
</html>
