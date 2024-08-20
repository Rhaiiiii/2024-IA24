# Iniciando um projeto Node.js com TypeScript 😼 🔥 

## 1. Criando um projeto no GitHub

### Crie um novo repositório no GitHub:

`1 ->` Vá até github.com.

`2 ->` No canto superior direito, clique no ícone de "+" e depois em "New repository".

`3 ->` Dê um nome ao seu repositório (ex: "Trabalho de programação") e clique em "Create repository".

## 2. Inicializando o projeto Node.js

Ainda no terminal siga os seguintes passos para inicializar o projeto Node.js:

``Copie e cole uma linha de cada vez para que não dê erro!``

```bash
npm init -y
npm install express cors sqlite3 sqlite
npm install --save-dev typescript nodemon ts-node @types/express @types/cors
npx tsc --init
mkdir src
touch src/app.ts
```
Logo após isso aparecerá novos aquivos.

## 3. Configuranado o `tsconfig.json`

Abra o arquivo `tsconfig.json` que foi criado na sua pasta. 

Agora, procure por esta linha:

```json
"outDir": "./",
```
*Para facilitar a procura, pesquise na pasta **tsconfig.json** utilizando o "**Ctrl**" mais a tecla "**F**" (E não se esqueça de adicionar uma vírgula antes de pular um linha e o `// antes de rootDir`)*

Mude para:
```json
"outDir": "./dist",
```
Depois, adicione a linha abaixo logo embaixo da que você acabou de alterar:
```json
"rootDir": "./src",
```
O arquivo vai ficar mais ou menos assim:


```json
{
  "compilerOptions": {
    "target": "ES2017",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

## 4. Configurando o `package.json`

Abra o arquivo `package.json`:

Pressione `Ctrl + F` no seu teclado para abrir a barra de pesquisa.
Digite "scripts" na barra de pesquisa. Isso deve te levar para a seção `"scripts"` dentro do `package.json`.

Adicione o seguinte código na seção `"scripts"`:

Você verá algo como `"scripts": { "test": "echo \"Error: no test specified\" && exit 1" }.`

Substitua esse conteúdo pelo seguinte:

```json
"scripts": {
  "dev": "nodemon src/app.ts"
}
```
E o seu codigo ficará assim:

```json
 "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "npx nodemon src/app.ts"
  },
```

## 5. Criando arquivo inicial do servidor

No arquivo `app.ts (que está dentro da pasta src)`, cole o código para criar o servidor:

```typescript
import express from 'express';
import cors from 'cors';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

## 6. Inicializando o servidor

### Instalação da REST Client

Para começar, você deverá instalar a extensão **REST Client** no VSCode. Siga os passos abaixo:

`*1 ->` Clique no ícone de **Extensões** (que parece um cubo desmontando) na barra lateral esquerda.

`2 ->` Na barra de pesquisa na parte superior, digite **REST Client**.

`3 ->` Quando a extensão aparecer nos resultados, clique em **Instalar**.

`4 ->` Aguarde a instalação ser concluída antes de prosseguir para o próximo passo.


Agora, volte para o terminal e digite o seguinte comando para rodar o servidor:

```bash
npm run dev
```

 Se tudo ocorrer bem, você verá a mensagem `Server running on port 3333` no terminal.

`1 ->` Clique no botão **Abrir no navegador**.

`2 ->` O navegador será aberto e você verá a mensagem **Hello World** na página.

## 7. Configurando o banco de dados

Dentro da pasta **src**, crie um arquivo chamado **database.ts** e adicione o seguinte código:

**Copie e cole o código abaixo no database.ts!**

```typescript
import { Database, open } from 'sqlite';
import sqlite3 from 'sqlite3';

let instance: Database | null = null;

export async function connect() {
  if (instance) return instance;

  const db = await open({
     filename: './src/database.sqlite',
     driver: sqlite3.Database
   });
  
  await db.exec(`
    CREATE TABLE IF NOT EXISTS users (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      name TEXT,
      email TEXT
    )
  `);

  instance = db;
  return db;
}
```

## 8. Adicionando o banco de dados ao servidor

`1 ->` No antigo arquivo `app.ts`

`2 ->` Apague todo o código que está lá.

`3 ->` Cole o código abaixo no lugar:

