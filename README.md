<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Crop Care</title>
  <style>
    :root{
      --accent:#2e8b57;
      --accent-2:#ffd166;
      --card:#ffffff;
      --bg:#f6fff6;
      --muted:#666;
      --glass: rgba(255,255,255,0.85);
    }
    *{box-sizing:border-box}
    body{
      margin:0;font-family:Inter,Arial,Helvetica,sans-serif;
      background:linear-gradient(180deg,#f3fff3 0%, #eafbea 100%);
      color:#222; -webkit-font-smoothing:antialiased;
    }

    /* Layout */
    .full { min-height:100vh; display:flex; flex-direction:column; }

    /* PAGE 1: language selection */
    #page1 { display:flex; align-items:center; justify-content:center; padding:20px; min-height:100vh; }
    .lang-wrap { width:100%; max-width:920px; }
    .lang-title { text-align:center; color:var(--accent); margin-bottom:16px; font-size:24px; font-weight:800; }
    .lang-grid {
      display:grid; gap:10px;
      grid-template-columns:repeat(2,1fr);
    }
    @media (min-width:720px){ .lang-grid { grid-template-columns:repeat(4,1fr);} }
    @media (min-width:1080px){ .lang-grid { grid-template-columns:repeat(6,1fr);} }

    .lang-btn{
      background:linear-gradient(180deg,var(--accent) 0%, #256b45 100%); color:white; border:none;
      padding:14px 10px; font-size:15px; border-radius:10px; cursor:pointer; width:100%;
      display:flex; align-items:center; justify-content:flex-start; gap:10px;
      box-shadow:0 6px 14px rgba(0,0,0,0.08);
    }
    .lang-btn .lang-flag { width:36px; height:36px; display:inline-flex; align-items:center; justify-content:center; background:rgba(255,255,255,0.12); border-radius:8px; font-weight:700; }
    .lang-btn:hover{ transform:translateY(-4px); transition:all .18s ease; }

    /* APP (PAGE 2) */
    #app { display:none; flex-direction:column; min-height:100vh; }

    header.appbar{
      background:var(--accent); color:white; padding:12px 14px; display:flex; align-items:center; justify-content:space-between;
      position:sticky; top:0; z-index:20;
    }
    .app-title { font-weight:800; display:flex; align-items:center; gap:8px; font-size:18px; }
    .menu { position:relative; }
    .menu button { background:none; color:white; border:none; font-size:22px; cursor:pointer; padding:6px; }
    .menu-panel { position:absolute; right:6px; top:48px; background:var(--glass); border-radius:12px; box-shadow:0 10px 30px rgba(0,0,0,0.12); display:none; overflow:hidden; min-width:220px; backdrop-filter: blur(6px); }
    .menu-panel button { width:100%; text-align:left; padding:12px 14px; border:none; background:none; cursor:pointer; border-bottom:1px solid rgba(0,0,0,0.06); font-weight:600; color:#0e4732;}
    .menu-panel button:last-child{border-bottom:none}
    .menu-panel button:hover{background:rgba(0,0,0,0.02)}

    /* content */
    .content { padding:16px; padding-bottom:120px; display:block; flex:1; overflow:auto; max-width:980px; margin:0 auto; width:100%; }
    .card { background:var(--card); padding:12px; border-radius:12px; box-shadow:0 6px 20px rgba(10,20,10,0.04); margin-bottom:12px; }
    .row { display:flex; gap:12px; align-items:center; }
    label { display:block; margin-bottom:6px; font-weight:700; color:#0b4b33; }
    select,input,textarea { width:100%; padding:10px; border-radius:8px; border:1px solid #e6eee6; font-size:15px; background:#fbfffb; }
    .btn-primary { background:var(--accent-2); border:none; padding:12px; border-radius:12px; font-weight:800; cursor:pointer; width:100%; margin-top:10px; box-shadow:0 8px 18px rgba(255,209,102,0.18); }
    .btn-primary:hover{ transform:translateY(-3px) }
    .small-muted { color:var(--muted); font-size:13px; margin-top:6px }

    /* bottom nav fixed */
    .bottom-nav {
      position:fixed; left:12px; right:12px; bottom:12px; height:72px; background:white;
      display:flex; align-items:center; justify-content:space-around; border-radius:16px; box-shadow:0 18px 40px rgba(0,0,0,0.08); z-index:40;
      padding:8px 18px;
    }
    .nav-btn { background:none;border:none;display:flex;flex-direction:column;align-items:center;gap:4px;color:var(--accent);cursor:pointer; font-size:13px; width:64px; }
    .nav-icon { font-size:20px }

    /* mic button */
    #micBtn { width:64px; height:64px; border-radius:50%; border:none; background:var(--accent-2); font-size:28px; cursor:pointer; display:block; margin:12px auto; box-shadow:0 10px 28px rgba(0,0,0,0.10) }
    .pulse { animation:micPulse 1s infinite; }
    @keyframes micPulse { 0%{ box-shadow:0 0 0 0 rgba(255,209,102,0.7)} 70%{ box-shadow:0 0 0 18px rgba(255,209,102,0)} 100%{ box-shadow:0 0 0 0 rgba(255,209,102,0)} }

    /* result area */
    .result { margin-top:12px; padding:12px; border-radius:12px; background:#fff; border:1px solid #eef7ee; display:flex; gap:12px; align-items:center; flex-wrap:wrap; }
    .crop-icon { font-size:40px; width:56px; height:56px; display:flex; align-items:center; justify-content:center; background:#fff8e6; border-radius:12px; box-shadow:0 6px 16px rgba(0,0,0,0.04) }
    .meta { font-weight:800; color:#114427 }
    .tag { padding:6px 10px; background:#f3fff3; border-radius:8px; font-weight:700; color:#0b4b33; border:1px dashed rgba(10,80,40,0.06) }

    /* modal overlay for language menu & terms */
    .modal-overlay { position:fixed; inset:0; display:none; align-items:center; justify-content:center; z-index:60; }
    .modal { background:white; border-radius:12px; width:92%; max-width:720px; max-height:80vh; overflow:auto; padding:16px; box-shadow:0 30px 60px rgba(0,0,0,0.18); }
    .modal h3 { margin-top:0; }
    .modal .lang-grid { grid-template-columns:repeat(3,1fr); gap:8px; }
    @media (max-width:520px){ .modal .lang-grid { grid-template-columns:repeat(2,1fr); } }

    /* hidden helper */
    .hidden { display:none!important; }

    /* responsiveness adjustments */
    @media (max-width:600px){
      .lang-btn{ font-size:14px; padding:12px }
      .bottom-nav{ padding:8px 10px; left:8px; right:8px }
    }
  </style>
</head>
<body>
  <div class="full">

    <!-- PAGE 1: Language Selection -->
    <main id="page1">
      <div class="lang-wrap">
        <h1 class="lang-title">Select a Language</h1>
        <div class="lang-grid" id="languageButtons"></div>
      </div>
    </main>

    <!-- PAGE 2: App -->
    <section id="app">
      <header class="appbar">
        <div class="app-title">🌱 <span id="appName">Crop Care</span></div>
        <div class="menu">
          <button id="menuToggleBtn" aria-label="more">⋮</button>
          <div class="menu-panel" id="menuPanel" role="menu" aria-hidden="true">
            <button id="menuLanguage">Change Language</button>
            <button id="menuMute">Mute Voice</button>
            <button id="menuTerms">Terms & Conditions</button>
            <button id="menuSettings">Settings</button>
          </div>
        </div>
      </header>

      <div class="content" id="contentArea">

        <!-- HOME PANEL -->
        <div id="panel-home" class="panel card">
          <h2 id="titleMain">Crop Recommendation</h2>

          <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px">
            <div>
              <label id="soilLabel">🌱 Soil Type</label>
              <select id="soil">
                <option value="Loamy">Loamy</option>
                <option value="Clay">Clay</option>
                <option value="Sandy">Sandy</option>
              </select>
            </div>

            <div>
              <label id="weatherLabel">☀ Weather/Season</label>
              <select id="weather">
                <option value="Summer">Summer</option>
                <option value="Winter">Winter</option>
                <option value="Rainy">Rainy</option>
              </select>
            </div>
          </div>

          <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px;margin-top:12px">
            <div>
              <label id="landLabel">🌾 Land Size</label>
              <select id="land">
                <option value="Large">Large</option>
                <option value="Medium">Medium</option>
                <option value="Small">Small</option>
              </select>
            </div>
            <div>
              <label id="waterLabel">💧 Water Availability</label>
              <select id="water">
                <option value="High">High</option>
                <option value="Medium">Medium</option>
                <option value="Low">Low</option>
              </select>
            </div>
          </div>

          <button class="btn-primary" id="recommendBtn">Recommend a Crop</button>

          <div id="recommendResult" class="result hidden" aria-live="polite"></div>

          <div class="card" style="margin-top:14px; text-align:center">
            <div style="font-weight:800; margin-bottom:8px" id="voiceChatLabel">Voice Chat</div>
            <button id="micBtn" title="Tap to speak">🎤</button>
            <div id="voiceText" class="small-muted"></div>
          </div>
        </div>

        <!-- CALCULATOR PANEL -->
        <div id="panel-calculator" class="panel card hidden">
          <h2 id="calcTitle">Calculator</h2>
          <div style="display:grid;gap:10px">
            <label id="calcAreaLabel">Area (hectares)</label>
            <input id="calcArea" type="number" min="0" value="1" step="0.1" />

            <label id="calcYieldLabel">Expected yield (kg per hectare)</label>
            <input id="calcYield" type="number" min="0" value="2000" />

            <label id="calcPriceLabel">Market price (per kg)</label>
            <input id="calcPrice" type="number" min="0" value="20" />

            <label id="calcFertPerHaLabel">Fertilizer needed (kg per hectare)</label>
            <input id="calcFertPerHa" type="number" min="0" value="100" />

            <label id="calcPestPerHaLabel">Pesticide needed (L per hectare)</label>
            <input id="calcPestPerHa" type="number" min="0" value="2" />

            <button class="btn-primary" id="calcBtn">Calculate</button>

            <div id="calcResult" class="small-muted"></div>
          </div>
        </div>

        <!-- FEEDBACK PANEL -->
        <div id="panel-feedback" class="panel card hidden">
          <h2 id="feedbackTitle">Feedback</h2>
          <label id="feedbackLabel">Your feedback</label>
          <textarea id="feedbackText" rows="6"></textarea>
          <button class="btn-primary" id="feedbackSubmitBtn">Submit Feedback</button>
          <div id="feedbackMsg" class="small-muted"></div>
        </div>

        <!-- PROFILE PANEL -->
        <div id="panel-profile" class="panel card hidden">
          <h2 id="profileTitle">Profile</h2>
          <label id="profileNameLabel">Farmer Name</label>
          <input id="profileName" type="text" placeholder="e.g. Raju" />
          <label id="profileAgeLabel">Age</label>
          <input id="profileAge" type="number" min="18" />
          <label id="profileLandLabel">Total Land (hectares)</label>
          <input id="profileLand" type="number" min="0" step="0.1" />
          <button class="btn-primary" id="profileSaveBtn">Save Profile</button>
          <div id="profileMsg" class="small-muted"></div>
        </div>

      </div> <!-- content -->

      <!-- bottom nav -->
      <nav class="bottom-nav" role="navigation" aria-label="main navigation">
        <button class="nav-btn" id="navHomeBtn" onclick="showPanel('home')"><div class="nav-icon">🏠</div><div id="navHomeLabel">Home</div></button>
        <button class="nav-btn" id="navCalcBtn" onclick="showPanel('calculator')"><div class="nav-icon">🧮</div><div id="navCalcLabel">Calculator</div></button>
        <button class="nav-btn" id="navFeedbackBtn" onclick="showPanel('feedback')"><div class="nav-icon">💬</div><div id="navFeedbackLabel">Feedback</div></button>
        <button class="nav-btn" id="navProfileBtn" onclick="showPanel('profile')"><div class="nav-icon">👤</div><div id="navProfileLabel">Profile</div></button>
      </nav>
    </section>

  </div>

  <!-- Modal overlays -->
  <div id="langModal" class="modal-overlay" aria-hidden="true">
    <div class="modal card" role="dialog" aria-modal="true">
      <h3 id="modalLangTitle">Choose Language</h3>
      <div class="lang-grid" id="modalLanguageButtons"></div>
      <div style="text-align:right;margin-top:12px">
        <button class="btn-primary" id="modalCloseBtn">Close</button>
      </div>
    </div>
  </div>

  <div id="termsModal" class="modal-overlay" aria-hidden="true">
    <div class="modal card" role="dialog" aria-modal="true">
      <h3>Terms & Conditions — AI-based crop recommendation</h3>
      <p>
        Crop Care provides AI-assisted recommendations based on simple heuristics and user inputs (soil type, water, season, land size).
        These recommendations are advisory only. Users must consult local agricultural extension officers or agronomists and follow label instructions when using fertilizers and pesticides.
        The app does not assume liability for crop outcomes. Always follow safe handling guidelines and local regulations.
        Sure! Here’s a *point-wise version* of the Terms and Conditions for your AI-based crop recommendation app:

---

# *Terms and Conditions – AI-Based Crop Recommendation App*

*Effective Date:* \[Insert Date]

By using *\[App Name]*, you agree to the following terms:

1. *Acceptance of Terms*

   * By using the app, you confirm you are a farmer or authorized user.
   * You agree to comply with these Terms and Conditions and the Privacy Policy.

2. *Services Provided*

   * AI-based crop recommendations based on location, soil, and weather.
   * Suggestions for fertilizer, irrigation, and pest management.
   * Notifications, tips, and alerts for better crop management.
   * *Note:* Recommendations are informational only; results are not guaranteed.

3. *User Obligations*

   * Provide accurate and up-to-date information.
   * Use the app only for lawful agricultural purposes.
   * Do not share account credentials or misuse the app.

4. *Limitations of Liability*

   * The app does not guarantee accuracy or crop yield.
   * The app is not liable for any crop losses or financial losses.
   * Users are responsible for decisions based on the app’s recommendations.

5. *Intellectual Property*

   * All content, AI algorithms, and features belong to *\[App Name/Company]*.
   * Users cannot copy, distribute, or modify app content without permission.

6. *Data Collection and Privacy*

   * The app collects personal and farming-related data to provide accurate recommendations.
   * Data will be handled according to the Privacy Policy and not sold to third parties.
   * Users consent to anonymized data being used for research and improvement of the app.

7. *Third-Party Services*

   * The app may use external services (weather APIs, soil databases, etc.).
   * The app is not responsible for the accuracy or availability of third-party services.

8. *Termination*

   * Access may be suspended or terminated for violating these terms.
   * Users can stop using the app anytime.

9. *Updates and Modifications*

   * The app may update features or Terms and Conditions without prior notice.
   * Continued use of the app constitutes acceptance of updated terms.

10. *Governing Law*

    * Terms are governed by the laws of *\[Your Country/State]*.
    * Any disputes will be resolved in the jurisdiction of *\[Your City/Region]*.

11. *Contact Information*

    * Email: \[[support@yourapp.com](mailto:support@yourapp.com)]
    * Phone: \[Your Contact Number]

---

If you want, I can also create a *short, farmer-friendly version in simple language* with just 5–6 easy-to-understand points that you can show in the app itself. It will be easy for farmers to read and accept.

      </p>
      <div style="text-align:right;margin-top:12px">
        <button class="btn-primary" id="termsCloseBtn">Close</button>
      </div>
    </div>
  </div>

  <script>
    /*****************************************************************************
     * Complete single-file Crop Care app
     * - 23 languages translations inline
     * - Voice assistant (TTS + Web Speech Recognition)
     * - Language selection, mute/unmute, modal language list, terms modal
     * - Recommend crop with emoji, fertilizer (🧪) & pesticide (🐛❌) icons
     * - Bottom nav panels: home/calculator/feedback/profile (panels open in content area)
     * - Calculator to compute yield, fertilizer & pesticide quantities and estimated profit
     *****************************************************************************/

    /****************************
     * TRANSLATIONS (23 languages)
     * keyed by language name used on page1
     ****************************/
    const translations = {
      English: {
        title: "Crop Recommendation",
        soil: "🌱 Soil Type",
        weather: "☀ Weather/Season",
        land: "🌾 Land Size",
        water: "💧 Water Availability",
        recommend: "Recommend a Crop",
        result: "Recommended Crop",
        menu_language: "Change Language",
        menu_mute: "Mute Voice",
        menu_terms: "Terms & Conditions",
        menu_settings: "Settings",
        nav_home: "Home",
        nav_calc: "Calculator",
        nav_feedback: "Feedback",
        nav_profile: "Profile",
        voice_chat: "Voice Chat",
        calc_title: "Calculator",
        calc_area: "Area (hectares)",
        calc_yield: "Expected yield (kg/ha)",
        calc_price: "Market price (per kg)",
        calc_fert_ha: "Fertilizer (kg/ha)",
        calc_pest_ha: "Pesticide (L/ha)",
        feedback: "Your feedback",
        profile_name: "Farmer Name",
        profile_age: "Age",
        profile_land: "Total Land (ha)",
        terms_short: "AI-based recommendations are advisory only."
      },
      Hindi: {
        title: "फसल सिफारिश",
        soil: "🌱 मिट्टी का प्रकार",
        weather: "☀ मौसम / ऋतु",
        land: "🌾 भूमि का आकार",
        water: "💧 पानी की उपलब्धता",
        recommend: "फसल सुझाएँ",
        result: "सुझाई गई फसल",
        menu_language: "भाषा बदलें",
        menu_mute: "ध्वनि बंद/चालू",
        menu_terms: "नियम और शर्तें",
        menu_settings: "सेटिंग्स",
        nav_home: "होम",
        nav_calc: "कैलकुलेटर",
        nav_feedback: "प्रतिक्रिया",
        nav_profile: "प्रोफ़ाइल",
        voice_chat: "वॉइस चैट",
        calc_title: "कैलकुलेटर",
        calc_area: "क्षेत्र (हेक्टेयर)",
        calc_yield: "उपज (किग्रा/हेक्टेयर)",
        calc_price: "बाजार मूल्य (प्रति किग्रा)",
        calc_fert_ha: "उर्वरक (किग्रा/हेक्टेयर)",
        calc_pest_ha: "कीटनाशक (ली/हेक्टेयर)",
        feedback: "आपकी प्रतिक्रिया",
        profile_name: "किसान का नाम",
        profile_age: "उम्र",
        profile_land: "कुल जमीन (हे)"
      },
      Telugu: {
        title: "పంట సూచన",
        soil: "🌱 మట్టి రకం",
        weather: "☀ వాతావరణం / ఋతువు",
        land: "🌾 భూమి పరిమాణం",
        water: "💧 నీటి లభ్యత",
        recommend: "పంట సూచించండి",
        result: "సూచింపబడిన పంట",
        menu_language: "భాష మార్చు",
        menu_mute: "వాయిస్ మ్యూట్/అన్‌మ్యూట్",
        menu_terms: "నియమాలు మరియు షరతులు",
        menu_settings: "సెట్టింగ్స్",
        nav_home: "హోమ్",
        nav_calc: "క్యాల్క్యులేటర్",
        nav_feedback: "ఫీడ్‌బ్యాక్",
        nav_profile: "ప్రొఫైల్",
        voice_chat: "వాయిస్ చాట్",
        calc_title: "క్యాల్క్యులేటర్",
        calc_area: "భూమి (హెక్టేరు)",
        calc_yield: "ఉత్పత్తి (కిలో / హే)",
        calc_price: "ధర (ప్రతి కిలో)",
        calc_fert_ha: "ఎరువు (kg/ha)",
        calc_pest_ha: "పెస్ట్ (L/ha)",
        feedback: "మీ అభిప్రాయం",
        profile_name: "వ్యవసాయిగారి పేరు",
        profile_age: "వయస్సు",
        profile_land: "మొత్తం భూమి (హె)"
      },
      Bengali: {
        title: "ফসল সুপারিশ",
        soil: "🌱 মাটির ধরন",
        weather: "☀ আবহাওয়া / ঋতু",
        land: "🌾 জমির আকার",
        water: "💧 জলের প্রাপ্যতা",
        recommend: "ফসল সুপারিশ করুন",
        result: "সুপারিশকৃত ফসল",
        menu_language: "ভাষা পরিবর্তন করুন",
        menu_mute: "মিউট/আনমিউট",
        menu_terms: "শর্তাবলী",
        menu_settings: "সেটিংস",
        nav_home: "হোম",
        nav_calc: "ক্যালকুলেটর",
        nav_feedback: "প্রতিক্রিয়া",
        nav_profile: "প্রোফাইল",
        voice_chat: "ভয়েস চ্যাট",
        calc_title: "ক্যালকুলেটর",
        calc_area: "এলাকা (হেক্টর)",
        calc_yield: "উৎপাদন (কেজি/হা)",
        calc_price: "দাম (প্রতি কেজি)",
        calc_fert_ha: "সার (কেজি/হা)",
        calc_pest_ha: "পেস্টিসাইড (লি/হা)",
        feedback: "আপনার মন্তব্য",
        profile_name: "কৃষকের নাম",
        profile_age: "বয়স",
        profile_land: "মোট জমি (হে)"
      },
      Malayalam: {
        title: "വിള ശുപാർശ",
        soil: "🌱 മണ്ണിന്റെ തരം",
        weather: "☀ കാലാവസ്ഥ / സീസൺ",
        land: "🌾 ഭൂമി വലിപ്പം",
        water: "💧 ജല ലഭ്യത",
        recommend: "വിള ശുപാർശ ചെയ്യുക",
        result: "ശുപാർശ ചെയ്ത വിള",
        menu_language: "ഭാഷ മാറ്റുക",
        menu_mute: "മ്യൂട്ട്/അൺമ്യൂട്ട്",
        menu_terms: "നിബന്ധനകൾ",
        menu_settings: "സെറ്റിംഗ്സ്",
        nav_home: "ഹോം",
        nav_calc: "ക്യാല്കുലേറ്റർ",
        nav_feedback: "ഫീഡ്ബാക്ക്",
        nav_profile: "പ്രൊഫൈൽ",
        voice_chat: "വോയ്സ് ചാറ്റ്",
        calc_title: "ക്യാല്കുലേറ്റർ",
        calc_area: "എരിയ (ഹെക്ടർ)",
        calc_yield: "ഉപജ (കിജി/ഹെ)",
        calc_price: "വില (പ്രതി കിജി)",
        calc_fert_ha: "സർ (കിജി/ഹെ)",
        calc_pest_ha: "പെസ്റ്റ് (ലി/ഹെ)",
        feedback: "നിങ്ങളുടെ പ്രതികരണം",
        profile_name: "കൃഷിക്കാരന്റെ പേര്",
        profile_age: "വയസ്",
        profile_land: "മൊത്തം ഭൂമി (ഹെ)"
      },
      Kannada: {
        title: "ಬೆಳೆ ಶಿಫಾರಸು",
        soil: "🌱 ಮಣ್ಣಿನ ಪ್ರಕಾರ",
        weather: "☀ ಹವಾಮಾನ/ಋತು",
        land: "🌾 ಭೂಮಿ ಗಾತ್ರ",
        water: "💧 ನೀರಿನ ಲಭ್ಯತೆ",
        recommend: "ಬೆಳೆ ಶಿಫಾರಸು ಮಾಡಿ",
        result: "ಶಿಫಾರಸು ಮಾಡಿದ ಬೆಳೆ",
        menu_language: "ಭಾಷೆ ಬದಲಿಸಿ",
        menu_mute: "ಮ್ಯೂಟ್/ಅನ್‌ಮ್ಯೂಟ್",
        menu_terms: "ನಿಯಮಗಳು ಮತ್ತು ಷರತ್ತುಗಳು",
        menu_settings: "ಸಂಯೋಜನೆಗಳು",
        nav_home: "ಮುಖಪುಟ",
        nav_calc: "ಕ್ಯಾಲ್ಕ್ಯುಲೇಟರ್",
        nav_feedback: "ಪ್ರತಿಕ್ರಿಯೆ",
        nav_profile: "ಪ್ರೊಫೈಲ್",
        voice_chat: "ವಾಯ್ಸ್ ಚಾಟ್",
        calc_title: "ಕ್ಯಾಲ್ಕ್ಯುಲೇಟರ್",
        calc_area: "ಪ್ರදේශ (ಹೆಕ್ಟೇರ್)",
        calc_yield: "ಉತ್ಪಾದನೆ (kg/ha)",
        calc_price: "ಬೆಲೆ (ಪ್ರತಿ kg)",
        calc_fert_ha: "ಸಾರ (kg/ha)",
        calc_pest_ha: "ಕೀಟನಾಶಕ (L/ha)",
        feedback: "ನಿಮ್ಮ ಪ್ರತಿಕ್ರಿಯೆ",
        profile_name: "ಕೃಷಕರ ಹೆಸರು",
        profile_age: "ವಯಸ್ಸು",
        profile_land: "ಒಟ್ಟು ಭೂಮಿ (ha)"
      },
      Odia: {
        title: "ଫସଲ ସୁପାରିଶ",
        soil: "🌱 ମାଟି ପ୍ରକାର",
        weather: "☀ ପାକାଳ/ଋତୁ",
        land: "🌾 ଜମିର ଆକାର",
        water: "💧 ଜଳ ଉପଲବ୍ଧତା",
        recommend: "ଫସଲ ସୁପାରିଶ କରନ୍ତୁ",
        result: "ସୁପାରିଶ ଫସଲ",
        menu_language: "ଭାଷା ବଦଳାନ୍ତୁ",
        menu_mute: "ମ୍ୟୁଟ୍/ଅନମ୍ୟୁଟ୍",
        menu_terms: "ନିୟମ ଏବଂ ସର୍ତ୍ତ",
        menu_settings: "ସେଟିଂସ୍",
        nav_home: "ହୋମ୍",
        nav_calc: "କ୍ୟାଲକୁଲେଟର୍",
        nav_feedback: "ମତାମତ",
        nav_profile: "ପ୍ରୋଫାଇଲ୍",
        voice_chat: "ଭଏସ୍ ଚାଟ୍",
        calc_title: "କ୍ୟାଲ୍କୁଲେଟର୍",
        calc_area: "ମାଳିକ (ହେକ୍ଟାର)",
        calc_yield: "ଉତ୍ପାଦନ (କି.ଗ୍ରା/ହେ)",
        calc_price: "ଦାମ (ପ୍ରତି କି.ଗ୍ରା)",
        calc_fert_ha: "ଉର୍ବରକ (କି.ଗ୍ରା/ହେ)",
        calc_pest_ha: "ପେଷ୍ଟିସାଇଡ୍ (ଲି/ହେ)",
        feedback: "ଆପଣଙ୍କର ମତାମତ",
        profile_name: "କୃଷକଙ୍କ ନାମ",
        profile_age: "ବୟସ",
        profile_land: "ମୋଟ ଜମି (ହେ)"
      },
      Punjabi: {
        title: "ਫਸਲ ਸਿਫਾਰਸ਼",
        soil: "🌱 ਮਿੱਟੀ ਦੀ ਕਿਸਮ",
        weather: "☀ ਮੌਸਮ / ਰੁੱਤ",
        land: "🌾 ਜ਼ਮੀਨ ਦਾ ਆਕਾਰ",
        water: "💧 ਪਾਣੀ ਦੀ ਉਪਲਬਧਤਾ",
        recommend: "ਫਸਲ ਦੀ ਸਿਫਾਰਸ਼ ਕਰੋ",
        result: "ਸਿਫਾਰਸ਼ ਕੀਤੀ ਫਸਲ",
        menu_language: "ਭਾਸ਼ਾ ਬਦਲੋ",
        menu_mute: "ਮਿਊਟ/ਅਨਮਿਊਟ",
        menu_terms: "ਨਿਯਮ ਅਤੇ ਸ਼ਰਤਾਂ",
        menu_settings: "ਸੈਟਿੰਗਾਂ",
        nav_home: "ਘਰ",
        nav_calc: "ਕੈਲਕੁਲੇਟਰ",
        nav_feedback: "ਫੀਡਬੈਕ",
        nav_profile: "ਪ੍ਰੋਫ਼ਾਈਲ",
        voice_chat: "ਵੋਇਸ ਚੈਟ",
        calc_title: "ਕੈਲਕੁਲੇਟਰ",
        calc_area: "ਖੇਤਰ (ਹੈਕਟਰ)",
        calc_yield: "ਉਪਜ (kg/ha)",
        calc_price: "ਕੀਮਤ (ਪਰ kg)",
        calc_fert_ha: "ਖਾਦ (kg/ha)",
        calc_pest_ha: "ਕੀਟਨਾਸ਼ਕ (L/ha)",
        feedback: "ਤੁਹਾਡੇ ਸੁਝਾਅ",
        profile_name: "ਕਿਸਾਨ ਦਾ ਨਾਮ",
        profile_age: "ਉਮਰ",
        profile_land: "ਕੁੱਲ ਜ਼ਮੀਨ (ha)"
      },
      Assamese: {
        title: "ফচল পৰামৰ্শ",
        soil: "🌱 মাটিৰ ধৰণ",
        weather: "☀ বতৰত / ঋতু",
        land: "🌾 জমিৰ আকাৰ",
        water: "💧 পানীৰ উপলব্ধতা",
        recommend: "ফচল পৰামৰ্শ দিয়ক",
        result: "পৰামৰ্শকৃত ফচল",
        menu_language: "ভাষা সলনি কৰক",
        menu_mute: "মিউট/আনমিউট",
        menu_terms: "নিয়ম আৰু শর্ত",
        menu_settings: "ছেটিংচ",
        nav_home: "হোম",
        nav_calc: "কেলকুলেটৰ",
        nav_feedback: "প্ৰতিক্ৰিয়া",
        nav_profile: "প্ৰোফাইল",
        voice_chat: "ভয়েছ চেট",
        calc_title: "কেলকুলেটৰ",
        calc_area: "এলাকা (হেক্টৰ)",
        calc_yield: "উৎপাদন (kg/ha)",
        calc_price: "দাম (প্ৰতি kg)",
        calc_fert_ha: "সাৰ (kg/ha)",
        calc_pest_ha: "কীটনাশক (L/ha)",
        feedback: "আপোনাৰ মতামত",
        profile_name: "কৃষকৰ নাম",
        profile_age: "বয়স",
        profile_land: "মুঠ ভূমি (ha)"
      },
      Maithili: {
        title: "फसल सिफारिस",
        soil: "🌱 माटिक प्रकार",
        weather: "☀ मौसम / ऋतु",
        land: "🌾 जमिन क आकार",
        water: "💧 पानी उपलब्धता",
        recommend: "फसल सुझाउ",
        result: "सुझावल फसल",
        menu_language: "भाषा बदलू",
        menu_mute: "म्यूट/अनम्यूट",
        menu_terms: "नियम आ शर्त",
        menu_settings: "सेटिंग्स",
        nav_home: "होम",
        nav_calc: "कैलकुलेटर",
        nav_feedback: "फीडबैक",
        nav_profile: "प्रोफाइल",
        voice_chat: "वॉइस चैट",
        calc_title: "कैलकुलेटर",
        calc_area: "क्षेत्र (हेक्टेयर)",
        calc_yield: "उपज (kg/ha)",
        calc_price: "मूल्य (प्रति kg)",
        calc_fert_ha: "उर्वरक (kg/ha)",
        calc_pest_ha: "कीटनाशक (L/ha)",
        feedback: "अपन प्रतिकिया",
        profile_name: "किसान के नाम",
        profile_age: "उम्र",
        profile_land: "कुल जमीन (ha)"
      },
      Sanskrit: {
        title: "कृषि सिफारिश",
        soil: "🌱 भूमेः प्रकारः",
        weather: "☀ ऋतु / मौसम",
        land: "🌾 भूमेः परिमाणम्",
        water: "💧 जल उपलब्धता",
        recommend: "फसल् निर्दिशेत्",
        result: "निर्दिष्टा फसलः",
        menu_language: "भाषां परिवर्तय",
        menu_mute: "निःशब्ध/शब्ध",
        menu_terms: "नियमाः तथा शर्ताः",
        menu_settings: "विन्यासाः",
        nav_home: "गृह",
        nav_calc: "गणक",
        nav_feedback: "प्रतिक्रियाः",
        nav_profile: "प्रोफाइल",
        voice_chat: "वाणी संवाद",
        calc_title: "गणक",
        calc_area: "क्षेत्रम् (हेक्टेयर)",
        calc_yield: "उत्पादनम् (kg/ha)",
        calc_price: "मूल्यः (प्रति kg)",
        calc_fert_ha: "उर्वरकः (kg/ha)",
        calc_pest_ha: "कीटनाशकः (L/ha)",
        feedback: "तव प्रतिक्रिया",
        profile_name: "कृषकस्य नाम",
        profile_age: "वयः",
        profile_land: "कुल भूमि (ha)"
      },
      Konkani: {
        title: "फुडवांची शिफारस",
        soil: "🌱 माती प्रकार",
        weather: "☀ हवामान/ऋतु",
        land: "🌾 जमीन आकार",
        water: "💧 पाणी उपलब्धता",
        recommend: "फुडवा सुचवा",
        result: "सुचलेली फसल",
        menu_language: "भाषा बदला",
        menu_mute: "म्युट/अनम्युट",
        menu_terms: "नियम आणि अट",
        menu_settings: "सेटिंग्स",
        nav_home: "होम",
        nav_calc: "कॅल्क्युलेटर",
        nav_feedback: "अभिप्राय",
        nav_profile: "प्रोफाइल",
        voice_chat: "वॉइस चॅट",
        calc_title: "कॅल्क्युलेटर",
        calc_area: "एरिया (हेक्टर)",
        calc_yield: "उपज (kg/ha)",
        calc_price: "किंमत (प्रति kg)",
        calc_fert_ha: "खते (kg/ha)",
        calc_pest_ha: "किटनाशक (L/ha)",
        feedback: "तुमचो फीडबॅक",
        profile_name: "शेतकर्‍याचे नाव",
        profile_age: "वय",
        profile_land: "एकूण जमीन (ha)"
      },
      Sindhi: {
        title: "فصل جي سفارش",
        soil: "🌱 مٽي جو قسم",
        weather: "☀ موسم/فصل",
        land: "🌾 زمين جو ماپ",
        water: "💧 پاڻي جي دستيابي",
        recommend: "فصل جي سفارش ڪريو",
        result: "سفارش ٿيل فصل",
        menu_language: "ٻولي بدلائي",
        menu_mute: "ميوٽ/انميوٽ",
        menu_terms: "شرطون ۽ ضابطا",
        menu_settings: "سيٽنگون",
        nav_home: "هوم",
        nav_calc: "ڪلڪيوليٽر",
        nav_feedback: "راءِ",
        nav_profile: "پروفائل",
        voice_chat: "وائس چيٽ",
        calc_title: "ڪلڪيوليٽر",
        calc_area: "علائقو (هيڪٽر)",
        calc_yield: "پيداواري (kg/ha)",
        calc_price: "قيمت (في kg)",
        calc_fert_ha: "زرعي سر (kg/ha)",
        calc_pest_ha: "پيٽيسائيڊ (L/ha)",
        feedback: "توھان جي راءِ",
        profile_name: "ڪسان جو نالو",
        profile_age: "عمر",
        profile_land: "ڪل زمين (ha)"
      },
      Dogri: {
        title: "फसल सिफारिश",
        soil: "🌱 मिट्टी द किसम",
        weather: "☀ मौसम/ऋतु",
        land: "🌾 जमीन द आकार",
        water: "💧 पानी द उपलब्धता",
        recommend: "फसल सुझाओ",
        result: "सुझाई गई फसल",
        menu_language: "भाषा बदलो",
        menu_mute: "म्यूट/अनम्यूट",
        menu_terms: "नियम",
        menu_settings: "सेटिंग्स",
        nav_home: "होम",
        nav_calc: "कैलकुलेटर",
        nav_feedback: "फीडबैक",
        nav_profile: "प्रोफ़ाइल",
        voice_chat: "वॉइस चैट",
        calc_title: "कैलकुलेटर",
        calc_area: "क्षेत्र (हैक्टेयर)",
        calc_yield: "उपज (kg/ha)",
        calc_price: "कीमत (प्रति kg)",
        calc_fert_ha: "खाद (kg/ha)",
        calc_pest_ha: "कीटनाशक (L/ha)",
        feedback: "तुम्हारी प्रतिक्रिया",
        profile_name: "किसान द नाम",
        profile_age: "उम्र",
        profile_land: "कुल जमीन (ha)"
      },
      Manipuri: {
        title: "চরাই সুপারিশ",
        soil: "🌱 মাটি প্রকার",
        weather: "☀ আবহাওয়া / ঋতু",
        land: "🌾 জমির আয়তন",
        water: "💧 জলের প্রাপ্যতা",
        recommend: "চরাই সুপারিশ করুন",
        result: "সুপারিশকৃত ফসল",
        menu_language: "ভাষা পরিবর্তন করুন",
        menu_mute: "মিউট/আনমিউট",
        menu_terms: "নিয়ম ও শর্ত",
        menu_settings: "সেটিংস",
        nav_home: "হোম",
        nav_calc: "ক্যালকুলেটর",
        nav_feedback: "ফিডব্যাক",
        nav_profile: "প্রোফাইল",
        voice_chat: "ভয়েস চ্যাট",
        calc_title: "ক্যালকুলেটর",
        calc_area: "এলাকা (হেক্টর)",
        calc_yield: "উৎপাদন (kg/ha)",
        calc_price: "মূল্য (প্রতি kg)",
        calc_fert_ha: "সার (kg/ha)",
        calc_pest_ha: "পেস্ট (L/ha)",
        feedback: "আপনার প্রতিক্রিয়া",
        profile_name: "চাষির নাম",
        profile_age: "বয়স",
        profile_land: "মোট জমি (ha)"
      },
      Bodo: {
        title: "फसल सिफारिस",
        soil: "🌱 माटि प्रकार",
        weather: "☀ मौसम/ऋतु",
        land: "🌾 जमीन आकार",
        water: "💧 पानी उपलब्धता",
        recommend: "फसल सिफारिस करो",
        result: "सुझा फसल",
        menu_language: "भाषा बदला",
        menu_mute: "म्यूट/अनम्यूट",
        menu_terms: "नियम",
        menu_settings: "सेटिंग्स",
        nav_home: "होम",
        nav_calc: "कैलकुलेटर",
        nav_feedback: "फीडबैक",
        nav_profile: "प्रोफाइल",
        voice_chat: "वॉइस चेट",
        calc_title: "कैलकुलेटर",
        calc_area: "एरिया (ha)",
        calc_yield: "उपज (kg/ha)",
        calc_price: "मूल्य (प्रति kg)",
        calc_fert_ha: "खाद (kg/ha)",
        calc_pest_ha: "पेस्ट (L/ha)",
        feedback: "अपन फीडबैक",
        profile_name: "किसान नाओं",
        profile_age: "उमर",
        profile_land: "कुल जमीन (ha)"
      },
      Nepali: {
        title: "बाली सिफारिस",
        soil: "🌱 माटोको प्रकार",
        weather: "☀ मौसम / ऋतु",
        land: "🌾 जमिनको आकार",
        water: "💧 पानीको उपलब्धता",
        recommend: "बाली सिफारिस गर्नुहोस्",
        result: "सिफारिश गरिएको बाली",
        menu_language: "भाषा परिवर्तन गर्नुहोस्",
        menu_mute: "म्यूट/अनम्यूट",
        menu_terms: "नियम र शर्तहरू",
        menu_settings: "सेटिङ्स",
        nav_home: "होम",
        nav_calc: "क्यालकुलेटर",
        nav_feedback: "प्रतिक्रिया",
        nav_profile: "प्रोफाइल",
        voice_chat: "भ्वाइस च्याट",
        calc_title: "क्यालकुलेटर",
        calc_area: "क्षेत्र (ha)",
        calc_yield: "उपज (kg/ha)",
        calc_price: "मूल्य (प्रति kg)",
        calc_fert_ha: "खाद (kg/ha)",
        calc_pest_ha: "पेस्ट (L/ha)",
        feedback: "तपाईंको प्रतिक्रियाहरू",
        profile_name: "किसानको नाम",
        profile_age: "उमेर",
        profile_land: "कुल जग्गा (ha)"
      },
      Santali: {
        title: "ᱯᱟᱱᱛᱟ ᱦᱟᱹᱞᱤ",
        soil: "🌱 ᱢᱟᱴᱤ ᱠᱚ",
        weather: "☀ ᱣᱮᱛᱚᱨ/ᱥᱤᱡᱚᱱ",
        land: "🌾 ᱡᱟᱢᱤᱱ ᱮᱫ",
        water: "💧 ᱜᱟᱹᱲᱤ ᱠᱚ",
        recommend: "ᱯᱟᱱᱛᱟ ᱦᱟᱹᱞᱤ ᱢᱮ",
        result: "ᱯᱟᱱᱛᱟ ᱦᱟᱹᱞᱤ",
        menu_language: "ᱚᱞ ᱚᱞᱤᱠ ᱢᱟᱲᱟᱝ",
        menu_mute: "ᱣᱚᱭᱥ ᱢᱭᱩᱴ/ᱟᱱᱢᱭᱩᱴ",
        menu_terms: "ᱱᱤᱭᱚᱢ",
        menu_settings: "ᱥᱮᱛᱤᱝ",
        nav_home: "ᱦᱚᱢ",
        nav_calc: "ᱠᱮᱞᱠᱩᱞᱮᱛᱚᱨ",
        nav_feedback: "ᱯᱨᱤᱛᱤᱠᱨᱤᱭᱟ",
        nav_profile: "ᱯᱨᱚᱯᱟᱭᱤᱞ",
        voice_chat: "ᱯᱟᱱᱛᱟ ᱚᱞ",
        calc_title: "କାଲକୁଲେଟର",
        calc_area: "ᱟᱨିଆ (ha)",
        calc_yield: "ᱯᱟᱱᱛା ᱯᱳᱯ (kg/ha)",
        calc_price: "ମୂଲ୍ୟ (per kg)",
        calc_fert_ha: "ଏରୁରକ (kg/ha)",
        calc_pest_ha: "ପେଷ୍ଟ (L/ha)",
        feedback: "ତୁମ ମତ",
        profile_name: "ନାମ",
        profile_age: "ବୟସ",
        profile_land: "ଜମି (ha)"
      },
      Kashmiri: {
        title: "فصل سفارش",
        soil: "🌱 مٹی کی قسم",
        weather: "☀ موسم/فصل",
        land: "🌾 زمین کا سائز",
        water: "💧 پانی کی دستیابی",
        recommend: "فصل کی سفارش کریں",
        result: "سفارش شدہ فصل",
        menu_language: "زبان تبدیل کریں",
        menu_mute: "میوٹ/انمِیوٹ",
        menu_terms: "شرائط و ضوابط",
        menu_settings: "سیٹنگز",
        nav_home: "ہوم",
        nav_calc: "کیلکولیٹر",
        nav_feedback: "رائے",
        nav_profile: "پروفائل",
        voice_chat: "وائس چیٹ",
        calc_title: "کیلکولیٹر",
        calc_area: "رقبہ (ha)",
        calc_yield: "فی ہیکٹر پیداوار (kg/ha)",
        calc_price: "قیمت (فی kg)",
        calc_fert_ha: "کھاد (kg/ha)",
        calc_pest_ha: "پیسٹ (L/ha)",
        feedback: "آپکی رائے",
        profile_name: "کسان کا نام",
        profile_age: "عمر",
        profile_land: "کل زمین (ha)"
      },
      Tulu: {
        title: "ಬೆಳೆ ಸಲಹೆ",
        soil: "🌱 ಮಣ್ಣು ಪ್ರಕಾರ",
        weather: "☀ ಹವಾಮಾನ/ಕಾಲ",
        land: "🌾 ಭೂಮಿ ಗಾತ್ರ",
        water: "💧 ನೀರು ಲಭ್ಯತೆ",
        recommend: "ಬೆಳೆ ಸಲಹೆಮಾಡು",
        result: "ಶಿಫಾರಸು ಮಾಡಿದ ಬೆಳೆ",
        menu_language: "ಭಾಷೆ ಬದಲಿಸಿ",
        menu_mute: "ಮ್ಯೂಟ್/ಅನ್‌ಮ್ಯೂಟ್",
        menu_terms: "ನಿಯಮಗಳು",
        menu_settings: "ಸೆಟ್ಟಿಂಗ್ಸ್",
        nav_home: "ಹೋಮ್",
        nav_calc: "ಕ್ಯಾಲ್ಕುಲೇಟರ್",
        nav_feedback: "ಪ್ರತಿಕ್ರಿಯಾ",
        nav_profile: "ಪ್ರೊಫೈಲ್",
        voice_chat: "ವಾಯ್ಸ್ ಚಾಟ್",
        calc_title: "ಕ್ಯಾಲ್ಕುಲೇಟರ್",
        calc_area: "ಕ್ಷೇತ್ರ (ha)",
        calc_yield: "ಉತ್ಪಾದನೆ (kg/ha)",
        calc_price: "ಬೆಲೆ (ಪ್ರತಿ kg)",
        calc_fert_ha: "ಸಾರ (kg/ha)",
        calc_pest_ha: "ಪೆಸ್ತ್ (L/ha)",
        feedback: "ನಿಮ್ಮ ಪ್ರತಿಕ್ರಿಯೆ",
        profile_name: "ಕೃಷಕಾರರ ಹೆಸರು",
        profile_age: "ವಯಸ್ಸು",
        profile_land: "ಒಟ್ಟು ಭೂಮಿ (ha)"
      },
      Bhojpuri: {
        title: "फसल सलाह",
        soil: "🌱 मिट्टी के प्रकार",
        weather: "☀ मौसम/ऋतु",
        land: "🌾 जमीन के आकार",
        water: "💧 पानी की उपलब्धता",
        recommend: "फसल सुझाव",
        result: "सुझाई गई फसल",
        menu_language: "भाषा बदल",
        menu_mute: "म्यूट/अनम्यूट",
        menu_terms: "नियम और शर्त",
        menu_settings: "सेटिंग्स",
        nav_home: "होम",
        nav_calc: "कैलकुलेटर",
        nav_feedback: "फीडबैक",
        nav_profile: "प्रोफ़ाइल",
        voice_chat: "वॉइस चैट",
        calc_title: "कैलकुलेटर",
        calc_area: "क्षेत्र (ha)",
        calc_yield: "उपज (kg/ha)",
        calc_price: "मूल्य (प्रति kg)",
        calc_fert_ha: "खाद (kg/ha)",
        calc_pest_ha: "कीटनाशक (L/ha)",
        feedback: "आपके सुझाव",
        profile_name: "किसान का नाम",
        profile_age: "उम्र",
        profile_land: "कुल जमीन (ha)"
      },
      Awadhi: {
        title: "फसल सिफारिस",
        soil: "🌱 मिट्टी क किसिम",
        weather: "☀ मौसम/ऋतु",
        land: "🌾 जमीन क आकार",
        water: "💧 पानी क उपलब्धता",
        recommend: "फसल सुझावा",
        result: "सुझाई फसल",
        menu_language: "भाषा बदल",
        menu_mute: "म्यूट/अनम्यूट",
        menu_terms: "नियम अउर शर्त",
        menu_settings: "सेटिंग्स",
        nav_home: "घर",
        nav_calc: "कैलकुलेटर",
        nav_feedback: "फीडबैक",
        nav_profile: "प्रोफाइल",
        voice_chat: "वॉइस चैट",
        calc_title: "कैलकुलेटर",
        calc_area: "क्षेत्र (ha)",
        calc_yield: "उपज (kg/ha)",
        calc_price: "कीमत (प्रति kg)",
        calc_fert_ha: "उर्वरक (kg/ha)",
        calc_pest_ha: "पेस्ट (L/ha)",
        feedback: "आपन राय",
        profile_name: "किसान के नाँव",
        profile_age: "उमर",
        profile_land: "कुल जमीन (ha)"
      },
      Haryanvi: {
        title: "फसल सलाह",
        soil: "🌱 मिट्टी की किस्म",
        weather: "☀ मौसम/ऋतु",
        land: "🌾 जमीन का साइज",
        water: "💧 पानी की उपलब्धता",
        recommend: "फसल सुझाओ",
        result: "सुझाई फसल",
        menu_language: "भाषा बदलो",
        menu_mute: "म्यूट/अनम्यूट",
        menu_terms: "नियम और शर्त",
        menu_settings: "सेटिंग्स",
        nav_home: "होम",
        nav_calc: "कैलकुलेटर",
        nav_feedback: "फीडबैक",
        nav_profile: "प्रोफाइल",
        voice_chat: "वॉइस चैट",
        calc_title: "कैलकुलेटर",
        calc_area: "क्षेत्र (ha)",
        calc_yield: "उपज (kg/ha)",
        calc_price: "मूल्य (प्रति kg)",
        calc_fert_ha: "खाद (kg/ha)",
        calc_pest_ha: "कीटनाशक (L/ha)",
        feedback: "अपनी राय",
        profile_name: "किसान का नाम",
        profile_age: "उम्र",
        profile_land: "कुल जमीन (ha)"
      }
    };

    // map language -> locale for speech (best effort)
    const localeMap = {
      English: "en-IN", Hindi: "hi-IN", Telugu: "te-IN", Bengali: "bn-IN", Malayalam: "ml-IN",
      Kannada: "kn-IN", Odia: "or-IN", Punjabi: "pa-IN", Assamese: "as-IN", Maithili: "mai-IN",
      Sanskrit: "sa-IN", Konkani: "kok-IN", Sindhi: "sd-PK", Dogri: "doi-IN", Manipuri: "mni-IN",
      Bodo: "brx-IN", Nepali: "ne-NP", Santali: "sat-IN", Kashmiri: "ks-IN", Tulu: "tcy-IN",
      Bhojpuri: "bho-IN", Awadhi: "awa-IN", Haryanvi: "hi-IN"
    };

    // crop DB with fertilizer & pesticide suggestions (with icons)
    const cropDB = [
      { name: "Wheat", icon: "🌾", fertilizers: "🧪 NPK 20-20-10 @ 120 kg/ha", pesticides: "🐛❌ Carbaryl — follow label" },
      { name: "Rice", icon: "🍚", fertilizers: "🧪 Urea @ 100 kg/ha + DAP", pesticides: "🐛❌ Tricyclazole — follow label" },
      { name: "Maize", icon: "🌽", fertilizers: "🧪 DAP @ 150 kg/ha", pesticides: "🐛❌ Lambda-cyhalothrin — follow label" },
      { name: "Potato", icon: "🥔", fertilizers: "🧪 NPK 16-16-16 @ 200 kg/ha", pesticides: "🐛❌ Chlorothalonil — follow label" },
      { name: "Tomato", icon: "🍅", fertilizers: "🧪 Balanced NPK + organic manure", pesticides: "🐛❌ Neem oil / Copper fungicide" },
      { name: "Sugarcane", icon: "🌴", fertilizers: "🧪 High N + K application", pesticides: "🐛❌ Soil borne disease management" },
      { name: "Sunflower", icon: "🌻", fertilizers: "🧪 Moderate N, higher P", pesticides: "🐛❌ Integrated pest management" },
      { name: "Pulses", icon: "🫘", fertilizers: "🧪 Low N; Rhizobial inoculant", pesticides: "🐛❌ Botanical/Low-toxicity sprays" }
    ];

    // languages list in same order as requested
    const languageList = [
      "Telugu","English","Hindi","Bengali","Malayalam","Kannada","Odia","Punjabi","Assamese","Maithili",
      "Sanskrit","Konkani","Sindhi","Dogri","Manipuri","Bodo","Nepali","Santali","Kashmiri","Tulu",
      "Bhojpuri","Awadhi","Haryanvi"
    ];

    /************************
     * BUILD LANGUAGE BUTTONS (PAGE 1 & modal)
     ************************/
    function buildLanguageButtons() {
      const container = document.getElementById("languageButtons");
      const modalContainer = document.getElementById("modalLanguageButtons");
      container.innerHTML = "";
      modalContainer.innerHTML = "";
      languageList.forEach(lang => {
        const btn = createLangButton(lang);
        container.appendChild(btn);
        // modal copy
        const mbtn = createLangButton(lang, true);
        modalContainer.appendChild(mbtn);
      });
    }

    function createLangButton(lang, small=false){
      const btn = document.createElement("button");
      btn.className = "lang-btn";
      btn.type = "button";
      // flag/emblem placeholder: first two letters
      const flag = document.createElement("span");
      flag.className = "lang-flag";
      flag.textContent = lang.slice(0,2).toUpperCase();
      const text = document.createElement("span");
      text.textContent = lang;
      btn.appendChild(flag);
      btn.appendChild(text);
      btn.onclick = () => selectLanguage(lang);
      if(small) btn.style.fontSize = "14px";
      return btn;
    }

    /**********************
     * STATE
     **********************/
    let currentLanguage = "English";
    let muted = false;

    /**********************
     * SELECT LANGUAGE
     **********************/
    function selectLanguage(lang){
      currentLanguage = lang;
      applyTranslations();
      // announce language selected with TTS (if not muted)
      const speakPhrase = speakPhrases[lang] || ("You selected " + lang);
      if(!muted) speakText(speakPhrase, {lang: localeMap[lang] || 'en-IN'});
      // switch to app
      document.getElementById('page1').style.display = 'none';
      document.getElementById('app').style.display = 'flex';
      // hide modals
      closeModal('langModal');
      // show home panel
      showPanel('home', true);
    }

    // sample spoken phrases (approximate / short)
    const speakPhrases = {
      English: "You selected English",
      Hindi: "आपने हिन्दी चुनी",
      Telugu: "మీరు తెలుగు ఎంచుకున్నారు",
      Bengali: "আপনি বাংলা নির্বাচন করেছেন",
      Malayalam: "നീങ്ങൾ മലയാളം തിരഞ്ഞെടുത്തു",
      Kannada: "ನೀವು ಕನ್ನಡ ಆಯ್ಕೆಮಾಡಿದ್ದೀರಿ",
      Odia: "ଆପଣ ଓଡ଼ିଆ ଚୟନ କରିଛନ୍ତି",
      Punjabi: "ਤੁਸੀਂ ਪੰਜਾਬੀ ਚੁਣੀ ਹੈ",
      Assamese: "আপুনি অসমীয়া নিৰ্বাচন কৰিছে",
      Maithili: "अहाँ मैथिली चुनने छी",
      Sanskrit: "भाषां निवेदितवान्",
      Konkani: "तुसां कोंकणी निवडलो",
      Sindhi: "توھان سنڌي چونڊيو",
      Dogri: "तुस्से डोगरी चुनी",
      Manipuri: "আপুনি মণিপুরী বাছনি কৰিছে",
      Bodo: "आहाँ बोड़ो बाचनाय",
      Nepali: "तपाईंले नेपाली चयन गर्नुभयो",
      Santali: "ᱟᱹᱞᱟ ᱥᱟᱱᱛᱟᱞ",
      Kashmiri: "تُس منز زبان چُن",
      Tulu: "ನೀನು ತುಳು ಆಯ್ದಿ",
      Bhojpuri: "रउआ भोजपुरी चुननी",
      Awadhi: "आपने अवधी चुनी",
      Haryanvi: "तू हणयाणवी चुनी"
    };

    /************************
     * APPLY TRANSLATIONS TO UI
     ************************/
    function applyTranslations(){
      const t = translations[currentLanguage] || translations.English;
      // Main labels
      document.getElementById("titleMain").textContent = t.title;
      document.getElementById("soilLabel").textContent = t.soil;
      document.getElementById("weatherLabel").textContent = t.weather;
      document.getElementById("landLabel").textContent = t.land;
      document.getElementById("waterLabel").textContent = t.water;
      document.getElementById("recommendBtn").textContent = t.recommend;

      // menu
      document.getElementById("menuLanguage").textContent = t.menu_language;
      document.getElementById("menuMute").textContent = muted ? (t.menu_mute + " (Muted)") : t.menu_mute;
      document.getElementById("menuTerms").textContent = t.menu_terms;
      document.getElementById("menuSettings").textContent = t.menu_settings;

      // bottom nav labels
      document.getElementById("navHomeLabel").textContent = t.nav_home;
      document.getElementById("navCalcLabel").textContent = t.nav_calc;
      document.getElementById("navFeedbackLabel").textContent = t.nav_feedback;
      document.getElementById("navProfileLabel").textContent = t.nav_profile;

      // voice label
      document.getElementById("voiceChatLabel").textContent = t.voice_chat || "Voice Chat";

      // calculator labels
      document.getElementById("calcTitle").textContent = t.calc_title || "Calculator";
      document.getElementById("calcAreaLabel").textContent = t.calc_area || "Area (hectares)";
      document.getElementById("calcYieldLabel").textContent = t.calc_yield || "Expected yield (kg per hectare)";
      document.getElementById("calcPriceLabel").textContent = t.calc_price || "Market price (per kg)";
      document.getElementById("calcFertPerHaLabel").textContent = t.calc_fert_ha || "Fertilizer (kg/ha)";
      document.getElementById("calcPestPerHaLabel").textContent = t.calc_pest_ha || "Pesticide (L/ha)";

      // feedback/profile labels & placeholders
      document.getElementById("feedbackTitle").textContent = t.feedback || "Feedback";
      document.getElementById("feedbackLabel").textContent = t.feedback || "Your feedback";
      document.getElementById("profileTitle").textContent = t.profile_name || "Profile";
      document.getElementById("profileNameLabel").textContent = t.profile_name || "Farmer Name";
      document.getElementById("profileAgeLabel").textContent = t.profile_age || "Age";
      document.getElementById("profileLandLabel").textContent = t.profile_land || "Total Land (ha)";
      document.getElementById("profileName").setAttribute('placeholder', t.profile_name || "Farmer Name");
      // modal title
      document.getElementById("modalLangTitle").textContent = t.menu_language || "Choose Language";
    }

    /************************
     * MENU & MODALS
     ************************/
    document.getElementById('menuToggleBtn').addEventListener('click', (e) => {
      const panel = document.getElementById('menuPanel');
      panel.style.display = panel.style.display === 'flex' ? 'none' : 'flex';
      panel.style.flexDirection = 'column';
      panel.setAttribute('aria-hidden', panel.style.display === 'none');
    });

    // Show language modal (instead of going back to full page)
    document.getElementById('menuLanguage').addEventListener('click', () => {
      openModal('langModal');
      document.getElementById('menuPanel').style.display = 'none';
    });

    // Mute toggle
    document.getElementById('menuMute').addEventListener('click', () => {
      muted = !muted;
      applyTranslations();
      document.getElementById('menuPanel').style.display = 'none';
      const t = translations[currentLanguage] || translations.English;
      // announce state briefly if unmuting
      if(!muted) speakText(t.menu_mute + " enabled", {lang: localeMap[currentLanguage] || 'en-IN'});
      else alert("Voice muted");
    });

    // Terms modal
    document.getElementById('menuTerms').addEventListener('click', () => {
      openModal('termsModal');
      document.getElementById('menuPanel').style.display = 'none';
    });

    document.getElementById('menuSettings').addEventListener('click', () => {
      alert("Settings (placeholder) — add app preferences here.");
      document.getElementById('menuPanel').style.display = 'none';
    });

    // modal helpers
    function openModal(id){
      document.getElementById(id).style.display = 'flex';
      document.getElementById(id).setAttribute('aria-hidden', 'false');
    }
    function closeModal(id){
      document.getElementById(id).style.display = 'none';
      document.getElementById(id).setAttribute('aria-hidden', 'true');
    }
    document.getElementById('modalCloseBtn').addEventListener('click', () => closeModal('langModal'));
    document.getElementById('termsCloseBtn').addEventListener('click', () => closeModal('termsModal'));

    // clicking outside modal closes
    document.querySelectorAll('.modal-overlay').forEach(overlay => {
      overlay.addEventListener('click', (e) => {
        if(e.target === overlay) overlay.style.display = 'none';
      });
    });

    /************************
     * RECOMMEND CROP (with fertilizer & pesticide icons)
     ************************/
    function recommendCrop(){
      const soil = document.getElementById('soil').value;
      const weather = document.getElementById('weather').value;
      const land = document.getElementById('land').value;
      const water = document.getElementById('water').value;

      // simple heuristic picker (expandable)
      let chosen = cropDB[0]; // default
      if(weather === 'Rainy' && water === 'High') chosen = findCrop('Rice');
      else if(weather === 'Summer' && soil === 'Sandy') chosen = findCrop('Maize');
      else if(soil === 'Clay' && water === 'High') chosen = findCrop('Sugarcane');
      else if(weather === 'Winter' && soil === 'Loamy') chosen = findCrop('Wheat');
      else if(soil === 'Loamy' && weather === 'Summer') chosen = findCrop('Maize');
      else if(water === 'Low') chosen = findCrop('Pulses');
      else chosen = findCrop('Wheat');

      const t = translations[currentLanguage] || translations.English;
      const container = document.getElementById('recommendResult');
      container.innerHTML = `
        <div class="crop-icon">${chosen.icon}</div>
        <div style="flex:1; min-width:220px">
          <div class="meta">${t.result}: ${localizeCropName(chosen.name)}</div>
          <div style="margin-top:8px"><span class="tag">🧪 Fertilizer</span> <span style="margin-left:8px">${chosen.fertilizers}</span></div>
          <div style="margin-top:6px"><span class="tag">🐛❌ Pesticide</span> <span style="margin-left:8px">${chosen.pesticides}</span></div>
          <div style="margin-top:10px" class="small-muted">Recommendation is advisory. Follow local guidelines & product labels.</div>
        </div>
        <div style="min-width:120px; text-align:center">
          <button class="btn-primary" onclick="saveRecommendation()">Save</button>
        </div>
      `;
      container.classList.remove('hidden');

      // speak result
      if(!muted){
        const speak = ${t.result}: ${chosen.name}. Fertilizer recommendation: ${stripEmojis(chosen.fertilizers)}. Pesticide recommendation: ${stripEmojis(chosen.pesticides)}.;
        speakText(speak, {lang: localeMap[currentLanguage] || 'en-IN'});
      }
    }

    function findCrop(name){
      return cropDB.find(c => c.name === name) || cropDB[0];
    }

    function localizeCropName(name){
      // Could add per-language crop-name mapping. For now return english name.
      return name;
    }

    function stripEmojis(text){
      // remove emoji characters for clearer TTS
      return text.replace(/[\u{1F300}-\u{1F6FF}\u{1F900}-\u{1F9FF}\u{2600}-\u{26FF}\u{2700}-\u{27BF}]/gu, '');
    }

    function saveRecommendation(){
      alert("Saved recommendation to local device (placeholder).");
    }

    // wire recommend button
    document.getElementById('recommendBtn').addEventListener('click', recommendCrop);

    /************************
     * SPEECH (TTS + Recognition)
     ************************/
    const micBtn = document.getElementById('micBtn');
    let recognition = null;

    function isSpeechRecognitionSupported(){
      return !!(window.SpeechRecognition || window.webkitSpeechRecognition);
    }

    function startVoice(){
      if(!isSpeechRecognitionSupported()){
        alert('Speech recognition not supported. Use Chrome/Edge for best support.');
        return;
      }
      const SR = window.SpeechRecognition || window.webkitSpeechRecognition;
      recognition = new SR();
      recognition.lang = localeMap[currentLanguage] || 'en-IN';
      recognition.interimResults = false;
      recognition.maxAlternatives = 1;

      micBtn.classList.add('pulse');
      recognition.start();

      recognition.onresult = (evt) => {
        const spoken = evt.results[0][0].transcript;
        const vt = document.getElementById('voiceText');
        vt.textContent = (translations[currentLanguage]?.voice_chat || 'You said') + ": " + spoken;
        // speak what user said (echo) if not muted
        if(!muted) speakText(spoken, {lang: localeMap[currentLanguage]||'en-IN'});
        // Basic voice commands: allow quick fill of fields like "soil loamy", "weather rainy", "recommend"
        handleVoiceCommands(spoken.toLowerCase());
      };

      recognition.onerror = (e) => {
        console.error('Recognition error', e);
        micBtn.classList.remove('pulse');
      };

      recognition.onend = () => {
        micBtn.classList.remove('pulse');
      };
    }

    micBtn.addEventListener('click', startVoice);

    function speakText(text, opts={}){
      try{
        const utter = new SpeechSynthesisUtterance(text);
        if(opts.lang) utter.lang = opts.lang;
        // some browsers choose voices based on lang; choose a match if possible
        const voices = window.speechSynthesis.getVoices();
        if(opts.lang && voices && voices.length){
          const v = voices.find(x => x.lang && x.lang.startsWith(opts.lang.split('-')[0]));
          if(v) utter.voice = v;
        }
        window.speechSynthesis.cancel();
        window.speechSynthesis.speak(utter);
      } catch(err){
        console.warn('TTS error', err);
      }
    }

    // basic voice commands parser (very small)
    function handleVoiceCommands(text){
      if(text.includes('recommend') || text.includes('suggest') || text.includes('सुझ') || text.includes('सुझा')){
        recommendCrop();
        return;
      }
      // set soil
      if(text.includes('loamy') || text.includes('लोमी') || text.includes('लोमी') || text.includes('loam') || text.includes('लौमी')){
        document.getElementById('soil').value = 'Loamy';
      } else if(text.includes('clay') || text.includes('क्ले') || text.includes('clay')){
        document.getElementById('soil').value = 'Clay';
      } else if(text.includes('sandy') || text.includes('रेती') || text.includes('sandy')){
        document.getElementById('soil').value = 'Sandy';
      }
      // set water
      if(text.includes('high') || text.includes('उच्च') || text.includes('high')){
        document.getElementById('water').value = 'High';
      } else if(text.includes('medium') || text.includes('मध्यम') || text.includes('medium')){
        document.getElementById('water').value = 'Medium';
      } else if(text.includes('low') || text.includes('कम') || text.includes('low')){
        document.getElementById('water').value = 'Low';
      }
    }

    /************************
     * PANELS & NAV
     ************************/
    function showPanel(panel, skipAnnounce=false){
      const panels = document.querySelectorAll('.panel');
      panels.forEach(p => p.classList.add('hidden'));
      if(panel === 'home') document.getElementById('panel-home').classList.remove('hidden');
      else if(panel === 'calculator') document.getElementById('panel-calculator').classList.remove('hidden');
      else if(panel === 'feedback') document.getElementById('panel-feedback').classList.remove('hidden');
      else if(panel === 'profile') document.getElementById('panel-profile').classList.remove('hidden');
      // voice announce panel change
      if(!muted && !skipAnnounce){
        const t = translations[currentLanguage] || translations.English;
        const label = (panel==='home'? t.nav_home : panel==='calculator'? t.nav_calc : panel==='feedback'? t.nav_feedback : t.nav_profile);
        speakText(label, {lang: localeMap[currentLanguage]||'en-IN'});
      }
    }
    // expose to global for nav buttons inline onclick
    window.showPanel = showPanel;

    /************************
     * CALCULATOR
     ************************/
    function calculateProfit(){
      const area = parseFloat(document.getElementById('calcArea').value) || 0;
      const yieldPerHa = parseFloat(document.getElementById('calcYield').value) || 0;
      const price = parseFloat(document.getElementById('calcPrice').value) || 0;
      const fertPerHa = parseFloat(document.getElementById('calcFertPerHa').value) || 0;
      const pestPerHa = parseFloat(document.getElementById('calcPestPerHa').value) || 0;

      const totalYield = area * yieldPerHa; // kg
      const income = totalYield * price;
      const fertUnitCost = 40; // ₹ per kg (placeholder)
      const pestUnitCost = 300; // ₹ per L (placeholder)
      const totalFertNeeded = fertPerHa * area;
      const totalPestNeeded = pestPerHa * area;
      const fertCost = totalFertNeeded * fertUnitCost;
      const pestCost = totalPestNeeded * pestUnitCost;
      const totalCost = fertCost + pestCost;
      const profit = income - totalCost;

      const t = translations[currentLanguage] || translations.English;
      const resultBox = document.getElementById('calcResult');
      resultBox.innerHTML = `
        <div><strong>Estimated yield:</strong> ${totalYield.toLocaleString()} kg</div>
        <div><strong>Estimated income:</strong> ₹ ${income.toLocaleString()}</div>
        <div><strong>Fertilizer needed:</strong> ${totalFertNeeded.toLocaleString()} kg (est. ₹ ${fertCost.toLocaleString()})</div>
        <div><strong>Pesticide needed:</strong> ${totalPestNeeded.toLocaleString()} L (est. ₹ ${pestCost.toLocaleString()})</div>
        <div style="margin-top:8px"><strong>Estimated profit:</strong> ₹ ${profit.toLocaleString()}</div>
        <div class="small-muted" style="margin-top:6px">(Estimates exclude labor & other costs.)</div>
      `;
      if(!muted) speakText(${t.calc_title}: calculated. Estimated profit rupees ${Math.round(profit)}, {lang: localeMap[currentLanguage]||'en-IN'});
    }

    document.getElementById('calcBtn').addEventListener('click', calculateProfit);

    /************************
     * FEEDBACK & PROFILE
     ************************/
    function submitFeedback(){
      const text = document.getElementById('feedbackText').value.trim();
      if(!text){ alert('Please enter feedback'); return; }
      document.getElementById('feedbackMsg').textContent = translations[currentLanguage]?.feedback ? 'Thanks — your feedback has been recorded.' : 'Thanks — your feedback has been recorded.';
      document.getElementById('feedbackText').value = '';
      if(!muted) speakText('Thank you for your feedback', {lang: localeMap[currentLanguage]||'en-IN'});
    }
    document.getElementById('feedbackSubmitBtn').addEventListener('click', submitFeedback);

    function saveProfile(){
      const name = document.getElementById('profileName').value.trim();
      const age = document.getElementById('profileAge').value.trim();
      const land = document.getElementById('profileLand').value.trim();
      if(!name){ alert('Please enter your name'); return; }
      document.getElementById('profileMsg').textContent = 'Profile saved.';
      if(!muted) speakText('Profile saved', {lang: localeMap[currentLanguage]||'en-IN'});
    }
    document.getElementById('profileSaveBtn').addEventListener('click', saveProfile);

    /************************
     * INITIALIZATION
     ************************/
    buildLanguageButtons();
    applyTranslations();
    // start on page1
    document.getElementById('page1').style.display = 'block';
    document.getElementById('app').style.display = 'none';

    // accessibility: close menu when clicking outside
    document.addEventListener('click', (e) => {
      const menu = document.getElementById('menuPanel');
      const toggle = document.getElementById('menuToggleBtn');
      if(!menu.contains(e.target) && e.target !== toggle){
        menu.style.display = 'none';
      }
    });

    // make modal language buttons choose language (they were wired when building the buttons)
    // ensure speechSynthesis voices loaded
    window.speechSynthesis.onvoiceschanged = () => { /* voices become available */ };

    /************************
     * Extra nicety: clicking a language in modal hides modal and applies immediately (handled by selectLanguage)
     ************************/
  </script>
</body>
</html>
