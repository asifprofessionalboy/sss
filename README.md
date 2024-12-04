<button class="btn" onclick="toggleCard()">Open</button>

  <div class="card" id="card">
    <div>
      <button>
        <svg xmlns="http://www.w3.org/2000/svg" width="30" height="30" viewBox="0 0 48 48">
          <linearGradient id="gradient1" x1="9.812" x2="38.361" y1="9.812" y2="38.361" gradientUnits="userSpaceOnUse">
            <stop offset="0" stop-color="#f44f5a"></stop>
            <stop offset=".443" stop-color="#ee3d4a"></stop>
            <stop offset="1" stop-color="#e52030"></stop>
          </linearGradient>
          <path fill="url(#gradient1)" d="M24,4C12.955,4,4,12.955,4,24s8.955,20,20,20s20-8.955,20-20C44,12.955,35.045,4,24,4z"></path>
        </svg>
        Supporting Hand
        <span class="badge">1</span>
      </button>
    </div>
    <div>
      <button>
        <svg xmlns="http://www.w3.org/2000/svg" width="30" height="30" viewBox="0 0 48 48">
          <path fill="url(#gradient1)" d="M24,4C12.955,4,4,12.955,4,24s8.955,20,20,20s20-8.955,20-20C44,12.955,35.045,4,24,4z"></path>
        </svg>
        Block/Unblock
      </button>
    </div>
    <div>
      <button>
        <svg xmlns="http://www.w3.org/2000/svg" width="30" height="30" viewBox="0 0 48 48">
          <path fill="url(#gradient1)" d="M24,4C12.955,4,4,12.955,4,24s8.955,20,20,20s20-8.955,20-20C44,12.955,35.045,4,24,4z"></path>
        </svg>
        Grievance
      </button>
    </div>
  </div>

  <script>
    function toggleCard() {
      const card = document.getElementById('card');
      card.classList.toggle('show');
    }
  </script>
</body>
</html>












ChatGPT can 
