# Noctua — Backend

**API REST para gestão e acompanhamento acadêmico**, desenvolvida com Java e Spring Boot como parte do Trabalho de Conclusão de Curso (TCC) em Análise e Desenvolvimento de Sistemas.

O **Noctua** é uma plataforma web que centraliza informações sobre turmas, alunos, avaliações, notas e frequência, facilitando a rotina dos professores e oferecendo recursos para acompanhar o desempenho estudantil.

O backend é responsável pelas regras de negócio, persistência de dados, autenticação, processamento de informações acadêmicas e integração com inteligência artificial.

Entre seus principais diferenciais estão a **classificação automatizada do desempenho acadêmico**, a **importação de alunos com IA generativa** e os mecanismos de segurança baseados em JWT e autenticação em dois fatores (2FA).

> Este repositório contém a API do Noctua. A interface React e a infraestrutura Docker estão disponíveis em repositórios independentes, vinculados ao final desta documentação.

## Funcionalidades

### Gestão acadêmica

- **Turmas:** cadastro, consulta, atualização e exclusão, com filtros de pesquisa.
- **Alunos:** cadastro, edição, listagem paginada e gerenciamento de matrículas.
- **Avaliações:** criação e gerenciamento de avaliações vinculadas às turmas.
- **Notas:** lançamento, atualização e cálculo de médias ponderadas.
- **Frequência:** registro de faltas e cálculo da frequência por período acadêmico.
- **Desempenho acadêmico:** classificação de indicadores de notas e frequência.
- **Dashboard:** disponibilização de métricas e avisos acadêmicos para os professores.
- **Boletins:** geração e exportação de relatórios acadêmicos em PDF e Excel.

### Inteligência artificial

O Noctua possui integração com a **API Gemini do Google**, utilizando o modelo Gemini 2.5 Flash para auxiliar na importação de listas de alunos.

O fluxo permite:

1. Receber um arquivo contendo uma lista de estudantes, como uma imagem ou PDF.
2. Enviar o conteúdo à API de inteligência artificial.
3. Solicitar a extração dos nomes encontrados no documento.
4. Processar a resposta estruturada em JSON.
5. Retornar os nomes para conferência e utilização na interface do sistema.

A integração também registra informações de consumo de tokens, utilizadas no monitoramento administrativo.

O recurso foi concebido para reduzir o trabalho manual de digitação e facilitar a inclusão de estudantes nas turmas.

### Autenticação e segurança

- Autenticação de usuários utilizando JWT.
- Controle de acesso por meio do Spring Security.
- Perfis de usuário **ADMIN** e **PROFESSOR**.
- Autenticação em dois fatores (2FA) baseada em TOTP.
- Integração com aplicativos autenticadores, como Google Authenticator.
- Recuperação e redefinição de senha por e-mail.
- Criptografia de senhas utilizando BCrypt.
- Validação de dados de entrada.
- Rotas autenticadas e gerenciamento de permissões.

### Administração

O sistema disponibiliza recursos administrativos para:

- Cadastro e gerenciamento de administradores.
- Consulta de indicadores de monitoramento.
- Acompanhamento de professores cadastrados.
- Consulta de registros de utilização da inteligência artificial.
- Acompanhamento de informações operacionais da plataforma.

## Tecnologias utilizadas

| Categoria | Tecnologias |
| --- | --- |
| Linguagem | Java 21 |
| Framework | Spring Boot 3.5.7 |
| API | Spring Web, REST |
| Segurança | Spring Security, JWT, BCrypt |
| Autenticação 2FA | GoogleAuth (TOTP) |
| Persistência | Spring Data JPA, Hibernate |
| Banco de dados | MySQL |
| Inteligência artificial | Google Gemini 2.5 Flash |
| E-mail | Spring Boot Mail, SMTP |
| Exportação | Apache POI, Apache PDFBox |
| Testes | JUnit 5, Mockito, Spring Boot Test, H2 |
| Cobertura de testes | JaCoCo |
| Build | Maven |
| Infraestrutura | Docker, Docker Compose |

