# oficina-mecanica

Sistema de Gerenciamento de Oficina Mecânica — SAEP/SENAI
Aplicação web para apoiar a rotina de uma oficina mecânica, permitindo gerenciar usuários, clientes, veículos e agendamentos de serviços. O projeto foi desenvolvido em Python com Flask e utiliza MySQL para persistência dos dados.
Funcionalidades
- Autenticação de usuários: login, logout, validação de credenciais e controle de sessão.
- Sessões: duração configurada para 15 minutos.
- Dashboard: resumo da quantidade de clientes, veículos e agendamentos, além dos próximos serviços.
- Clientes: cadastro, listagem, busca, edição e exclusão.
- Validação de CPF: verifica se o documento contém 11 dígitos numéricos.
- Proteção de dados: geração de hash Bcrypt para o CPF e verificação de senhas com Bcrypt.
- Veículos: cadastro, listagem, edição e exclusão, com vínculo obrigatório a um cliente.
- Agendamentos: cadastro, listagem cronológica, atualização de data e exclusão.
- Relacionamentos no banco: agendamentos associados a clientes e veículos.
- Mensagens ao usuário: feedback de sucesso e erro nas operações.
Tecnologias utilizadas
- Python
- Flask
- MySQL
- mysql-connector-python
- Bcrypt
- HTML5, CSS3 e JavaScript para a interface (templates e arquivos estáticos)
Estrutura do projeto
Organize os arquivos aproximadamente desta forma:
oficina_mecanica/
├── app.py
├── rotas.py
├── banco.py
├── autenticacao.py
├── clientes.py
├── veiculos.py
├── agendamento.py
├── db.sql
├── requirements.txt
├── templates/
│   ├── login.html
│   ├── dashboard.html
│   ├── clientes.html
│   ├── veiculos.html
│   └── agendamentos.html
└── static/
    ├── css/
    └── js/
A estrutura de templates/ e static/ é a esperada pela aplicação Flask; os arquivos de interface devem estar presentes no projeto para que as páginas sejam renderizadas corretamente.

Pré-requisitos
Antes de executar, instale:
- Python 3.10 ou superior (recomendado).
- MySQL Server.
- pip, gerenciador de pacotes do Python.
Instalação e configuração
1. Baixe ou clone o projeto
Coloque os arquivos do projeto em uma pasta, por exemplo oficina_mecanica/, e abra o terminal nessa pasta.
2. Crie e ative um ambiente virtual
Windows (CMD/PowerShell):
python -m venv venv
venv\Scripts\activate
Linux/macOS:
python3 -m venv venv
source venv/bin/activate
3. Instale as dependências
Crie um arquivo requirements.txt com:
Flask==3.0.3
mysql-connector-python==8.4.0
bcrypt==4.1.3
Werkzeug==3.0.3
Depois execute:
pip install -r requirements.txt
4. Configure o banco de dados
1. Inicie o servidor MySQL.
2. Abra o MySQL Workbench ou outro cliente MySQL.
3. Execute o conteúdo do arquivo db.sql.
Também é possível importar pelo terminal, a partir da pasta do projeto:
mysql -u root -p < db.sql
O script SQL cria o banco saep_db, as tabelas usuarios, clientes, veiculos e agendamentos, além de inserir registros de demonstração.

Atenção: o script contém comandos DROP TABLE IF EXISTS e recria as tabelas. Executá-lo novamente pode apagar os dados existentes nessas tabelas. Faça backup antes de executá-lo em um banco com dados importantes.
5. Configure as credenciais do banco
O módulo banco.py permite configurar a conexão por variáveis de ambiente:
Variável	Padrão utilizado
DB_HOST	localhost
DB_USER	root
DB_PASSWORD	valor definido no ambiente; caso não exista, o código atual usa Senai@118
DB_NAME	saep_db
DB_PORT	3306


