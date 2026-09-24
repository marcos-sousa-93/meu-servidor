# 1. Instalação
`CMD`
```cmd
npm init -y
```

`CMD`
```cmd
npm install socket.io
```

`CMD`
```cmd
npm install express
```

# 2. Servidor (server.js)

```javascript
const express = require('express');
const http = require('http');
const { Server } = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = new Server(server, {
  cors: {
    origin: '*',
    methods: ['GET', 'POST']
  }
});

// Servir arquivos estáticos (opcional, para o cliente HTML)
app.use(express.static('public'));

io.on('connection', (socket) => {
  console.log(`✅ Cliente conectado: ${socket.id}`);

  // Mensagem de boas-vindas
  socket.emit('mensagem', `Bem-vindo! Seu ID é ${socket.id}`);

  // Receber mensagem do cliente
  socket.on('mensagem', (data) => {
    console.log(`📩 Recebido de ${socket.id}:`, data);
    // Retransmitir para todos
    io.emit('mensagem', `[${socket.id}]: ${data}`);
  });

  // Desconexão
  socket.on('disconnect', () => {
    console.log(`❌ Cliente desconectado: ${socket.id}`);
  });
});

const PORT = 3000;
server.listen(PORT, () => {
  console.log(`🚀 Servidor rodando em http://localhost:${PORT}`);
});
```