## Arquitetura do backend

O backend foi organizado em camadas, com separação de responsabilidades para facilitar a manutenção, evolução e testes da aplicação.

### Controllers

Responsáveis por receber as requisições HTTP, validar os dados de entrada conforme as regras implementadas e encaminhar as operações aos serviços correspondentes.

### Services

Concentram as regras de negócio, como cálculos de médias, frequência, autenticação, geração de documentos e integração com inteligência artificial.

### Repositories

Responsáveis pela persistência e consulta de dados, utilizando Spring Data JPA.

### Entities

Representam os objetos de domínio e seu mapeamento para o banco de dados.

### DTOs

Estruturam os dados recebidos e retornados pela API, separando os modelos de comunicação das entidades persistidas.

### Config

Reúne componentes relacionados à configuração da aplicação, incluindo Spring Security, autenticação JWT e inicialização de dados.

## Estrutura do projeto

```text
src/
├── main/
│   ├── java/com/noctua/backend/
│   │   ├── config/        # Segurança, JWT e configurações
│   │   ├── controller/    # Endpoints REST
│   │   ├── dto/           # Objetos de transferência
│   │   ├── entity/        # Entidades JPA
│   │   ├── enums/         # Enumerações de domínio
│   │   ├── repository/    # Acesso ao banco de dados
│   │   └── service/       # Regras de negócio e integrações
│   └── resources/
│       └── application.properties
└── test/
    ├── java/             # Testes automatizados
    └── resources/         # Configurações de teste
```

## Regras de negócio acadêmicas

### Cálculo de frequência

O sistema calcula o percentual de frequência com base no número de aulas previstas e nos períodos de ausência registrados para cada estudante.

A fórmula utilizada é:

**Frequência (%) = [(Aulas previstas − Períodos faltados) / Aulas previstas] × 100**

A classificação de frequência implementada considera os seguintes intervalos:

| Frequência | Classificação |
| --- | --- |
| 90% ou mais | Alta |
| 80% a menos de 90% | Regular |
| 75% a menos de 80% | Atenção |
| Abaixo de 75% | Crítica |

O registro de faltas contempla de 1 a 6 períodos de ausência por ocorrência, com validações relacionadas ao período acadêmico e ao ano letivo.

A exclusão de registros de frequência utiliza inativação lógica, preservando os dados no banco.

### Avaliações e médias

A API oferece operações para cadastro de avaliações, lançamento e atualização de notas e cálculo de médias ponderadas.

Os dados acadêmicos são processados conforme as informações das avaliações e as configurações das turmas.

Esses resultados são utilizados pelo frontend para apresentar indicadores de desempenho e apoiar a identificação de necessidades de acompanhamento pedagógico.

## Principais endpoints

A API disponibiliza endpoints REST organizados por domínio.

### Autenticação e usuários

| Método | Endpoint | Descrição |
| --- | --- | --- |
| POST | `/auth/register` | Cadastro de usuário |
| POST | `/auth/login` | Autenticação |
| POST | `/auth/2fa/verify-login` | Validação do 2FA durante o login |
| POST | `/auth/forgot-password` | Solicitação de recuperação de senha |
| POST | `/auth/reset-password` | Redefinição de senha |
| GET | `/auth/me` | Consulta do usuário autenticado |
| PUT | `/auth/me` | Atualização dos dados do usuário |
| DELETE | `/auth/me` | Exclusão da conta |

### Autenticação em dois fatores

| Método | Endpoint | Descrição |
| --- | --- | --- |
| POST | `/2fa/setup` | Inicia a configuração do 2FA |
| POST | `/2fa/verify-setup` | Confirma a configuração |
| DELETE | `/2fa/disable` | Desativa o 2FA |

### Turmas e alunos

