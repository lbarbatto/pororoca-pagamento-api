# pororoca-pagamento-api

### Diagrama de Classe

```mermaid

classDiagram
    direction TB
    
    %% Superclass and Generalization
    User <|-- Client
    User <|-- Seller
    User <|-- Admin
    User <|-- Owner
    User <|-- Manager
    
    %% Manager and Event
    Manager "1" *-- "1" Event : manages

    %% Owner and Store
    Owner "1" *-- "1" Store : owns

    %% Store and Product
    Store "1" *-- "*" Product : contains

    %% Client, Cart, and Product
    Client "1" *-- "1" Cart : has
    Cart "1" *-- "*" Product : contains
    Cart "1" *-- "1" DiscountStrategy : applies

    %% CartService and its relationship
    CartService "1" *-- "*" Product : manages

    %% DiscountStrategy implementations
    DiscountStrategy <|-- FixedAmountDiscount
    DiscountStrategy <|-- PercentageDiscount

    %% PermissionService for Admins
    PermissionService "1" *-- "1" Admin : managesPermissions

    %% PaymentService, Cart, and Client
    PaymentService "1" *-- "*" Cart : processes
    PaymentService "1" *-- "1" Client : processesFor

    %% NotificationService for Admins
    NotificationService "1" *-- "*" Admin : notifies

    %% APIExterna and PaymentService
    APIExterna "1" --> "1" PaymentService : usedBy

    %% Classes definitions for attributes and methods
    class User {
        - Long id
        - String name
        - String email
        - String phone
        - String password
        + getId()
        + getName()
        + setEmail()
        + setPassword()
    }
    
    class Client {
        - Cart cart
        + getCart()
        + makePurchase()
    }
    
    class Seller {
        - Store store
        + getStore()
        + listProducts()
    }
    
    class Admin {
        - AdminType adminType
        + updatePermissions()
        + manageSystem()
    }
    
    class Owner {
        - Store store
        + createStore()
        + manageStore()
    }
    
    class Manager {
        - ManagerType managerType
        - List~Store~ stores
        + getStores()
        + manageEvent()
    }
    
    class Event {
        - Long id
        - String name
        - Date date
        - Location location
        + getDetails()
    }
    
    class Store {
        - Long id
        - String name
        - Location location
        - List~Product~ products
        + addProduct()
        + removeProduct()
    }
    
    class Product {
        - Long id
        - String name
        - Double price
        - int stockQuantity
        + updateStock()
        + getDetails()
    }
    
    class Cart {
        - Long id
        - List~Product~ products
        - DiscountStrategy discountStrategy
        + calculateTotal()
        + applyDiscount()
        + addProduct()
        + removeProduct()
    }
    
    class CartService {
        + addProductToCart()
        + removeProductFromCart()
        + clearCart()
    }
    
    class DiscountStrategy {
        + calculateDiscount(cartTotal: Double): Double
    }
    
    class FixedAmountDiscount {
        - double discountValue
        + calculateDiscount(cartTotal: Double): Double
    }
    
    class PercentageDiscount {
        - double percentage
        + calculateDiscount(cartTotal: Double): Double
    }
    
    class PermissionService {
        + grantPermissions()
        + revokePermissions()
        + updatePermissions()
    }
    
    class PaymentService {
        + processPayment(cart: Cart, client: Client): String
    }
    
    class NotificationService {
        + sendLoginNotification(admin: Admin)
        + sendLogoutNotification(admin: Admin)
    }
    
    class APIExterna {
        + makePayment(cart: Cart, client: Client): String
    }

```
### Fluxograma
Representa processos e fluxos de trabalho. Pode ser usado para ilustrar:

O fluxo de compra do comprador.
O processo de registro e validação de usuários.

```mermaid

flowchart TD
    A[Comprador Seleciona Produtos] --> B[Adiciona ao Carrinho]
    B --> C[Confirmação do Carrinho]
    C --> D[Efetua Pagamento]
    D --> E{Pagamento Aprovado?}
    E -->|Sim| F[QR Code Gerado]
    E -->|Não| G[Erro no Pagamento]

```

### Caso de Uso
É útil para visualizar como os diferentes usuários interagem com o sistema

```mermaid

graph TD
    Usuario -->|Login| Sistema
    Sistema -->|Visualizar Lojas| Comprador
    Comprador -->|Adicionar Produto ao Carrinho| Carrinho
    Carrinho -->|Efetuar Pagamento| Pagamento
    Lojista -->|Cadastrar Produto| Sistema
    Gerente -->|Gerar Relatórios| Sistema

```

