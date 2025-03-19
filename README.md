
## Conectar seu Formulário HTML a um banco de dados SQLite

1. **Configurar o Ambiente de Desenvolvimento**:
   - Certifique-se de ter o Python instalado em seu sistema.
   - Instale o Flask utilizando o pip:
     ```bash
     pip install flask
     ```

2. **Criar a Estrutura do Projeto**:
   - Crie um diretório para o projeto e, dentro dele, um arquivo principal (`app.py`) e um diretório para templates (por exemplo, `templates/`) onde o arquivo HTML do formulário será armazenado.

3. **Desenvolver o Formulário HTML**:
   - No diretório `templates/`, salve seu formulário HTML com o nome `formulario.html`. Certifique-se de que o atributo `action` do formulário esteja configurado para enviar os dados para uma rota específica do Flask:
     ```html
     <form action="/submit" method="post">
     ```

4. **Configurar o Banco de Dados SQLite**:
   - No arquivo `app.py`, importe os módulos necessários e configure o banco de dados:
     ```python
     from flask import Flask, render_template, request, redirect
     import sqlite3

     app = Flask(__name__)

     def init_sqlite_db():
         conn = sqlite3.connect('clientes.db')
         print("Banco de dados aberto com sucesso")

         conn.execute('''
             CREATE TABLE IF NOT EXISTS clientes (
                 id INTEGER PRIMARY KEY AUTOINCREMENT,
                 nome TEXT NOT NULL,
                 email TEXT NOT NULL,
                 telefone TEXT NOT NULL,
                 genero TEXT NOT NULL,
                 data_nascimento TEXT NOT NULL,
                 cidade TEXT NOT NULL,
                 estado TEXT NOT NULL,
                 endereco TEXT NOT NULL
             );
         ''')
         print("Tabela criada com sucesso")
         conn.close()

     init_sqlite_db()
     ```

5. **Desenvolver as Rotas do Flask**:
   - Crie a rota para exibir o formulário e a rota para processar os dados enviados:
     ```python
     @app.route('/')
     def index():
         return render_template('formulario.html')

     @app.route('/submit', methods=['POST'])
     def submit():
         if request.method == 'POST':
             try:
                 nome = request.form['nome']
                 email = request.form['email']
                 telefone = request.form['telefone']
                 genero = request.form['genero']
                 data_nascimento = request.form['data_nascimento']
                 cidade = request.form['cidade']
                 estado = request.form['estado']
                 endereco = request.form['endereco']

                 with sqlite3.connect('clientes.db') as conn:
                     cursor = conn.cursor()
                     cursor.execute('''
                         INSERT INTO clientes (
                             nome, email, telefone, genero, data_nascimento, cidade, estado, endereco
                         ) VALUES (?, ?, ?, ?, ?, ?, ?, ?)
                     ''', (nome, email, telefone, genero, data_nascimento, cidade, estado, endereco))
                     conn.commit()
                     msg = "Registro inserido com sucesso"
             except Exception as e:
                 conn.rollback()
                 msg = "Erro ao inserir registro: " + str(e)
             finally:
                 conn.close()
                 return render_template('resultado.html', msg=msg)
     ```

6. **Criar o Template para Exibir o Resultado**:
   - No diretório `templates/`, crie um arquivo `resultado.html` para exibir mensagens de sucesso ou erro:
     ```html
     <!DOCTYPE html>
     <html lang="pt-br">
     <head>
         <meta charset="UTF-8">
         <meta name="viewport" content="width=device-width, initial-scale=1.0">
         <title>Resultado</title>
     </head>
     <body>
         <h1>{{ msg }}</h1>
         <a href="/">Voltar ao formulário</a>
     </body>
     </html>
     ```

7. **Executar a Aplicação Flask**:
   - Adicione o bloco de código para executar a aplicação:
     ```python
     if __name__ == '__main__':
         app.run(debug=True)
     ```

8. **Testar a Aplicação**:
   - Execute o arquivo `app.py` e acesse `http://127.0.0.1:5000/` no navegador para visualizar o formulário.
   - Preencha os dados e envie o formulário para verificar se as informações são armazenadas corretamente no banco de dados SQLite.
