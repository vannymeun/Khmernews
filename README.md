<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>NewsHub</title>
  <script src="https://cdn.tailwindcss.com "></script>
  <style>
    .line-clamp-2 {
      display: -webkit-box;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
      overflow: hidden;
    }
    [dir="rtl"] .text-left { text-align: right; }
    [dir="rtl"] .flex-row { flex-direction: row-reverse; }
  </style>
</head>
<body class="bg-gray-50 text-gray-900 min-h-screen font-sans">

<!-- Header -->
<header class="bg-white shadow-sm sticky top-0 z-50 p-4 flex justify-between items-center">
  <h1 class="text-xl font-bold text-indigo-600">NewsHub</h1>

  <!-- Language Dropdown -->
  <select id="lang-switcher" class="border rounded px-2 py-1 text-sm">
    <option value="en">English</option>
    <option value="es">Español</option>
    <option value="km">ភាសាខ្មែរ</option>
    <option value="ar">العربية</option>
  </select>
</header>

<!-- Hero Section -->
<section class="bg-gradient-to-r from-indigo-600 to-purple-600 text-white py-12 px-4 text-center mb-6">
  <h2 id="welcome" class="text-2xl md:text-3xl font-bold">Loading...</h2>
</section>

<!-- Search Bar -->
<div class="px-4 mb-4">
  <input type="text" id="search" placeholder="Search news..." class="w-full p-2 border rounded" />
</div>

<!-- Category Filter -->
<div class="px-4 mb-4 flex gap-2 overflow-x-auto">
  <button onclick="setCategory('All')" class="bg-indigo-100 text-indigo-700 px-3 py-1 rounded-full whitespace-nowrap">All</button>
  <button onclick="setCategory('Technology')" class="bg-gray-100 px-3 py-1 rounded-full whitespace-nowrap">Technology</button>
  <button onclick="setCategory('Science')" class="bg-gray-100 px-3 py-1 rounded-full whitespace-nowrap">Science</button>
  <button onclick="setCategory('Environment')" class="bg-gray-100 px-3 py-1 rounded-full whitespace-nowrap">Environment</button>
</div>

<!-- Article List -->
<div id="app" class="p-4 space-y-6"></div>