| Método | Endpoint | Descrição |
| --- | --- | --- |
| GET | `/turmas` | Lista turmas |
| POST | `/turmas` | Cria uma turma |
| GET | `/turmas/{id}` | Consulta uma turma |
| PUT | `/turmas/{id}` | Atualiza uma turma |
| DELETE | `/turmas/{id}` | Exclui uma turma |
| GET | `/turmas/{turmaId}/alunos` | Lista alunos da turma |
| POST | `/turmas/{turmaId}/alunos` | Cadastra um aluno |
| POST | `/turmas/{turmaId}/alunos/importar` | Extrai nomes de alunos com IA |

### Avaliações e frequência

| Método | Endpoint | Descrição |
| --- | --- | --- |
| GET | `/turmas/{turmaId}/avaliacoes` | Lista avaliações |
| POST | `/turmas/{turmaId}/avaliacoes` | Cadastra avaliação |
| GET | `/turmas/{turmaId}/avaliacoes/media-ponderada` | Consulta média ponderada |
| POST | `/frequencias` | Registra ausência |
| GET | `/frequencias/aluno/{alunoId}` | Consulta faltas |
| GET | `/frequencias/aluno/{alunoId}/periodo/{periodo}/percentual` | Calcula frequência |
| GET | `/frequencias/aluno/{alunoId}/periodo/{periodo}/classificacao` | Classifica frequência |

### Boletins e administração

| Método | Endpoint | Descrição |
| --- | --- | --- |
| GET | `/turmas/{turmaId}/boletim/export/anual` | Exportação anual em Excel |
| GET | `/turmas/{turmaId}/boletim/export/anual/pdf` | Exportação anual em PDF |
| GET | `/turmas/{turmaId}/boletim/export/periodo/{periodo}` | Exportação por período em Excel |
| GET | `/turmas/{turmaId}/boletim/export/periodo/{periodo}/pdf` | Exportação por período em PDF |
| GET | `/admins/monitoramento` | Monitoramento administrativo |
| GET | `/admins/logs` | Consulta de registros administrativos |
| GET | `/professores/metricas` | Métricas do dashboard do professor |

A maior parte dos endpoints exige autenticação. Alguns também dependem de parâmetros, filtros, corpo da requisição e regras de autorização aplicadas pela aplicação.

## Como executar localmente

### Pré-requisitos

- Java 21
- MySQL 8
- Git
- Maven, ou o Maven Wrapper incluído no projeto

Também é possível executar a aplicação utilizando a infraestrutura Docker disponível no repositório `noctua-environment`.

### 1. Clonar o repositório

```bash
git clone https://github.com/isabelaspaz/noctua-backend.git
cd noctua-backend
```

### 2. Configurar as variáveis de ambiente

A aplicação utiliza variáveis de ambiente para conectar-se ao banco de dados e configurar seus serviços.

Principais variáveis:

| Variável | Finalidade |
| --- | --- |
| `MYSQL_URL` | URL JDBC de conexão ao MySQL |
| `MYSQL_USER` | Usuário do banco de dados |
| `MYSQL_PASSWORD` | Senha do banco de dados |
| `JWT_SECRET` | Chave de assinatura JWT |
| `GOOGLE_API_KEY` | Chave da API Gemini |
| `SPRING_MAIL_HOST` | Servidor SMTP |
| `SPRING_MAIL_PORT` | Porta SMTP |
| `SPRING_MAIL_USERNAME` | Usuário SMTP |
| `SPRING_MAIL_PASSWORD` | Senha SMTP |
| `FRONTEND_URL` | Endereço do frontend |

Exemplo ilustrativo de configuração:

```dotenv
MYSQL_URL=jdbc:mysql://localhost:3306/noctua?serverTimezone=UTC
MYSQL_USER=noctua
MYSQL_PASSWORD=your_database_password
JWT_SECRET=your_secure_random_jwt_secret
GOOGLE_API_KEY=your_google_api_key
```

**Importante:** os valores acima são placeholders. O backend exige credenciais válidas e uma chave JWT segura para funcionar corretamente.

