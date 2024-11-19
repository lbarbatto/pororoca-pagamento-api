# pororoca-pagamento-api

### Diagrama de Classe

```mermaid

classDiagram
    class Usuario {
        <<abstract>>
        + Long id
        + String nome
        + String email
        + String senha
        + LocalDateTime dataCriacao
        + void login()
        + void logout()
    }

    class Comprador {
        + void criarConta()
        + void editarConta()
        + void excluirConta()
        + Carrinho carrinho
        + void adicionarProduto(Produto produto)
        + void removerProduto(Produto produto)
        + QRCode realizarPagamento(APIExterna api, Carrinho carrinho)
    }

    class Lojista {
        + void criarConta(String convite)
        + void editarConta()
        + void excluirConta()
        + Loja loja
        + void convidarVendedor(String email)
        + void editarLoja()
        + void excluirProduto(Produto produto)
        + void verRelatorios()
        + void solicitarTransferencia()
    }

    class Vendedor {
        + void criarConta(String convite)
        + void editarConta()
        + void excluirConta()
        + Loja loja
        + void validarQRCode(QRCode qrcode)
        + double consultarSaldoEntregas()
    }

    class Gerente {
        + void criarConta()
        + void editarConta()
        + void excluirConta()
        + void criarPaginaEvento()
        + void convidarLojista(String email)
        + void bloquearLojista()
        + void verRelatoriosGerais()
        + void autorizarTransferencia()
    }

    class Administrador {
        + void acessarManutencaoSistema()
        + void verRelatoriosDetalhados()
        + void verLogsSistema()
        + void gerarRelatoriosErros()
    }

    class Carrinho {
        + List~Produto~ produtos
        + void adicionarProduto(Produto produto)
        + void removerProduto(Produto produto)
        + double calcularTotal()
    }

    class Loja {
        + String nome
        + String descricao
        + List~Produto~ produtos
        + void adicionarProduto(Produto produto)
        + void editarProduto(Produto produto)
        + void excluirProduto(Produto produto)
    }

    class Produto {
        + Long id
        + String nome
        + String descricao
        + double preco
        + int quantidadeEstoque
    }

    class APIExterna {
        <<interface>>
        + QRCode processarPagamento(Carrinho carrinho, Comprador comprador)
    }

    Usuario <|-- Comprador
    Usuario <|-- Lojista
    Usuario <|-- Vendedor
    Usuario <|-- Gerente
    Usuario <|-- Administrador
    Lojista "1" --> "1" Loja
    Vendedor "1" --> "1" Loja
    Comprador "1" --> "1" Carrinho
    Carrinho "1" --> "*" Produto
    Loja "1" --> "*" Produto
    Comprador "1" --> "1" APIExterna

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
### Diagrama de Gantt
Ótimo para planejamento e acompanhamento do desenvolvimento do sistema. Pode ser usado para representar o cronograma do projeto.

```mermaid




```