### Diagrama de Entidade-Relacionamento (ERD)
Visualiza as entidades do banco de dados e seus relacionamentos.

```mermaid

erDiagram
    Usuario {
        Long id
        String nome
        String email
        String senha
        String tipo_usuario
    }
    Loja {
        Long id
        String nome
        String descricao
    }
    Produto {
        Long id
        String nome
        double preco
        String descricao
    }
    Transacao {
        Long id
        Date dataHora
        int quantidade
        double valorTotal
    }

    Usuario ||--o{ Loja : "cria"
    Loja ||--o{ Produto : "possui"
    Usuario ||--o{ Transacao : "realiza"
    Produto ||--o{ Transacao : "pertence"

```

### Diagrama de Sequência
Mostra a interação entre objetos no tempo, útil para processos como:

Validação de QR Code pelo vendedor.
Criação de relatórios pelo gerente.

```mermaid

sequenceDiagram
    participant Comprador
    participant Vendedor
    participant Sistema

    Comprador->>Vendedor: Apresenta QR Code
    Vendedor->>Sistema: Envia QR Code para validação
    Sistema-->>Vendedor: Retorna status de validação
    Vendedor-->>Comprador: Confirma entrega

```
### Diagrama de Componentes
Ilustra a arquitetura do sistema em termos de componentes, como microserviços, APIs, e dependências.

```mermaid

graph LR
    UI[Frontend - App Mobile]
    API[Backend - API REST]
    DB[(Banco de Dados)]
    Storage[(Armazenamento de Imagens)]
    Auth[Serviço de Autenticação]
    Logs[Monitoramento e Logs]

    UI --> API
    API --> DB
    API --> Storage
    API --> Auth
    API --> Logs

```
### Diagrama de Estado
Mostra os estados pelos quais um objeto passa, como o carrinho de compras.

```mermaid

stateDiagram
    [*] --> CarrinhoVazio
    CarrinhoVazio --> ProdutosAdicionados: Adicionar Produto
    ProdutosAdicionados --> CarrinhoVazio: Remover todos os produtos
    ProdutosAdicionados --> PedidoConfirmado: Confirmar Pedido
    PedidoConfirmado --> [*]

```
### Matriz de Responsabilidade
Rastreia atividades por papel ou ator no sistema.

```mermaid

graph TB
    subgraph Comprador
        tarefa1[Adicionar ao Carrinho]
        tarefa2[Efetuar Pagamento]
    end

    subgraph Lojista
        tarefa3[Cadastrar Produto]
        tarefa4[Gerar Relatório de Vendas]
    end

    subgraph Gerente
        tarefa5[Criar Evento]
        tarefa6[Autorizar Transferências]
    end

```
### GitFlow
Ótimo para planejamento e acompanhamento do desenvolvimento das funcionalidades e versões do sistema.

```mermaid
gitGraph
   commit id: "a1" tag: "Start"
   branch develop
   commit id: "b1" tag: "Initial develop"
   branch feature/user-service
   checkout feature/user-service
   commit id: "c1" tag: "User service created"
   checkout develop
   merge feature/user-service id: "d1" tag: "Merge: User Service"
   branch feature/payment-service
   checkout feature/payment-service
   commit id: "e1" tag: "Payment service in progress"
   checkout develop
   merge feature/payment-service id: "f1" tag: "Merge: Payment Service"
   checkout main
   merge develop id: "g1" tag: "Release v1.0" type: HIGHLIGHT
   commit id: "h1" tag: "Hotfix applied"
   checkout develop
   commit id: "i1" tag: "Ongoing feature work"

```
### Diagrama Relacional
Ótimo para compreender o fluxo das relações entre as entidades do sistema.

```mermaid

erDiagram
    CLIENT {
        Long id PK
        String name
        String email
        String phone
        String password
    }

    SELLER {
        Long id PK
        String name
        String email
        String phone
        String password
        Long store_id FK
    }

    ADMIN {
        Long id PK
        String name
        String email
        String phone
        String password
    }

    CART {
        Long id PK
        Long client_id FK
        Double total
    }

    PRODUCT {
        Long id PK
        String name
        Double price
        Integer quantity
    }

    DISCOUNT {
        Long id PK
        String type
        Double value
    }

    STORE {
        Long id PK
        String name
        Long owner_id FK
    }

    CLIENT ||--o| CART : "has"
    CART ||--o| PRODUCT : "contains"
    SELLER ||--|{ STORE : "manages"
    CART ||--|{ DISCOUNT : "applies"
    ADMIN ||--|{ CLIENT : "manages"

```

```mermaid

```

