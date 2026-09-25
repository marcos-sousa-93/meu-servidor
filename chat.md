# Começando
### Bem-vindo ao tutorial do Socket.IO!

Neste tutorial, criaremos um aplicativo de bate-papo básico. Ele não exige praticamente nenhum conhecimento prévio de Node.js ou Socket.IO, sendo ideal para usuários de todos os níveis de conhecimento.

# Introdução
Desenvolver um aplicativo de bate-papo usando stacks populares de aplicações web como LAMP (PHP) normalmente é muito difícil. Envolve consultar o servidor constantemente para verificar alterações, controlar timestamps e é muito mais lento do que deveria ser.

Tradicionalmente, os sockets têm sido a solução em torno da qual a maioria dos sistemas de bate-papo em tempo real são arquitetados, fornecendo um canal de comunicação bidirecional entre um cliente e um servidor.

Isso significa que o servidor pode enviar mensagens aos clientes. Sempre que você escreve uma mensagem no chat, a ideia é que o servidor a receba e a envie para todos os outros clientes conectados.

# Comece a usar esse tutorial
### Ferramentas

Qualquer editor de texto (desde um editor de texto básico até uma IDE completa como o VS Code ) deve ser suficiente para concluir este tutorial.

Além disso, ao final de cada etapa, você encontrará um link para algumas plataformas online ( CodeSandbox e StackBlitz , por exemplo), que permitem executar o código diretamente do seu navegador:

# Configuração de sintaxe
No mundo Node.js, existem duas maneiras de importar módulos:

O método tradicional: CommonJS
```javascript
const { Server } = require("socket.io");
```
# Inicialização do projeto
 
 `express` O primeiro objetivo é configurar uma página web HTML simples que exiba um formulário e uma lista de mensagens. Para isso, usaremos o framework web Node.js. Certifique-se de que o Node.js esteja instalado.

Primeiro, vamos criar um `package.json` arquivo de manifesto que descreva nosso projeto. Recomendo que você o coloque em um diretório vazio dedicado (vou chamá-lo de `socket-chat-example`).

```javascript
{
  "name": "socket-chat-example",
  "version": "0.0.1",
  "description": "my first socket.io app",
  "type": "commonjs",
  "dependencies": {}
}
```

Agora, para preencher facilmente a `dependencies` propriedade com os itens necessários, usaremos `npm install`:
```cmd
npm install express@4
```

Após a instalação, podemos criar um `index.js` arquivo que configurará nossa aplicação.
```javascript
const express = require('express');
const { createServer } = require('node:http');

const app = express();
const server = createServer(app);

app.get('/', (req, res) => {
  res.send('<h1>Hello world</h1>');
});

server.listen(3000, () => {
  console.log('server running at http://localhost:3000');
});
```

Isso significa que:

O Express é inicializado `app` como um manipulador de funções que você pode fornecer a um servidor HTTP (como visto na linha 5).
Definimos um manipulador de rotas `/` que é chamado quando acessamos a página inicial do nosso site.
Configuramos o servidor HTTP para escutar na porta 3000.
Se você executar o comando `node index.js`, deverá ver o seguinte:

Servindo HTML
Até agora, `index.js` estamos chamando `res.send` e passando uma string de HTML. Nosso código ficaria muito confuso se simplesmente colocássemos todo o HTML da nossa aplicação ali, então, em vez disso, vamos criar um `index.html` arquivo e servi-lo.

Vamos refatorar nosso manipulador de rotas para usar `sendFile` em vez disso.

```javascript
const express = require('express');
const { createServer } = require('node:http');
const { join } = require('node:path');

const app = express();
const server = createServer(app);

app.get('/', (req, res) => {
  res.sendFile(join(__dirname, 'index.html'));
});

server.listen(3000, () => {
  console.log('server running at http://localhost:3000');
});
```

Inclua o seguinte em seu index.htmlarquivo:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>Socket.IO chat</title>
    <style>
      body { margin: 0; padding-bottom: 3rem; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; }

      #form { background: rgba(0, 0, 0, 0.15); padding: 0.25rem; position: fixed; bottom: 0; left: 0; right: 0; display: flex; height: 3rem; box-sizing: border-box; backdrop-filter: blur(10px); }
      #input { border: none; padding: 0 1rem; flex-grow: 1; border-radius: 2rem; margin: 0.25rem; }
      #input:focus { outline: none; }
      #form > button { background: #333; border: none; padding: 0 1rem; margin: 0.25rem; border-radius: 3px; outline: none; color: #fff; }

      #messages { list-style-type: none; margin: 0; padding: 0; }
      #messages > li { padding: 0.5rem 1rem; }
      #messages > li:nth-child(odd) { background: #efefef; }
    </style>
  </head>
  <body>
    <ul id="messages"></ul>
    <form id="form" action="">
      <input id="input" autocomplete="off" /><button>Send</button>
    </form>
  </body>
