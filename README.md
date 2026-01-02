<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Student Assignment System</title>
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
body { font-family: Arial; background:#f4f6f8; margin:0 }
.container { max-width:900px; margin:auto; padding:15px }
.card { background:#fff; padding:15px; margin-bottom:15px; border-radius:8px }
input, textarea, button { width:100%; padding:10px; margin-top:8px }
button { background:#007bff; color:#fff; border:none; border-radius:6px }
button.small { width:auto; padding:6px 12px }
.hidden { display:none }
.status { font-weight:bold }
</style>
</head>

<body>

<div class="container">

<h2>📘 Assignment Portal – Admin: Nizor</h2>

<!-- AUTH -->
<div class="card" id="auth">
  <h3>Student Register</h3>
  <input id="r_name" placeholder="Full Name">
  <input id="r_email" placeholder="Email">
  <input id="r_pass" type="password" placeholder="Password">
  <button onclick="register()">Create Account</button>

  <hr>

  <h3>Student Login</h3>
  <input id="l_email" placeholder="Email">
  <input id="l_pass" type="password" placeholder="Password">
  <button onclick="login()">Login</button>
</div>

<!-- STUDENT DASHBOARD -->
<div class="card hidden" id="studentDash">
  <h3>Welcome, <span id="studentName"></span></h3>
  <button onclick="logout()">Logout</button>
  <h4>Available Assignments</h4>
  <div id="assignmentList"></div>
</div>

<!-- ADMIN PANEL -->
<div class="card">
  <h3>Admin – Post Assignment</h3>
  <input id="a_title" placeholder="Assignment Title">
  <textarea id="a_desc" placeholder="Assignment Description"></textarea>
  <button onclick="postAssignment()">Post</button>
</div>

<div class="card">
  <h3>Admin – Review Submissions</h3>
  <div id="adminView"></div>
</div>

</div>

<script>
let users = JSON.parse(localStorage.getItem("users") || "[]");
let assignments = JSON.parse(localStorage.getItem("assignments") || "[]");
let submissions = JSON.parse(localStorage.getItem("submissions") || "[]");
let currentUser = JSON.parse(localStorage.getItem("currentUser"));

function save() {
  localStorage.setItem("users", JSON.stringify(users));
  localStorage.setItem("assignments", JSON.stringify(assignments));
  localStorage.setItem("submissions", JSON.stringify(submissions));
}

function register() {
  users.push({
    name:r_name.value,
    email:r_email.value,
    pass:r_pass.value
  });
  save();
  alert("Account created. Please login.");
}

function login() {
  let u = users.find(x => x.email===l_email.value && x.pass===l_pass.value);
  if(!u){ alert("Invalid login"); return; }
  currentUser = u;
  localStorage.setItem("currentUser", JSON.stringify(u));
  showStudent();
}

function logout() {
  localStorage.removeItem("currentUser");
  location.reload();
}

function showStudent() {
  auth.classList.add("hidden");
  studentDash.classList.remove("hidden");
  studentName.innerText = currentUser.name;
  renderAssignments();
}

function postAssignment() {
  assignments.push({
    id:Date.now(),
    title:a_title.value,
    desc:a_desc.value
  });
  save();
  renderAssignments();
}

function renderAssignments() {
  assignmentList.innerHTML="";
  assignments.forEach(a=>{
    assignmentList.innerHTML += `
      <div class="card">
        <h4>${a.title}</h4>
        <p>${a.desc}</p>
        <textarea id="work_${a.id}" placeholder="Your submission"></textarea>
        <button class="small" onclick="submitWork(${a.id})">Submit</button>
      </div>
    `;
  });
}

function submitWork(id) {
  submissions.push({
    aid:id,
    student:currentUser.name,
    text:document.getElementById("work_"+id).value,
    status:"Pending"
  });
  save();
  alert("Submitted. Wait for approval.");
  renderAdmin();
}

function renderAdmin() {
  adminView.innerHTML="";
  submissions.forEach((s,i)=>{
    adminView.innerHTML += `
      <div class="card">
        <p><b>Student:</b> ${s.student}</p>
        <p>${s.text}</p>
        <p class="status">${s.status}</p>
        <button onclick="approve(${i})">Approve</button>
      </div>
    `;
  });
}

function approve(i){
  submissions[i].status="Approved";
  save();
  renderAdmin();
}

if(currentUser) showStudent();
renderAdmin();
</script>

</body>
</html># Student---assignment---site
Student assignment portal
