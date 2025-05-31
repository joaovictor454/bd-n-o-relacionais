# bd-n-o-relacionais
atividade undade 4
CÓDIGO BACKEND:
require('dotenv').config();
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');

const app = express();
app.use(express.json());
app.use(cors());

mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log("🟢 Conectado ao MongoDB"))
  .catch(err => console.error("🔴 Erro ao conectar", err));

const TaskSchema = new mongoose.Schema({
  title: String,
  description: String,
  assignedTo: String,
  status: { type: String, enum: ['Pendente', 'Em Progresso', 'Concluído'], default: 'Pendente' }
});

const Task = mongoose.model('Task', TaskSchema);

app.post('/tasks', async (req, res) => {
  const task = new Task(req.body);
  await task.save();
  res.status(201).json(task);
});

app.get('/tasks', async (req, res) => {
  const tasks = await Task.find();
  res.json(tasks);
});

app.put('/tasks/:id', async (req, res) => {
  const task = await Task.findByIdAndUpdate(req.params.id, req.body, { new: true });
  res.json(task);
});

app.delete('/tasks/:id', async (req, res) => {
  await Task.findByIdAndDelete(req.params.id);
  res.json({ message: "Tarefa excluída" });
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`🚀 Servidor rodando na porta ${PORT}`));


ESTILO CSS: 
body {
    font-family: Arial, sans-serif;
    max-width: 900px;
    margin: auto;
    padding: 20px;
    background: #f2f2f275; /* fundo cinza esfumaçado */
  }
  
  h1, h2, h3 {
    text-align: center;
    
    color: #2f2f2f;
  }
  
  input, select, button {
    padding: 10px;
    margin: 8px 0;
    border: 1px solid #666666ad;
    border-radius: 8px;
    box-shadow: 2px 2px 5px rgba(0,0,0,0.1);
    width: 100%;
    box-sizing: border-box;
  }
  
  button {
    background-color: #4CAF50;
    color: white;
    border: none;
    font-weight: bold;
    transition: background 0.3s ease;
    cursor: pointer;
  }
  
  button:hover {
    background-color: #45a049;
  }
  
  .tabs {
    margin-bottom: 20px;
  }
  
  .tabs button {
    padding: 10px 15px;
    margin-right: 5px;
    border: none;
    background: #666666ad;
    border-radius: 6px 6px 0 0;
    box-shadow: 1px 1px 3px rgba(0,0,0,0.1);
  }
  
  .tabs button.active {
    background: #666666ad;
    font-weight: bold;
  }
  
  section {
    background: #f1f1f1;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 3px 3px 8px rgba(0,0,0,0.1);
    margin-top: 10px;
  }
  
  ul {
    list-style: none;
    padding-left: 0;
  }
  
  ul li {
    margin-bottom: 8px;
  }
  
  .pedido-detalhes {
    border: 1px solid #666666ad;
    padding: 15px;
    margin-top: 10px;
    border-radius: 10px;
    background: #666666ad;
    box-shadow: 2px 2px 6px rgba(0,0,0,0.1);
  }

  HTML:

  <!-- public/index.html -->
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Minha API - Interface</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <h1>Interface da API</h1>

  <form id="createForm">
    <input type="text" id="nome" placeholder="Nome" required />
    <input type="text" id="descricao" placeholder="Descrição" required />
    <button type="submit">Criar Item</button>
  </form>

  <h2>Itens Cadastrados</h2>
  <ul id="itemList"></ul>

  <script src="script.js"></script>
</body>
</html>


DATABASE:
require('dotenv').config();
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGO_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true
    });
    console.log(" Conectado ao MongoDB");
  } catch (error) {
    console.error(" Erro ao conectar ao MongoDB", error);
    process.exit(1);
  }
};

module.exports = connectDB;


TASK:
const mongoose = require('mongoose');

const TaskSchema = new mongoose.Schema({
  title: { type: String, required: true },
  description: String,
  assignedTo: String,
  status: { type: String, enum: ['Pendente', 'Em Progresso', 'Concluído'], default: 'Pendente' },
}, { timestamps: true });

module.exports = mongoose.model('Task', TaskSchema);

const express = require('express');
const Task = TaskSchema; 
const router = express.Router();


router.post('/', async (req, res) => {
  const task = new Task(req.body);
  await task.save();
  res.status(201).json(task);
});


router.get('/', async (req, res) => {
  const tasks = await Task.find();
  res.json(tasks);
});


router.put('/:id', async (req, res) => {
  const task = await Task.findByIdAndUpdate(req.params.id, req.body, { new: true });
  res.json(task);
});


router.delete('/:id', async (req, res) => {
  await Task.findByIdAndDelete(req.params.id);
  res.json({ message: "Tarefa excluída" });
});

module.exports = router;


.ENV:
PORT=5000
MONGO_URI=mongodb://localhost:27017


NODE:
const caminhoWindows = 'C:\\Users\\ian.narcizo_uscsonli\\Desktop\\BancoDeDados';
console.log(caminhoWindows);


SERVER:
const express = require('express');
const cors = require('cors');
const path = require('path');
const connectDB = require('./routes/database'); 

const app = express();
const PORT = 3000;

app.use(express.json());
app.use(cors());

connectDB();

//const tasksRoutes = require(path.join(__dirname, 'models', 'routes', 'Task'));
const tasksRoutes = require('./routes/Task');
app.use('/tasks', tasksRoutes);