</html>
```

Integrando Socket.IO
O Socket.IO é composto por duas partes:

Um servidor que se integra (ou é montado) no servidor HTTP do Node.js (o `socket.io` pacote).
Uma biblioteca cliente que é carregada no lado do navegador (o `socket.io-client` pacote)

Durante o desenvolvimento, `socket.io` o cliente é atendido automaticamente, como veremos, então por enquanto só precisamos instalar um módulo:

```cmd
npm install socket.io
```

Isso instalará o módulo e adicionará a dependência ao arquivo `package.json` . Agora, vamos editar o arquivo `index.js` para adicioná-lo:

```javascript
const express = require('express');
const { createServer } = require('node:http');
const { join } = require('node:path');
const { Server } = require('socket.io');

const app = express();
const server = createServer(app);
const io = new Server(server);

app.get('/', (req, res) => {
  res.sendFile(join(__dirname, 'index.html'));
});

io.on('connection', (socket) => {
  console.log('a user connected');
});

server.listen(3000, () => {
  console.log('server running at http://localhost:3000');
});
```

Observe que inicializo uma nova instância `socket.io` passando o serverobjeto (o servidor HTTP). Em seguida, escuto o `connection` evento de sockets recebidos e o registro no console.

Agora, no arquivo index.html, adicione o seguinte trecho de código antes da `</body>` tag de fechamento do corpo (</body>):

```javascript
<script src="/socket.io/socket.io.js"></script>
<script>
  const socket = io();
</script>
```

Isso é tudo o que é preciso para carregar o `socket.io-client`, que expõe um `io` global (e o endpoint `GET/socket.io/socket.io.js`), e então conectar.

Se você quiser usar a versão local do arquivo JS do lado do cliente, você pode encontrá-la em `node_modules/socket.io/client-dist/socket.io.js`.

Se você reiniciar o processo (pressionando Control+C e executando-o `node index.js` novamente) e, em seguida, atualizar a página da web, deverá ver a mensagem "um usuário se conectou" no console.

Tente abrir várias abas e você verá várias mensagens.

Cada soquete também dispara um `disconnect` evento especial:
```javascript
io.on('connection', (socket) => {
  console.log('a user connected');
  socket.on('disconnect', () => {
    console.log('user disconnected');
  });
});
```

Eventos de emissão
A ideia principal por trás do Socket.IO é que você pode enviar e receber quaisquer eventos que desejar, com quaisquer dados que desejar. Quaisquer objetos que possam ser codificados como JSON funcionarão, e dados binários também são suportados.

Vamos configurar para que, quando o usuário digitar uma mensagem, o servidor a receba como um `chat message` evento. A `script` seção `index.html` agora deve ter a seguinte aparência:

```javascript
<script src="/socket.io/socket.io.js"></script>
<script>
  const socket = io();

  const form = document.getElementById('form');
  const input = document.getElementById('input');

  form.addEventListener('submit', (e) => {
    e.preventDefault();
    if (input.value) {
      socket.emit('chat message', input.value);
      input.value = '';
    }
  });
</script>
```

E `index.js` imprimimos o `chat message` evento:

```javascript
io.on('connection', (socket) => {
  socket.on('chat message', (msg) => {
    console.log('message: ' + msg);
  });
});
```

Radiodifusão
O próximo objetivo é emitir o evento do servidor para os demais usuários.

Para enviar um evento para todos, o Socket.IO nos fornece o `io.emit()` método.
```javascript
// Isso emitirá o evento para todos os sockets conectados.
io.emit('hello', 'world'); 
```

Se você deseja enviar uma mensagem para todos, exceto para um determinado socket emissor, temos a `broadcast` flag para emitir a partir desse socket:
```javascript
io.on('connection', (socket) => {
  socket.broadcast.emit('hi');
});
```

Neste caso, por uma questão de simplicidade, enviaremos a mensagem a todos, incluindo o remetente.

```javascript
io.on('connection', (socket) => {
  socket.on('chat message', (msg) => {
    io.emit('chat message', msg);
  });
});
```

E do lado do cliente, quando capturamos um `chat message` evento, nós o incluímos na página.

```javascript
<script src="/socket.io/socket.io.js"></script>
<script>
  const socket = io();

  const form = document.getElementById('form');
  const input = document.getElementById('input');
  const messages = document.getElementById('messages');

  form.addEventListener('submit', (e) => {
    e.preventDefault();
    if (input.value) {
      socket.emit('chat message', input.value);
      input.value = '';
    }
  });

  socket.on('chat message', (msg) => {
    const item = document.createElement('li');
    item.textContent = msg;
    messages.appendChild(item);
    window.scrollTo(0, document.body.scrollHeight);
  });