<script>
  let lang = localStorage.getItem("lang") || "en";
  let category = "All";
  let currentPage = 1;
  const articlesPerPage = 5;

  const langSwitcher = document.getElementById("lang-switcher");
  langSwitcher.value = lang;

  langSwitcher.addEventListener("change", () => {
    lang = langSwitcher.value;
    localStorage.setItem("lang", lang);
    document.documentElement.setAttribute("dir", ["ar"].includes(lang) ? "rtl" : "ltr");
    render();
  });

  document.getElementById("search").addEventListener("input", e => {
    searchQuery = e.target.value.toLowerCase();
    currentPage = 1;
    render();
  });

  let searchQuery = "";

  function setCategory(newCategory) {
    category = newCategory;
    currentPage = 1;
    render();
  }

  function formatDate(dateStr) {
    return new Date(dateStr).toLocaleDateString(document.documentElement.dir === "rtl" ? "ar-SA" : "en-US");
  }

  // Mock Articles
  const articles = {
    en: [
      { id: 1, title: "Global Climate Summit Announces New Green Energy Pact", summary: "World leaders unite...", category: "Environment", image: "https://picsum.photos/seed/news1/800/450 ", author: "Jane Doe", date: "2025-04-01" },
      { id: 2, title: "AI Breakthrough in Healthcare Diagnostics Redefines Early Detection", summary: "New AI models can detect...", category: "Technology", image: "https://picsum.photos/seed/news2/800/450 ", author: "John Smith", date: "2025-03-29" },
      { id: 3, title: "SpaceX Successfully Launches First Crewed Mission to Mars Orbit", summary: "A historic milestone...", category: "Science", image: "https://picsum.photos/seed/news3/800/450 ", author: "Emily Johnson", date: "2025-03-27" },
      { id: 4, title: "Economy Shows Signs of Strong Recovery After Global Inflation Crisis", summary: "New economic indicators show growth...", category: "Environment", image: "https://picsum.photos/seed/news4/800/450 ", author: "David Lee", date: "2025-03-25" },
      { id: 5, title: "International Sports Festival Returns with Record Participation", summary: "Over 200 countries compete...", category: "Science", image: "https://picsum.photos/seed/news5/800/450 ", author: "Sophia Martinez", date: "2025-03-22" }
    ],
    es: [
      { id: 1, title: "Cumbre Mundial del Clima Anuncia Nuevo Pacto de Energía Verde", summary: "Los líderes mundiales se unen para acelerar la transición energética y reducir emisiones de carbono para 2030.", category: "Medio Ambiente", image: "https://picsum.photos/seed/news1/800/450 ", author: "María López", date: "2025-04-01" },
      { id: 2, title: "Avance en IA Revoluciona el Diagnóstico Médico Temprano", summary: "Nuevos modelos de IA detectan enfermedades en etapas iniciales con más del 99% de precisión.", category: "Tecnología", image: "https://picsum.photos/seed/news2/800/450 ", author: "Carlos Pérez", date: "2025-03-29" },
      { id: 3, title: "Viaje Histórico a Marte Comienza con Éxito", summary: "Primer viaje tripulado a Marte completado.", category: "Ciencia", image: "https://picsum.photos/seed/news3/800/450 ", author: "Luisa Fernanda", date: "2025-03-27" },
      { id: 4, title: "Recuperación Económica Tras Crisis de Inflación", summary: "La economía mundial muestra signos de recuperación.", category: "Medio Ambiente", image: "https://picsum.photos/seed/news4/800/450 ", author: "Javier Ruiz", date: "2025-03-25" },
      { id: 5, title: "Festival Deportivo Mundial Rompe Récords", summary: "Más de 200 países compiten este año.", category: "Ciencia", image: "https://picsum.photos/seed/news5/800/450 ", author: "Clara Domínguez", date: "2025-03-22" }
    ],
    km: [
      { id: 1, title: "សន្និបាតអន្តរជាតិដោះស្រាយកំដៅពិភពលោក ប្រកាសកិច្ចព្រមព្រៀងថាមពលបៃតងថ្មី", summary: "អ្នកដឹកនាំពិភពលោកចូលរួមគ្នាដើម្បីបញ្រ្ជាប់ដំណើរការផ្លាស់ប្ដូរទៅប្រើថាមពលបានវិញនិងកាត់បន្ថយការបំភាយឧស្ម័ន CO₂ មុនឆ្នាំ 2030។", category: "បរិស្ថាន", image: "https://picsum.photos/seed/news1/800/450 ", author: "សុខ ភក្ត្រា", date: "2025-04-01" },
      { id: 2, title: "ការអភិវឌ្ឍន៍បញ្ញាសិប្បនិម្មិតក្នុងវិស័យសុខាភិបាល បំប្លែងវិធីរកឃើញជំងឺនៅដំណាក់កាលដំបូង", summary: "ម៉ូដែលបញ្ញាសិប្បនិម្មិតថ្មីអាចរកឃើញជំងឺនៅដំណាក់កាលដំបូងដោយភាពត្រឹមត្រូវលើសពី 99%", category: "បច្ចេកវិទ្យា", image: "https://picsum.photos/seed/news2/800/450 ", author: "ហេង សុផារ៉ា", date: "2025-03-29" }
    ],
    ar: [
      { id: 1, title: "قمة المناخ العالمية تعلن عن اتفاقية جديدة للطاقة النظيفة", summary: "يتوحد قادة العالم لتسريع الانتقال إلى الطاقة المتجددة وتقليل الانبعاثات الكربونية بحلول عام 2030.", category: "البيئة", image: "https://picsum.photos/seed/news1/800/450 ", author: "سارة أحمد", date: "2025-04-01" },
      { id: 2, title: "اكتشاف جديد في الذكاء الاصطناعي يعيد تعريف التشخيص المبكر", summary: "يمكن لنماذج الذكاء الاصطناعي الجديدة اكتشاف الأمراض في مراحلها الأولى بدقة تزيد عن 99٪.", category: "التكنولوجيا", image: "https://picsum.photos/seed/news2/800/450 ", author: "محمد علي", date: "2025-03-29" }
    ]
  };

  function render() {
    const filtered = articles[lang].filter(a =>
      (category === "All" || a.category === category) &&
      (searchQuery === "" || a.title.toLowerCase().includes(searchQuery))
    );

    const paginated = filtered.slice(0, currentPage * articlesPerPage);

    const app = document.getElementById("app");
    app.innerHTML = paginated.map(article => `
      <div onclick="showArticle(${article.id})" class="bg-white shadow rounded-lg overflow-hidden cursor-pointer">
        <img src="${article.image}" alt="${article.title}" class="w-full h-40 object-cover">
        <div class="p-3">
          <span class="inline-block px-2 py-1 bg-indigo-100 text-indigo-700 rounded-full text-xs uppercase mb-1">${article.category}</span>
          <h3 class="font-semibold line-clamp-2">${article.title}</h3>
          <p class="text-sm text-gray-600 mt-1">${article.summary}</p>
          <div class="text-xs text-gray-500 mt-2 flex justify-between">
            <span>${article.author}</span>
            <span>${formatDate(article.date)}</span>
          </div>
        </div>
      </div>
    `).join("");
  }

  function showArticle(id) {
    const article = articles[lang].find(a => a.id === id);
    if (!article) return;

    const app = document.getElementById("app");
    app.innerHTML = `
      <div class="bg-white shadow rounded-lg p-4 mb-6">
        <button onclick="render()" class="text-indigo-600 mb-4">&larr; Back</button>
        <img src="${article.image}" alt="${article.title}" class="w-full h-48 object-cover rounded mb-4">
        <h2 class="text-xl font-bold mb-2">${article.title}</h2>
        <p class="text-sm mb-4">${article.summary}</p>
        <div class="text-xs text-gray-500 flex justify-between">
          <span>${article.author}</span>
          <span>${formatDate(article.date)}</span>
        </div>
      </div>
    `;
  }

  function formatDate(dateStr) {
    return new Date(dateStr).toLocaleDateString(document.documentElement.dir === "rtl" ? "ar-SA" : "en-US");
  }

  window.addEventListener("scroll", () => {
    if (window.innerHeight + window.scrollY >= document.body.offsetHeight - 500) {
      currentPage++;
      render();
    }
  });

  document.documentElement.setAttribute("dir", ["ar"].includes(lang) ? "rtl" : "ltr");
  render();
</script>
</body>
</html>
