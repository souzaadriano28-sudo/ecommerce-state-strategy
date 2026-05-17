# Sistema de Gerenciamento de Pedidos (E-commerce)

Este projeto é uma API RESTful desenvolvida em **Java com Spring Boot**, construída para gerenciar o ciclo de vida de pedidos de um e-commerce. O sistema foca fortemente na aplicação de **Padrões de Projeto (Design Patterns)** para resolver problemas complexos de transição de status e cálculos dinâmicos de fretes.

## 🛠 Tecnologias Utilizadas
* **Java 17**
* **Spring Boot 3** (Web, Data JPA)
* **H2 Database** (Banco de dados em memória)
* **Swagger / OpenAPI** (Documentação e teste de endpoints)
* **Padrões de Projeto:** State e Strategy 

---

## 🏗 Diagrama de Classes

A arquitetura do sistema foi desenhada para isolar a lógica de negócios e as regras de transição.

![Diagrama de Classes](diagrama de classes ecommerce-state-strategy.png)

---

## 🧠 Padrões de Projeto Aplicados e Justificativas

Conforme os requisitos do projeto, aplicamos dois padrões comportamentais do GoF (Gang of Four) para garantir um código limpo, testável e escalável.

### 1. Padrão State (Máquina de Estados do Pedido)
**Contexto:** O pedido possui regras rígidas de transição. Por exemplo: um pedido não pode ser cancelado se já foi enviado, e um pedido pago **jamais pode ser pago novamente** (evitando transações financeiras duplicadas).

**Por que foi utilizado:** Se utilizássemos variáveis primitivas (como `String status`) e vários blocos de `if/else` dentro da classe `Pedido`, o código ficaria frágil e propenso a falhas de segurança lógica. O **Padrão State** delega o comportamento para classes de estado independentes (`AguardandoPagamentoState`, `PagoState`, `EnviadoState`, `CanceladoState`).

**Como resolveu o problema:** Cada classe de estado conhece exatamente as transições permitidas. O `PagoState`, por exemplo, lança uma exceção imediata caso o método `pagar()` seja chamado, blindando o e-commerce contra pagamentos duplos, respeitando estritamente o enunciado.

### 2. Padrão Strategy (Cálculo de Frete)
**Contexto:** No momento da criação do pedido, o cliente precisa saber o valor do frete. Temos regras que calculam 5% (Terrestre) ou 10% (Aéreo) do valor da compra, com a exigência de suportar novas formas de envio no futuro.

**Por que foi utilizado:** O **Padrão Strategy** isola os algoritmos de cálculo de frete da entidade `Pedido`. Dessa forma, o pedido não precisa conhecer a matemática envolvida, ele apenas recebe o valor calculado.

**Como resolveu o problema:** Criamos a interface `FreteStrategy` e as implementações `FreteTerrestre` e `FreteAereo`. A principal vantagem é que isso atende ao princípio **Open/Closed (SOLID)**: se o e-commerce decidir adicionar o "Frete Marítimo" amanhã, basta criar uma nova classe que implemente a interface, sem a necessidade de alterar ou quebrar o código existente.

---

## 🚀 Como Executar e Testar

1. Clone o repositório.
2. Abra o projeto em sua IDE (IntelliJ, Eclipse) e certifique-se de baixar as dependências do Maven.
3. Execute a classe principal da aplicação Spring Boot.
4. Com a aplicação no ar, abra o navegador e acesse a interface interativa do Swagger:
   **`http://localhost:8080/swagger-ui.html`**

Pelo Swagger, você pode:
* **POST /api/pedidos:** Criar pedidos testando os fretes `TERRESTRE` ou `AEREO`.
* **PUT /api/pedidos/{id}/pagar:** Testar o pagamento (tente pagar duas vezes para ver o State bloqueando a ação!).
* **PUT /api/pedidos/{id}/enviar:** Testar o envio de um pedido.
* **PUT /api/pedidos/{id}/cancelar:** Testar as regras de cancelamento.