</script>
```

Visão geral da API
Antes de prosseguirmos, vamos dar uma olhada rápida na API fornecida pelo Socket.IO:

API comum
Os seguintes métodos estão disponíveis tanto para o cliente quanto para o servidor.

Emissão básica

Como vimos na etapa nº 4 , você pode enviar quaisquer dados para o outro lado com `socket.emit()`:

Do cliente para o servidor

`Cliente`
```javascript
socket.emit('hello', 'world');
```

`Servidor`
```javascript
io.on('connection', (socket) => {
  socket.on('hello', (arg) => {
    console.log(arg); // 'world'
  });
});
```

Do servidor para o cliente

`Servidor`
```javascript
io.on('connection', (socket) => {
  socket.emit('hello', 'world');
});
```

`Cliente`
```javascript
socket.on('hello', (arg) => {
  console.log(arg); // 'world'
});
```

Você pode enviar qualquer número de argumentos, e todas as estruturas de dados serializáveis ​​são suportadas, incluindo objetos binários como ArrayBuffer , TypedArray ou Buffer (somente Node.js):

Do cliente para o servidor

`Cliente`
```javascript
socket.emit('hello', 1, '2', { 3: '4', 5: Uint8Array.from([6]) });
```

`Servidor`
```javascript
io.on('connection', (socket) => {
  socket.on('hello', (arg1, arg2, arg3) => {
    console.log(arg1); // 1
    console.log(arg2); // '2'
    console.log(arg3); // { 3: '4', 5: <Buffer 06> }
  });
});
```

Do servidor para o cliente

`Servidor`
```javascript
io.on('connection', (socket) => {
  socket.emit('hello', 1, '2', { 3: '4', 5: Buffer.from([6]) });
});
```

`Cliente`
```javascript
socket.on('hello', (arg1, arg2, arg3) => {
  console.log(arg1); // 1
  console.log(arg2); // '2'
  console.log(arg3); // { 3: '4', 5: ArrayBuffer (1) [ 6 ] }
});
```

dica
JSON.stringify()Não é necessário invocar objetos:

```javascript
// MAU
socket.emit('hello', JSON.stringify({ name: 'John' }));

