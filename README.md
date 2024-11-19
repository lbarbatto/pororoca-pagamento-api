# pororoca-pagamento-api

### Diagrama de Classe

```mermaid

classDiagram
    %% Classes principais
    class Usuario {
        <<abstract>>
        - Long id
        - String nome
        - String email
        - String phone
        - String senha
        + criarConta()
        + editarConta()
        + excluirConta()
    }
    
    class Comprador {
        - Carrinho carrinho
        + adicionarAoCarrinho(Produto produto)
        + removerDoCarrinho(Produto produto)
        + efetuarPagamento(Pagamento pagamento)
        + gerarQRCodeDinamico(): String
    }

    class Lojista {
        - List~Produto~ produtos
        - List~Vendedor~ vendedores
        - boolean conviteAceito
        + criarLoja(PaginaLoja loja)
        + editarLoja(PaginaLoja loja)
        + excluirLoja()
        + convidarVendedor(Vendedor vendedor)
        + gerarRelatorioVendas(): Relatorio
        + solicitarTransferencia(Valor valor)
    }

    class Vendedor {
        - PaginaLoja loja
        + validarEntrega(QRCode qrcode): boolean
        + consultarSaldo(): double
    }

    class Gerente {
        - List~PaginaEvento~ eventos
        + criarEvento(PaginaEvento evento)
        + editarEvento(PaginaEvento evento)
        + excluirEvento(PaginaEvento evento)
        + convidarLojista(Lojista lojista)
        + bloquearLojista(Lojista lojista)
        + autorizarTransferencia(Valor valor)
        + gerarRelatorios(): List~Relatorio~
    }

    class Administrador {
        + acessarLogsSistema()
        + visualizarLogsSeguranca()
        + gerarRelatoriosDetalhados(): Relatorio
    }

    %% Classes auxiliares
    class PaginaLoja {
        - String nome
        - String descricao
        - Lojista proprietario
        - List~Produto~ produtos
        + editarPagina(String nome, String descricao)
    }

    class Produto {
        - String nome
        - double preco
        - String descricao
    }

    class Carrinho {
        - List~Produto~ produtos
        + adicionarProduto(Produto produto)
        + removerProduto(Produto produto)
        + calcularTotal(): double
    }

    class PaginaEvento {
        - String nome
        - String descricao
        - Gerente responsavel
        - List~PaginaLoja~ lojas
        + listarLojas()
    }

    class Pagamento {
        - double valor
        - String metodoPagamento
        + processar(): boolean
    }

    class QRCode {
        - String codigo
        + validarCodigo(): boolean
    }

    class Relatorio {
        - String tipo
        - Date dataGeracao
        + gerarRelatorio()
    }

    %% Relações entre as classes
    Usuario <|-- Comprador
    Usuario <|-- Lojista
    Usuario <|-- Vendedor
    Usuario <|-- Gerente
    Usuario <|-- Administrador

    Lojista "1" o-- "1" PaginaLoja
    Lojista "1" o-- "*" Produto
    Lojista "1" o-- "*" Vendedor

    Gerente "1" o-- "*" PaginaEvento
    PaginaEvento "1" o-- "*" PaginaLoja

    Comprador "1" o-- "1" Carrinho
    Carrinho "1" o-- "*" Produto
    Comprador "1" --> "1" QRCode

    Pagamento "1" --> "1" Comprador
    Relatorio "*" --> "1" Usuario
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

