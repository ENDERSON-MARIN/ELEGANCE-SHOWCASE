# 💅 Elegance Beauty — Plataforma Unificada de Beleza, E-Commerce & Educação

> **📌 Showcase / Case Study Técnico (White-Label SaaS)**  
> Este repositório é uma demonstração de arquitetura e estudo de caso do **Elegance Beauty**, um ecossistema completo para o mercado de beleza e estética que integra agendamento de serviços em tempo real, e-commerce de produtos físicos e uma plataforma de cursos online. Este documento detalha a arquitetura de software, padrões de projeto e decisões de engenharia adotados no sistema.

---

<p align="center">
  <img src="https://img.shields.io/badge/Next.js_15.4-000000?style=for-the-badge&logo=nextdotjs&logoColor=white" />
  <img src="https://img.shields.io/badge/React_19.1-61DAFB?style=for-the-badge&logo=react&logoColor=black" />
  <img src="https://img.shields.io/badge/Node.js_22+-339933?style=for-the-badge&logo=node.js&logoColor=white" />
  <img src="https://img.shields.io/badge/Express_5.1-000000?style=for-the-badge&logo=express&logoColor=white" />
  <img src="https://img.shields.io/badge/PostgreSQL_17-4169E1?style=for-the-badge&logo=postgresql&logoColor=white" />
  <img src="https://img.shields.io/badge/Redis_7-DC382D?style=for-the-badge&logo=redis&logoColor=white" />
  <img src="https://img.shields.io/badge/Prisma_6.17-2D3748?style=for-the-badge&logo=prisma&logoColor=white" />
  <img src="https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white" />
</p>

---

## 🎯 Sobre o Projeto & Impacto de Negócio

O **Elegance Beauty** foi projetado para unificar a jornada completa do cliente no mercado da beleza em uma única plataforma web de alta performance:

### 💡 Três Pilares Integrados:
* **📅 Agendamento de Serviços:** Gestão dinâmica de horários, regras de disponibilidade por profissional, cancelamentos e notificações transacionais em tempo real via WebSockets.
* **🛍️ E-Commerce com Checkout:** Catálogo de produtos físicos com gestão de estoque, variações (tamanho/cor), cálculo de frete e múltiplos endereços de entrega.
* **🎓 Educação & Cursos Online:** Plataforma EAD com distribuição de videoaulas, controle de progresso por módulo, matrículas e liberação de acessos após confirmação de pagamento.

---

## 📸 Demonstração Visual (UI / UX)

| Agendamento & Calendário Profissional | Loja Virtual & Checkout | Plataforma de Cursos |
| :---: | :---: | :---: |
| ![Agendamento](assets/Elegance_Scheduling.png) | ![E-commerce](assets/Elegance_Store.png) | ![EAD](assets/Elegance_Courses.png) |

---

## 🏗️ Arquitetura de Software & Diagrama do Sistema

O backend foi construído sob os princípios da **Arquitetura Hexagonal Modular (Ports & Adapters)** e **SOLID**, permitindo desacoplamento total das regras de negócio em relação aos gateways de pagamento (MercadoPago) e provedores de storage de mídia (Local / Cloudflare R2 / AWS S3).

```mermaid
flowchart TD
    subgraph Client ["Frontend App (Next.js 15 + React 19)"]
        UI[UI Components - shadcn/ui + Radix]
        TQ[TanStack Query v5 - State & Cache]
        MP_SDK[MercadoPago Checkout Bricks]
        WS_C[Socket.IO Client]
    end

    subgraph Edge ["Routing Layer"]
        CF[Cloudflare Tunnel / Dokploy]
    end

    subgraph Backend ["Backend Service (Node.js 22 + Express 5)"]
        subgraph Hexagonal ["Arquitetura Hexagonal Modular"]
            HTTP_A[HTTP Controllers / Better Auth]
            WS_A[WebSocket Handlers]
            UC[Use Cases / Core Modules]
            PAY_STRATEGY[Payment Strategy Interface]
            STORAGE_FACTORY[MediaStorageFactory]
        end
        WORKER[BullMQ Video Worker - FFmpeg]
    end

    subgraph External ["External Services & Gateways"]
        MP_GATEWAY[MercadoPago API - PIX & Cartão]
        BREVO[Brevo Email SDK - Double Opt-in]
    end

    subgraph Persistence ["Persistence & Storage Layer"]
        PG[(PostgreSQL 17 + Prisma 6)]
        REDIS[(Redis 7 - Queue & Cache)]
        R2[Cloudflare R2 / AWS S3 Storage]
    end

    UI --> TQ & WS_C & MP_SDK
    TQ -->|REST API| CF
    WS_C -->|WebSockets| CF
    CF --> HTTP_A & WS_A
    HTTP_A & WS_A --> UC
    UC --> PAY_STRATEGY & STORAGE_FACTORY
    PAY_STRATEGY -->|Webhooks & Checkout| MP_GATEWAY
    UC -->|E-mails Transacionais| BREVO
    UC -->|Enfileirar Vídeo| REDIS
    REDIS --> WORKER
    UC -->|Prisma ORM| PG
    STORAGE_FACTORY & WORKER -->|AWS SDK S3| R2
```
## 🛠️ Tech Stack Completa

