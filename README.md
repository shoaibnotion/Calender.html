<!-- Islamic Calendar Widget -->
<title>Islamic Calendar Widget</title>
<style>
  body {
    margin: 0;
    font-family: 'Segoe UI', sans-serif;
    background: #f0f4f8;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }
  .widget {
    background: rgba(255, 255, 255, 0.8);
    border-radius: 16px;
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.15);
    padding: 20px 24px;
    text-align: center;
    width: 320px;
    backdrop-filter: blur(12px);
  }
  .widget h1 {
    font-size: 20px;
    margin: 0;
  }
  .date {
    font-size: 16px;
    margin: 10px 0;
    color: #444;
  }
  .prayer-times {
    text-align: left;
    margin-top: 20px;
    font-size: 14px;
  }
</style>

<div class="widget">
  <h1>📅 Islamic Date Today</h1>
  <div class="date" id="hijriDate">Loading...</div>
  <div class="date" id="gregDate">Loading...</div>
  <div class="prayer-times" id="prayerTimes">Fetching prayer times...</div>
</div>

<script>
  async function loadDatesAndPrayers() {
    const today = new Date();
    const dateStr = `${today.getDate()}-${today.getMonth() + 1}-${today.getFullYear()}`;

    const hijriRes = await fetch(`https://api.aladhan.com/v1/gToH?date=${dateStr}`);
    const hijriData = await hijriRes.json();
    const hijri = hijriData.data.hijri;
    const greg = hijriData.data.gregorian;

    document.getElementById('hijriDate').textContent =
      `${hijri.weekday.en}, ${hijri.day} ${hijri.month.en} ${hijri.year} AH`;

    document.getElementById('gregDate').textContent =
      `${greg.weekday.en}, ${greg.day} ${greg.month.en} ${greg.year}`;

    const city = "Delhi"; // You can customize this
    const country = "India";
    const prayerRes = await fetch(
      `https://api.aladhan.com/v1/timingsByCity?city=${city}&country=${country}&method=2`
    );
    const prayerData = await prayerRes.json();
    const timings = prayerData.data.timings;

    // Only show main six prayers (Fajr, Sunrise, Dhuhr, Asr, Maghrib, Isha)
    const mainPrayers = ["Fajr", "Sunrise", "Dhuhr", "Asr", "Maghrib", "Isha"];
    const prayerHTML = mainPrayers
      .map(name => `<div><strong>${name}</strong>: ${timings[name]}</div>`)
      .join('');

    document.getElementById('prayerTimes').innerHTML = prayerHTML;
  }

  loadDatesAndPrayers();
</script>