// BOM
socket.emit('hello', { name: 'John' });
```

Eventos são ótimos, mas em alguns casos você pode preferir uma API de requisição-resposta mais clássica. No Socket.IO, esse recurso se chama "acknowledgements" (confirmações).

Está disponível em dois sabores:

Com uma 
Você pode adicionar uma função de retorno (callback) como último argumento do evento `emit()`, e essa função será chamada assim que a outra parte confirmar o evento:

Do cliente para o servidor

`Cliente`
```javascript
socket.timeout(5000).emit('request', { foo: 'bar' }, 'baz', (err, response) => {
  if (err) {
    // the server did not acknowledge the event in the given delay
  } else {
    console.log(response.status); // 'ok'
  }
});
```

`Servidor`
```javascript
io.on('connection', (socket) => {
  socket.on('request', (arg1, arg2, callback) => {
    console.log(arg1); // { foo: 'bar' }
    console.log(arg2); // 'baz'
    callback({
      status: 'ok'
    });
  });
});
```

Do servidor para o cliente

`Servidor`
```javascript
io.on('connection', (socket) => {
  socket.timeout(5000).emit('request', { foo: 'bar' }, 'baz', (err, response) => {
    if (err) {
      // the client did not acknowledge the event in the given delay
    } else {
      console.log(response.status); // 'ok'
    }
  });
});
```

`Cliente`
```javascript
socket.on('request', (arg1, arg2, callback) => {
  console.log(arg1); // { foo: 'bar' }
  console.log(arg2); // 'baz'
  callback({
    status: 'ok'
  });
});
```

Com uma promessa
O `emitWithAck()` método oferece a mesma funcionalidade, mas retorna uma Promise que será resolvida assim que a outra parte reconhecer o evento:

Do cliente para o servidor
`Cliente`
```javascript
try {
  const response = await socket.timeout(5000).emitWithAck('request', { foo: 'bar' }, 'baz');
  console.log(response.status); // 'ok'
} catch (e) {
  // the server did not acknowledge the event in the given delay
}
```

`Servidor`
```javascript
io.on('connection', (socket) => {
  socket.on('request', (arg1, arg2, callback) => {
    console.log(arg1); // { foo: 'bar' }
    console.log(arg2); // 'baz'
    callback({
      status: 'ok'
    });
  });
});
```

Ouvintes abrangentes
Um ouvinte genérico é um ouvinte que será chamado para qualquer evento recebido. Isso é útil para depurar sua aplicação:

`Remetente`
```javascript
socket.emit('hello', 1, '2', { 3: '4', 5: Uint8Array.from([6]) });
```

`Receptor`
```javascript
socket.onAny((eventName, ...args) => {
  console.log(eventName); // 'hello'
  console.log(args); // [ 1, '2', { 3: '4', 5: ArrayBuffer (1) [ 6 ] } ]
});
```

Da mesma forma, para pacotes de saída:

```javascript
socket.onAnyOutgoing((eventName, ...args) => {
  console.log(eventName); // 'hello'
  console.log(args); // [ 1, '2', { 3: '4', 5: ArrayBuffer (1) [ 6 ] } ]
});
```

API do servidor
Radiofusão
Como vimos na etapa nº 5 , você pode transmitir um evento para todos os clientes conectados com `io.emit()`:

```javascript
io.emit('hello', 'world');
```

<pre>
           |--->   "hello" evento   --->   Cliente
Servidor   |--->   "hello" evento   --->   Cliente
           |--->   "hello" evento   --->   Cliente
</pre>

Na terminologia do Socket.IO, uma sala é um canal arbitrário ao qual os sockets podem se conectar e sair. Ela pode ser usada para transmitir eventos para um subconjunto de clientes conectados:

```javascript
io.on('connection', (socket) => {
  // join the room named 'some room'
  socket.join('some room');
  
  // broadcast to all connected clients in the room
  io.to('some room').emit('hello', 'world');

  // broadcast to all connected clients except those in the room
  io.except('some room').emit('hello', 'world');

  // leave the room
  socket.leave('some room');
});
```

<pre>
           |--->   "hello" evento   --->   Cliente
Servidor   |--->   "hello" evento   --->   Cliente
                                           Cliente  ( não incluído no grupo-alvo )
</pre>

Lidar com desconexões
Agora, vamos destacar duas propriedades realmente importantes do Socket.IO:

Um cliente Socket.IO nem sempre está conectado.
Um servidor Socket.IO não armazena nenhum evento.

Cuidado
Mesmo em uma rede estável, não é possível manter uma conexão ativa indefinidamente.

Isso significa que seu aplicativo precisa ser capaz de sincronizar o estado local do cliente com o estado global no servidor após uma desconexão temporária.

observação
O cliente Socket.IO tentará se reconectar automaticamente após um pequeno atraso. No entanto, qualquer evento perdido durante o período de desconexão será efetivamente perdido para este cliente.

<pre>
           |--->   "Chat mensagem" evento   --->   Cliente
Servidor   |--->   "Chat mensagem" evento   --->   Cliente
                                                   Cliente  ( não conectado )
</pre>

Recuperação do estado da conexão
Primeiro, vamos lidar com as desconexões fingindo que não houve desconexão: esse recurso é chamado de "Recuperação do estado da conexão".

Essa funcionalidade armazenará temporariamente todos os eventos enviados pelo servidor e tentará restaurar o estado do cliente quando ele se reconectar:

restaurar seus quartos
Envie os eventos que você perdeu.
Deve ser ativado no servidor:

`index.js`
```javascript
const io = new Server(server, {
  connectionStateRecovery: {}
});
```

observação
O botão "Desconectar" foi adicionado para fins de demonstração.

```javascript
<form id="form" action="">
  <input id="input" autocomplete="off" /><button>Send</button>
  <button id="toggle-btn">Disconnect</button>