### Frontend Ecosystem
- **Core:** Next.js 15.4 (App Router), React 19.1, TypeScript 5.8
- **Styling & UI:** Tailwind CSS v4, shadcn/ui, Radix UI
- **State Management & Fetching:** TanStack Query v5 (React Query)
- **Forms & Validation:** React Hook Form v7, Zod v4
- **Auth & Session:** Better Auth 1.4 (Google OAuth 2.0 + E-mail/Senha)
- **Payments UI:** MercadoPago Checkout Bricks (`@mercadopago/sdk-react`)
- **Calendar & Charts:** `react-big-calendar`, `recharts`
- **Testing:** Vitest

### Backend & Infrastructure
- **Runtime & Framework:** Node.js 22+, Express 5.1 (Arquitetura Hexagonal Modular)
- **Database & ORM:** PostgreSQL 17, Prisma ORM 6.17
- **Authentication & RBAC:** Better Auth 1.4 (Sessões seguras, Roles: ADMIN, CLIENT, Professional Flags)
- **Background Jobs & Queues:** BullMQ v6 + Redis 7 (processamento assíncrono de vídeo)
- **Payments Engine:** MercadoPago SDK Node (`@mercadopago/sdk-node`) via Strategy Pattern
- **Transactional Email:** Brevo SDK v5 (`@getbrevo/brevo`) com templates HTML e Double Opt-in
- **Cloud Storage:** MediaStorageFactory (Suporte agnóstico para Local, Cloudflare R2 e AWS S3)
- **Containerization & Deployment:** Docker Compose, Cloudflare Tunnel (Dokploy)
- **Testing:** Vitest, Supertest, `fast-check` (Property-Based Testing)

---

## ⚡ Destaques da Engenharia & Decisões Técnicas

1. **Pipeline de Processamento Assíncrono de Vídeo (BullMQ + Redis + FFmpeg)**  
   Uploads de vídeo para cursos e produtos respondem imediatamente (`HTTP 201 Processing`). O arquivo RAW é enfileirado no Redis via BullMQ e processado por workers usando FFmpeg (CRF 26/28 H.264), emitindo notificações via Socket.IO ao término.

2. **Padrão Strategy para Gateways de Pagamento**  
   A camada de pagamentos foi construída sob uma interface agnóstica (`PaymentProviderStrategy`). A implementação atual integra o MercadoPago (PIX, Cartão e Webhooks), permitindo a adição de novos provedores (Stripe, Pagar.me) sem alterar as regras de negócio de pedidos.

3. **MediaStorageFactory (Arquitetura Agnóstica de Storage)**  
   Implementação do padrão Factory para gerenciamento de arquivos. Em ambiente de desenvolvimento, opera com storage em disco local; em produção, comuta transparentemente para Cloudflare R2 ou AWS S3 via API S3 com custo zero de *egress fees*.

4. **Testes de Integração e Property-Based Testing**  
   Suíte de testes de integração rodando em container isolado de banco de dados (`postgres_test`) combinada com testes baseados em propriedades usando `fast-check` para validação matemática e lógica de invariantes de domínio.

---

## 📂 Estrutura Modular do Projeto

```text
elegance-beauty/
├── app/
│   ├── backend/                        # API Node.js + Express 5 (Hexagonal Modular)
│   │   ├── src/
│   │   │   ├── app/
│   │   │   │   ├── modules/            # Módulos de Negócio Isolados
│   │   │   │   │   ├── analytics/      # Métricas e relatórios financeiros
│   │   │   │   │   ├── ecommerce/      # Produtos, estoque e variações
│   │   │   │   │   ├── education/      # Cursos, módulos e progresso EAD
│   │   │   │   │   ├── orders/         # Pedidos (E-commerce + Cursos)
│   │   │   │   │   ├── payments/       # Gateway de Pagamento (Strategy Pattern)
│   │   │   │   │   └── scheduling/     # Agendamentos e disponibilidade
│   │   │   ├── infrastructure/
│   │   │   │   ├── external-services/  # MediaStorageFactory, VideoProcessingWorker
│   │   │   │   ├── http/               # Controllers, Middlewares, Routes
│   │   │   │   └── database/           # Cliente Prisma & Migrações
│   │   └── prisma/                     # Schemas PostgreSQL
│   ├── frontend/                       # Next.js 15 App Router + React 19
│   │   └── src/
│   │       ├── app/
│   │       │   ├── (auth)/             # Login, Registro, OAuth
│   │       │   ├── (dashboard)/        # Painel Administrativo & Profissional
│   │       │   └── (public)/           # Agendamento, Loja, Cursos & Checkout
│   │       ├── _components/            # Componentes UI (shadcn/ui)
│   │       └── _contexts/              # Carrinho e Estado Global
│   └── docker-compose.yml              # PostgreSQL (Dev/Test) + Redis 7
```
## 🧪 Suíte de Testes

O projeto conta com testes unitários e de integração focados na camada de domínio e casos de uso:

```bash
# Execução dos testes via Vitest
pnpm vitest run --reporter=verbose "feed"
pnpm vitest run --reporter=verbose "message"
pnpm vitest run --reporter=verbose "group"
```
## 👨‍💻 Autor & Engenharia de Software

**Enderson Millan** — *Software Engineer / Full Stack Developer*

- 🌐 **Website / Portfólio:** [endersonmillan.com](https://www.endersonmillan.com/)
- 💼 **LinkedIn:** [linkedin.com/in/enderson-millan](https://www.linkedin.com/in/enderson-millan)
- ✉️ **E-mail:** [millanendersondev@gmail.com](mailto:millanendersondev@gmail.com)
- 📺 **YouTube:** [@millanendersondev](https://www.youtube.com/@millanendersondev)
