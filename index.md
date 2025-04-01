<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Movie Recommender</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
      @font-face {
        font-family: 'LucideIcons';
        /* Updated CDN link for lucide font */
        src: url(https://cdn.jsdelivr.net/npm/lucide-static@latest/font/Lucide.ttf) format('truetype');
      }
      .lucide {
        font-family: 'LucideIcons';
        font-size: 1.25rem; /* Adjust size as needed */
        line-height: 1;
        display: inline-block; /* Prevents layout shifts */
      }
      /* Basic styles */
      body {
        font-family: 'Inter', sans-serif;
        background-color: #f3f4f6; /* Light gray background */
      }
      /* Custom scrollbar for results */
      #results::-webkit-scrollbar {
        width: 8px;
      }
      #results::-webkit-scrollbar-track {
        background: #e5e7eb; /* Gray-200 */
        border-radius: 10px;
      }
      #results::-webkit-scrollbar-thumb {
        background-color: #9ca3af; /* Gray-400 */
        border-radius: 10px;
        border: 2px solid #e5e7eb; /* Gray-200 */
      }
      #results::-webkit-scrollbar-thumb:hover {
        background-color: #6b7280; /* Gray-500 */
      }
      /* Simple fade-in animation for results */
      .movie-card {
        animation: fadeIn 0.5s ease-in-out;
      }
      @keyframes fadeIn {
        from { opacity: 0; transform: translateY(10px); }
        to { opacity: 1; transform: translateY(0); }
      }
      /* Message Box Styles */
      #message-box {
        position: fixed;
        top: 20px;
        left: 50%;
        transform: translateX(-50%);
        padding: 12px 20px;
        border-radius: 8px;
        background-color: #ef4444; /* Red-500 */
        color: white;
        font-weight: 500;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        z-index: 1000;
        display: none; /* Hidden by default */
        opacity: 0;
        transition: opacity 0.3s ease-in-out;
      }
      #message-box.show {
        display: block;
        opacity: 1;
      }
      #message-box.info {
         background-color: #3b82f6; /* Blue-500 */
      }
       #message-box.error {
         background-color: #ef4444; /* Red-500 */
      }
      /* Style for movie posters */
      .movie-poster {
        width: 60px; /* Adjust size as needed */
        height: 90px;
        object-fit: cover;
        border-radius: 4px;
        background-color: #e5e7eb; /* Placeholder bg */
      }
      /* Style for provider logos (if used) or text */
      .provider-list span {
          display: inline-block;
          margin-right: 6px;
          margin-bottom: 4px;
          padding: 2px 6px;
          background-color: #e5e7eb; /* Gray-200 */
          border-radius: 4px;
          font-size: 0.7rem; /* Smaller font for providers */
          line-height: 1.2;
      }
    </style>