Ajuste as variáveis de acordo com a configuração local do MySQL. No Windows PowerShell, por exemplo:
$env:DB_HOST = "localhost"
$env:DB_USER = "root"
$env:DB_PASSWORD = "SUA_SENHA_MYSQL"
$env:DB_NAME = "saep_db"
$env:DB_PORT = "3306"
Substitua SUA_SENHA_MYSQL pela senha real do seu MySQL.
6. Configure a chave secreta do Flask
Defina uma chave própria para as sessões. No PowerShell:
$env:SECRET_KEY = "substitua-por-uma-chave-longa-e-aleatoria"
Não utilize a chave padrão em produção e não compartilhe credenciais reais no repositório.
Como executar
Com o ambiente virtual ativado, as dependências instaladas e o banco configurado, execute na pasta onde está app.py:
python app.py
O terminal deverá indicar o endereço local. Abra no navegador:

http://127.0.0.1:5000

A rota inicial direciona para o login quando não há sessão autenticada.
Usuários de demonstração
O script SQL fornece os seguintes logins e senhas de demonstração:
Perfil	Login	Senha
Administrador / Gerente	admin	admin123
Mecânico Chefe	mecanico	senha123
Recepcionista	recepcao	rec123


Use essas credenciais somente no ambiente de demonstração. Troque-as antes de qualquer implantação real.
Rotas principais
Rota	Finalidade
/	Redireciona para o dashboard ou para o login
/login	Autenticação
/logout	Encerramento da sessão
/dashboard	Resumo do sistema
/clientes	Listagem e busca de clientes
/clientes/novo	Cadastro de cliente (POST)
/clientes/editar/<id>	Edição de cliente (POST)
/clientes/excluir/<id>	Exclusão de cliente
/veiculos	Listagem de veículos
/veiculos/novo	Cadastro de veículo (POST)
/veiculos/editar/<id>	Edição de veículo (POST)
/veiculos/excluir/<id>	Exclusão de veículo
/agendamentos	Listagem de agendamentos
/agendamentos/novo	Cadastro de agendamento (POST)
/agendamentos/atualizar-data/<id>	Alteração de data (POST)
/agendamentos/excluir/<id>	Exclusão de agendamento


Modelo de dados
- usuarios: dados de acesso, cargo, estado ativo e hash da senha.
- clientes: dados cadastrais e campos relacionados ao CPF.
- veiculos: informações do veículo e chave estrangeira para o cliente proprietário.
- agendamentos: serviço, data e horário, status, valor estimado, observações e vínculos com cliente e veículo.
Os relacionamentos são garantidos por chaves estrangeiras no MySQL.
Segurança e observações
- As senhas dos usuários são verificadas usando Bcrypt.
- O código gera um hash Bcrypt para o CPF. Como Bcrypt é um hash unidirecional, ele não permite recuperar o CPF original a partir do hash.
- O código atual também mantém um campo cpf em texto formatado na tabela. Portanto, o CPF não está protegido apenas por existir o campo cpf_hash. Para um ambiente real, revise o armazenamento e a exibição desse dado, aplique controles de acesso e siga as exigências da LGPD.
- O código de conexão possui uma senha padrão de banco como fallback. Configure DB_PASSWORD e remova credenciais padrão antes de publicar o projeto.
- Defina uma SECRET_KEY forte por variável de ambiente.
- O servidor é iniciado com debug=True, adequado apenas para desenvolvimento local. Desative o modo debug em produção.
- As rotas de exclusão atualmente utilizam requisições GET. Para implantação real, prefira POST/DELETE com proteção CSRF.
- A sessão está configurada para 15 minutos; revise o comportamento de renovação conforme a política de segurança desejada.
Solução de problemas
Erro de conexão com o MySQL
- Confirme que o serviço MySQL está em execução.
- Confira host, porta, usuário, senha e nome do banco.
- Teste se o banco saep_db foi criado e se as tabelas existem.
Erro ModuleNotFoundError
- Ative o ambiente virtual.
- Execute pip install -r requirements.txt.
Erro ao renderizar uma página
- Confirme que os templates HTML necessários estão na pasta templates/.
- Verifique se os nomes dos arquivos correspondem aos usados em render_template().
Acesso redirecionado para o login
- Faça login novamente; as rotas internas exigem uma sessão autenticada.
Objetivo acadêmico
Projeto desenvolvido no contexto de estudos de desenvolvimento de sistemas, com foco em aplicações web usando Python, integração com banco de dados relacional, autenticação e operações CRUD.
Status: documentação inicial baseada nos módulos Python e no script SQL fornecidos. A execução completa depende também dos templates HTML e dos arquivos estáticos da interface.
