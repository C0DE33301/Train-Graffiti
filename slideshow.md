---
layout: default
permalink: /slideshow
permalink_name: /Slideshow
title: States → Cities
---

<nav class="breadcrumb" id="breadcrumb">
  <a onclick="navigate('states')">States</a>
</nav>

<main class="content" id="content"></main>

<div id="slideshow-source" style="display:none;">
{% comment %}
    Define all possible city paths based on the JS data structure (state/city).
    This allows Jekyll to correctly match the static image files.
{% endcomment %}
{% assign all_city_keys = 
    "california/losangeles california/sanfrancisco california/sandiego 
     texas/houston texas/austin texas/dallas texas/fortworth 
     newyork/newyorkcity newyork/buffalo newyork/rochester" | split: " " 
%}

{% for image in site.static_files %}
    {% for path_pair in all_city_keys %}
        {% assign state_key = path_pair | split: "/" | first %}
        {% assign city_key = path_pair | split: "/" | last %}
        
        {% comment %} Construct the target image path root for comparison {% endcomment %}
        {% assign image_path_root = 'assets/images/' | append: state_key | append: '/' | append: city_key | append: '/' %}

        {% if image.path contains image_path_root %}
            <div class="mySlides fade" data-city="{{ city_key }}" style="display:none;">
                <img src="{{ image.path | relative_url }}" style="width:100%">
                {% comment %} Use the image name for the text caption {% endcomment %}
                <div class="text">{{ image.name | split: '/' | last | remove: '.jpg' | remove: '.png' | remove: '.gif' }}</div>
            </div>
        {% endif %}
    {% endfor %}
{% endfor %}
</div>

<script>
// Helper function to create a URL-friendly key from the city name
function createCityKey(cityName) {
    return cityName.toLowerCase().replace(/\s/g, '');
}

// 2. SIMPLIFIED DATA STRUCTURE (Client-side JavaScript)
const data = {
  states: {
    california: {
      label: "California",
      cities: [
        "Los Angeles",
        "San Francisco",
        "San Diego"
      ]
    },
    texas: {
      label: "Texas",
      cities: [
        "Houston",
        "Austin",
        "Dallas",
        "Fort Worth"
      ]
    },
    newyork: {
      label: "New York",
      cities: [
        "New York City",
        "Buffalo",
        "Rochester"
      ]
    }
  }
};

/* -------------------- DOM refs -------------------- */
const breadcrumbEl = document.getElementById('breadcrumb');
const contentEl    = document.getElementById('content');

/* -------------------- breadcrumb -------------------- */
function setBreadcrumb(parts) {
  breadcrumbEl.innerHTML = '';
  parts.forEach((p, i) => {
    const a = document.createElement('a');
    a.textContent = p.label;
    a.onclick = () => navigate(p.key);
    breadcrumbEl.appendChild(a);
    if (i < parts.length - 1) {
      const sep = document.createElement('span');
      sep.className = 'sep';
      sep.textContent = '>';
      breadcrumbEl.appendChild(sep);
    }
  });
}

/* -------------------- navigation -------------------- */
function navigate(path) {
  if (path === 'states') return showStates();
  if (path.includes('/')) {
    const [stateKey, cityKey] = path.split('/');
    return showCity(stateKey, cityKey);
  }
  return showState(path);
}

/* -------------------- UI builders -------------------- */
function showStates() {
  setBreadcrumb([{ key: 'states', label: 'States' }]);
  const ul = document.createElement('ul');
  for (const sKey of Object.keys(data.states)) {
    const li = document.createElement('li');
    const a  = document.createElement('a');
    a.textContent = data.states[sKey].label;
    a.onclick = () => navigate(sKey);
    li.appendChild(a);
    ul.appendChild(li);
  }
  contentEl.innerHTML = '';
  const card = document.createElement('div'); card.className = 'card';
  card.append(ul);
  contentEl.appendChild(card);
}

function showState(stateKey) {
  const state = data.states[stateKey];
  if (!state) return showStates();
  setBreadcrumb([
    { key: 'states', label: 'States' },
    { key: stateKey, label: state.label }
  ]);
  const ul = document.createElement('ul');
  
  // Logic now assumes state.cities is always an ARRAY
  state.cities.forEach(cityName => {
    const cKey = createCityKey(cityName); 
    const li = document.createElement('li');
    const a  = document.createElement('a');
    a.textContent = cityName; 
    a.onclick = () => navigate(`${stateKey}/${cKey}`);
    li.appendChild(a);
    ul.appendChild(li);
  });
  
  contentEl.innerHTML = '';
  const card = document.createElement('div'); card.className = 'card';
  card.append(ul);
  contentEl.appendChild(card);
}

function showCity(stateKey, cityKey) {
  const state = data.states[stateKey];
  if (!state) return showStates();
  
  // Find the original city name in the array that matches the generated cityKey
  const cityLabel = state.cities.find(name => createCityKey(name) === cityKey);
  
  if (!cityLabel) return showState(stateKey); // Not found

  // Set a generic description since the 'text' property was removed
  const cityText = `You are viewing the details for ${cityLabel}, one of the major cities in ${state.label}.`;

  setBreadcrumb([
    { key: 'states', label: 'States' },
    { key: stateKey, label: state.label },
    { key: `${stateKey}/${cityKey}`, label: cityLabel }
  ]);
  
  contentEl.innerHTML = '';
  const card = document.createElement('div'); card.className = 'card';
  const h = document.createElement('h2'); h.textContent = `${cityLabel}, ${state.label}`;
  const p = document.createElement('p'); p.textContent = cityText;
  card.append(h, p);
  contentEl.appendChild(card);

  // --- GENERIC SLIDESHOW LOGIC ---
  const slideshowSource = document.getElementById('slideshow-source');
  
  // 1. Find all slides where data-city matches the current cityKey
  const slidesHtml = Array.from(slideshowSource.querySelectorAll(`[data-city="${cityKey}"]`))
                          .map(slide => slide.outerHTML)
                          .join('');

  if (slidesHtml) {
      const slideshowWrapper = document.createElement('div');
      slideshowWrapper.className = 'slideshow-container';
      slideshowWrapper.id = `${cityKey}-slideshow`; 

      // 2. Inject the filtered slides
      slideshowWrapper.innerHTML = slidesHtml;
      contentEl.appendChild(slideshowWrapper);

      // --- Slideshow Initialization (Generic) ---
      (function() {
          let slideIndex = 1; 
          const slides = slideshowWrapper.getElementsByClassName('mySlides');

          function showSlides(n) {
            if (slides.length === 0) return; 

            if (n > slides.length) slideIndex = 1;
            if (n < 1) slideIndex = slides.length;

            Array.from(slides).forEach(s => s.style.display = 'none');
            // Make the current slide visible
            slides[slideIndex - 1].style.display = 'block';
          }

          function plusSlides(n) {
            showSlides(slideIndex += n);
          }
          
          // 3. Dynamically create buttons
          const prevA = document.createElement('a');
          prevA.className = 'prev';
          prevA.innerHTML = '&#10094;';
          prevA.onclick = () => plusSlides(-1); 
          slideshowWrapper.appendChild(prevA);

          const nextA = document.createElement('a');
          nextA.className = 'next';
          nextA.innerHTML = '&#10095;';
          nextA.onclick = () => plusSlides(1); 
          slideshowWrapper.appendChild(nextA);
          
          showSlides(slideIndex);
      })();
  }
}

/* -------------------- init -------------------- */
showStates();
</script>