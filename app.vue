<template>
 <div id="app">
  <div class="container">
    <div class="container-search">
      <h1>Flea Market</h1>
      <label for="item-name"></label>
      <input type="text" id="item-name" placeholder="Digite o nome do item">
      <button id="search-button" @click="getItemDetails">Buscar</button>
      <div id="item-details"></div>
    </div>
    <div class="container-toDo">
      <h3>Objetivo</h3>
      <div class="todo-input">
        <input v-model="newTask" @keyup.enter="addTask" placeholder="Adicionar texto" />
        <button @click="addTask">Adicionar</button>
      </div>
      <ul>
        <li v-for="(task, index) in tasks" :key="index">
          <span v-if="!task.isEditing" :class="{ done: task.done }" @click="toggleTask(index)">{{ task.text }}</span>
          <input v-else v-model="task.text" @keyup.enter="updateTask(index)" @blur="updateTask(index)" />
          <button @click="removeTask(index)">x</button>
          <button @click="editTask(index)">{{ task.isEditing ? 'Salvar' : 'Editar' }}</button>
        </li>
      </ul>
    </div>
  </div>
 </div>  
</template>

<script>
export default {
  data() {
    return {
      newTask: '',
      tasks: []
    };
  },
  methods: {
    getItemDetails() {
      console.log('Função getItemDetails chamada');
      const itemName = document.getElementById('item-name').value;
      if (!itemName) {
        alert('Por favor, digite o nome de um item.');
        return;
      }

      const apiKey = '6mqCZblKUKT9FzjP';
      const baseUrl = 'https://api.tarkov-market.app/api/v1/item';

      fetch(`${baseUrl}?q=${itemName}`, {
        headers: {
          'x-api-key': apiKey
        }
      })
      .then(response => {
        if (!response.ok) {
          throw new Error('Erro na solicitação: ' + response.status);
        }
        return response.json();
      })
      .then(data => {
        const detailsDiv = document.getElementById('item-details');
        detailsDiv.innerHTML = ''; // Limpar resultados anteriores
        console.log('Dados recebidos:', data);

        if (data.length > 0) {
          data.forEach(item => {
            const itemDiv = document.createElement('div');
            itemDiv.classList.add('item');
            itemDiv.innerHTML = `
              <h3>${item.name}</h3>
              <p>Preço: ${item.price}</p>
              <p>Preço Médio 24h: ${item.avg24hPrice}</p>
              <p>Preço Médio 7 dias: ${item.avg7daysPrice}</p>
              <img src="${item.icon}" alt="${item.name}">
              <p><a href="${item.link}" target="_blank">Mais detalhes</a></p>
            `;
            detailsDiv.appendChild(itemDiv);
          });
        } else {
          detailsDiv.innerHTML = '<p>Item não encontrado.</p>';
        }
      })
      .catch(error => {
        console.error('Erro na solicitação:', error);
        document.getElementById('item-details').innerHTML = '<p>Erro na solicitação.</p>';
      });
    },
    addTask() {
      if (this.newTask.trim() !== '') {
        this.tasks.push({ text: this.newTask, done: false, isEditing: false });
        this.newTask = '';
      }
    },
    toggleTask(index) {
      this.tasks[index].done = !this.tasks[index].done;
    },
    removeTask(index) {
      this.tasks.splice(index, 1);
    },
    editTask(index) {
      this.tasks[index].isEditing = !this.tasks[index].isEditing;
      if (!this.tasks[index].isEditing) {
        this.updateTask(index);
      }
    },
    updateTask(index) {
      if (this.tasks[index].text.trim() === '') {
        this.tasks.splice(index, 1);
      } else {
        this.tasks[index].isEditing = false;
      }
    }
  }
}
</script>

<style>
body {
  font-family: Arial, sans-serif;
  background-color: #f4f4f4;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0;
  padding: 0;
  min-height: 100vh;
}

.container {
  display: flex;
  justify-content: space-between; /* Distribui os containers lado a lado */
  align-items: flex-start;
  width: 90%;
  max-width: 1200px;
  margin: 20px; /* Espaçamento opcional entre os containers */
}

.container-search,
.container-toDo {
  background-color: #fff;
  padding: 20px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  text-align: center;
  flex: 1; /* Cresce e encolhe proporcionalmente */
  margin-right: 10px; /* Espaçamento entre os containers */
}

input,
button {
  margin: 10px 0;
  padding: 10px;
  width: calc(100% - 40px);
}

button {
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}

#item-details {
  margin-top: 20px;
  text-align: left;
}

.item {
  margin-bottom: 20px;
}

.todo-input {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.todo-input input {
  width: calc(100% - 100px);
  padding: 8px;
  margin-bottom: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

.todo-input button {
  width: 100px;
  background-color: #28a745;
  color: white;
  border: none;
  cursor: pointer;
}

.container-toDo ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

.container-toDo li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 0;
  border-bottom: 1px solid #f1f1f1;
}

.container-toDo li:last-child {
  border-bottom: none;
}

.container-toDo span {
  cursor: pointer;
}

.container-toDo .done {
  text-decoration: line-through;
  color: #aaa;
}

.container-toDo button {
  background: none;
  border: none;
  color: #ff0000;
  cursor: pointer;
  font-size: 16px;
}

.container-toDo button:focus {
  outline: none;
}

/* Media Queries para Responsividade */
@media (max-width: 768px) {
  .container {
    flex-direction: column; /* Empilhar em telas menores */
  }

  .container-search,
  .container-toDo {
    margin-right: 0; /* Remover margem entre containers em coluna */
    margin-bottom: 20px; /* Espaçamento entre containers empilhados */
  }

  .container-search {
    order: -1; /* Coloca o container-search acima do container-toDo */
  }
}
</style>