</form>

<script>
  const toggleButton = document.getElementById('toggle-btn');

  toggleButton.addEventListener('click', (e) => {
    e.preventDefault();
    if (socket.connected) {
      toggleButton.innerText = 'Connect';
      socket.disconnect();
    } else {
      toggleButton.innerText = 'Disconnect';
      socket.connect();
    }
  });
</script>
```

Ótimo! Agora, você pode perguntar:

Mas essa é uma funcionalidade incrível, por que ela não está ativada por padrão?

Existem vários motivos para isso:

Nem sempre funciona; por exemplo, se o servidor falhar abruptamente ou for reiniciado, o estado do cliente pode não ser salvo.
Nem sempre é possível ativar esse recurso ao aumentar a escala.
dica
Dito isso, é realmente um ótimo recurso, já que você não precisa sincronizar o estado do cliente após uma desconexão temporária (por exemplo, quando o usuário alterna do Wi-Fi para o 4G).

Exploraremos uma solução mais geral na próxima etapa.

Entrega de servidor
Existem duas maneiras comuns de sincronizar o estado do cliente após a reconexão:

ou o servidor envia o estado completo
ou o cliente mantém um registro do último evento que processou e o servidor envia as partes que faltam.
Ambas são soluções totalmente válidas e a escolha de uma dependerá do seu caso de uso. Neste tutorial, optaremos pela segunda opção.

Primeiro, vamos persistir as mensagens do nosso aplicativo de bate-papo. Hoje em dia existem muitas ótimas opções; aqui , usaremos o SQLite .

dica
Se você não está familiarizado com o SQLite, existem muitos tutoriais disponíveis online, como este .

Vamos instalar os pacotes necessários:

```javascript
npm install sqlite
```

```javascript
npm install sqlite3
```

`index.js`
```javascript
const express = require('express');
const { createServer } = require('node:http');
const { join } = require('node:path');
const { Server } = require('socket.io');
const sqlite3 = require('sqlite3');
const { open } = require('sqlite');

async function main() {
  // open the database file
  const db = await open({
    filename: 'chat.db',
    driver: sqlite3.Database
  });

  // create our 'messages' table (you can ignore the 'client_offset' column for now)
  await db.exec(`
    CREATE TABLE IF NOT EXISTS messages (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        client_offset TEXT UNIQUE,
        content TEXT
    );
  `);

  const app = express();
  const server = createServer(app);
  const io = new Server(server, {
    connectionStateRecovery: {}
  });

  app.get('/', (req, res) => {
    res.sendFile(join(__dirname, 'index.html'));
  });

  io.on('connection', (socket) => {
    socket.on('chat message', async (msg) => {
      let result;
      try {
        // store the message in the database
        result = await db.run('INSERT INTO messages (content) VALUES (?)', msg);
      } catch (e) {
        // TODO handle the failure
        return;
      }
      // include the offset with the message
      io.emit('chat message', msg, result.lastID);
    });
  });

  server.listen(3000, () => {
    console.log('server running at http://localhost:3000');
  });
}

main();
```

O cliente então acompanhará o deslocamento:

`index.html`
```javascript
<script>
  const socket = io({
    auth: {
      serverOffset: 0
    }
  });

  const form = document.getElementById('form');
  const input = document.getElementById('input');
  const messages = document.getElementById('messages');

  form.addEventListener('submit', (e) => {
    e.preventDefault();
    if (input.value) {
      socket.emit('chat message', input.value);
      input.value = '';
    }
  });

  socket.on('chat message', (msg, serverOffset) => {
    const item = document.createElement('li');
    item.textContent = msg;
    messages.appendChild(item);
    window.scrollTo(0, document.body.scrollHeight);
    socket.auth.serverOffset = serverOffset;
  });
</script>
```

E, finalmente, o servidor enviará as mensagens em falta após a (re)conexão:

`index.js`
```javascript
// [...]

io.on('connection', async (socket) => {
  socket.on('chat message', async (msg) => {
    let result;
    try {
      result = await db.run('INSERT INTO messages (content) VALUES (?)', msg);
    } catch (e) {
      // TODO handle the failure
      return;
    }
    io.emit('chat message', msg, result.lastID);
  });

  if (!socket.recovered) {
    // if the connection state recovery was not successful
    try {
      await db.each('SELECT id, content FROM messages WHERE id > ?',
        [socket.handshake.auth.serverOffset || 0],
        (_err, row) => {
          socket.emit('chat message', row.content, row.id);
        }
      )
    } catch (e) {
      // something went wrong
    }
  }
});

