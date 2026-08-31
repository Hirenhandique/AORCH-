<!DOCTYPE html><html xmlns:th="http://www.thymeleaf.org"><head><meta charset="UTF-8"><title>Admin Dashboard</title><link rel="stylesheet" th:href="@{/css/style.css}"></head>
<body><nav class="navbar"><div>Admin Panel</div><a href="/logout">Logout</a> | <a href="/">View Site</a></nav>
<div class="section"><h2>Add Publication</h2>
<form th:action="@{/admin/pub/add}" th:object="${newPub}" method="post"><input th:field="*{title}" placeholder="Title" required><input th:field="*{author}" placeholder="Author"><input th:field="*{year}" type="number" placeholder="Year"><input th:field="*{keywords}" placeholder="Keywords"><input th:field="*{scholarLink}" placeholder="Scholar Link"><button class="btn">Add Publication</button></form>
<table><tr th:each="p:${publications}"><td th:text="${p.title}"></td><td><form th:action="@{'/admin/pub/delete/'+${p.id}}" method="post"><button>Delete</button></form></td></tr></table>

<h2 style="margin-top:2rem">Add News / Event</h2>
<form th:action="@{/admin/news/add}" th:object="${newNews}" method="post"><input th:field="*{title}" placeholder="Event Title" required><input th:field="*{date}" type="date"><input th:field="*{type}" placeholder="Type: Seminar/Press"><textarea th:field="*{content}" placeholder="Details"></textarea><button class="btn">Add News</button></form>
</div></body></html>
