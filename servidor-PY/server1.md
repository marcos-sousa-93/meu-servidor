# servidor em python

`import socketio`: Importa a biblioteca Socket.IO para comunicação em tempo real.
<hr>

`from aiohttp import web`: Importa o servidor web assíncrono usado pelo Socket.IO.
<hr>

`sio = socketio.AsyncServer(async_mode="aiohttp")`: Cria o servidor Socket.IO usando o aiohttp.
<hr>

`app = web.Application()`: Cria a aplicação HTTP que receberá as conexões.
<hr>

`sio.attach(app)`: Conecta o servidor Socket.IO à aplicação HTTP.
<hr>

`@sio.event`: Registra a função abaixo como manipuladora do evento.
<hr>

`async def connect(sid, environ, auth):`: Executa esta função quando um cliente se conecta.
<hr>

`print(f"Cliente conectado: {sid}")`:  Exibe no terminal o identificador do cliente.
<hr>

`@sio.on("message")`: Associa a função abaixo ao evento personalizado "message".
<hr>

`async def handle_message(sid, data):`: Trata mensagens recebidas no evento "message".
<hr>

`await sio.send(data, to=sid)`: Devolve a mensagem somente ao cliente que a enviou.
<hr>

`async def disconnect(sid, reason):`: Executa esta função quando um cliente se desconecta.
<hr>

`print(f"Cliente desconectado: {sid}")`: Registra no terminal o identificador desconectado.
<hr>

`if __name__ == "__main__":`: Confirma que este arquivo foi executado diretamente.
<hr>

`web.run_app(app, host="0.0.0.0", port=5000)`: Inicia o servidor na porta 5000.
<hr>

### server.py
```python
import socketio
from aiohttp import web


sio = socketio.AsyncServer(async_mode="aiohttp")
app = web.Application()
sio.attach(app)


@sio.event
async def connect(sid, environ, auth):
    print(f"Cliente conectado: {sid}")


@sio.on("message") 
async def handle_message(sid, data):
    await sio.send(data, to=sid)


@sio.event 
async def disconnect(sid, reason): 
    print(f"Cliente desconectado: {sid}")  


if __name__ == "__main__":  
    web.run_app(app, host="0.0.0.0", port=5000)  
```
<hr>

### Instala o socket.io
```cmd
pip install "python-socketio[aiohttp]"
```
<hr>

### inicia o servidor python
```cmd
python server.py
```
