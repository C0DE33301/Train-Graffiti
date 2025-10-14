--- 
layout: default
permalink: /slideshow
permalink_name: /slideshow
title: My Slideshow
---

<!-- Show JPG Images!!! -->

<p>Texas</p>
<div class="button-container">
    <button onclick="showCategory('austin')">Austin</button>
    <button onclick="showCategory('dallas')">Dallas</button>
    <button onclick="showCategory('fortworth')">Fort Worth</button>
</div>

<div class="slideshow-container" id="slideshow">
    <!-- austin Slides -->
    {% for image in site.static_files %}
      {% if image.path contains 'assets/images/texas/austin/oct52025/' %}
          <div class="mySlides fade" data-category="austin">
              <img src="{{ image.path | relative_url }}" style="width:100%">
              <div class="text">{{ image.name | split: '/' | last | remove: '.jpg' }}</div>
          </div>
      {% endif %}
    {% endfor %}

    <!-- dallas Slides -->
    {% for image in site.static_files %}
      {% if image.path contains 'assets/images/texas/austin/test2/' %}
          <div class="mySlides fade" data-category="dallas" style="display:none;">
              <img src="{{ image.path | relative_url }}" style="width:100%">
              <div class="text">{{ image.name | split: '/' | last | remove: '.jpg' }}</div>
          </div>
      {% endif %}
    {% endfor %}

    <!-- fortworth Slides -->
    {% for image in site.static_files %}
      {% if image.path contains 'assets/images/texas/austin/test3/' %}
          <div class="mySlides fade" data-category="fortworth" style="display:none;">
              <img src="{{ image.path | relative_url }}" style="width:100%">
              <div class="text">{{ image.name | split: '/' | last | remove: '.jpg' }}</div>
          </div>
      {% endif %}
    {% endfor %}

    <a class="prev" onclick="plusSlides(-1)">&#10094;</a>
    <a class="next" onclick="plusSlides(1)">&#10095;</a>
</div>

<br>

<style>
    .button-container {
        display: flex;  /* Use flexbox for horizontal layout */
        justify-content: center; /* Center the buttons */
        margin-bottom: 20px; /* Space below the button container */
    }

    .slideshow-container {
        position: relative;
        max-width: 100%;
        margin: auto;
    }

    .mySlides {
        display: none;
    }

    .prev, .next {
        cursor: pointer;
        position: absolute;
        top: 50%;
        width: auto;
        padding: 16px;
        color: white;
        font-weight: bold;
        font-size: 18px;
        transition: 0.6s ease;
        border-radius: 0 3px 3px 0;
        user-select: none;
    }

    .next {
        right: 0;
        border-radius: 3px 0 0 3px;
    }

    .text {
        color: #f2f2f2;
        font-size: 15px;
        padding: 8px 12px;
        position: absolute;
        bottom: 8px;
        width: 100%;
        text-align: center;
    }

    button {
        margin: 0 10px;
        padding: 10px 20px;
        font-size: 16px;
        cursor: pointer;
        background-color: black; /* White background */
        color: white; /* Black text */
        border: 2px solid white; /* Black border */
        border-radius: 5px; /* Rounded corners */
        transition: background-color 0.3s, color 0.3s; /* Smooth transition */
    }

    button:hover {
        background-color: white; /* Black background on hover */
        color: black; /* White text on hover */
    }
</style>

<script>
    let slideIndex = 1;
    let currentCategory = 'cats';

    function showCategory(category) {
        currentCategory = category;
        slideIndex = 1;
        const slides = document.getElementsByClassName("mySlides");
        Array.from(slides).forEach(slide => {
            slide.style.display = slide.dataset.category === category ? "block" : "none";
        });
        showSlides(slideIndex);
    }

    function plusSlides(n) {
        showSlides(slideIndex += n);
    }

    function currentSlide(n) {
        showSlides(slideIndex = n);
    }

    function showSlides(n) {
        const slides = document.getElementsByClassName("mySlides");
        const austin = document.getElementsByClassName("dot");
        
        // Cycle through slides
        if (n > slides.length) { slideIndex = 1; }
        if (n < 1) { slideIndex = slides.length; }

        // Hide all slides
        for (let i = 0; i < slides.length; i++) {
            slides[i].style.display = "none";  
        }

        // Remove active class from all austin
        for (let i = 0; i < austin.length; i++) {
            austin[i].className = austin[i].className.replace(" active", "");
        }

        // Show the current slide and set the active dot
        const currentSlides = Array.from(slides).filter(slide => slide.dataset.category === currentCategory);
        currentSlides[slideIndex - 1].style.display = "block";  
        austin[slideIndex - 1].className += " active";
    }

    // Initialize the display for the default category
    showCategory('cats');
</script>