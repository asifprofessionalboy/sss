<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Beautiful Card with Buttons</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f4f4f9;
      margin: 0;
      padding: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
    }

    .btn {
      border-radius: 50%;
      padding: 10px 15px;
      font-size: 18px;
      color: white;
      background-color: #007bff;
      border: none;
      cursor: pointer;
    }

    .card {
      display: none;
      background-color: white;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
      border-radius: 10px;
      padding: 20px;
      width: 300px;
      text-align: center;
    }

    .card.show {
      display: block;
    }

    .card button {
      background-color: transparent;
      border: none;
      cursor: pointer;
      margin: 10px 0;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .card button svg {
      margin-right: 8px;
    }

    .card .badge {
      background-color: #28a745;
      color: white;
      font-size: 12px;
      padding: 5px 10px;
      border-radius: 12px;
      margin-left: 10px;
    }
  </style>
</head>
<body>
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
