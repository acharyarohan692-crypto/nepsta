<!DOCTYPE html>
<html>
<head>
  <title>Nepsta</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      background: url('https://upload.wikimedia.org/wikipedia/commons/9/9b/Flag_of_Nepal.svg') no-repeat center center;
      background-size: cover;
    }

    .box {
      background: rgba(255, 255, 255, 0.95);
      padding: 25px;
      border-radius: 15px;
      width: 350px;
      box-shadow: 0 0 15px rgba(0,0,0,0.3);
      text-align: center;
    }

    h2 {
      color: #d91e2f; /* Nepal flag red */
    }

    input, button {
      width: 100%;
      margin-top: 12px;
      padding: 10px;
      border-radius: 8px;
      border: 1px solid #ccc;
      font-size: 16px;
    }

    button {
      background: #003893; /* Nepal flag blue */
      color: white;
      border: none;
      cursor: pointer;
      font-weight: bold;
    }

    button:hover {
      opacity: 0.9;
    }

    #app {
      display: none;
      background: rgba(255,255,255,0.95);
      padding: 25px;
      border-radius: 15px;
      box-shadow: 0 0 15px rgba(0,0,0,0.3);
      width: 350px;
      max-height: 90vh;
      overflow-y: auto;
    }

    .post-box {
      margin-bottom: 20px;
    }

    .post {
      background: white;
      padding: 10px;
      margin: 15px 0;
      border-radius: 10px;
      text-align: left;
    }

    .post img {
      width: 100%;
      border-radius: 10px;
      margin-top: 5px;
    }

    .comments {
      font-size: 14px;
      margin-top: 10px;
    }
  </style>
</head>
<body>

<!-- Login / Signup Box -->
<div id="auth" class="box">
  <h2>📸 Nepsta Login</h2>
  <input type="text" id="username" placeholder="Username">
  <input type="password" id="password" placeholder="Password">
  <button onclick="signup()">Sign Up</button>
  <button onclick="login()">Login</button>
</div>

<!-- Nepsta Main App -->
<div id="app">
  <h2>Welcome, <span id="currentUser"></span> 🎉</h2>

  <div class="post-box">
    <input type="file" id="imageFile">
    <button onclick="addPost()">Post Image</button>
  </div>

  <div id="postsContainer"></div>
  <button style="margin-top:10px;" onclick="logout()">Logout</button>
</div>

<script>
  // Get current user from localStorage
  let currentUser = null;

  // Posts stored per user
  let allPosts = JSON.parse(localStorage.getItem("nepstaPosts")) || {};

  function savePosts() {
    localStorage.setItem("nepstaPosts", JSON.stringify(allPosts));
  }

  // Sign Up
  function signup() {
    const user = document.getElementById("username").value;
    const pass = document.getElementById("password").value;
    if (!user || !pass) {
      alert("Enter username and password");
      return;
    }
    localStorage.setItem("user", user);
    localStorage.setItem("pass", pass);
    alert("Account created! Please login.");
  }

  // Login
  function login() {
    const user = document.getElementById("username").value;
    const pass = document.getElementById("password").value;

    const savedUser = localStorage.getItem("user");
    const savedPass = localStorage.getItem("pass");

    if (user === savedUser && pass === savedPass) {
      currentUser = user;
      document.getElementById("currentUser").innerText = user;
      document.getElementById("auth").style.display = "none";
      document.getElementById("app").style.display = "block";

      if (!allPosts[currentUser]) allPosts[currentUser] = [];
      renderPosts();
    } else {
      alert("Wrong ID or Password");
    }
  }

  // Logout
  function logout() {
    currentUser = null;
    document.getElementById("auth").style.display = "block";
    document.getElementById("app").style.display = "none";
  }

  // Add Post
  function addPost() {
    const fileInput = document.getElementById("imageFile");
    if (!fileInput.files[0]) {
      alert("Select an image first");
      return;
    }

    const reader = new FileReader();
    reader.onload = function(e) {
      allPosts[currentUser].unshift({
        image: e.target.result,
        likes: 0,
        comments: []
      });
      savePosts();
      renderPosts();
    };
    reader.readAsDataURL(fileInput.files[0]);
  }

  // Render Posts
  function renderPosts() {
    const container = document.getElementById("postsContainer");
    container.innerHTML = "";

    allPosts[currentUser].forEach((post, index) => {
      let commentsHTML = "";
      post.comments.forEach(c => {
        commentsHTML += `<p>💬 ${c}</p>`;
      });

      const div = document.createElement("div");
      div.className = "post";
      div.innerHTML = `
        <p>Posted by: ${currentUser}</p>
        <img src="${post.image}" />
        <p>❤️ Likes: ${post.likes}</p>
        <button onclick="likePost(${index})">Like</button>
        <input type="text" id="comment${index}" placeholder="Add comment">
        <button onclick="addComment(${index})">Comment</button>
        <div class="comments">${commentsHTML}</div>
      `;
      container.appendChild(div);
    });
  }

  // Like
  function likePost(index) {
    allPosts[currentUser][index].likes++;
    savePosts();
    renderPosts();
  }

  // Comment
  function addComment(index) {
    const input = document.getElementById("comment" + index);
    if (input.value) {
      allPosts[currentUser][index].comments.push(input.value);
      input.value = "";
      savePosts();
      renderPosts();
    }
  }

</script>

</body>
</html>
