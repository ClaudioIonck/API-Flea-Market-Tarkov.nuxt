<template>
  <div id="app">
    <div class="container">
      <h1>Flea market</h1>
      <label for="item-name"></label><input type="text" id="item-name" placeholder="Digite o nome do item">
      <button id="search-button" @click="getItemDetails">Buscar</button>
      <div id="item-details"></div>
    </div>
  </div>
</template>

<script>
export default {
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
  height: 100vh;
  margin: 0;
}

.container {
  background-color: #fff;
  padding: 20px;
  box-shadow: 0 0 10px rgba(0,0,0,0.1);
  text-align: center;
}

input, button {
  margin: 10px 0;
  padding: 10px;
  width: 80%;
}

button {
  background-color: #007BFF;
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
</style>