// [...]
```

A diferença em relação ao recurso "Recuperação do estado da conexão" é que uma recuperação bem-sucedida pode não precisar acessar o banco de dados principal (podendo, por exemplo, buscar as mensagens em um fluxo do Redis).

Certo, agora vamos falar sobre a entrega ao cliente.
Entrega ao cliente
Vejamos como podemos garantir que o servidor sempre receba as mensagens enviadas pelos clientes.

informações
Por padrão, o Socket.IO oferece uma garantia de entrega "no máximo uma vez" (também conhecida como "disparar e esquecer"), o que significa que não haverá novas tentativas caso a mensagem não chegue ao servidor.

 armazenados em buffer
Quando um cliente é desconectado, qualquer chamada `socket.emit()` é armazenada em buffer até que a conexão seja restabelecida:

Esse comportamento pode ser totalmente suficiente para sua aplicação. No entanto, existem alguns casos em que uma mensagem pode ser perdida:

A conexão é interrompida enquanto o evento está sendo enviado.
O servidor trava ou é reiniciado durante o processamento do evento.
O banco de dados está temporariamente indisponível.
Pelo menos 
Podemos implementar uma garantia de "pelo menos uma vez":

manualmente com confirmação:

```javascript
function emit(socket, event, arg) {
  socket.timeout(5000).emit(event, arg, (err) => {
    if (err) {
      // Sem confirmação do servidor, vamos tentar novamente.
      emit(socket, event, arg);
    }
  });
}

emit(socket, 'hello', 'world');
```

ou com a `retries` opção:

```javascript
const socket = io({
  ackTimeout: 10000,
  retries: 3
});

socket.emit('hello', 'world');
```

Em ambos os casos, o cliente tentará reenviar a mensagem até receber uma confirmação do servidor:

```javascript
io.on('connection', (socket) => {
  socket.on('hello', (value, callback) => {
    // uma vez que o evento seja gerenciado com sucesso
    callback();
  });
})
```

dica
Com essa `retries` opção, a ordem das mensagens é garantida, pois elas são enfileiradas e enviadas uma a uma. Isso não acontece com a primeira opção.

Exatamente 
O problema com as novas tentativas é que o servidor pode receber a mesma mensagem várias vezes, então precisa de uma maneira de identificar cada mensagem de forma exclusiva e armazená-la apenas uma vez no banco de dados.

Vamos ver como podemos implementar uma garantia de "exatamente uma vez" em nosso aplicativo de bate-papo.

Começaremos atribuindo um identificador único a cada mensagem no lado do cliente:

`ìndex.html`
```javascript
<script>
  let counter = 0;

  const socket = io({
    auth: {
      serverOffset: 0
    },
    // enable retries
    ackTimeout: 10000,
    retries: 3,
  });

  const form = document.getElementById('form');
  const input = document.getElementById('input');
  const messages = document.getElementById('messages');

  form.addEventListener('submit', (e) => {
    e.preventDefault();
    if (input.value) {
      // compute a unique offset
      const clientOffset = `${socket.id}-${counter++}`;
      socket.emit('chat message', input.value, clientOffset);
      input.value = '';
    }
  });

  socket.on('chat message', (msg, serverOffset) => {
    const item = document.createElement('li');
    item.textContent = msg;
    messages.appendChild(item);
    window.scrollTo(0, document.body.scrollHeight);
    socket.auth.serverOffset = serverOffset;
  });
</script>
```

observação
O `socket.id` atributo é um identificador aleatório de 20 caracteres que é atribuído a cada conexão.

Também poderíamos ter usado `getRandomValues()` para gerar um deslocamento único.

Em seguida, armazenamos esse deslocamento junto com a mensagem no servidor:

`index.js`
```javascript
// [...]

io.on('connection', async (socket) => {
  socket.on('chat message', async (msg, clientOffset, callback) => {
    let result;
    try {
      result = await db.run('INSERT INTO messages (content, client_offset) VALUES (?, ?)', msg, clientOffset);
    } catch (e) {
      if (e.errno === 19 /* SQLITE_CONSTRAINT */ ) {
        // the message was already inserted, so we notify the client
        callback();
      } else {
        // nothing to do, just let the client retry
      }
      return;
    }
    io.emit('chat message', msg, result.lastID);
    // acknowledge the event
    callback();
  });

  if (!socket.recovered) {
    try {
      await db.each('SELECT id, content FROM messages WHERE id > ?',
        [socket.handshake.auth.serverOffset || 0],
        (_err, row) => {
          socket.emit('chat message', row.content, row.id);
        }
      )
    } catch (e) {
      // something went wrong
    }
  }
});

