# MyfirstProject1
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Advanced Library System</title>

<style>
body {
  font-family: Arial;
  background: #eef2f7;
  text-align: center;
}

.container {
  width: 700px;
  margin: auto;
  background: white;
  padding: 20px;
  border-radius: 10px;
}

input {
  padding: 8px;
  margin: 5px;
  width: 60%;
}

button {
  padding: 6px 10px;
  margin: 3px;
  cursor: pointer;
}

.available { color: green; }
.issued { color: red; }

.dashboard {
  display: flex;
  justify-content: space-around;
  margin: 10px 0;
}

.card {
  background: #ddd;
  padding: 10px;
  border-radius: 8px;
  width: 30%;
}
</style>
</head>

<body>

<div class="container">
  <h1>📚 Advanced Library System</h1>

  <!-- Dashboard -->
  <div class="dashboard">
    <div class="card">Total Books: <span id="total"></span></div>
    <div class="card">Available: <span id="available"></span></div>
    <div class="card">Issued: <span id="issued"></span></div>
  </div>

  <!-- Add Book -->
  <input id="title" placeholder="Book Title">
  <input id="author" placeholder="Author">
  <button onclick="addBook()">Add Book</button>

  <!-- Search -->
  <br>
  <input id="search" placeholder="Search books..." onkeyup="renderBooks()">

  <h2>Books List</h2>
  <ul id="bookList"></ul>
</div>

<script>
let books = JSON.parse(localStorage.getItem("books")) || [];

function saveData() {
  localStorage.setItem("books", JSON.stringify(books));
}

function updateDashboard() {
  document.getElementById("total").innerText = books.length;
  document.getElementById("available").innerText =
    books.filter(b => b.available).length;
  document.getElementById("issued").innerText =
    books.filter(b => !b.available).length;
}

function renderBooks() {
  const list = document.getElementById("bookList");
  const search = document.getElementById("search").value.toLowerCase();

  list.innerHTML = "";

  books
    .filter(book =>
      book.title.toLowerCase().includes(search) ||
      book.author.toLowerCase().includes(search)
    )
    .forEach((book, index) => {
      let dueInfo = "";
      if (!book.available) {
        dueInfo = `<br>👤 ${book.member} | 📅 Due: ${book.dueDate}`;
      }

      list.innerHTML += `
        <li>
          <strong>${book.title}</strong> - ${book.author}
          <span class="${book.available ? 'available' : 'issued'}">
            (${book.available ? 'Available' : 'Issued'})
          </span>
          ${dueInfo}
          <br>
          ${
            book.available
              ? `<button onclick="issueBook(${index})">Issue</button>`
              : `<button onclick="returnBook(${index})">Return</button>`
          }
          <button onclick="deleteBook(${index})">Delete</button>
        </li>
      `;
    });

  updateDashboard();
}

function addBook() {
  const title = document.getElementById("title").value;
  const author = document.getElementById("author").value;

  if (!title || !author) {
    alert("Enter book details!");
    return;
  }

  books.push({
    title,
    author,
    available: true,
    member: "",
    dueDate: ""
  });

  saveData();
  renderBooks();

  document.getElementById("title").value = "";
  document.getElementById("author").value = "";
}

function issueBook(index) {
  const member = prompt("Enter member name:");
  const days = prompt("Enter due days:");

  if (!member || !days) return;

  let due = new Date();
  due.setDate(due.getDate() + parseInt(days));

  books[index].available = false;
  books[index].member = member;
  books[index].dueDate = due.toISOString().split("T")[0];

  saveData();
  renderBooks();
}

function returnBook(index) {
  books[index].available = true;
  books[index].member = "";
  books[index].dueDate = "";

  saveData();
  renderBooks();
}

function deleteBook(index) {
  books.splice(index, 1);
  saveData();
  renderBooks();
}

renderBooks();
</script>

</body>
</html>
