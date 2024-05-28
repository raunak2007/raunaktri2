---
title: Interview
search_exclude: true
permalink: /interview/
layout: none
---

{%- include rik_head.html -%}

<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Connected Users</title>
  <link rel="stylesheet" href="/css/styles.css">
  <link rel="stylesheet" href="/css/index.css">
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.min.css">
</head>
<body>
<div class="container">
  <div class="image-container">
    <img src="https://images.idgesg.net/images/article/2020/04/zoom_video_conferencing_online_meeting_remote_workers_one_user_connected_via_laptop_with_a_grid_of_twelve_participants_on_screen_2400x1600-100837446-large.jpg?auto=webp&quality=85,70" alt="Image">
  </div>

  <div class="main">
    <div class="new-meeting">
      <button id="newMeetingBtn">Create a New Meeting</button>
      <div class="join-meeting">
        <input type="text" placeholder="Meeting ID" id="meetingName">
        <button id="joinMeetingBtn">Join</button>
      </div>
    </div>
    <div class="connected-users">
      <button id="logoutBtn">Logout</button>
      <h2>Connected Users</h2>
      <ul id="userList"></ul>
    </div>
  </div>
</div>
<script>
  function loadAndDisplayUsers() {
    // check if the user is connected
    const connectedUser = localStorage.getItem('connectedUser');
    if (!connectedUser) {
        window.location = 'login/';
        return;
    }
    const userListElement = document.getElementById("userList");
    // Clear any existing content in the userListElement
    userListElement.innerHTML = "Loading...";
    // Retrieve the userList from Local Storage
    fetch('http://localhost:8020/api/v1/users')
        .then((response) => {
            return response.json();
        })
        .then((data) => {
            console.log(data);
            displayUsers(data, userListElement);
        });
  }
  function displayUsers(userList, userListElement) {
      userListElement.innerHTML = "";
      // Loop through the userList and create list items to display each user
      userList.forEach(user => {
          const listItem = document.createElement("li");
          listItem.innerHTML = `
                  <div>
                      <i class="fa fa-user-circle"></i>
                      ${user.username} <i class="user-email">(${user.email})</i>
                  </div>
                  <i class="fa fa-lightbulb-o ${user.status === "online" ? "online" : "offline"}"></i>
              `;
          userListElement.appendChild(listItem);
      });
  }
  // Call the loadAndDisplayUsers function when the page loads
  window.addEventListener("load", loadAndDisplayUsers);
  function handleLogout() {
      fetch('http://localhost:8020/api/v1/users/logout', {
          method: 'POST',
          headers: {
              'Content-Type': 'application/json'
          },
          body: localStorage.getItem('connectedUser')
      })
          .then((response) => {
              return response;
          })
          .then((data) => {
              localStorage.removeItem('connectedUser');
              window.location.href = "login/";
          });
  }
  const logoutBtn = document.getElementById("logoutBtn");
  logoutBtn.addEventListener("click", handleLogout);
  function handleNewMeeting() {
    const connectedUserData = localStorage.getItem('connectedUser');
    if (!connectedUserData) {
        console.error('No connected user data found in local storage.');
        return;
    }
    try {
        const connectedUser = JSON.parse(connectedUserData);
        window.open(`videocall?username=${connectedUser.username}`, "_blank");
    } catch (error) {
        console.error('Error parsing connected user data:', error);
    }
  }
  // Attach the handleNewMeeting function to the "Create a New Meeting" button
  const newMeetingBtn = document.getElementById("newMeetingBtn");
  newMeetingBtn.addEventListener("click", handleNewMeeting);
  function handleJoinMeeting() {
      const roomId = document.getElementById("meetingName").value;
      const connectedUser = JSON.parse(localStorage.getItem('connectedUser'));
      const url = `videocall?roomID=${roomId}&username=${connectedUser.username}`;
      window.open(url, "_blank");
  }
  const joinMeetingBtn = document.getElementById("joinMeetingBtn");
  joinMeetingBtn.addEventListener("click", handleJoinMeeting);
</script>
</body>
</html>

  <style>
    /* Resetting default styles */
    body {
        font-family: 'Georgia', serif;
        margin: 0;
        padding: 0;
        background-color: #FFFF00; /* Light blue background color */
        color: #333;
    }

    /* Container styles */
    .container {
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
    }

    /* Main container styles */
    .main {
        max-width: 600px;
        padding: 20px;
        background-color: #fff;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        border-radius: 10px;
        text-align: center;
    }

    /* Image container styles */
    .image-container {
        margin-right: 20px;
    }

    .image-container img {
        width: 100%;
        border-radius: 10px;
    }

    /* New meeting styles */
    .new-meeting {
        margin-bottom: 20px;
    }

    #newMeetingBtn {
        background-color: #60e085;
        color: #fff;
        padding: 10px 15px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-size: 16px;
    }

    #newMeetingBtn:hover {
        background-color: #4cb571;
    }

    .join-meeting {
        margin-top: 20px;
    }

    /* Join meeting styles */
    #meetingName {
        width: 100%;
        padding: 8px;
        margin-bottom: 16px;
        box-sizing: border-box;
        border: 1px solid #ccc;
        border-radius: 3px;
        transition: border-color 0.3s ease;
    }

    #meetingName:focus {
        border-color: #4caf50;
    }

    #joinMeetingBtn {
        background-color: #60e085;
        color: #fff;
        padding: 10px 15px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-size: 16px;
    }

    #joinMeetingBtn:hover {
        background-color: #4cb571;
    }

    /* Connected users styles */
    .connected-users {
        margin-top: 20px;
    }

    #logoutBtn {
        background-color: #60e085;
        color: #fff;
        padding: 10px 15px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        font-size: 16px;
    }

    #logoutBtn:hover {
        background-color: #4cb571;
    }

    h2 {
        margin-top: 20px;
    }

    #userList {
        list-style-type: none;
        padding: 0;
        margin: 0;
    }
  </style>