Não armazene credenciais reais no código-fonte ou em arquivos versionados.

### 3. Executar a aplicação

Com as variáveis de ambiente configuradas e o MySQL disponível, execute:

**Windows:**

```bash
./mvnw.cmd spring-boot:run
```

**Linux/macOS:**

```bash
./mvnw spring-boot:run
```

Por padrão, a API utiliza a porta `8080`, salvo configuração específica do ambiente.

### 4. Executar os testes

**Windows:**

```bash
./mvnw.cmd test
```

**Linux/macOS:**

```bash
./mvnw test
```

O projeto utiliza um banco H2 em memória para testes que dependem de persistência.

O relatório de cobertura do JaCoCo pode ser gerado durante a execução da fase de testes, conforme a configuração do Maven.

## Testes automatizados

O backend possui testes voltados aos principais componentes da aplicação, incluindo:

- Autenticação e validação de tokens JWT.
- Configuração de segurança.
- Autenticação em dois fatores.
- Cadastro e gerenciamento de alunos, turmas e professores.
- Criação e processamento de avaliações.
- Registro e cálculo de frequência.
- Geração de boletins.
- Integração com a API Gemini.
- Recuperação de senha e envio de e-mails.
- Serviços e controllers de administração.

São utilizadas ferramentas como **JUnit 5, Mockito, Spring Boot Test e H2**, com integração ao JaCoCo para geração de relatórios de cobertura.

A existência dos testes não representa, por si só, garantia de cobertura integral ou de ausência de falhas.

## Infraestrutura e Docker

O projeto inclui um `Dockerfile` com múltiplos estágios para preparação das dependências, compilação da aplicação e execução em uma imagem Java de runtime.

A infraestrutura integrada do Noctua é mantida no repositório `noctua-environment`, que contém as configurações de Docker Compose para execução conjunta dos serviços.

Durante o desenvolvimento acadêmico, também foi utilizada infraestrutura AWS com EC2, Aurora MySQL e automação com GitHub Actions.

**A infraestrutura AWS original foi desativada.**

## Segurança e boas práticas

O backend implementa mecanismos de autenticação, criptografia de senhas e validações de dados. Entretanto, por se tratar de um projeto acadêmico, qualquer implantação pública deve passar por revisão de segurança.

Recomendações para execução e implantação:

- Utilizar senhas fortes e credenciais exclusivas por ambiente.
- Armazenar segredos fora do repositório.
- Restringir o acesso ao banco de dados.
- Configurar corretamente CORS, HTTPS e políticas de autorização.
- Revisar os endpoints públicos e as permissões de cada perfil.
- Manter as dependências atualizadas.
- Evitar a utilização de dados pessoais reais em ambientes de demonstração.
- Revisar as configurações de logs e mensagens de erro antes de uma implantação em produção.

## Repositórios relacionados

- [Noctua Frontend](https://github.com/isabelaspaz/noctua-frontend) — Interface desenvolvida com React e Vite.
- [Noctua Backend](https://github.com/isabelaspaz/noctua-backend) — API e regras de negócio (este repositório).
- [Noctua Environment](https://github.com/isabelaspaz/noctua-environment) — Infraestrutura e configuração dos serviços.

## Sobre o projeto

O Noctua foi desenvolvido **em equipe** como Trabalho de Conclusão de Curso de Análise e Desenvolvimento de Sistemas.

O projeto integrou conhecimentos de engenharia de software, desenvolvimento de APIs, modelagem e persistência de dados, autenticação, testes automatizados, integração com inteligência artificial e infraestrutura.

Seu desenvolvimento teve como objetivo oferecer uma solução para organização acadêmica e acompanhamento do desempenho estudantil, combinando automação de processos e informações relevantes para a tomada de decisões pedagógicas.

As contribuições dos integrantes da equipe estão registradas no histórico de commits do projeto.

---

**Noctua — Projeto acadêmico de desenvolvimento full-stack.**
