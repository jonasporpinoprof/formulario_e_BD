## Comentários sobre o Projeto

O projeto é uma aplicação web simples utilizando Flask e SQLite para permitir que usuários deixem comentários. Ele consiste em:

- **index.html**: Um formulário para inserir comentários e uma lista que exibe os comentários armazenados.
- **app.py**: Um aplicativo Flask que gerencia a lógica do back-end, incluindo:
  - Inicialização do banco de dados SQLite.
  - Manipulação de requisições GET e POST para exibir e salvar comentários.
  - Redirecionamento após o envio do comentário.

### Possíveis Melhorias
1. **Validação e Segurança**:
   - Implementar sanitização de entradas para evitar injeção de SQL.
   - Evitar XSS utilizando `escape` no template Jinja.
   
2. **Melhoria na Interface**:
   - Aplicar CSS para um layout mais amigável.
   - Utilizar JavaScript para enviar os comentários sem recarregar a página (AJAX).

3. **Persistência de Banco de Dados**:
   - Considerar usar um banco de dados mais robusto para projetos maiores, como PostgreSQL ou MySQL.

---

## Tutorial para Replicar o Projeto

### 1. Criar um Ambiente Virtual no Windows
Antes de instalar as dependências, é recomendado criar um ambiente virtual para isolar o projeto. No terminal (cmd ou PowerShell), execute:
```sh
python -m venv venv
```
Ative o ambiente virtual:
```sh
venv\Scripts\activate
```

### 2. Instalar Dependências
Com o ambiente virtual ativado, instale as dependências necessárias:
```sh
pip install flask
```

### 3. Criar a Estrutura do Projeto
Crie uma pasta e dentro dela, crie os seguintes arquivos:
```sh
ProjetoPostGet
```

Crie os arquivos:
```sh
app.py
templates/index.html
```

### 4. Implementar o Código
#### **Arquivo `app.py`**
Copie e cole o seguinte código em `app.py`:
```python
from flask import Flask, render_template, request, redirect
import sqlite3

app = Flask(__name__)

def init_db():
    with sqlite3.connect("comments.db") as conn:
        cursor = conn.cursor()
        cursor.execute('''CREATE TABLE IF NOT EXISTS comments (
                          id INTEGER PRIMARY KEY AUTOINCREMENT,
                          name TEXT NOT NULL,
                          comment TEXT NOT NULL)''')
        conn.commit()

@app.route("/", methods=["GET", "POST"])
def index():
    if request.method == "POST":
        name = request.form["name"]
        comment = request.form["comment"]
        
        with sqlite3.connect("comments.db") as conn:
            cursor = conn.cursor()
            cursor.execute("INSERT INTO comments (name, comment) VALUES (?, ?)", (name, comment))
            conn.commit()
        
        return redirect("/")
    
    with sqlite3.connect("comments.db") as conn:
        cursor = conn.cursor()
        cursor.execute("SELECT name, comment FROM comments ORDER BY id DESC")
        comments = cursor.fetchall()
    
    return render_template("index.html", comments=comments)

if __name__ == "__main__":
    init_db()
    app.run(debug=True)
```

#### **Arquivo `templates/index.html`**
Adicione o seguinte código no arquivo `templates/index.html`:
```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Comentários</title>
</head>
<body>
    <h1>Deixe seu Comentário</h1>
    <form method="POST">
        <input type="text" name="name" placeholder="Seu nome" required>
        <textarea name="comment" placeholder="Seu comentário" required></textarea>
        <button type="submit">Enviar</button>
    </form>
    
    <h2>Comentários</h2>
    <ul>
        {% for name, comment in comments %}
            <li><strong>{{ name }}</strong>: {{ comment }}</li>
        {% endfor %}
    </ul>
</body>
</html>
```

### 5. Executar o Projeto
Execute o seguinte comando no terminal:
```sh
python app.py
```
Isso iniciará um servidor Flask, e você poderá acessar o site no navegador em `http://127.0.0.1:5000/`.

---