</head>
<body class="min-h-screen flex items-center justify-center p-4 bg-gradient-to-br from-indigo-100 via-purple-100 to-pink-100">

    <div class="bg-white p-6 sm:p-8 rounded-xl shadow-lg w-full max-w-4xl">
        <h1 class="text-2xl sm:text-3xl font-bold text-center text-gray-800 mb-6 sm:mb-8 flex items-center justify-center gap-2">
            <span class="lucide">&#xea5c;</span> Movie Recommender (TMDb + Watch Providers)
        </h1>

        <form id="recommendation-form" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 sm:gap-6 mb-6 sm:mb-8">
            <div>
                <label for="genre" class="block text-sm font-medium text-gray-700 mb-1">Genre</label>
                <select id="genre" name="genre" class="w-full p-2 border border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500 text-sm">
                    <option value="">Any</option>
                    </select>
            </div>
            <div>
                <label for="tmdb-rating" class="block text-sm font-medium text-gray-700 mb-1">Min TMDb Rating (0-10)</label>
                <input type="number" id="tmdb-rating" name="tmdb-rating" min="0" max="10" step="0.1" placeholder="e.g., 7.0" class="w-full p-2 border border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500 text-sm">
            </div>
            <div>
                <label for="decade" class="block text-sm font-medium text-gray-700 mb-1">Decade</label>
                <select id="decade" name="decade" class="w-full p-2 border border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500 text-sm">
                    <option value="">Any</option>
                    <option value="2020">2020s</option>
                    <option value="2010">2010s</option>
                    <option value="2000">2000s</option>
                    <option value="1990">1990s</option>
                    <option value="1980">1980s</option>
                    <option value="1970">1970s</option>
                    <option value="1960">1960s</option>
                    <option value="pre-1960">Pre-1960s</option>
                </select>
            </div>
            <div>
                <label for="language" class="block text-sm font-medium text-gray-700 mb-1">Original Language</label>
                <select id="language" name="language" class="w-full p-2 border border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500 text-sm">
                    <option value="">Any</option>
                    </select>
            </div>
            <div>
                <label for="country" class="block text-sm font-medium text-gray-700 mb-1">Country of Origin</label>
                <select id="country" name="country" class="w-full p-2 border border-gray-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500 text-sm">
                    <option value="">Any</option>
                    </select>
            </div>
             <div></div>
            <div class="md:col-span-2 lg:col-span-3 flex justify-center mt-4">
                <button type="submit" id="find-button" class="w-full sm:w-auto inline-flex justify-center items-center px-6 py-3 border border-transparent text-base font-medium rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500 transition duration-150 ease-in-out gap-2 disabled:opacity-50 disabled:cursor-not-allowed">
                    <span class="lucide">&#xea7e;</span> Find Movies
                </button>
            </div>
        </form>

        <div class="mt-8">
            <h2 class="text-xl sm:text-2xl font-semibold text-gray-800 mb-4 text-center">Recommendations</h2>
            <div id="results" class="bg-gray-50 p-4 rounded-lg border border-gray-200 min-h-[200px] max-h-[400px] overflow-y-auto">
                <p class="text-center text-gray-500">Enter your preferences and API key, then click "Find Movies".</p>
            </div>
        </div>
    </div>

    <div id="message-box"></div>

    <script>
        // --- TMDb Configuration ---
        // 🚨 IMPORTANT: Replace 'YOUR_TMDB_API_KEY_HERE' with your actual TMDb API key.
        // 🚨 WARNING: Exposing API keys in client-side code is insecure for production apps.
        // 🚨 Get your key from: https://www.themoviedb.org/settings/api
        const apiKey = '1897ffc0a507fadb98e68392bca2efd1'; // <-- PASTE YOUR KEY HERE
        const tmdbBaseUrl = 'https://api.themoviedb.org/3';
        const imageBaseUrl = 'https://image.tmdb.org/t/p/w200'; // Poster size

        // --- Mappings (Simplified) ---
        const genreMap = {
            "Action": 28, "Adventure": 12, "Animation": 16, "Comedy": 35, "Crime": 80,
            "Documentary": 99, "Drama": 18, "Family": 10751, "Fantasy": 14, "History": 36,
            "Horror": 27, "Music": 10402, "Mystery": 9648, "Romance": 10749, "Sci-Fi": 878,
            "TV Movie": 10770, "Thriller": 53, "War": 10752, "Western": 37
        };
        const languageMap = { // ISO 639-1 codes
            "English": "en", "Spanish": "es", "French": "fr", "German": "de", "Japanese": "ja",
            "Korean": "ko", "Hindi": "hi", "Mandarin": "zh", "Italian": "it", "Portuguese": "pt"
        };
        const countryMap = { // ISO 3166-1 codes
            "USA": "US", "UK": "GB", "Canada": "CA", "France": "FR", "Germany": "DE",
            "Japan": "JP", "South Korea": "KR", "India": "IN", "China": "CN", "Spain": "ES",
            "Australia": "AU", "Italy": "IT", "Brazil": "BR"
        };

        // --- DOM Elements ---
        const form = document.getElementById('recommendation-form');
        const resultsDiv = document.getElementById('results');
        const genreSelect = document.getElementById('genre');
        const tmdbRatingInput = document.getElementById('tmdb-rating');
        const decadeSelect = document.getElementById('decade');
        const languageSelect = document.getElementById('language');
        const countrySelect = document.getElementById('country');
        const messageBox = document.getElementById('message-box');
        const findButton = document.getElementById('find-button');
        let messageTimeout;

        // --- Populate Select Options ---
        function populateSelect(selectElement, map) {
            Object.entries(map).forEach(([name, value]) => {
                const option = document.createElement('option');
                option.value = value;
                option.textContent = name;
                selectElement.appendChild(option);
            });
        }
        populateSelect(genreSelect, genreMap);
        populateSelect(languageSelect, languageMap);
        populateSelect(countrySelect, countryMap);

        // --- Functions ---
        function showMessage(message, type = 'error', duration = 4000) {
            messageBox.textContent = message;
            messageBox.className = '';
            messageBox.classList.add('show', type);
            if (messageTimeout) clearTimeout(messageTimeout);
            messageTimeout = setTimeout(() => { messageBox.classList.remove('show'); }, duration);
        }

        function getDecadeDates(selectedDecade) {
            if (!selectedDecade) return { gte: null, lte: null };
            if (selectedDecade === 'pre-1960') return { gte: null, lte: '1959-12-31' };
            const startYear = parseInt(selectedDecade, 10);
            const endYear = startYear + 9;
            return { gte: `${startYear}-01-01`, lte: `${endYear}-12-31` };
        }

        // --- NEW: Function to fetch watch providers for multiple movies ---
        async function fetchWatchProviders(movies) {
            // Create an array of promises, one for each movie's provider fetch
            const providerPromises = movies.map(movie => {
                const providerUrl = `${tmdbBaseUrl}/movie/${movie.id}/watch/providers?api_key=${apiKey}`;
                return fetch(providerUrl)
                    .then(response => {
                        if (!response.ok) {
                            // Don't throw here, just return null or an error indicator
                            // so Promise.allSettled continues
                            console.warn(`Failed to fetch providers for movie ${movie.id}: ${response.status}`);
                            return null;
                        }
                        return response.json();
                    })
                    .catch(error => {
                        console.error(`Error fetching providers for movie ${movie.id}:`, error);
                        return null; // Return null on network error
                    });
            });

            // Wait for all provider fetches to settle (either succeed or fail)
            const results = await Promise.allSettled(providerPromises);

            // Augment the original movie objects with the provider data
            const moviesWithProviders = movies.map((movie, index) => {
                if (results[index].status === 'fulfilled' && results[index].value) {
                    // Attach the provider data if the fetch was successful
                    movie.watchProviders = results[index].value.results;
                } else {
                    // Ensure the property exists but is empty/null if fetch failed
                    movie.watchProviders = null;
                }
                return movie;
            });

            return moviesWithProviders;
        }


        // --- UPDATED: Function to display recommendations ---
        function displayResults(movies) {
            resultsDiv.innerHTML = ''; // Clear previous results or loading message

            if (!movies || movies.length === 0) {
                resultsDiv.innerHTML = '<p class="text-center text-gray-500">No movies found matching your criteria.</p>';
                return;
            }

            const list = document.createElement('ul');
            list.className = 'space-y-3'; // Spacing between items

            movies.forEach(movie => {
                const listItem = document.createElement('li');
                listItem.className = 'movie-card bg-white p-3 rounded-lg shadow-sm border border-gray-100 hover:shadow-md transition-shadow duration-200 flex items-start gap-3';

                // Movie Poster
                const posterImg = document.createElement('img');
                posterImg.className = 'movie-poster flex-shrink-0';
                posterImg.src = movie.poster_path ? `${imageBaseUrl}${movie.poster_path}` : 'https://placehold.co/60x90/e2e8f0/cbd5e0?text=N/A';
                posterImg.alt = `Poster for ${movie.title}`;
                posterImg.onerror = function() { this.onerror=null; this.src='https://placehold.co/60x90/e2e8f0/cbd5e0?text=Error'; };
                listItem.appendChild(posterImg);

                // Movie Info Div
                const infoDiv = document.createElement('div');
                infoDiv.className = 'flex-grow';

                // Title and Year
                const titleYear = document.createElement('h3');
                titleYear.className = 'text-md font-semibold text-indigo-700 mb-1';
                const releaseYear = movie.release_date ? movie.release_date.substring(0, 4) : 'N/A';
                titleYear.textContent = `${movie.title} (${releaseYear})`;
                infoDiv.appendChild(titleYear);

                // Details (Rating, Language)
                const details = document.createElement('div');
                details.className = 'text-xs text-gray-600 space-y-0.5 mb-2'; // Added margin-bottom
                const rating = movie.vote_average ? movie.vote_average.toFixed(1) : 'N/A';
                const langCode = movie.original_language || 'N/A';
                details.innerHTML = `
                    <p><strong class="font-medium text-gray-700">TMDb Rating:</strong> ${rating}/10 (${movie.vote_count} votes)</p>
                    <p><strong class="font-medium text-gray-700">Language:</strong> ${langCode.toUpperCase()}</p>
                `;
                infoDiv.appendChild(details);

                // --- NEW: Where to Watch Section ---
                const watchDiv = document.createElement('div');
                watchDiv.className = 'mt-1'; // Add some top margin

                const watchTitle = document.createElement('p');
                watchTitle.className = 'text-xs font-medium text-gray-700 mb-0.5';
                watchTitle.textContent = 'Where to Watch (US Stream):';
                watchDiv.appendChild(watchTitle);

                const providerListDiv = document.createElement('div');
                providerListDiv.className = 'provider-list text-xs text-gray-600';

                // Check if provider data exists and has US flatrate options
                const usProviders = movie.watchProviders?.US?.flatrate;
                if (usProviders && usProviders.length > 0) {
                    usProviders.forEach(provider => {
                        const providerSpan = document.createElement('span');
                        // In a real app, you might use provider.logo_path with the TMDb image base URL
                        // For simplicity, we just show the name.
                        providerSpan.textContent = provider.provider_name;
                        providerListDiv.appendChild(providerSpan);
                    });
                     // Add TMDb link if available
                    const tmdbLink = movie.watchProviders?.US?.link;
                    if (tmdbLink) {
                        const linkEl = document.createElement('a');
                        linkEl.href = tmdbLink;
                        linkEl.target = '_blank'; // Open in new tab
                        linkEl.rel = 'noopener noreferrer';
                        linkEl.textContent = 'More options on TMDb';
                        linkEl.className = 'text-indigo-600 hover:text-indigo-800 text-xs block mt-1';
                        providerListDiv.appendChild(linkEl);
                    }
                } else if (movie.watchProviders === null) {
                     // Indicate if fetching providers failed for this movie
                     providerListDiv.innerHTML = '<span class="text-gray-400 text-xs italic">Could not fetch provider info.</span>';
                }
                 else {
                    providerListDiv.innerHTML = '<span class="text-gray-400 text-xs italic">No US streaming providers found.</span>';
                }

                watchDiv.appendChild(providerListDiv);
                infoDiv.appendChild(watchDiv);
                // --- End Where to Watch Section ---


                listItem.appendChild(infoDiv);
                list.appendChild(listItem);
            });

            resultsDiv.appendChild(list);
        }

        // --- UPDATED: Event Listener ---
        form.addEventListener('submit', async (event) => {
            event.preventDefault();

            if (apiKey === 'YOUR_TMDB_API_KEY_HERE' || !apiKey) {
                showMessage('Please enter your TMDb API key in the script section first.', 'error');
                return;
            }

            // --- Stage 1: Discover Movies ---
            findButton.disabled = true;
            findButton.innerHTML = `
                <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
                  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
                  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
                </svg>
                Finding movies...`; // Update loading text
            resultsDiv.innerHTML = '<p class="text-center text-gray-500">Finding matching movies...</p>';

            const selectedGenreId = genreSelect.value;
            const minTmdbRating = parseFloat(tmdbRatingInput.value) || 0;
            const selectedDecade = decadeSelect.value;
            const selectedLangCode = languageSelect.value;
            const selectedCountryCode = countrySelect.value;
            const { gte: releaseDateGte, lte: releaseDateLte } = getDecadeDates(selectedDecade);

            const discoverUrl = new URL(`${tmdbBaseUrl}/discover/movie`);
            discoverUrl.searchParams.append('api_key', apiKey);
            discoverUrl.searchParams.append('include_adult', 'false');
            discoverUrl.searchParams.append('include_video', 'false');
            discoverUrl.searchParams.append('language', 'en-US');
            discoverUrl.searchParams.append('sort_by', 'vote_average.desc');
            discoverUrl.searchParams.append('vote_count.gte', 100);
            if (selectedGenreId) discoverUrl.searchParams.append('with_genres', selectedGenreId);
            if (minTmdbRating > 0) discoverUrl.searchParams.append('vote_average.gte', minTmdbRating.toFixed(1));
            if (releaseDateGte) discoverUrl.searchParams.append('primary_release_date.gte', releaseDateGte);
            if (releaseDateLte) discoverUrl.searchParams.append('primary_release_date.lte', releaseDateLte);
            if (selectedLangCode) discoverUrl.searchParams.append('with_original_language', selectedLangCode);
            if (selectedCountryCode) discoverUrl.searchParams.append('with_origin_country', selectedCountryCode);

            let initialMovies = [];
            try {
                const response = await fetch(discoverUrl);
                if (!response.ok) {
                    let errorMsg = `Error finding movies: ${response.status} ${response.statusText}`;
                    try { const errorData = await response.json(); if (errorData.status_message) errorMsg = `TMDb Error: ${errorData.status_message}`; } catch (e) {}
                    throw new Error(errorMsg);
                }
                const data = await response.json();
                initialMovies = data.results;

                if (initialMovies.length === 0) {
                     displayResults([]); // Show "No movies found" message
                     findButton.disabled = false;
                     findButton.innerHTML = `<span class="lucide">&#xea7e;</span> Find Movies`;
                     return; // Stop processing if no movies found
                 }

                // --- Stage 2: Fetch Watch Providers ---
                findButton.innerHTML = `
                    <svg class="animate-spin -ml-1 mr-3 h-5 w-5 text-white" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">...</svg>
                    Fetching providers...`; // Update loading text
                resultsDiv.innerHTML = '<p class="text-center text-gray-500">Fetching watch provider information...</p>';

                const moviesWithProviders = await fetchWatchProviders(initialMovies);
                displayResults(moviesWithProviders);

            } catch (error) {
                console.error("API Fetch Error:", error);
                showMessage(`Failed to process request. ${error.message}`, 'error');
                resultsDiv.innerHTML = `<p class="text-center text-red-600">Error loading recommendations. Check console for details.</p>`;
            } finally {
                 findButton.disabled = false;
                 findButton.innerHTML = `<span class="lucide">&#xea7e;</span> Find Movies`;
            }
        });

        // Initial placeholder message
        resultsDiv.innerHTML = '<p class="text-center text-gray-500">Enter your preferences and API key in the script, then click "Find Movies".</p>';

    </script>

</body>
</html>