// [...]
```

Dessa forma, a restrição UNIQUE na `client_offset` coluna impede a duplicação da mensagem.
Cuidado
Não se esqueça de confirmar o evento, caso contrário o cliente continuará tentando (até um `retries` determinado número de vezes).

```javascript
socket.on('chat message', async (msg, clientOffset, callback) => {
  // ... and finally
  callback();
});
```

informações
Novamente, a garantia padrão ("no máximo uma vez") pode ser suficiente para sua aplicação, mas agora você sabe como torná-la mais confiável.

Na próxima etapa, veremos como podemos expandir nossa aplicação horizontalmente.

Escala horizontal
Agora que nossa aplicação é resiliente a interrupções temporárias de rede, vamos ver como podemos escalá-la horizontalmente para suportar milhares de clientes simultâneos.

observação
A escalabilidade horizontal (também conhecida como "escalonamento horizontal") significa adicionar novos servidores à sua infraestrutura para atender às novas demandas.
A escalabilidade vertical (também conhecida como "aumento de escala") significa adicionar mais recursos (poder de processamento, memória, armazenamento, etc.) à sua infraestrutura existente.
Primeiro passo: vamos usar todos os núcleos disponíveis do host. Por padrão, o Node.js executa seu código Javascript em uma única thread, o que significa que, mesmo com uma CPU de 32 núcleos, apenas um núcleo será utilizado. Felizmente, o `cluster` módulo do Node.js oferece uma maneira prática de criar uma thread de trabalho por núcleo.

Também precisaremos de uma maneira de encaminhar eventos entre os servidores Socket.IO. Chamamos esse componente de "Adaptador".

<pre>
                                    |--->   "hello" evento   --->   (Cliente)
 io.emit("hello")  -> (Servidor) -> |--->   "hello" evento   --->   (Cliente)
                          |         |--->   "hello" evento   --->   (Cliente)
                          |
                         \|/        |--->   "hello" evento   --->   (Cliente)
                      (Servidor) -> |--->   "hello" evento   --->   (Cliente)
                                    |--->   "hello" evento   --->   (Cliente)
</pre>

Então vamos instalar o adaptador de cluster:

`CMD`
```cmd
npm install @socket.io/cluster-adapter
```

Agora vamos ligá-lo:

```javascript
const express = require('express');
const { createServer } = require('node:http');
const { join } = require('node:path');
const { Server } = require('socket.io');
const sqlite3 = require('sqlite3');
const { open } = require('sqlite');
const { availableParallelism } = require('node:os');
const cluster = require('node:cluster');
const { createAdapter, setupPrimary } = require('@socket.io/cluster-adapter');

if (cluster.isPrimary) {
  const numCPUs = availableParallelism();
  // create one worker per available core
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork({
      PORT: 3000 + i
    });
  }
  
  // set up the adapter on the primary thread
  return setupPrimary();
}

async function main() {
  const app = express();
  const server = createServer(app);
  const io = new Server(server, {
    connectionStateRecovery: {},
    // set up the adapter on each worker thread
    adapter: createAdapter()
  });

  // [...]

  // each worker will listen on a distinct port
  const port = process.env.PORT;

  server.listen(port, () => {
    console.log(`server running at http://localhost:${port}`);
  });
}

main();
```

observação
Na maioria dos casos, você também precisaria garantir que todas as requisições HTTP de uma sessão Socket.IO chegassem ao mesmo servidor (também conhecido como "sessão persistente"). Isso não é necessário aqui, pois cada servidor Socket.IO tem sua própria porta.

E isso finalmente conclui nosso aplicativo de bate-papo! Neste tutorial, vimos como:

Enviar um evento entre o cliente e o servidor.
transmitir um evento para todos ou um subconjunto de clientes conectados
lidar com desconexões temporárias
escalar para fora
Agora você deve ter uma visão geral melhor dos recursos oferecidos pelo Socket.IO. Chegou a hora de criar seu próprio aplicativo em tempo real!

Código final do servidor

```javascript
const express = require('express');
const { createServer } = require('node:http');
const { join } = require('node:path');
const { Server } = require('socket.io');
const sqlite3 = require('sqlite3');
const { open } = require('sqlite');
const { availableParallelism } = require('node:os');
const cluster = require('node:cluster');
const { createAdapter, setupPrimary } = require('@socket.io/cluster-adapter');

