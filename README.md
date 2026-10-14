<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Carmen's Electronic Health Record / Registro Médico Personal</title>
  
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Lucide Icons -->
  <script src="https://unpkg.com/lucide@latest"></script>
  <!-- Google Fonts -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">

  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
          },
          colors: {
            teal: {
              50: '#f0fdfa',
              100: '#ccfbf1',
              500: '#14b8a6',
              600: '#0d9488',
              700: '#0f766e',
              800: '#115e59',
              900: '#134e4a',
            }
          }
        }
      }
    }
  </script>

  <style>
    body {
      font-family: 'Inter', sans-serif;
    }

    /* Print Specific Styles */
    @media print {
      .no-print {
        display: none !important;
      }
      body {
        background-color: #ffffff !important;
        color: #000000 !important;
        padding: 0 !important;
      }
      .print-card {
        border: 1px solid #e5e7eb !important;
        box-shadow: none !important;
        break-inside: avoid;
        margin-bottom: 1rem !important;
      }
      .print-container {
        max-width: 100% !important;
        width: 100% !important;
        padding: 0 !important;
        margin: 0 !important;
      }
      .editable-input {
        border: none !important;
        background: transparent !important;
        padding: 0 !important;
      }
    }
  </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex flex-col">

  <!-- TOP HEADER / NAVBAR -->
  <header class="bg-teal-700 text-white sticky top-0 z-50 shadow-md no-print">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-3.5 flex flex-wrap justify-between items-center gap-4">
      
      <!-- Brand & User Title -->
      <div class="flex items-center space-x-3">
        <div class="p-2 bg-teal-600 rounded-xl shadow-inner flex items-center justify-center">
          <i data-lucide="activity" class="w-6 h-6 text-teal-100"></i>
        </div>
        <div>
          <h1 class="text-xl font-bold tracking-tight leading-none" data-en="EHR — Carmen" data-es="RMP — Carmen">EHR — Carmen</h1>
          <p class="text-xs text-teal-200 mt-1" data-en="Personal Electronic Health Record" data-es="Registro Médico Personal Electrónico">Personal Electronic Health Record</p>
        </div>
      </div>

      <!-- Action Controls -->
      <div class="flex items-center gap-2 sm:gap-3 flex-wrap">
        <!-- Search Bar -->
        <div class="relative">
          <input type="text" id="searchInput" onkeyup="filterContent()" 
                 placeholder="Search records..." 
                 data-en-placeholder="Search records..." 
                 data-es-placeholder="Buscar registros..." 
                 class="w-36 sm:w-48 pl-8 pr-3 py-1.5 text-xs sm:text-sm bg-teal-800/60 border border-teal-500/40 rounded-lg text-white placeholder-teal-300 focus:outline-none focus:ring-2 focus:ring-teal-300 transition-all">
          <i data-lucide="search" class="w-4 h-4 text-teal-300 absolute left-2.5 top-2.5"></i>
        </div>

        <!-- Language Toggle -->
        <button onclick="toggleLanguage()" id="langBtn" class="flex items-center gap-1.5 bg-teal-800 hover:bg-teal-900 border border-teal-500/50 px-3 py-1.5 rounded-lg text-xs sm:text-sm font-medium transition-all shadow-sm">
          <i data-lucide="globe" class="w-4 h-4 text-teal-200"></i>
          <span id="langLabel" class="font-semibold text-teal-100">EN | ES</span>
        </button>

        <!-- Save Indicator -->
        <button onclick="saveToLocalStorage()" class="flex items-center gap-1.5 bg-emerald-600 hover:bg-emerald-700 text-white px-3 py-1.5 rounded-lg text-xs sm:text-sm font-medium transition-all shadow-sm">
          <i data-lucide="save" class="w-4 h-4"></i>
          <span data-en="Save" data-es="Guardar">Save</span>
        </button>

        <!-- Print / PDF -->
        <button onclick="window.print()" class="flex items-center gap-1.5 bg-white text-teal-800 hover:bg-teal-50 px-3 py-1.5 rounded-lg text-xs sm:text-sm font-semibold transition-all shadow-sm">
          <i data-lucide="printer" class="w-4 h-4 text-teal-700"></i>
          <span data-en="Print / PDF" data-es="Imprimir / PDF">Print / PDF</span>
        </button>
      </div>
    </div>
  </header>

  <!-- MAIN CONTAINER -->
  <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-6 flex-1 w-full print-container">

    <!-- NAVIGATION TABS (No-Print) -->
    <nav class="flex border-b border-slate-200 mb-6 overflow-x-auto no-print scrollbar-none pb-1" id="navTabs">
      <button onclick="switchTab('overview')" id="tab-overview" class="tab-btn active border-teal-600 text-teal-700 font-semibold px-4 py-2.5 border-b-2 whitespace-nowrap text-sm flex items-center gap-2">
        <i data-lucide="user" class="w-4 h-4"></i> <span data-en="Overview & Emergency" data-es="Resumen y Emergencia">Overview & Emergency</span>
      </button>
      <button onclick="switchTab('conditions')" id="tab-conditions" class="tab-btn border-transparent text-slate-500 hover:text-slate-700 px-4 py-2.5 border-b-2 whitespace-nowrap text-sm flex items-center gap-2">
        <i data-lucide="stethoscope" class="w-4 h-4"></i> <span data-en="Active Conditions & Allergies" data-es="Condiciones y Alergias">Active Conditions & Allergies</span>
      </button>
      <button onclick="switchTab('medications')" id="tab-medications" class="tab-btn border-transparent text-slate-500 hover:text-slate-700 px-4 py-2.5 border-b-2 whitespace-nowrap text-sm flex items-center gap-2">
        <i data-lucide="pill" class="w-4 h-4"></i> <span data-en="Medications" data-es="Medicamentos">Medications</span>
      </button>
      <button onclick="switchTab('history')" id="tab-history" class="tab-btn border-transparent text-slate-500 hover:text-slate-700 px-4 py-2.5 border-b-2 whitespace-nowrap text-sm flex items-center gap-2">
        <i data-lucide="file-text" class="w-4 h-4"></i> <span data-en="Medical History" data-es="Historial Médico">Medical History</span>
      </button>
      <button onclick="switchTab('family')" id="tab-family" class="tab-btn border-transparent text-slate-500 hover:text-slate-700 px-4 py-2.5 border-b-2 whitespace-nowrap text-sm flex items-center gap-2">
        <i data-lucide="users" class="w-4 h-4"></i> <span data-en="Family History" data-es="Historial Familiar">Family History</span>
      </button>
      <button onclick="switchTab('immunizations')" id="tab-immunizations" class="tab-btn border-transparent text-slate-500 hover:text-slate-700 px-4 py-2.5 border-b-2 whitespace-nowrap text-sm flex items-center gap-2">
        <i data-lucide="syringe" class="w-4 h-4"></i> <span data-en="Immunizations" data-es="Vacunas">Immunizations</span>
      </button>
    </nav>

    <!-- SECTION: OVERVIEW & EMERGENCY CARD -->
    <div id="section-overview" class="section-content space-y-6">
      
      <!-- EMERGENCY MEDICAL BADGE / HERO CARD -->
      <div class="bg-gradient-to-r from-red-600 to-rose-700 text-white rounded-2xl p-6 shadow-xl border border-rose-500 print-card relative overflow-hidden">
        <div class="absolute -right-6 -bottom-6 text-rose-500/20 pointer-events-none">
          <i data-lucide="shield-alert" class="w-48 h-48"></i>
        </div>

        <div class="flex flex-col md:flex-row justify-between md:items-center gap-6 relative z-10">
          <div>
            <div class="inline-flex items-center gap-1.5 bg-rose-800/80 px-3 py-1 rounded-full text-xs font-semibold tracking-wide uppercase text-rose-200 mb-3 border border-rose-400/30">
              <i data-lucide="alert-circle" class="w-3.5 h-3.5"></i>
              <span data-en="Emergency Quick Response Card" data-es="Tarjeta de Respuesta Rápida de Emergencia">Emergency Quick Response Card</span>
            </div>
            <h2 class="text-3xl font-extrabold tracking-tight">Carmen</h2>
            <p class="text-rose-100 text-sm mt-1 flex items-center gap-4 flex-wrap">
              <span><strong data-en="Age:" data-es="Edad:">Age:</strong> 29</span>
              <span>•</span>
              <span><strong data-en="Blood Type:" data-es="Grupo Sanguíneo:">Blood Type:</strong> <span class="bg-white text-rose-700 px-2 py-0.5 rounded-md font-bold">A+ (A Positive)</span></span>
            </p>
          </div>

          <!-- Crucial Alerts -->
          <div class="bg-white/10 backdrop-blur-md rounded-xl p-4 border border-white/20 max-w-md w-full">
            <h3 class="text-xs font-bold uppercase tracking-wider text-rose-200 mb-2 flex items-center gap-1.5">
              <i data-lucide="heart-pulse" class="w-4 h-4"></i>
              <span data-en="Critical Alerts" data-es="Alertas Críticas">Critical Alerts</span>
            </h3>
            <ul class="text-xs sm:text-sm space-y-1.5 text-rose-50">
              <li class="flex items-start gap-2">
                <i data-lucide="check-circle" class="w-4 h-4 text-emerald-300 shrink-0 mt-0.5"></i>
                <span><strong data-en="Key Diagnosis:" data-es="Diagnóstico Clave:">Key Diagnosis:</strong> <span data-en="Atopic Dermatitis (Eczema)" data-es="Dermatitis Atópica (Eczema)">Atopic Dermatitis (Eczema)</span></span>
              </li>
              <li class="flex items-start gap-2">
                <i data-lucide="check-circle" class="w-4 h-4 text-emerald-300 shrink-0 mt-0.5"></i>
                <span><strong data-en="Drug Allergies:" data-es="Alergias a Drogas:">Drug Allergies:</strong> <span data-en="NKDA (No Known Drug Allergies)" data-es="NKDA (Sin Alergias Conocidas)">NKDA (No Known Drug Allergies)</span></span>
              </li>
            </ul>
          </div>
        </div>
      </div>

      <!-- PERSONAL DETAILS & CONTACT CARDS GRID -->
      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
        
        <!-- Core Details -->
        <div class="bg-white rounded-xl p-5 border border-slate-200 shadow-sm hover:shadow-md transition-shadow print-card">
          <div class="flex items-center gap-3 border-b border-slate-100 pb-3 mb-4">
            <div class="p-2 bg-teal-50 text-teal-600 rounded-lg">
              <i data-lucide="user-check" class="w-5 h-5"></i>
            </div>
            <h3 class="font-bold text-slate-800 text-base" data-en="Personal Details" data-es="Datos Personales">Personal Details</h3>
          </div>
          <div class="space-y-3 text-sm">
            <div>
              <label class="text-xs font-semibold text-slate-400 uppercase" data-en="Full Name" data-es="Nombre Completo">Full Name</label>
              <input type="text" id="info-fullname" value="Carmen" class="w-full font-medium text-slate-800 bg-slate-50 border border-slate-200 rounded-lg p-2 mt-0.5 focus:bg-white focus:ring-2 focus:ring-teal-500 outline-none transition-all">
            </div>
            <div class="grid grid-cols-2 gap-3">
              <div>
                <label class="text-xs font-semibold text-slate-400 uppercase" data-en="Age" data-es="Edad">Age</label>
                <input type="number" id="info-age" value="29" class="w-full font-medium text-slate-800 bg-slate-50 border border-slate-200 rounded-lg p-2 mt-0.5 focus:bg-white focus:ring-2 focus:ring-teal-500 outline-none transition-all">
              </div>
              <div>
                <label class="text-xs font-semibold text-slate-400 uppercase" data-en="Blood Type" data-es="Grupo Sanguíneo">Blood Type</label>
                <input type="text" id="info-blood" value="A+" class="w-full font-bold text-teal-700 bg-slate-50 border border-slate-200 rounded-lg p-2 mt-0.5 focus:bg-white focus:ring-2 focus:ring-teal-500 outline-none transition-all">
              </div>
            </div>
            <div>
              <label class="text-xs font-semibold text-slate-400 uppercase" data-en="Primary Language" data-es="Idioma Principal">Primary Language</label>
              <input type="text" id="info-lang" value="English / Spanish" class="w-full font-medium text-slate-800 bg-slate-50 border border-slate-200 rounded-lg p-2 mt-0.5 focus:bg-white focus:ring-2 focus:ring-teal-500 outline-none transition-all">
            </div>
          </div>
        </div>

        <!-- Emergency Contact -->
        <div class="bg-white rounded-xl p-5 border border-slate-200 shadow-sm hover:shadow-md transition-shadow print-card">
          <div class="flex items-center gap-3 border-b border-slate-100 pb-3 mb-4">
            <div class="p-2 bg-rose-50 text-rose-600 rounded-lg">
              <i data-lucide="phone-call" class="w-5 h-5"></i>
            </div>
            <h3 class="font-bold text-slate-800 text-base" data-en="Emergency Contact" data-es="Contacto de Emergencia">Emergency Contact</h3>
          </div>
          <div class="space-y-3 text-sm">
            <div>
              <label class="text-xs font-semibold text-slate-400 uppercase" data-en="Contact Name" data-es="Nombre de Contacto">Contact Name</label>
              <input type="text" id="info-em-name" value="Maria (Mother)" class="w-full font-medium text-slate-800 bg-slate-50 border border-slate-200 rounded-lg p-2 mt-0.5 focus:bg-white focus:ring-2 focus:ring-teal-500 outline-none transition-all">
            </div>
            <div>
              <label class="text-xs font-semibold text-slate-400 uppercase" data-en="Phone Number" data-es="Teléfono">Phone Number</label>
              <input type="text" id="info-em-phone" value="+1 (555) 019-2834" class="w-full font-medium text-slate-800 bg-slate-50 border border-slate-200 rounded-lg p-2 mt-0.5 focus:bg-white focus:ring-2 focus:ring-teal-500 outline-none transition-all">
            </div>
            <div>
              <label class="text-xs font-semibold text-slate-400 uppercase" data-en="Relationship" data-es="Relación">Relationship</label>
              <input type="text" id="info-em-rel" value="Mother / Madre" class="w-full font-medium text-slate-800 bg-slate-50 border border-slate-200 rounded-lg p-2 mt-0.5 focus:bg-white focus:ring-2 focus:ring-teal-500 outline-none transition-all">
            </div>
          </div>
        </div>

        <!-- Primary Care Physician -->
        <div class="bg-white rounded-xl p-5 border border-slate-200 shadow-sm hover:shadow-md transition-shadow print-card md:col-span-2 lg:col-span-1">
          <div class="flex items-center gap-3 border-b border-slate-100 pb-3 mb-4">
            <div class="p-2 bg-blue-50 text-blue-600 rounded-lg">
              <i data-lucide="building-2" class="w-5 h-5"></i>
            </div>
            <h3 class="font-bold text-slate-800 text-base" data-en="Primary Care Physician" data-es="Médico de Cabecera">Primary Care Physician</h3>
          </div>
          <div class="space-y-3 text-sm">
            <div>
              <label class="text-xs font-semibold text-slate-400 uppercase" data-en="Doctor / Clinic" data-es="Doctor / Clínica">Doctor / Clinic</label>
              <input type="text" id="info-doc-name" value="Dr. Elena Rostova - Dermatology" class="w-full font-medium text-slate-800 bg-slate-50 border border-slate-200 rounded-lg p-2 mt-0.5 focus:bg-white focus:ring-2 focus:ring-teal-500 outline-none transition-all">
            </div>
            <div>
              <label class="text-xs font-semibold text-slate-400 uppercase" data-en="Clinic Contact" data-es="Teléfono de la Clínica">Clinic Contact</label>
              <input type="text" id="info-doc-phone" value="+1 (555) 432-8765" class="w-full font-medium text-slate-800 bg-slate-50 border border-slate-200 rounded-lg p-2 mt-0.5 focus:bg-white focus:ring-2 focus:ring-teal-500 outline-none transition-all">
            </div>
            <div>
              <label class="text-xs font-semibold text-slate-400 uppercase" data-en="Hospital Affiliation" data-es="Hospital Afiliado">Hospital Affiliation</label>
              <input type="text" id="info-doc-hosp" value="Central Health Medical Center" class="w-full font-medium text-slate-800 bg-slate-50 border border-slate-200 rounded-lg p-2 mt-0.5 focus:bg-white focus:ring-2 focus:ring-teal-500 outline-none transition-all">
            </div>
          </div>
        </div>

      </div>
    </div>

    <!-- SECTION: ACTIVE CONDITIONS & ALLERGIES -->
    <div id="section-conditions" class="section-content hidden space-y-6">
      
      <!-- MAIN CONDITION HIGHLIGHT: ATOPIC DERMATITIS -->
      <div class="bg-white rounded-2xl border border-teal-200 shadow-sm p-6 print-card">
        <div class="flex flex-wrap items-center justify-between gap-4 border-b border-slate-100 pb-4 mb-4">
          <div class="flex items-center gap-3">
            <div class="p-3 bg-amber-50 text-amber-600 rounded-xl">
              <i data-lucide="sparkles" class="w-6 h-6"></i>
            </div>
            <div>
              <div class="flex items-center gap-2">
                <h3 class="text-xl font-bold text-slate-800" data-en="Atopic Dermatitis" data-es="Dermatitis Atópica">Atopic Dermatitis</h3>
                <span class="bg-amber-100 text-amber-800 text-xs font-semibold px-2.5 py-0.5 rounded-full" data-en="Active Condition" data-es="Condición Activa">Active Condition</span>
              </div>
              <p class="text-xs text-slate-500 mt-0.5" data-en="Chronic Inflammatory Skin Condition (Eczema)" data-es="Afección Cutánea Inflamatoria Crónica (Eczema)">Chronic Inflammatory Skin Condition (Eczema)</p>
            </div>
          </div>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <!-- Triggers -->
          <div class="bg-slate-50 rounded-xl p-4 border border-slate-200">
            <h4 class="font-bold text-slate-700 text-sm mb-3 flex items-center gap-2">
              <i data-lucide="flame" class="w-4 h-4 text-orange-500"></i>
              <span data-en="Known Triggers & Sensitivity" data-es="Desencadenantes Conocidos y Sensibilidad">Known Triggers & Sensitivity</span>
            </h4>
            <ul class="space-y-2 text-sm text-slate-600">
              <li class="flex items-start gap-2">
                <i data-lucide="check" class="w-4 h-4 text-teal-600 shrink-0 mt-0.5"></i>
                <span data-en="Fragrances, synthetic soaps, heavily scented lotions" data-es="Fragancias, jabones sintéticos, lociones perfumadas">Fragrances, synthetic soaps, heavily scented lotions</span>
              </li>
              <li class="flex items-start gap-2">
                <i data-lucide="check" class="w-4 h-4 text-teal-600 shrink-0 mt-0.5"></i>
                <span data-en="Wool and rough synthetic fabrics" data-es="Lana y telas sintéticas ásperas">Wool and rough synthetic fabrics</span>
              </li>
              <li class="flex items-start gap-2">
                <i data-lucide="check" class="w-4 h-4 text-teal-600 shrink-0 mt-0.5"></i>
                <span data-en="Extreme dry weather & dry indoor heating" data-es="Clima extremadamente seco y calefacción seca">Extreme dry weather & dry indoor heating</span>
              </li>
            </ul>
          </div>

          <!-- Routine Management -->
          <div class="bg-slate-50 rounded-xl p-4 border border-slate-200">
            <h4 class="font-bold text-slate-700 text-sm mb-3 flex items-center gap-2">
              <i data-lucide="shield-check" class="w-4 h-4 text-teal-600"></i>
              <span data-en="Management & Treatment Strategy" data-es="Estrategia de Manejo y Tratamiento">Management & Treatment Strategy</span>
            </h4>
            <ul class="space-y-2 text-sm text-slate-600">
              <li class="flex items-start gap-2">
                <i data-lucide="check" class="w-4 h-4 text-teal-600 shrink-0 mt-0.5"></i>
                <span data-en="Daily apply fragrance-free emollient moisturizers after bathing" data-es="Aplicación diaria de cremas emolientes sin fragancia tras el baño">Daily apply fragrance-free emollient moisturizers after bathing</span>
              </li>
              <li class="flex items-start gap-2">
                <i data-lucide="check" class="w-4 h-4 text-teal-600 shrink-0 mt-0.5"></i>
                <span data-en="Topical Hydrocortisone / Steroid ointment for acute flare-ups" data-es="Hidrocortisona tópica / ungüento para brotes agudos">Topical Hydrocortisone / Steroid ointment for acute flare-ups</span>
              </li>
              <li class="flex items-start gap-2">
                <i data-lucide="check" class="w-4 h-4 text-teal-600 shrink-0 mt-0.5"></i>
                <span data-en="Lukewarm short showers; avoid hot water" data-es="Duchas cortas con agua tibia; evitar agua muy caliente">Lukewarm short showers; avoid hot water</span>
              </li>
            </ul>
          </div>
        </div>
      </div>

      <!-- ALLERGIES SECTION -->
      <div class="bg-white rounded-2xl border border-slate-200 shadow-sm p-6 print-card">
        <div class="flex items-center gap-3 border-b border-slate-100 pb-3 mb-4">
          <div class="p-2 bg-rose-50 text-rose-600 rounded-lg">
            <i data-lucide="ban" class="w-5 h-5"></i>
          </div>
          <h3 class="font-bold text-slate-800 text-lg" data-en="Allergies & Sensitivities" data-es="Alergias y Sensibilidades">Allergies & Sensitivities</h3>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div class="p-4 bg-emerald-50 rounded-xl border border-emerald-200 flex items-start gap-3">
            <i data-lucide="shield-check" class="w-6 h-6 text-emerald-600 shrink-0 mt-0.5"></i>
            <div>
              <h4 class="font-bold text-emerald-900 text-sm" data-en="No Known Drug Allergies (NKDA)" data-es="Sin Alergias Medicamentosas Conocidas (NKDA)">No Known Drug Allergies (NKDA)</h4>
              <p class="text-xs text-emerald-700 mt-1" data-en="No adverse severe drug reactions reported to antibiotics or painkillers." data-es="No se han reportado reacciones adversas a antibióticos ni analgésicos.">No adverse severe drug reactions reported to antibiotics or painkillers.</p>
            </div>
          </div>

          <div class="p-4 bg-amber-50 rounded-xl border border-amber-200 flex items-start gap-3">
            <i data-lucide="alert-triangle" class="w-6 h-6 text-amber-600 shrink-0 mt-0.5"></i>
            <div>
              <h4 class="font-bold text-amber-900 text-sm" data-en="Environmental & Skin Contact Sensitivities" data-es="Sensibilidades Ambientales y de Contacto">Environmental & Skin Contact Sensitivities</h4>
              <p class="text-xs text-amber-800 mt-1" data-en="High sensitivity to harsh detergents, nickel jewelry, and scented skincare products." data-es="Alta sensibilidad a detergentes fuertes, joyas de níquel y cosméticos con perfume.">High sensitivity to harsh detergents, nickel jewelry, and scented skincare products.</p>
            </div>
          </div>
        </div>
      </div>

    </div>

    <!-- SECTION: MEDICATIONS -->
    <div id="section-medications" class="section-content hidden space-y-6">
      <div class="bg-white rounded-2xl border border-slate-200 shadow-sm p-6 print-card">
        
        <div class="flex flex-wrap justify-between items-center gap-4 mb-4">
          <div class="flex items-center gap-3">
            <div class="p-2 bg-teal-50 text-teal-600 rounded-lg">
              <i data-lucide="pill" class="w-5 h-5"></i>
            </div>
            <div>
              <h3 class="font-bold text-slate-800 text-lg" data-en="Active Medications & Supplements" data-es="Medicamentos Actuales y Suplementos">Active Medications & Supplements</h3>
              <p class="text-xs text-slate-500" data-en="Manage current prescriptions and daily health supplements" data-es="Gestionar medicamentos recetados y suplementos diarios">Manage current prescriptions and daily health supplements</p>
            </div>
          </div>
          
          <button onclick="addMedicationRow()" class="no-print bg-teal-600 hover:bg-teal-700 text-white text-xs font-semibold px-3 py-2 rounded-lg flex items-center gap-1.5 transition-all shadow-sm">
            <i data-lucide="plus-circle" class="w-4 h-4"></i>
            <span data-en="Add Medication" data-es="Añadir Medicamento">Add Medication</span>
          </button>
        </div>

        <div class="overflow-x-auto">
          <table class="w-full text-left text-sm" id="medsTable">
            <thead class="bg-slate-50 border-y border-slate-200 text-xs font-semibold text-slate-500 uppercase">
              <tr>
                <th class="py-3 px-3" data-en="Medication Name" data-es="Medicamento">Medication Name</th>
                <th class="py-3 px-3" data-en="Dosage" data-es="Dosis">Dosage</th>
                <th class="py-3 px-3" data-en="Frequency" data-es="Frecuencia">Frequency</th>
                <th class="py-3 px-3" data-en="Purpose" data-es="Propósito">Purpose</th>
                <th class="py-3 px-3 no-print text-right" data-en="Action" data-es="Acción">Action</th>
              </tr>
            </thead>
            <tbody class="divide-y divide-slate-100" id="medsTableBody">
              <!-- Default Rows populated dynamically or manually -->
            </tbody>
          </table>
        </div>

      </div>
    </div>

    <!-- SECTION: MEDICAL & SURGICAL HISTORY -->
    <div id="section-history" class="section-content hidden space-y-6">
      <div class="bg-white rounded-2xl border border-slate-200 shadow-sm p-6 print-card">
        
        <div class="flex items-center gap-3 border-b border-slate-100 pb-3 mb-6">
          <div class="p-2 bg-indigo-50 text-indigo-600 rounded-lg">
            <i data-lucide="history" class="w-5 h-5"></i>
          </div>
          <h3 class="font-bold text-slate-800 text-lg" data-en="Medical & Surgical History Summary" data-es="Resumen de Historial Médico y Quirúrgico">Medical & Surgical History Summary</h3>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
          <!-- Surgeries -->
          <div class="border border-slate-200 rounded-xl p-4 bg-slate-50/50">
            <h4 class="font-bold text-slate-800 text-sm mb-3 flex items-center gap-2">
              <i data-lucide="scissors" class="w-4 h-4 text-indigo-600"></i>
              <span data-en="Surgical History" data-es="Historial Quirúrgico">Surgical History</span>
            </h4>
            <div class="p-3 bg-emerald-50 text-emerald-800 rounded-lg text-sm font-medium border border-emerald-200 flex items-center gap-2">
              <i data-lucide="check-circle-2" class="w-4 h-4 text-emerald-600"></i>
              <span data-en="No Prior Surgeries or Major Procedures" data-es="Sin Cirugías ni Procedimientos Previos">No Prior Surgeries or Major Procedures</span>
            </div>
          </div>

          <!-- Hospitalizations -->
          <div class="border border-slate-200 rounded-xl p-4 bg-slate-50/50">
            <h4 class="font-bold text-slate-800 text-sm mb-3 flex items-center gap-2">
              <i data-lucide="building" class="w-4 h-4 text-indigo-600"></i>
              <span data-en="Hospitalizations & ER Visits" data-es="Hospitalizaciones y Urgencias">Hospitalizations & ER Visits</span>
            </h4>
            <div class="p-3 bg-emerald-50 text-emerald-800 rounded-lg text-sm font-medium border border-emerald-200 flex items-center gap-2">
              <i data-lucide="check-circle-2" class="w-4 h-4 text-emerald-600"></i>
              <span data-en="No Recorded Major Hospitalizations" data-es="Sin Hospitalizaciones Registradas">No Recorded Major Hospitalizations</span>
            </div>
          </div>
        </div>

        <!-- Custom Notes Area -->
        <div class="mt-6">
          <label class="block text-xs font-semibold text-slate-500 uppercase mb-2" data-en="Additional Clinical Notes / Doctor Remarks" data-es="Notas Clínicas Adicionales / Observaciones">Additional Clinical Notes / Doctor Remarks</label>
          <textarea id="history-notes" rows="4" class="w-full bg-slate-50 border border-slate-200 rounded-xl p-3 text-sm text-slate-700 focus:bg-white focus:ring-2 focus:ring-teal-500 outline-none transition-all" placeholder="Enter optional historical notes here..."></textarea>
        </div>

      </div>
    </div>

    <!-- SECTION: FAMILY MEDICAL HISTORY -->
    <div id="section-family" class="section-content hidden space-y-6">
      <div class="bg-white rounded-2xl border border-slate-200 shadow-sm p-6 print-card">
        
        <div class="flex items-center gap-3 border-b border-slate-100 pb-3 mb-6">
          <div class="p-2 bg-purple-50 text-purple-600 rounded-lg">
            <i data-lucide="users" class="w-5 h-5"></i>
          </div>
          <h3 class="font-bold text-slate-800 text-lg" data-en="Family Health History" data-es="Historial Médico Familiar">Family Health History</h3>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
          
          <!-- Mother -->
          <div class="bg-slate-50 border border-slate-200 rounded-xl p-4">
            <div class="flex items-center gap-2 mb-3">
              <div class="p-1.5 bg-purple-100 text-purple-700 rounded-md">
                <i data-lucide="user" class="w-4 h-4"></i>
              </div>
              <h4 class="font-bold text-slate-800 text-sm" data-en="Mother" data-es="Madre">Mother</h4>
            </div>
            <textarea id="fam-mother" rows="3" class="w-full text-xs sm:text-sm bg-white border border-slate-200 rounded-lg p-2.5 text-slate-700 focus:ring-2 focus:ring-teal-500 outline-none transition-all" placeholder="E.g. History of mild seasonal allergies / eczema..."></textarea>
          </div>

          <!-- Father -->
          <div class="bg-slate-50 border border-slate-200 rounded-xl p-4">
            <div class="flex items-center gap-2 mb-3">
              <div class="p-1.5 bg-blue-100 text-blue-700 rounded-md">
                <i data-lucide="user" class="w-4 h-4"></i>
              </div>
              <h4 class="font-bold text-slate-800 text-sm" data-en="Father" data-es="Padre">Father</h4>
            </div>
            <textarea id="fam-father" rows="3" class="w-full text-xs sm:text-sm bg-white border border-slate-200 rounded-lg p-2.5 text-slate-700 focus:ring-2 focus:ring-teal-500 outline-none transition-all" placeholder="E.g. Mild hypertension managed with diet..."></textarea>
          </div>

          <!-- Siblings -->
          <div class="bg-slate-50 border border-slate-200 rounded-xl p-4">
            <div class="flex items-center gap-2 mb-3">
              <div class="p-1.5 bg-teal-100 text-teal-700 rounded-md">
                <i data-lucide="users" class="w-4 h-4"></i>
              </div>
              <h4 class="font-bold text-slate-800 text-sm" data-en="Siblings" data-es="Hermanos/as">Siblings</h4>
            </div>
            <textarea id="fam-siblings" rows="3" class="w-full text-xs sm:text-sm bg-white border border-slate-200 rounded-lg p-2.5 text-slate-700 focus:ring-2 focus:ring-teal-500 outline-none transition-all" placeholder="E.g. Healthy / No major chronic conditions..."></textarea>
          </div>

        </div>

      </div>
    </div>

    <!-- SECTION: IMMUNIZATIONS -->
    <div id="section-immunizations" class="section-content hidden space-y-6">
      <div class="bg-white rounded-2xl border border-slate-200 shadow-sm p-6 print-card">
        
        <div class="flex items-center gap-3 border-b border-slate-100 pb-3 mb-6">
          <div class="p-2 bg-emerald-50 text-emerald-600 rounded-lg">
            <i data-lucide="syringe" class="w-5 h-5"></i>
          </div>
          <h3 class="font-bold text-slate-800 text-lg" data-en="Immunization Log" data-es="Registro de Vacunación">Immunization Log</h3>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-2 gap-4" id="vaccineContainer">
          <!-- Vaccine items generated dynamically -->
        </div>

      </div>
    </div>

  </main>

  <!-- FOOTER -->
  <footer class="bg-white border-t border-slate-200 py-4 text-center text-xs text-slate-400 no-print mt-auto">
    <p data-en="Carmen's Personal Electronic Health Record • Confidential & Secure Local Data" data-es="Registro Médico Personal de Carmen • Datos Confidenciales y Locales">Carmen's Personal Electronic Health Record • Confidential & Secure Local Data</p>
  </footer>

  <!-- JAVASCRIPT LOGIC -->
  <script>
    // State variables
    let currentLang = 'en'; // 'en' or 'es'
    
    // Default Medication Data
    let medicationsData = [
      { name: "Hydrocortisone 1% Ointment", dosage: "Thin layer", frequency: "As needed (PRN)", purpose: "Eczema Flare-ups" },
      { name: "Cetirizine (Zyrtec)", dosage: "10 mg", frequency: "Once daily (PM)", purpose: "Itching / Antihistamine" },
      { name: "Vitamin D3", dosage: "2000 IU", frequency: "Daily", purpose: "General Wellness / Immune Support" }
    ];

    // Default Vaccines
    let vaccinesData = [
      { name: "Tdap (Tetanus, Diphtheria, Pertussis)", date: "2022-05-14", status: "Up to Date / Al Día" },
      { name: "MMR (Measles, Mumps, Rubella)", date: "Childhood Completed", status: "Protected / Protegida" },
      { name: "COVID-19 mRNA Booster", date: "2023-10-10", status: "Completed / Completada" },
      { name: "Annual Influenza (Flu)", date: "2023-11-01", status: "Recommended Annual / Recomendada" }
    ];

    // Initialize application
    window.addEventListener('DOMContentLoaded', () => {
      lucide.createIcons();
      loadFromLocalStorage();
      renderMedications();
      renderVaccines();
      updateLanguageDisplay();
    });

    // Tab Switcher Logic
    function switchTab(tabId) {
      // Hide all sections
      document.querySelectorAll('.section-content').forEach(el => el.classList.add('hidden'));
      // Show targeted section
      const target = document.getElementById('section-' + tabId);
      if (target) target.classList.remove('hidden');

      // Update button styles
      document.querySelectorAll('.tab-btn').forEach(btn => {
        btn.classList.remove('active', 'border-teal-600', 'text-teal-700', 'font-semibold');
        btn.classList.add('border-transparent', 'text-slate-500');
      });

      const activeBtn = document.getElementById('tab-' + tabId);
      if (activeBtn) {
        activeBtn.classList.add('active', 'border-teal-600', 'text-teal-700', 'font-semibold');
        activeBtn.classList.remove('border-transparent', 'text-slate-500');
      }
    }

    // Toggle Multilingual (English / Spanish)
    function toggleLanguage() {
      currentLang = currentLang === 'en' ? 'es' : 'en';
      updateLanguageDisplay();
    }

    function updateLanguageDisplay() {
      // Translate elements with data-en / data-es attributes
      document.querySelectorAll('[data-en]').forEach(el => {
        const text = el.getAttribute(`data-${currentLang}`);
        if (text) el.textContent = text;
      });

      // Translate placeholders
      document.querySelectorAll('[data-en-placeholder]').forEach(el => {
        const placeholder = el.getAttribute(`data-${currentLang}-placeholder`);
        if (placeholder) el.placeholder = placeholder;
      });

      // Update button text
      const langLabel = document.getElementById('langLabel');
      if (langLabel) {
        langLabel.textContent = currentLang === 'en' ? 'EN | ES' : 'ES | EN';
      }
    }

    // Render Medications
    function renderMedications() {
      const tbody = document.getElementById('medsTableBody');
      if (!tbody) return;
      tbody.innerHTML = '';

      medicationsData.forEach((item, index) => {
        const tr = document.createElement('tr');
        tr.className = "hover:bg-slate-50/80 transition-colors";
        tr.innerHTML = `
          <td class="py-3 px-3"><input type="text" value="${item.name}" onchange="updateMed(${index}, 'name', this.value)" class="w-full bg-transparent font-medium border-b border-transparent hover:border-slate-300 focus:border-teal-500 outline-none"></td>
          <td class="py-3 px-3"><input type="text" value="${item.dosage}" onchange="updateMed(${index}, 'dosage', this.value)" class="w-full bg-transparent border-b border-transparent hover:border-slate-300 focus:border-teal-500 outline-none"></td>
          <td class="py-3 px-3"><input type="text" value="${item.frequency}" onchange="updateMed(${index}, 'frequency', this.value)" class="w-full bg-transparent border-b border-transparent hover:border-slate-300 focus:border-teal-500 outline-none"></td>
          <td class="py-3 px-3"><input type="text" value="${item.purpose}" onchange="updateMed(${index}, 'purpose', this.value)" class="w-full bg-transparent border-b border-transparent hover:border-slate-300 focus:border-teal-500 outline-none"></td>
          <td class="py-3 px-3 no-print text-right">
            <button onclick="removeMedicationRow(${index})" class="text-rose-500 hover:text-rose-700 p-1 rounded-md hover:bg-rose-50 transition-all">
              <i data-lucide="trash-2" class="w-4 h-4"></i>
            </button>
          </td>
        `;
        tbody.appendChild(tr);
      });

      lucide.createIcons();
    }

    function addMedicationRow() {
      medicationsData.push({ name: "New Medication", dosage: "0 mg", frequency: "Daily", purpose: "General" });
      renderMedications();
      saveToLocalStorage();
    }

    function removeMedicationRow(index) {
      medicationsData.splice(index, 1);
      renderMedications();
      saveToLocalStorage();
    }

    function updateMed(index, key, val) {
      medicationsData[index][key] = val;
      saveToLocalStorage();
    }

    // Render Vaccine Cards
    function renderVaccines() {
      const container = document.getElementById('vaccineContainer');
      if (!container) return;
      container.innerHTML = '';

      vaccinesData.forEach((v, index) => {
        const div = document.createElement('div');
        div.className = "p-4 border border-slate-200 rounded-xl bg-slate-50 flex items-start justify-between gap-3";
        div.innerHTML = `
          <div class="flex items-start gap-3">
            <i data-lucide="check-circle" class="w-5 h-5 text-emerald-600 shrink-0 mt-0.5"></i>
            <div>
              <h4 class="font-bold text-slate-800 text-sm">${v.name}</h4>
              <p class="text-xs text-slate-500 mt-1"><strong data-en="Date:" data-es="Fecha:">Date:</strong> ${v.date}</p>
              <span class="inline-block mt-2 text-xs font-semibold px-2 py-0.5 bg-emerald-100 text-emerald-800 rounded-md">${v.status}</span>
            </div>
          </div>
        `;
        container.appendChild(div);
      });

      lucide.createIcons();
    }

    // Filter/Search Logic
    function filterContent() {
      const q = document.getElementById('searchInput').value.toLowerCase();
      // Simple text highlighting or tab filtering
      const textNodes = document.querySelectorAll('.section-content p, .section-content h3, .section-content span, .section-content td');
      
      if (!q) {
        textNodes.forEach(node => node.classList.remove('bg-yellow-200'));
        return;
      }

      textNodes.forEach(node => {
        if (node.textContent.toLowerCase().includes(q)) {
          node.classList.add('bg-yellow-200');
        } else {
          node.classList.remove('bg-yellow-200');
        }
      });
    }

    // Save/Load Local Storage
    function saveToLocalStorage() {
      const state = {
        fullName: document.getElementById('info-fullname')?.value,
        age: document.getElementById('info-age')?.value,
        blood: document.getElementById('info-blood')?.value,
        emName: document.getElementById('info-em-name')?.value,
        emPhone: document.getElementById('info-em-phone')?.value,
        emRel: document.getElementById('info-em-rel')?.value,
        docName: document.getElementById('info-doc-name')?.value,
        docPhone: document.getElementById('info-doc-phone')?.value,
        docHosp: document.getElementById('info-doc-hosp')?.value,
        historyNotes: document.getElementById('history-notes')?.value,
        famMother: document.getElementById('fam-mother')?.value,
        famFather: document.getElementById('fam-father')?.value,
        famSiblings: document.getElementById('fam-siblings')?.value,
        meds: medicationsData
      };

      localStorage.setItem('carmen_ehr_data', JSON.stringify(state));
      
      // Visual feedback toast
      showNotification(currentLang === 'en' ? 'Record Saved Locally!' : '¡Registro Guardado Localmente!');
    }

    function loadFromLocalStorage() {
      const saved = localStorage.getItem('carmen_ehr_data');
      if (!saved) return;

      try {
        const state = JSON.parse(saved);
        if (state.fullName) document.getElementById('info-fullname').value = state.fullName;
        if (state.age) document.getElementById('info-age').value = state.age;
        if (state.blood) document.getElementById('info-blood').value = state.blood;
        if (state.emName) document.getElementById('info-em-name').value = state.emName;
        if (state.emPhone) document.getElementById('info-em-phone').value = state.emPhone;
        if (state.emRel) document.getElementById('info-em-rel').value = state.emRel;
        if (state.docName) document.getElementById('info-doc-name').value = state.docName;
        if (state.docPhone) document.getElementById('info-doc-phone').value = state.docPhone;
        if (state.docHosp) document.getElementById('info-doc-hosp').value = state.docHosp;
        if (state.historyNotes) document.getElementById('history-notes').value = state.historyNotes;
        if (state.famMother) document.getElementById('fam-mother').value = state.famMother;
        if (state.famFather) document.getElementById('fam-father').value = state.famFather;
        if (state.famSiblings) document.getElementById('fam-siblings').value = state.famSiblings;
        if (state.meds && Array.isArray(state.meds)) medicationsData = state.meds;
      } catch(e) {
        console.error("Error loading localStorage record", e);
      }
    }

    function showNotification(msg) {
      const toast = document.createElement('div');
      toast.className = 'fixed bottom-4 right-4 bg-emerald-700 text-white px-4 py-2.5 rounded-xl shadow-lg text-sm font-medium z-50 flex items-center gap-2 no-print transition-all transform translate-y-0';
      toast.innerHTML = `<i data-lucide="check-circle" class="w-4 h-4"></i> ${msg}`;
      document.body.appendChild(toast);
      lucide.createIcons();

      setTimeout(() => {
        toast.style.opacity = '0';
        setTimeout(() => toast.remove(), 300);
      }, 2500);
    }
  </script>
</body>
</html>