```typescript
import express from 'express';
import cors from 'cors';
import { connect } from './database';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.post('/users', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;

  const result = await db.run('INSERT INTO users (name, email) VALUES (?, ?)', [name, email]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [result.lastID]);

  res.json(user);
});

app.get('/users', async (req, res) => {
  const db = await connect();
  const users = await db.all('SELECT * FROM users');

  res.json(users);
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```


## 9. Testando a inserção de dados

### Criar o Arquivo no GitHub

`1 ->` Crie um arquivo chamada **fora da pasta src**.

`2 ->` Nomeie o arquivo como `teste.http`.

### Adicionar o Código

No arquivo teste.http, cole o seguinte código:

```typescript
POST (>>>>>>>>>>SEU LINK /users<<<<<<<<<<) HTTP/1.1
Content-Type: application/json
Authorization: token xxx

{
  "name": "John Doe",
  "email": "john@example.com"
}
```
### Configurar a Porta

`1 ->` Ao lado do `terminal` terá a seção **Portas** (ou pode estar dentro dos três pontinhos **...**).

`2 ->` Clique em Portas e encontre a sua porta (por exemplo, 3333). Clique com o botão direito no número da porta.

`3 ->` Selecione Visibilidade da porta e altere de Private para Public.

### Copiar o Endereço

Após mudar a visibilidade, copie o link que aparece em Endereço Encaminhado

### Atualizar o Código no GitHub

Volte para o arquivo `teste.http` no GitHub e substitua ``>>>>>>>>>>SEU LINK /users<<<<<<<<<<`` pelo link copiado. 

*(Lembre-se de que o link deve terminar com /users, e você deve remover os parênteses e setinhas.)*

```http
POST https://meusite/users HTTP/1.1
Content-Type: application/json
Authorization: token xxx

{
  "name": "John Doe",
  "email": "john@example.com"
}
```
### Enviar a Requisição

Agora, clique na opção **'Send Request'** que aparecerá encima da primeira linha em **Post**.

Se tudo ocorrer bem, você verá a resposta:

```json
{
  "id": 1,
  "name": "John Doe",
  "email": "johndoe@mail.com"
}
```
## 10. Atualizando `teste.http`

Vamos atualizar o arquivo `teste.http`

```bash
POST (>>>>>>>>>>SEU LINK /users<<<<<<<<<<) HTTP/1.1
Content-Type: application/json
Authorization: token xxx

{
  "name": "John Doe",
  "email": "john@example.com"
}
####

PUT  (>>>>>>>>>>SEU LINK /users/1<<<<<<<<<<) HTTP/1.1
Content-Type: application/json

{
  "name": "John Doe update",
  "email": "john@example.com"
}

####

DELETE  (>>>>>>>>>>SEU LINK /users/1<<<<<<<<<<) HTTP/1.1
```

### Atualize também o código do `app.ts`

**Copie e cole!**

```typescript
import express from 'express';
import cors from 'cors';
import { connect } from './database';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.post('/users', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;

  const result = await db.run('INSERT INTO users (name, email) VALUES (?, ?)', [name, email]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [result.lastID]);

  res.json(user);
});

app.get('/users', async (req, res) => {
  const db = await connect();
  const users = await db.all('SELECT * FROM users');

  res.json(users);
});

app.put('/users/:id', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;
  const { id } = req.params;

  await db.run('UPDATE users SET name = ?, email = ? WHERE id = ?', [name, email, id]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [id]);

  res.json(user);
});

app.delete('/users/:id', async (req, res) => {
  const db = await connect();
  const { id } = req.params;

  await db.run('DELETE FROM users WHERE id = ?', [id]);

  res.json({ message: 'User deleted' });
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```
Cheque as funções POST, PUT e DELETE e verificar se tudo está funcionando conforme esperado no programa.

## 11. Criando uma página

Crie uma pasta chamada **public** e um arquivo dentro da pasta chamado **index.html**

E dentro da pasta **index.html**, adicione o seguinte código:

```html
<!DOCTYPE html>
<html lang="pt-BR">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="index.css">
  <title>Valorant</title>
</head>

<body>
  <div class="container">
    <h1>Valorant</h1>
    <h3>Se increva para ganhar skin no Valorant!</h3>

    <form>
      <input type="text" name="name" placeholder="Nome" required>
      <input type="email" name="email" placeholder="Email" required>
      <button type="submit">Cadastrar</button>
    </form>

    <table>
      <thead>
        <tr>
          <th>Id</th>
          <th>Nome</th>
          <th>Email</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody>
        <!--  -->
      </tbody>
    </table>
  </div>

  <script>
    const form = document.querySelector('form')

    form.addEventListener('submit', async (event) => {
      event.preventDefault()

      const name = form.name.value
      const email = form.email.value

      await fetch('/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name, email })
      })

      form.reset()
      fetchData()
    })

    const tbody = document.querySelector('tbody')

    async function fetchData() {
      const resp = await fetch('/users')
      const data = await resp.json()

      tbody.innerHTML = ''

      data.forEach(user => {
        const tr = document.createElement('tr')
        tr.innerHTML = `
          <td>${user.id}</td>
          <td>${user.name}</td>
          <td>${user.email}</td>
          <td>
            <button class="excluir">Excluir</button>
            <button class="editar">Editar</button>
          </td>
        `

        const btExcluir = tr.querySelector('button.excluir')
        const btEditar = tr.querySelector('button.editar')

        btExcluir.addEventListener('click', async () => {
          await fetch(`/users/${user.id}`, { method: 'DELETE' })
          tr.remove()
        })

        btEditar.addEventListener('click', async () => {
          const name = prompt('Novo nome:', user.name)
          const email = prompt('Novo email:', user.email)

          await fetch(`/users/${user.id}`, {
            method: 'PUT',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ name, email })
          })

          fetchData()
        })

        tbody.appendChild(tr)
      })
    }

    fetchData()
  </script>
</body>

</html>
```
E depois, crie outro arquivo só que agora sendo **index.css** e cole o seguinte código:

```bash
html, body {
    height: 100%;
    margin: 0;
    padding: 0;
  }
  
  body {
    background-image: url(https://images8.alphacoders.com/135/1356860.png);
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    color: #fff;
    font-family: Arial, sans-serif;
    text-align: center;
    line-height: 1.5;
  }
  
  .container {
    background: rgba(0, 0, 0, 0.7); 
    border-radius: 10px;
    padding: 20px;
    max-width: 800px;
    margin: 50px auto;
  }
  
  h1 {
    color: #8e2eac;
    font-size: 2.5em;
    margin-bottom: 10px;
  }
  
  h3 {
    font-size: 1.5em;
    margin-bottom: 20px;
  }
  
  form {
    margin-bottom: 20px;
  }
  
  form input[type="text"],
  form input[type="email"] {
    padding: 10px;
    border: none;
    border-radius: 5px;
    margin: 5px;
    width: calc(50% - 22px);
  }
  
  form button {
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    background-color: #8e2eac;
    color: #fff;
    cursor: pointer;
    font-size: 1em;
  }
  
  form button:hover {
    background-color: #4a0466;
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }
  
  table th, table td {
    padding: 10px;
    border: 1px solid #cf8ff5;
  }
  
  table th {
    background-color: #5f3399;
  }
  
  table td {
    background-color: #976ad1;
  }
  
  table button {
    padding: 5px 10px;
    border: none;
    border-radius: 5px;
    color: #fff;
    cursor: pointer;
    font-size: 0.9em;
    margin: 2px;
  }
  
  table button.excluir {
    background-color: #dc3545;
  }
  
  table button.excluir:hover {
    background-color: #c82333;
  }
  
  table button.editar {
    background-color: #28a745;
  }
  
  table button.editar:hover {
    background-color: #218838;
  }
```
## 12. Listando os usuários

Agora, adicione uma nova rota no app.ts para listar todos os usuários:

**Copie e cole o código abaixo no app.ts!**

```typescript
app.get('/users', async (req, res) => {
  const db = await connect();
  const users = await db.all('SELECT * FROM users');
  res.json(users);
});

```

## 13. Aterando app.ts

**1 ->** Na linha 9 onde tem: app.use(express.json());

voce irá colocar embaixo o seguinte codigo:

```typescript
app.use(express.static(__dirname + '/../public'))
```

E o comeco do seu arquivo `app.ts` tem que estar assim:

```typescript
import express from 'express'
import cors from 'cors'
import { connect } from './database'

const port = 3333
const app = express()

app.use(cors())
app.use(express.json())
app.use(express.static(__dirname + '/../public'))

app.get('/users', async (req, res) => {
  const db = await connect()
  const users = await db.all('SELECT * FROM users')
  res.json(users)
})
```
