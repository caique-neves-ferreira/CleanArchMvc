# Clean Architecture com .NET 🚀

Bem-vindo ao repositório **CleanArchMvc**. Este projeto é uma Prova de Conceito (PoC) desenvolvida para demonstrar como construir aplicações .NET altamente escaláveis, testáveis e de fácil manutenção utilizando os princípios da **Clean Architecture** (Arquitetura Limpa).

## 🛑 O Problema (Por que usar essa arquitetura?)

Em sistemas tradicionais (N-Tier), é comum vermos a camada de Negócios depender fortemente da camada de Dados (Banco de dados, ORMs como Entity Framework). 

**O impacto disso em ambientes corporativos reais (Fintechs/Bancos):**
1. **Acoplamento Extremo:** Trocar o banco de dados de SQL Server para MongoDB exige reescrever a regra de negócio.
2. **Testabilidade Nula:** Como a regra de negócio está amarrada ao framework web ou ao banco, escrever testes unitários isolados se torna uma tarefa quase impossível.
3. **Complexidade Evolutiva:** Adicionar uma nova interface (ex: trocar MVC por uma API REST para um app mobile) quebra as validações de negócio.

## 💡 A Solução (Clean Architecture)

Este projeto resolve esses gargalos implementando a **Inversão de Dependência**. A camada central do sistema (`Domain`) não conhece **absolutamente nada** sobre o mundo exterior (não sabe o que é HTTP, não sabe o que é SQL, não sabe o que é JSON).

As dependências fluem de fora para dentro. A camada de `Infrastructure` e `Web` se adaptam ao `Domain`, garantindo que o coração da aplicação permaneça puro, isolado e 100% coberto por testes unitários rápidos.

## 📐 Diagrama de Arquitetura

Aqui está a representação visual das camadas e como as dependências fluem (de fora para o centro):

```mermaid
graph TD
    subgraph "External Layers (Adapters & Frameworks)"
        UI[Web UI / MVC / API]
        DB[(Database / SQL Server)]
        ExtAPI[External APIs]
    end

    subgraph "Infrastructure Layer"
        Repo[Repositories & EF Core]
        IoC[IoC Container]
    end

    subgraph "Application Layer"
        Services[Application Services / Interfaces]
        CQRS[Commands / Queries / DTOs]
    end

    subgraph "Domain Layer (Core)"
        Entities[Entities & Value Objects]
        DomainInt[Domain Interfaces]
    end

    %% Dependency flow
    UI -. "References" .-> Services
    UI -. "References" .-> IoC
    
    Repo -. "Implements" .-> DomainInt
    Repo -. "Uses" .-> DB
    
    Services -. "Uses" .-> DomainInt
    Services -. "Uses" .-> Entities
    
    %% Inversion of Control highlight
    DomainInt -. "Inversion of Control" .- Repo

    classDef core fill:#10b981,stroke:#047857,stroke-width:2px,color:#fff;
    classDef app fill:#3b82f6,stroke:#1d4ed8,stroke-width:2px,color:#fff;
    classDef infra fill:#f59e0b,stroke:#b45309,stroke-width:2px,color:#fff;
    classDef external fill:#6b7280,stroke:#374151,stroke-width:2px,color:#fff;

    class Entities,DomainInt core;
    class Services,CQRS app;
    class Repo,IoC infra;
    class UI,DB,ExtAPI external;
