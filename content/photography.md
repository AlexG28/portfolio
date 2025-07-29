+++
date = '2025-07-29T16:19:39-04:00'
draft = false
title = 'Photography'
+++

<div class="photo-gallery">
  <div class="gallery-grid">
    <div class="gallery-item">
      <img src="/images/cliffs.jpg" alt="Sunset over mountains" loading="lazy" onclick="openModal(this)">
    </div>
    <div class="gallery-item">
      <img src="/images/desert.jpg" alt="Urban street photography" loading="lazy" onclick="openModal(this)">
    </div>
    <div class="gallery-item">
      <img src="/images/dubai.jpg" alt="Portrait in natural light" loading="lazy" onclick="openModal(this)">
    </div>
    <div class="gallery-item">
      <img src="/images/sunset.jpg" alt="Abstract architecture" loading="lazy" onclick="openModal(this)">
    </div>
    <div class="gallery-item">
      <img src="/images/manhattan.jpg" alt="Abstract architecture" loading="lazy" onclick="openModal(this)">
    </div>
    <div class="gallery-item">
      <img src="/images/new_york.jpg" alt="Abstract architecture" loading="lazy" onclick="openModal(this)">
    </div>
    <div class="gallery-item">
      <img src="/images/night_sky.jpg" alt="Abstract architecture" loading="lazy" onclick="openModal(this)">
    </div>
    <div class="gallery-item">
      <img src="/images/palm.jpg" alt="Abstract architecture" loading="lazy" onclick="openModal(this)">
    </div>
  </div>
</div>

<!-- Modal for full-screen image -->
<div id="imageModal" class="modal" onclick="closeModal()">
  <span class="close" onclick="closeModal()">&times;</span>
  <img id="modalImage" class="modal-content">
  <div id="modalCaption"></div>
</div>

<style>
.photo-gallery {
  margin: 2rem 0;
}

.gallery-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(400px, 1fr));
  gap: 2rem;
  margin: 0 auto;
  max-width: 1400px;
}

.gallery-item {
  position: relative;
  overflow: hidden;
  border-radius: 12px;
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  cursor: pointer;
}

.gallery-item:hover {
  transform: translateY(-6px);
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.25);
}

.gallery-item img {
  width: 100%;
  height: 350px;
  object-fit: cover;
  display: block;
  transition: transform 0.3s ease;
}

.gallery-item:hover img {
  transform: scale(1.05);
}

/* Modal styles */
.modal {
  display: none;
  position: fixed;
  z-index: 1000;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.9);
  backdrop-filter: blur(5px);
}

.modal-content {
  margin: auto;
  display: block;
  max-width: 90%;
  max-height: 90%;
  object-fit: contain;
  border-radius: 8px;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.5);
}

#modalCaption {
  margin: auto;
  display: block;
  width: 80%;
  max-width: 700px;
  text-align: center;
  color: white;
  padding: 20px 0;
  font-size: 1.1rem;
  font-weight: 500;
}

.close {
  color: #f1f1f1;
  font-size: 40px;
  font-weight: bold;
  position: absolute;
  top: 15px;
  right: 35px;
  cursor: pointer;
  z-index: 1001;
  transition: color 0.3s ease;
}

.close:hover,
.close:focus {
  color: #bbb;
  text-decoration: none;
}

@media (max-width: 768px) {
  .gallery-grid {
    grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
    gap: 1.5rem;
  }
  
  .gallery-item img {
    height: 280px;
  }
  
  .close {
    top: 10px;
    right: 25px;
    font-size: 30px;
  }
  
  #modalCaption {
    font-size: 1rem;
    padding: 15px 0;
  }
}
</style>

<script>
    function openModal(img) {
        const modal = document.getElementById("imageModal");
        const modalImg = document.getElementById("modalImage");
        const captionText = document.getElementById("modalCaption");
        
        modal.style.display = "block";
        modalImg.src = img.src;
        captionText.innerHTML = img.alt;
        
        // Prevent body scroll when modal is open
        document.body.style.overflow = "hidden";
    }

    function closeModal() {
        const modal = document.getElementById("imageModal");
        modal.style.display = "none";
        
        // Restore body scroll
        document.body.style.overflow = "auto";
    }

    // Close modal when pressing Escape key
    document.addEventListener('keydown', function(event) {
        if (event.key === "Escape") {
            closeModal();
        }
    });

    // Close modal when clicking outside the image
    document.getElementById("imageModal").addEventListener('click', function(event) {
        if (event.target === this) {
            closeModal();
        }
    });
</script>