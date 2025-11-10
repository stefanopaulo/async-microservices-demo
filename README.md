# 🚀 Full Flow: Comunicação Assíncrona com Microservices e RabbitMQ

Este repositório demonstra a implementação de uma **arquitetura de microserviços** utilizando **comunicação assíncrona** baseada no protocolo **AMQP (RabbitMQ / LavinMQ)**.  
O projeto simula o fluxo completo de **cadastro de um usuário** seguido pelo **envio de um e-mail de notificação**.

---

## 🎯 Objetivo do Projeto

O principal objetivo é **praticar e demonstrar o desacoplamento de serviços** e o uso de um **Message Broker** para garantir a **confiabilidade na comunicação** entre microserviços, evitando falhas em cascata e garantindo resiliência.

---

## 🧱 Arquitetura e Fluxo de Comunicação

A solução é composta por **dois microserviços** que se comunicam de forma assíncrona através de um **broker RabbitMQ / LavinMQ**.

### Fluxo completo:
1. **Client (Cliente)** → envia uma requisição `POST` para cadastrar um novo usuário (`/users`);
2. **User Microservice (Produtor)**  
   - Recebe a requisição HTTP;  
   - Salva o usuário no seu banco de dados (PostgreSQL);  
   - Publica uma mensagem (com os dados do usuário) no Broker;
3. **Broker (RabbitMQ / LavinMQ)**  
   - Recebe e enfileira a mensagem;
4. **Email Microservice (Consumidor)**  
   - Escuta (`@RabbitListener`) a fila no Broker;  
   - Ao receber a mensagem, aciona a lógica de envio de e-mail (via SMTP Gmail);  
   - Salva o registro do e-mail no seu próprio banco (PostgreSQL).

---

## 🛠️ Tecnologias Utilizadas

| Categoria | Tecnologia | Descrição |
|------------|-------------|-----------|
| **Linguagem** | Java | Linguagem base dos microserviços |
| **Framework** | Spring Boot | Desenvolvimento rápido e robusto de APIs |
| **Mensageria** | RabbitMQ / CloudAMQP | Broker para comunicação assíncrona (AMQP) |
| **Banco de Dados** | PostgreSQL | Banco relacional usado por ambos os serviços |
| **Dependências Spring** | Web, Data JPA, AMQP, Mail | APIs REST, persistência, mensageria e envio de e-mail |
| **Build** | Maven | Gerenciador de dependências e build |
| **E-mail** | SMTP Gmail | Serviço real de envio de e-mails |

---

## ⚙️ Configuração Local e Credenciais

O projeto utiliza o serviço **gratuito CloudAMQP (LavinMQ)**, eliminando a necessidade de subir um container RabbitMQ local.

### 1️⃣ Configuração do Message Broker

No arquivo `application.properties` de **ambos os microserviços**, substitua os valores abaixo pelas suas credenciais do CloudAMQP:

```properties
# ------------------------------------------------------------------
# CONEXÃO COM O MESSAGE BROKER (CLOUD AMQP / LAVINMQ)
# ------------------------------------------------------------------
spring.rabbitmq.addresses=amqps://cirnbxusr:[SUA_SENHA_AQUI]@collie.lmg.cloudamqp.com:5671
spring.rabbitmq.ssl.enabled=true
