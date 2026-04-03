<!doctype html>
<html lang="en" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MakeMe</title>
  <script src="https://tailwindcss.com"></script>
  <script src="https://jsdelivr.net"></script>
  <style>
    /* THEME ENGINE VARIABLES */
    :root {
      --accent: #ff964f;
      --accent-secondary: #ff6b2b;
      --accent-light: #fff3e8;
      --text-accent: #ff964f;
      --bg-color: #fef7f0;
      --surface: #ffffff;
      --text-main: #2d2017;
      --bg-pattern: none;
    }

    /* DARK MODE OVERRIDE */
    body.dark-mode {
      --bg-color: #1a1a1a;
      --surface: #2d2d2d;
      --text-main: #f0f0f0;
    }

    /* CUSTOM THEMES */
    body.theme-galaxy {
      --accent: #9370db; 
      --accent-secondary: #4b0082;
      --accent-light: #f3e8ff;
      --text-accent: #4b0082; /* Dark Purple */
      --bg-color: #e0d7ff;
      --bg-pattern: radial-gradient(circle, white 1px, transparent 1px);
    }
    body.theme-spring {
      --accent: #90ee90;
      --accent-secondary: #2e8b57;
      --accent-light: #e8f9e8;
      --text-accent: #006400; /* Dark Green */
      --bg-color: #f0fff0;
      --bg-pattern: radial-gradient(circle, #ffc0cb 2px, #add8e6 2px, #ffeb3b 2px, transparent 3px);
    }
    body.theme-sorbet {
      --accent: #ff69b4;
      --accent-secondary: #32cd32;
      --accent-light: #e0f7fa;
      --text-accent: #0000ff; /* Blue */
      --bg-pattern: repeating-linear-gradient(45deg, #ffe4e1, #ffe4e1 10px, #e0ffff 10px, #e0ffff 20px, #98fb98 20px, #98fb98 30px);
    }

    /* APPLICATION STYLES */
    *{box-sizing:border-box;margin:0;padding:0}
    html,body{height:100%;font-family:'DM Sans',sans-serif;overflow:hidden; background-color: var(--bg-color); color: var(--text-main); background-image: var(--bg-pattern); background-size: 40px 40px;}
    .app-shell{height:100%;width:100%;display:flex;flex-direction:column;}
    .tab-content{flex:1;overflow-y:auto; padding-bottom: 80px;}
    .accent {color: var(--text-accent) !important;}
    .bg-accent {background: var(--accent) !important;}
    .bg-accent-light {background: var(--accent-light) !important;}
    .recipe-card{background: var(--surface); border-radius:16px; box-shadow:0 2px 12px rgba(0,0,0,0.05); overflow:hidden; transition:transform 0.2s}
    .modal-overlay{position:fixed;inset:0;background:rgba(0,0,0,0.5);z-index:100;display:flex;align-items:flex-end;justify-content:center}
    .modal-sheet{background: var(--surface); color: var(--text-main); border-radius:24px 24px 0 0; width:100%; max-height:90%; overflow-y:auto; padding:24px; animation:slideUp 0.3s ease}
    @keyframes slideUp{from{transform:translateY(100%)}to{transform:translateY(0)}}
    .theme-preview { width: 24px; height: 24px; border-radius: 50%; border: 1px solid #ddd; }
  </style>
 </head>
 <body class="h-full">
  <div class="app-shell" id="app">
   <div id="toast-container"></div>
   <div class="tab-content" id="tab-content">
     <!-- Default View (Home) -->
     <div class="p-6">
       <h1 class="text-3xl font-bold mb-2 accent">MakeMe</h1>
       <p class="text-gray-500">Welcome to your dashboard!</p>
     </div>
   </div>
   
   <div class="bottom-bar fixed bottom-0 left-0 right-0 bg-white border-t border-gray-100 px-2 py-2 flex justify-around items-center z-50">
    <button onclick="switchTab('home')" class="flex flex-col items-center gap-1 p-2 rounded-xl transition-all"> 
      <i data-lucide="home" class="w-5 h-5"></i> <span class="text-xs font-semibold">Home</span> 
    </button> 
    <button onclick="switchTab('profile')" class="flex flex-col items-center gap-1 p-2 rounded-xl transition-all"> 
      <i data-lucide="user" class="w-5 h-5"></i> <span class="text-xs font-semibold">Profile</span> 
    </button>
   </div>
  </div>

  <div id="modal-overlay" class="modal-overlay" style="display:none" onclick="if(event.target===this)closeModal()"></div>

  <script>
    // ===== STATE =====
    let currentTheme = 'default';
    let isDarkMode = false;

    // Initialize Lucide Icons
    lucide.createIcons();

    function switchTab(tab) {
      const content = document.getElementById('tab-content');
      if (tab === 'profile') {
        content.innerHTML = `
          <div class="relative p-6 flex flex-col items-center">
            <button onclick="openSettings()" class="absolute top-6 right-6 p-2 bg-white/80 rounded-full shadow-sm text-gray-700">
              <i data-lucide="settings" class="w-6 h-6"></i>
            </button>
            <div class="w-24 h-24 bg-accent rounded-full mb-4 flex items-center justify-center text-white text-3xl font-bold">U</div>
            <h2 class="text-2xl font-bold">Your Profile</h2>
            <div class="mt-6 w-full space-y-4">
              <div class="recipe-card p-4">Your favorite recipes will appear here.</div>
            </div>
          </div>
        `;
      } else {
        content.innerHTML = `<div class="p-6"><h1 class="text-3xl font-bold mb-2 accent">MakeMe</h1><p>Home dashboard content...</p></div>`;
      }
      lucide.createIcons();
    }

    function openSettings() {
      const modal = document.getElementById('modal-overlay');
      modal.style.display = 'flex';
      modal.innerHTML = `
        <div class="modal-sheet">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-bold">Settings</h2>
            <button onclick="closeModal()"><i data-lucide="x"></i></button>
          </div>
          
          <div class="space-y-6">
            <section>
              <h3 class="font-semibold mb-3 border-b pb-1">Privacy</h3>
              <div class="flex justify-between items-center py-2">
                <span class="text-sm">Visibility of follower/friend list</span>
                <input type="checkbox" class="w-5 h-5 accent-orange-500">
              </div>
              <div class="flex justify-between items-center py-2">
                <span class="text-sm">Appear in others' follower lists</span>
                <input type="checkbox" class="w-5 h-5 accent-orange-500">
              </div>
            </section>

            <section>
              <h3 class="font-semibold mb-3 border-b pb-1">Mode</h3>
              <div class="flex gap-4">
                <button onclick="toggleDarkMode(false)" class="flex-1 py-2 border rounded-lg ${!isDarkMode ? 'bg-accent text-white' : ''}">Light</button>
                <button onclick="toggleDarkMode(true)" class="flex-1 py-2 border rounded-lg ${isDarkMode ? 'bg-accent text-white' : ''}">Dark</button>
              </div>
            </section>

            <section>
              <h3 class="font-semibold mb-3 border-b pb-1">Designs</h3>
              <div class="space-y-4">
                <!-- Default -->
                <div onclick="updateTheme('default')" class="cursor-pointer">
                   <div class="flex items-center gap-2 font-medium">
                      <div class="theme-preview" style="background:#ff964f"></div> Default mode
                   </div>
                   <p class="text-xs text-gray-500 ml-8">Typical orange dashboard</p>
                </div>
                <!-- Galaxy -->
                <div onclick="updateTheme('galaxy')" class="cursor-pointer">
                   <div class="flex items-center gap-2 font-medium">
                      <div class="theme-preview" style="background:#9370db"></div> Galaxy
                   </div>
                   <p class="text-xs text-gray-500 ml-8">Cook out of this world with galaxy theme!</p>
                </div>
                <!-- Spring -->
                <div onclick="updateTheme('spring')" class="cursor-pointer">
                   <div class="flex items-center gap-2 font-medium">
                      <div class="theme-preview" style="background:#90ee90"></div> Spring
                   </div>
                   <p class="text-xs text-gray-500 ml-8">Explore recipies with a hint of color on your dashboard!</p>
                </div>
                <!-- Sorbet -->
                <div onclick="updateTheme('sorbet')" class="cursor-pointer">
                   <div class="flex items-center gap-2 font-medium">
                      <div class="theme-preview" style="background:linear-gradient(to right, #ff69b4, #e0ffff, #98fb98)"></div> Sorbet
                   </div>
                   <p class="text-xs text-gray-500 ml-8">Explore your sweet side with sorbet-themed colors!</p>
                </div>
              </div>
            </section>
          </div>
        </div>
      `;
      lucide.createIcons();
    }

    function updateTheme(theme) {
      document.body.classList.remove('theme-galaxy', 'theme-spring', 'theme-sorbet');
      if (theme !== 'default') {
        document.body.classList.add('theme-' + theme);
      }
      currentTheme = theme;
      closeModal();
    }

    function toggleDarkMode(isDark) {
      isDarkMode = isDark;
      if (isDark) document.body.classList.add('dark-mode');
      else document.body.classList.remove('dark-mode');
      openSettings(); // Refresh UI
    }

    function closeModal() { document.getElementById('modal-overlay').style.display = 'none'; }
  </script>
 </body>
</html>
<!doctype html>
<html lang="en" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>MakeMe</title>
  <script src="https://tailwindcss.com"></script>
  <script src="https://jsdelivr.net"></script>
  <style>
    /* THEME ENGINE VARIABLES */
    :root {
      --accent: #ff964f;
      --accent-secondary: #ff6b2b;
      --accent-light: #fff3e8;
      --text-accent: #ff964f;
      --bg-color: #fef7f0;
      --surface: #ffffff;
      --text-main: #2d2017;
      --bg-pattern: none;
    }

    /* DARK MODE OVERRIDE */
    body.dark-mode {
      --bg-color: #1a1a1a;
      --surface: #2d2d2d;
      --text-main: #f0f0f0;
    }

    /* CUSTOM THEMES */
    body.theme-galaxy {
      --accent: #9370db; 
      --accent-secondary: #4b0082;
      --accent-light: #f3e8ff;
      --text-accent: #4b0082; /* Dark Purple */
      --bg-color: #e0d7ff;
      --bg-pattern: radial-gradient(circle, white 1px, transparent 1px);
    }
    body.theme-spring {
      --accent: #90ee90;
      --accent-secondary: #2e8b57;
      --accent-light: #e8f9e8;
      --text-accent: #006400; /* Dark Green */
      --bg-color: #f0fff0;
      --bg-pattern: radial-gradient(circle, #ffc0cb 2px, #add8e6 2px, #ffeb3b 2px, transparent 3px);
    }
    body.theme-sorbet {
      --accent: #ff69b4;
      --accent-secondary: #32cd32;
      --accent-light: #e0f7fa;
      --text-accent: #0000ff; /* Blue */
      --bg-pattern: repeating-linear-gradient(45deg, #ffe4e1, #ffe4e1 10px, #e0ffff 10px, #e0ffff 20px, #98fb98 20px, #98fb98 30px);
    }

    /* APPLICATION STYLES */
    *{box-sizing:border-box;margin:0;padding:0}
    html,body{height:100%;font-family:'DM Sans',sans-serif;overflow:hidden; background-color: var(--bg-color); color: var(--text-main); background-image: var(--bg-pattern); background-size: 40px 40px;}
    .app-shell{height:100%;width:100%;display:flex;flex-direction:column;}
    .tab-content{flex:1;overflow-y:auto; padding-bottom: 80px;}
    .accent {color: var(--text-accent) !important;}
    .bg-accent {background: var(--accent) !important;}
    .bg-accent-light {background: var(--accent-light) !important;}
    .recipe-card{background: var(--surface); border-radius:16px; box-shadow:0 2px 12px rgba(0,0,0,0.05); overflow:hidden; transition:transform 0.2s}
    .modal-overlay{position:fixed;inset:0;background:rgba(0,0,0,0.5);z-index:100;display:flex;align-items:flex-end;justify-content:center}
    .modal-sheet{background: var(--surface); color: var(--text-main); border-radius:24px 24px 0 0; width:100%; max-height:90%; overflow-y:auto; padding:24px; animation:slideUp 0.3s ease}
    @keyframes slideUp{from{transform:translateY(100%)}to{transform:translateY(0)}}
    .theme-preview { width: 24px; height: 24px; border-radius: 50%; border: 1px solid #ddd; }
  </style>
 </head>
 <body class="h-full">
  <div class="app-shell" id="app">
   <div id="toast-container"></div>
   <div class="tab-content" id="tab-content">
     <!-- Default View (Home) -->
     <div class="p-6">
       <h1 class="text-3xl font-bold mb-2 accent">MakeMe</h1>
       <p class="text-gray-500">Welcome to your dashboard!</p>
     </div>
   </div>
   
   <div class="bottom-bar fixed bottom-0 left-0 right-0 bg-white border-t border-gray-100 px-2 py-2 flex justify-around items-center z-50">
    <button onclick="switchTab('home')" class="flex flex-col items-center gap-1 p-2 rounded-xl transition-all"> 
      <i data-lucide="home" class="w-5 h-5"></i> <span class="text-xs font-semibold">Home</span> 
    </button> 
    <button onclick="switchTab('profile')" class="flex flex-col items-center gap-1 p-2 rounded-xl transition-all"> 
      <i data-lucide="user" class="w-5 h-5"></i> <span class="text-xs font-semibold">Profile</span> 
    </button>
   </div>
  </div>

  <div id="modal-overlay" class="modal-overlay" style="display:none" onclick="if(event.target===this)closeModal()"></div>

  <script>
    // ===== STATE =====
    let currentTheme = 'default';
    let isDarkMode = false;

    // Initialize Lucide Icons
    lucide.createIcons();

    function switchTab(tab) {
      const content = document.getElementById('tab-content');
      if (tab === 'profile') {
        content.innerHTML = `
          <div class="relative p-6 flex flex-col items-center">
            <button onclick="openSettings()" class="absolute top-6 right-6 p-2 bg-white/80 rounded-full shadow-sm text-gray-700">
              <i data-lucide="settings" class="w-6 h-6"></i>
            </button>
            <div class="w-24 h-24 bg-accent rounded-full mb-4 flex items-center justify-center text-white text-3xl font-bold">U</div>
            <h2 class="text-2xl font-bold">Your Profile</h2>
            <div class="mt-6 w-full space-y-4">
              <div class="recipe-card p-4">Your favorite recipes will appear here.</div>
            </div>
          </div>
        `;
      } else {
        content.innerHTML = `<div class="p-6"><h1 class="text-3xl font-bold mb-2 accent">MakeMe</h1><p>Home dashboard content...</p></div>`;
      }
      lucide.createIcons();
    }

    function openSettings() {
      const modal = document.getElementById('modal-overlay');
      modal.style.display = 'flex';
      modal.innerHTML = `
        <div class="modal-sheet">
          <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-bold">Settings</h2>
            <button onclick="closeModal()"><i data-lucide="x"></i></button>
          </div>
          
          <div class="space-y-6">
            <section>
              <h3 class="font-semibold mb-3 border-b pb-1">Privacy</h3>
              <div class="flex justify-between items-center py-2">
                <span class="text-sm">Visibility of follower/friend list</span>
                <input type="checkbox" class="w-5 h-5 accent-orange-500">
              </div>
              <div class="flex justify-between items-center py-2">
                <span class="text-sm">Appear in others' follower lists</span>
                <input type="checkbox" class="w-5 h-5 accent-orange-500">
              </div>
            </section>

            <section>
              <h3 class="font-semibold mb-3 border-b pb-1">Mode</h3>
              <div class="flex gap-4">
                <button onclick="toggleDarkMode(false)" class="flex-1 py-2 border rounded-lg ${!isDarkMode ? 'bg-accent text-white' : ''}">Light</button>
                <button onclick="toggleDarkMode(true)" class="flex-1 py-2 border rounded-lg ${isDarkMode ? 'bg-accent text-white' : ''}">Dark</button>
              </div>
            </section>

            <section>
              <h3 class="font-semibold mb-3 border-b pb-1">Designs</h3>
              <div class="space-y-4">
                <!-- Default -->
                <div onclick="updateTheme('default')" class="cursor-pointer">
                   <div class="flex items-center gap-2 font-medium">
                      <div class="theme-preview" style="background:#ff964f"></div> Default mode
                   </div>
                   <p class="text-xs text-gray-500 ml-8">Typical orange dashboard</p>
                </div>
                <!-- Galaxy -->
                <div onclick="updateTheme('galaxy')" class="cursor-pointer">
                   <div class="flex items-center gap-2 font-medium">
                      <div class="theme-preview" style="background:#9370db"></div> Galaxy
                   </div>
                   <p class="text-xs text-gray-500 ml-8">Cook out of this world with galaxy theme!</p>
                </div>
                <!-- Spring -->
                <div onclick="updateTheme('spring')" class="cursor-pointer">
                   <div class="flex items-center gap-2 font-medium">
                      <div class="theme-preview" style="background:#90ee90"></div> Spring
                   </div>
                   <p class="text-xs text-gray-500 ml-8">Explore recipies with a hint of color on your dashboard!</p>
                </div>
                <!-- Sorbet -->
                <div onclick="updateTheme('sorbet')" class="cursor-pointer">
                   <div class="flex items-center gap-2 font-medium">
                      <div class="theme-preview" style="background:linear-gradient(to right, #ff69b4, #e0ffff, #98fb98)"></div> Sorbet
                   </div>
                   <p class="text-xs text-gray-500 ml-8">Explore your sweet side with sorbet-themed colors!</p>
                </div>
              </div>
            </section>
          </div>
        </div>
      `;
      lucide.createIcons();
    }

    function updateTheme(theme) {
      document.body.classList.remove('theme-galaxy', 'theme-spring', 'theme-sorbet');
      if (theme !== 'default') {
        document.body.classList.add('theme-' + theme);
      }
      currentTheme = theme;
      closeModal();
    }

    function toggleDarkMode(isDark) {
      isDarkMode = isDark;
      if (isDark) document.body.classList.add('dark-mode');
      else document.body.classList.remove('dark-mode');
      openSettings(); // Refresh UI
    }

    function closeModal() { document.getElementById('modal-overlay').style.display = 'none'; }
  </script>
 </body>
</html>

