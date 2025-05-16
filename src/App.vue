<template>
  <div id="app">
    <!-- Ajooda-Logo -->
    <div class="logo-container">
      <img src="@/assets/ajooda-logo.png" alt="Ajooda Logo" class="logo" />
    </div>

    <h1>To-Do-Liste</h1>

    <!-- Eingabe für neue Aufgaben -->
    <div class="task-input">
      <input
        v-model="newTask.text"
        type="text"
        placeholder="Titel der Aufgabe"
      />
      <input
        v-model="newTask.dueDate"
        type="date"
        placeholder="Fälligkeitsdatum"
      />
      <input
        v-model="newTask.dueTime"
        type="time"
        placeholder="Fälligkeitszeit"
      />
      <select v-model="newTask.priority">
        <option value="" disabled selected>Dringlichkeit wählen</option>
        <option value="high">Hoch</option>
        <option value="medium">Mittel</option>
        <option value="low">Niedrig</option>
      </select>
      <button @click="addTask">Hinzufügen</button>
    </div>

    <!-- Suchfeld -->
    <div class="search-bar">
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Aufgabe suchen..."
      />
    </div>

    <!-- Filter Buttons -->
    <div class="filters">
      <button @click="setFilter('all')">Alle</button>
      <button @click="setFilter('active')">Offen</button>
      <button @click="setFilter('completed')">Erledigt</button>
    </div>

    <!-- Offene Aufgaben -->
    <h2 v-if="filter !== 'completed'">Offene Aufgaben</h2>
    <ul v-if="filter !== 'completed'">
      <li
        v-for="task in filteredTasks"
        :key="task.id"
        :class="[task.priority, { completed: task.completed }]"
      >
        <div>
          <span>{{ task.text }}</span>
          <small>Fällig: {{ task.dueDate }} {{ task.dueTime }}</small>
        </div>
        <div>
          <button @click="markCompleted(task.id)">Erledigt</button>
          <button @click="deleteTask(task.id)">Löschen</button>
        </div>
      </li>
    </ul>

    <!-- Erledigte Aufgaben -->
    <h2 v-if="filter === 'completed'">Erledigte Aufgaben</h2>
    <ul v-if="filter === 'completed'">
      <li
        v-for="task in completedTasks"
        :key="task.id"
        :class="[task.priority, 'completed']"
      >
        <div>
          <span>{{ task.text }}</span>
          <small>Fällig: {{ task.dueDate }} {{ task.dueTime }}</small>
        </div>
        <button @click="deleteTask(task.id)">Löschen</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTask: {
        text: "",
        dueDate: "",
        dueTime: "",
        priority: "",
      },
      tasks: [], // Liste aller Aufgaben
      searchQuery: "", // Suchtext
      filter: "all", // Aktueller Filter ('all', 'active', 'completed')
    };
  },
  computed: {
    filteredTasks() {
      // Zeigt Aufgaben basierend auf dem Filter und der Suche
      let filtered = this.tasks.filter((task) => !task.completed);
      if (this.filter === "active") {
        return filtered.filter((task) =>
          task.text.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      }
      if (this.filter === "all") {
        return this.tasks.filter((task) =>
          task.text.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      }
      return [];
    },
    completedTasks() {
      // Zeigt erledigte Aufgaben basierend auf der Suche
      return this.tasks
        .filter((task) => task.completed)
        .filter((task) =>
          task.text.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
    },
  },
  methods: {
    addTask() {
      // Fügt eine neue Aufgabe hinzu
      if (
        this.newTask.text.trim() === "" ||
        this.newTask.dueDate === "" ||
        this.newTask.dueTime === "" ||
        this.newTask.priority === ""
      ) {
        alert("Bitte alle Felder ausfüllen!");
        return;
      }
      this.tasks.push({
        id: Date.now(),
        text: this.newTask.text,
        dueDate: this.newTask.dueDate,
        dueTime: this.newTask.dueTime,
        priority: this.newTask.priority,
        completed: false,
      });
      this.newTask = { text: "", dueDate: "", dueTime: "", priority: "" }; // Felder zurücksetzen
    },
    deleteTask(id) {
      // Löscht eine Aufgabe
      this.tasks = this.tasks.filter((task) => task.id !== id);
    },
    markCompleted(id) {
      // Markiert eine Aufgabe als erledigt
      const task = this.tasks.find((task) => task.id === id);
      if (task) task.completed = true;
    },
    setFilter(filter) {
      // Setzt den Filter
      this.filter = filter;
    },
  },
};
</script>

<style>
/* Allgemeines Styling */
body {
  font-family: Arial, sans-serif;
  background: #f4f4f4;
  margin: 0;
  padding: 20px;
}

#app {
  max-width: 600px;
  margin: 0 auto;
  background: #fff;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.logo-container {
  text-align: center;
  margin-bottom: 20px;
}

.logo {
  max-width: 150px;
  height: auto;
}

h1, h2 {
  text-align: center;
  color: #333;
}

.task-input,
.search-bar {
  margin-bottom: 20px;
}

.task-input input,
.search-bar input,
.task-input select {
  width: 70%;
  margin-right: 10px;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.task-input button {
  padding: 10px 15px;
  background: #007bff;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.task-input button:hover {
  background: #0056b3;
}

.filters button {
  padding: 10px 15px;
  background: #6c757d;
  color: #fff;
  border: none;
  border-radius: 4px;
  margin-right: 5px;
  cursor: pointer;
}

.filters button:hover {
  background: #5a6268;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  padding: 10px;
  margin-bottom: 10px;
  border-bottom: 1px solid #ddd;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

li.high {
  background: #ffcccc;
}

li.medium {
  background: #fff0b3;
}

li.low {
  background: #d4f5d4;
}

li.completed {
  text-decoration: line-through;
  color: #999;
}
</style>
