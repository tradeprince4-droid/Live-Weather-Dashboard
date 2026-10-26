# Live-Weather-Dashboard
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Weather Dashboard</title>
<style>
*{box-sizing:border-box}
body{margin:0;min-height:100vh;background:linear-gradient(135deg,#101b36,#344d75);color:white;font-family:Arial;padding:25px}
main{max-width:800px;margin:auto}
h1{font-size:2.5rem}
form{display:flex;gap:8px}
input{flex:1;min-width:0;padding:13px;border:0;border-radius:9px}
button{padding:13px 18px;border:0;border-radius:9px;background:#7ce4f2;color:#092238;font-weight:bold;cursor:pointer}
.weather{margin-top:22px;padding:28px;border-radius:22px;background:#ffffff17;border:1px solid #ffffff2e}
.temp{font-size:4.5rem;font-weight:bold}
</style>
</head>
<body>
<main>
<p>☁ WEATHER / LIVE LOOKUP</p>
<h1>Weather, at a glance.</h1>
<p>Search a city to see current conditions.</p>
<form id="form"><input id="city" placeholder="Enter city, e.g. Chennai" required><button>Search</button></form>
<section class="weather" id="result"><h2>🌤 Welcome</h2><p>Enter a city to load weather data.</p></section>
</main>
<script>
const $=id=>document.getElementById(id);
function desc(c){
if(c===0)return "☀ Clear sky";
if([1,2,3].includes(c))return "🌤 Partly cloudy";
if([45,48].includes(c))return "🌫 Fog";
if([51,53,55,56,57].includes(c))return "🌦 Drizzle";
if([61,63,65,66,67,80,81,82].includes(c))return "🌧 Rain";
if([71,73,75,77,85,86].includes(c))return "❄ Snow";
if([95,96,99].includes(c))return "⛈ Thunderstorm";
return "Current conditions";
}
$("form").onsubmit=async e=>{
e.preventDefault();
const r=$("result");r.textContent="Loading weather...";
try{
const city=$("city").value.trim();
const g=await fetch("https://geocoding-api.open-meteo.com/v1/search?name="+encodeURIComponent(city)+"&count=1&language=en&format=json").then(x=>x.json());
if(!g.results?.length)throw Error("City not found.");
const p=g.results[0];
const w=await fetch(`https://api.open-meteo.com/v1/forecast?latitude=${p.latitude}&longitude=${p.longitude}&current=temperature_2m,relative_humidity_2m,apparent_temperature,weather_code,wind_speed_10m`).then(x=>x.json());
const c=w.current;
r.innerHTML="";
const place=document.createElement("p");place.textContent=p.name+", "+(p.country||"");
const temp=document.createElement("div");temp.className="temp";temp.textContent=Math.round(c.temperature_2m)+"°C";
const condition=document.createElement("h2");condition.textContent=desc(c.weather_code);
const details=document.createElement("p");details.textContent=`Feels like ${Math.round(c.apparent_temperature)}°C · Humidity ${c.relative_humidity_2m}% · Wind ${c.wind_speed_10m} km/h`;
r.append(place,temp,condition,details);
}catch(err){r.textContent="Could not load weather. Check your connection and city name."}
};
</script>
</body>
</html>
