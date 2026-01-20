# 📨 Sistema de Microserviços: Comunicação Assíncrona (User & Email)
Este projeto é uma aplicação baseada em arquitetura de microserviços desenvolvida em Java com Spring Boot, focada em demonstrar a comunicação assíncrona entre serviços através de um Message Broker (RabbitMQ).

O objetivo deste repositório é demonstrar um fluxo moderno de backend, onde a criação de um recurso (usuário) dispara eventos para outros serviços (envio de e-mail) de forma desacoplada e resiliente.

---

## 🎯 Objetivo do Projeto
Este projeto foi construído como peça de portfólio de arquitetura distribuída, com os seguintes objetivos principais:

* Implementar a comunicação entre microserviços utilizando o protocolo AMQP.
* Consolidar o uso do Spring Boot para criação de APIs REST rápidas e seguras.
* Aplicar o conceito de event-driven design (design orientado a eventos).
* Utilizar PostgreSQL para persistência de dados relacional.
* Integrar serviços de mensageria na nuvem com CloudAMQP.

**Decisões arquiteturais importantes:**

* **Desacoplamento:** O microserviço de usuários não conhece o microserviço de e-mail; ele apenas publica uma mensagem no Broker.
* **Persistência Dupla:** Ambos os microserviços possuem seus próprios bancos de dados, garantindo a autonomia de dados.
* **Resiliência:** Se o serviço de e-mail estiver fora do ar, a mensagem permanece na fila até que possa ser processada.

---

## 🛠️ Tecnologias Utilizadas
* **Linguagens:** Java (JDK 21)
* **Framework Principal:** Spring Boot (Web, Data JPA, Validation, AMQP, Mail)
* **Gerenciador de Dependências:** Maven
* **Banco de Dados:** PostgreSQL
* **Message Broker:** RabbitMQ & CloudAMQP
* **Protocolo de E-mail:** SMTP Gmail

---

## 🚀 Funcionalidades
* [x] **Criação de Usuário:** API para cadastro de usuários com validação de dados.
* [x] **Persistência de Usuário:** Gravação dos dados cadastrais no PostgreSQL via Spring Data JPA.
* [x] **Publicação de Evento:** Disparo automático de uma mensagem para a fila após o salvamento do usuário.
* [x] **Consumo de Mensagem:** Microserviço de E-mail "escuta" a fila e processa novas mensagens em tempo real.
* [x] **Envio de E-mail:** Integração com SMTP do Gmail para disparar e-mails de boas-vindas.
* [x] **Log de E-mails:** Registro de e-mails enviados salvo no banco de dados para auditoria.

---

## 📸 Demonstração

[screen-capture.webm](../../Downloads/screen-capture.webm)

---

## 🧠 Arquitetura do Projeto

**User Microservice**

* Responsável por receber a requisição POST /users.
* Salva o usuário no banco de dados.
* Atua como Producer, enviando o objeto de mensagem para o Broker.
* Broker (RabbitMQ)
* Gerencia a fila de mensagens e garante a entrega entre os serviços.

**Email Microservice**

* Atua como Consumer, escutando a fila de mensagens.
* Processa o envio do e-mail via SMTP.
* Registra o status do envio no seu banco de dados próprio.

---

## ⚙️ Como executar o projeto
Este projeto é composto por dois serviços independentes.

1. **Clone o repositório:**

```bash
git clone https://github.com/stefanopaulo/async-microservices-demo.git
```
2. **Configuração do CloudAMQP:**

* Crie uma instância no CloudAMQP ou execute localmente via Docker:

```bash
docker run -it --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3.12-management
```

3. **Banco de Dados:**

* Certifique-se de ter o PostgreSQL rodando.
* Crie dois bancos de dados: user_db e email_db.

4. **Variáveis de Ambiente:**

* Configure o application.properties de cada serviço com suas credenciais do banco, RabbitMQ e SMTP do Gmail.
### 👤 **Microserviço de Usuário (Porta 8081)**
```bash
# Configurações do Servidor
server.port=8081

# Banco de Dados (PostgreSQL)
spring.datasource.url=jdbc:postgresql://localhost:5432/user_db
spring.datasource.username=seu_usuario
spring.datasource.password=sua_senha
spring.jpa.hibernate.ddl-auto=update

# Mensageria - CloudAMQP (RabbitMQ)
# Substitua os valores abaixo pelos dados da sua instância no CloudAMQP
spring.rabbitmq.addresses=amqps://usuario_exemplo:senha_exemplo@host_exemplo.lmq.cloudamqp.com/vhost_exemplo

# Definição da Fila
broker.queue.email.name=default.email
```
### 📧 **Microserviço de E-mail (Porta 8082)**

```bash
# Configurações do Servidor
server.port=8082

# Banco de Dados (PostgreSQL)
spring.datasource.url=jdbc:postgresql://localhost:5432/email_db
spring.datasource.username=seu_usuario
spring.datasource.password=sua_senha
spring.jpa.hibernate.ddl-auto=update

# Mensageria - CloudAMQP (RabbitMQ)
spring.rabbitmq.addresses=amqps://usuario_exemplo:senha_exemplo@host_exemplo.lmq.cloudamqp.com/vhost_exemplo

# Fila que este microserviço ficará escutando
broker.queue.email.name=default.email

# Configuração de Envio de E-mail (SMTP Gmail)
spring.mail.host=smtp.gmail.com
spring.mail.port=587
spring.mail.username=seu_email_aqui@gmail.com
# Use a "Senha de App" gerada na sua conta Google
spring.mail.password=sua_senha_de_app_gerada
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true
```

5. **Execução:**

* Entre em cada pasta (user e email) e execute:

```bash
mvn spring-boot:run
```

## 👨‍💻 Autor
**Stefano Paulo**
*Desenvolvedor Java focado em sistemas distribuídos, microserviços e alta disponibilidade.*
