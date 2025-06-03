from pathlib import Path
import shutil
import zipfile

# Define the project structure
project_name = "todo-list"
base_path = Path(f"/mnt/data/{project_name}")
assets_path = base_path / "assets"
base_path.mkdir(parents=True, exist_ok=True)
assets_path.mkdir(parents=True, exist_ok=True)

# Create basic files
files = {
    base_path / "index.html": """<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>To-Do List</title>
  <link rel="stylesheet" href="assets/style.css" />
</head>
<body>
  <div class="container">
    <h1>📝 Lista de Tarefas</h1>
    <form id="task-form">
      <input type="text" id="task-input" placeholder="Nova tarefa..." required />
      <button type="submit">Adicionar</button>
    </form>
    <ul id="task-list"></ul>
  </div>
  <script src="assets/script.js"></script>
</body>
</html>
""",
    assets_path / "style.css": """body {
  font-family: Arial, sans-serif;
  background-color: #f0f4f8;
  margin: 0;
  padding: 0;
}

.container {
  max-width: 500px;
  margin: 50px auto;
  padding: 20px;
  background: #fff;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

h1 {
  text-align: center;
  color: #333;
}

form {
  display: flex;
  margin-bottom: 20px;
}

input[type="text"] {
  flex: 1;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px 0 0 4px;
}

button {
  padding: 10px 15px;
  border: none;
  background: #007bff;
  color: white;
  cursor: pointer;
  border-radius: 0 4px 4px 0;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  padding: 10px;
  border-bottom: 1px solid #eee;
  display: flex;
  justify-content: space-between;
}

li.completed {
  text-decoration: line-through;
  color: #aaa;
}
""",
    assets_path / "script.js": """document.addEventListener('DOMContentLoaded', () => {
  const taskForm = document.getElementById('task-form');
  const taskInput = document.getElementById('task-input');
  const taskList = document.getElementById('task-list');

  const tasks = JSON.parse(localStorage.getItem('tasks')) || [];

  const saveTasks = () => localStorage.setItem('tasks', JSON.stringify(tasks));

  const renderTasks = () => {
    taskList.innerHTML = '';
    tasks.forEach((task, index) => {
      const li = document.createElement('li');
      li.textContent = task.text;
      if (task.completed) li.classList.add('completed');
      li.addEventListener('click', () => {
        task.completed = !task.completed;
        saveTasks();
        renderTasks();
      });
      const removeBtn = document.createElement('button');
      removeBtn.textContent = '🗑️';
      removeBtn.onclick = (e) => {
        e.stopPropagation();
        tasks.splice(index, 1);
        saveTasks();
        renderTasks();
      };
      li.appendChild(removeBtn);
      taskList.appendChild(li);
    });
  };

  taskForm.addEventListener('submit', (e) => {
    e.preventDefault();
    tasks.push({ text: taskInput.value, completed: false });
    taskInput.value = '';
    saveTasks();
    renderTasks();
  });

  renderTasks();
});
""",
    base_path / "README.md": """# 📝 To-Do List

Aplicação simples de lista de tarefas usando HTML, CSS e JavaScript puro.

## Funcionalidades
- Adicionar tarefas
- Marcar tarefas como concluídas
- Remover tarefas
- Armazenamento local com localStorage

## Como usar
Abra `index.html` em qualquer navegador moderno.

## Próximos passos
- Adicionar backend com Node.js ou .NET
- Adicionar gráfico com Chart.js para mostrar produtividade

## Screenshot
![preview](preview.png)
"""
}

# Write all files
for path, content in files.items():
    path.write_text(content)

# Zip the folder
zip_path = f"/mnt/data/{project_name}.zip"
shutil.make_archive(base_path, 'zip', base_path)

zip_path