if (cluster.isPrimary) {
  const numCPUs = availableParallelism();
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork({
      PORT: 3000 + i
    });
  }

  return setupPrimary();
}

async function main() {
  const db = await open({
    filename: 'chat.db',
    driver: sqlite3.Database
  });

  await db.exec(`
    CREATE TABLE IF NOT EXISTS messages (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      client_offset TEXT UNIQUE,
      content TEXT
    );
  `);

  const app = express();
  const server = createServer(app);
  const io = new Server(server, {
    connectionStateRecovery: {},
    adapter: createAdapter()
  });

  app.get('/', (req, res) => {
    res.sendFile(join(__dirname, 'index.html'));
  });

  io.on('connection', async (socket) => {
    socket.on('chat message', async (msg, clientOffset, callback) => {
      let result;
      try {
        result = await db.run('INSERT INTO messages (content, client_offset) VALUES (?, ?)', msg, clientOffset);
      } catch (e) {
        if (e.errno === 19 /* SQLITE_CONSTRAINT */ ) {
          callback();
        } else {
          // nothing to do, just let the client retry
        }
        return;
      }
      io.emit('chat message', msg, result.lastID);
      callback();
    });

    if (!socket.recovered) {
      try {
        await db.each('SELECT id, content FROM messages WHERE id > ?',
          [socket.handshake.auth.serverOffset || 0],
          (_err, row) => {
            socket.emit('chat message', row.content, row.id);
          }
        )
      } catch (e) {
        // something went wrong
      }
    }
  });

  const port = process.env.PORT;

  server.listen(port, () => {
    console.log(`server running at http://localhost:${port}`);
  });
}

main();
```

Código final do cliente

```html
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>Socket.IO chat</title>
    <style>
      body { margin: 0; padding-bottom: 3rem; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; }

      #form { background: rgba(0, 0, 0, 0.15); padding: 0.25rem; position: fixed; bottom: 0; left: 0; right: 0; display: flex; height: 3rem; box-sizing: border-box; backdrop-filter: blur(10px); }
      #input { border: none; padding: 0 1rem; flex-grow: 1; border-radius: 2rem; margin: 0.25rem; }
      #input:focus { outline: none; }
      #form > button { background: #333; border: none; padding: 0 1rem; margin: 0.25rem; border-radius: 3px; outline: none; color: #fff; }

      #messages { list-style-type: none; margin: 0; padding: 0; }
      #messages > li { padding: 0.5rem 1rem; }
      #messages > li:nth-child(odd) { background: #efefef; }
    </style>
  </head>
  <body>
    <ul id="messages"></ul>
    <form id="form" action="">
      <input id="input" autocomplete="off" /><button>Send</button>
    </form>
    <script src="/socket.io/socket.io.js"></script>
    <script>
      let counter = 0;
  
      const socket = io({
        ackTimeout: 10000,
        retries: 3,
        auth: {
          serverOffset: 0
        }
      });
  
      const form = document.getElementById('form');
      const input = document.getElementById('input');
      const messages = document.getElementById('messages');
  
      form.addEventListener('submit', (e) => {
        e.preventDefault();
        if (input.value) {
          const clientOffset = `${socket.id}-${counter++}`;
          socket.emit('chat message', input.value, clientOffset);
          input.value = '';
        }
      });
  
      socket.on('chat message', (msg, serverOffset) => {
        const item = document.createElement('li');
        item.textContent = msg;
        messages.appendChild(item);
        window.scrollTo(0, document.body.scrollHeight);
        socket.auth.serverOffset = serverOffset;
      });
    </script>
  </body>
</html>
```

Aqui estão algumas ideias para melhorar o aplicativo:

Transmita uma mensagem aos usuários conectados quando alguém se conectar ou desconectar.
Adicionar suporte para apelidos.
Não envie a mesma mensagem para o usuário que a enviou. Em vez disso, anexe a mensagem diretamente assim que ele pressionar Enter.
Adicionar funcionalidade "{usuário} está digitando".
Mostre quem está online.
Adicionar mensagens privadas.
Compartilhe suas melhorias!
