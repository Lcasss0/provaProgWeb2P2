API Products Full
API REST em TypeScript, Express, Sequelize e PostgreSQL baseada no diagrama
fornecido. Inclui produtos, categorias, situacoes, usuarios, autenticacao JWT,
confirmacao de e-mail, recuperacao de senha e autorizacao por perfil.
Requisitos
Node.js 20 ou superior
PostgreSQL 14 ou superior, ou Docker
Executar
Copie .env.example para .env e altere os segredos.
Inicie o PostgreSQL:
docker compose up -d
Instale, migre, alimente e inicie:
npm install
npm run db:migrate
npm run db:seed
npm run dev
A API estara em http://localhost:3000. Teste com GET /health.
O seed cria admin@example.com com a senha Admin@123456, salvo se
ADMIN_EMAIL e ADMIN_PASSWORD forem alterados no .env.
E-mail
Configure SMTP_HOST, SMTP_PORT, SMTP_USER, SMTP_PASS e MAIL_FROM para
envio real. Sem SMTP, as mensagens sao impressas no terminal em formato JSON,
permitindo copiar o token durante o desenvolvimento.
Fluxo de autenticacao
POST /auth/register cria uma conta pendente e envia a confirmacao.
GET /auth/verify-email?token=... confirma o e-mail e ativa a conta.
POST /auth/login devolve um token JWT.
Envie Authorization: Bearer TOKEN nas rotas protegidas.
POST /auth/forgot-password envia um token descartavel de 30 minutos.
POST /auth/reset-password altera a senha usando esse token.
Tokens de e-mail e recuperacao nunca sao armazenados em texto puro. A API salva
somente o hash SHA-256 e invalida o token depois do uso.
Rotas principais
Metodo	Rota	Acesso
POST	/auth/register	Publico
GET	/auth/verify-email?token=	Publico
POST	/auth/resend-verification	Publico
POST	/auth/login	Publico
POST	/auth/forgot-password	Publico
POST	/auth/reset-password	Publico
PATCH	/auth/change-password	Autenticado
GET/PATCH	/me	Autenticado
GET	/products e /products/:id	Autenticado
POST/PUT/DELETE	/products	ADMIN
CRUD	/product-categories	Leitura publica, escrita ADMIN
CRUD	/product-situations	Leitura publica, escrita ADMIN
CRUD	/situations	ADMIN
GET/PATCH/DELETE	/users	ADMIN

Listagens aceitam page e limit. Produtos tambem aceitam search,
categoryId e situationId. Usuarios aceitam search.
Exemplos
Cadastro:
POST /auth/register
Content-Type: application/json

{
  "name": "Maria Silva",
  "email": "maria@example.com",
  "password": "Senha@123"
}
Criacao de produto por administrador:
POST /products
Authorization: Bearer TOKEN
Content-Type: application/json

{
  "name": "Produto exemplo",
  "description": "Descricao completa",
  "price": 99.90,
  "productCategoryId": 1,
  "productSituationId": 1
}
Seguranca
Senhas com bcrypt e custo 12.
JWT com expiracao configuravel.
Tokens aleatorios, com hash e prazo de validade.
Rate limit nas rotas de autenticacao.
Helmet, CORS, limite de JSON e mensagens sem vazamento de dados.
Respostas de recuperacao neutras contra enumeracao de usuarios.
Senhas e tokens excluidos das consultas normais.
Verificacao
npm run build
npm test
Em producao, use um JWT_SECRET aleatorio com ao menos 32 caracteres, configure
SMTP real, HTTPS e uma origem especifica em FRONTEND_URL.
