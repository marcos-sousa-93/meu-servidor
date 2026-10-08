# 1. Instalação
`CMD computador`
```cmd
npm init -y
```

`Terminal VScode`
```cmd
npm.cmd init -y
```

`CMD computador`
```cmd
npm install socket.io
```

`Terminal VScode`
```cmd
npm.cmd install socket.io
```

`CMD computador`
```cmd
npm install express
```

`Terminal VScode`
```cmd
npm.cmd install express
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
