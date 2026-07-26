<div align="center">

# ✈️ Dashboard Travel

### Monitor pessoal de passagens aéreas com automação N8N, múltiplas fontes de preço e alertas por IA

[![Live Demo](https://img.shields.io/badge/Live_Demo-cleybersilva.github.io-2b7fff?style=for-the-badge&logo=github)](https://cleybersilva.github.io/dashboardtravel/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![GitHub Pages](https://img.shields.io/badge/GitHub_Pages-active-brightgreen.svg)](https://cleybersilva.github.io/dashboardtravel/)

[![HTML5](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)](https://developer.mozilla.org/pt-BR/docs/Web/HTML)
[![CSS3](https://img.shields.io/badge/CSS3-1572B6?logo=css3&logoColor=white)](https://developer.mozilla.org/pt-BR/docs/Web/CSS)
[![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?logo=javascript&logoColor=black)](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript)
[![Supabase](https://img.shields.io/badge/Supabase-Postgres-3ecf8e?logo=supabase&logoColor=white)](https://supabase.com/)
[![N8N](https://img.shields.io/badge/N8N-automation-ea4b71?logo=n8n&logoColor=white)](https://n8n.io/)

[![Fontes de preço](https://img.shields.io/badge/fontes_de_preco-2-blue.svg)](#-arquitetura)
[![IA](https://img.shields.io/badge/IA-Claude_%2B_ChatGPT-a3e635.svg)](#-arquitetura)
[![Dependencies](https://img.shields.io/badge/dependencies-0-brightgreen.svg)](#-tecnologias)
[![Responsive](https://img.shields.io/badge/responsive-mobile_first-teal.svg)](#-design-system)
[![Dark Mode](https://img.shields.io/badge/theme-dark_only-0A1220.svg)](#-design-system)

[Live Demo](https://cleybersilva.github.io/dashboardtravel/) •
[Sobre](#-sobre-o-projeto) •
[Arquitetura](#-arquitetura) •
[Funcionalidades](#-funcionalidades) •
[Stack](#-tecnologias) •
[Design](#-design-system) •
[Como usar](#-como-usar-localmente) •
[Roadmap](#-roadmap) •
[Autor](#-autor)

---

</div>

## 📖 Sobre o Projeto

**Dashboard Travel** é um sistema pessoal de **monitoramento de preços de passagens aéreas** que roda de forma totalmente automatizada: uma automação em **N8N** varre múltiplos destinos a cada 30 minutos, consulta duas fontes de dados diferentes com fallback automático entre elas, grava o histórico em um banco **Supabase**, gera alertas em linguagem natural com **IA (Claude + ChatGPT)** e notifica em tempo real via **Telegram** — enquanto um dashboard estático publicado no **GitHub Pages** exibe tudo isso com atualização automática, sem precisar de servidor próprio.

O projeto nasceu de uma necessidade concreta: acompanhar preços de voos (Recife → Orlando, entre outros destinos) sem precisar checar manualmente vários sites todos os dias, com o menor custo operacional possível — hoje o sistema roda **inteiramente em camadas gratuitas** (GitHub Pages, Supabase free tier, Travelpayouts, N8N self-hosted).

### 🎯 Motivação

Comparar preços de passagem manualmente, em múltiplos sites, várias vezes ao dia, é repetitivo e fácil de esquecer — e promoções costumam durar poucas horas. **Este projeto automatiza isso de ponta a ponta**: a varredura acontece sozinha, a comparação com o histórico é automática, e o aviso chega direto no Telegram assim que um preço melhor aparece — com uma mensagem escrita por IA, contextualizando se vale a pena ou não.

### 💡 Diferenciais

| Diferencial | Descrição |
|-------------|-----------|
| **🔄 Fallback entre fontes** | Travelpayouts (cache, sem limite) como fonte principal; Skyscanner via RapidAPI (busca ao vivo) como reforço |
| **🧠 IA em cascata** | Claude → ChatGPT → mensagem padrão — nunca depende de uma IA só, nunca quebra o alerta |
| **🛡️ Proteção de cota automática** | Cache de aeroporto, throttle por destino e contador mensal real protegem o plano gratuito do RapidAPI |
| **📲 Multi-canal** | Alertas simultâneos para dois agentes de Telegram (Jarvis e Hermes) |
| **🎨 Design próprio** | Dark navy com dourado e azul-céu, glassmorphism, fundo animado, sem frameworks |
| **📦 Zero servidor** | Dashboard 100% estático no GitHub Pages; toda escrita de dado é feita via REST do Supabase |
| **🔓 Multi-destino dinâmico** | Qualquer pessoa com o link pode adicionar destinos pelo próprio dashboard; remoção protegida por senha |

---

## 🏗️ Arquitetura

```
N8N (cron a cada 30 min) -> Code Node (JS):
  1. Le destinos ativos no Supabase
  2. Travelpayouts (exato -> mes -> livre)
  3. Fallback: RapidAPI / Skyscanner
  4. Compara com o menor preco ja visto
  5. Gera alerta: Claude -> ChatGPT -> mensagem padrao
       |
       +--> Supabase (ofertas_voos, destinos_monitorados, contador_rapidapi)
       +--> Telegram (Jarvis)
       +--> Telegram (Hermes)

Supabase (REST, anon key, leitura publica)
       |
       v
Dashboard (GitHub Pages) -- polling a cada 60s
```

---

## ✨ Funcionalidades

### Automação (N8N)

- Varredura de **múltiplos destinos** a cada 30 minutos, cadastrados dinamicamente
- **3 níveis de busca** no Travelpayouts: datas exatas → mês → sem filtro (contorna falta de cache para rotas/datas específicas)
- **Fallback ao vivo** via Skyscanner (RapidAPI) quando o Travelpayouts não encontra nada
- **Cache de resolução de aeroporto** — reduz de 3 para 1 chamada de API por busca após a primeira vez
- **Throttle de 20h por destino** + **contador mensal real** protegem a cota gratuita do RapidAPI de estourar
- **Deduplicação inteligente**: só grava e só alerta quando o preço muda de verdade (evita spam de alertas repetidos)
- Mensagem de alerta gerada por IA (Claude → ChatGPT → texto padrão), sempre com link de busca da passagem

### Dashboard (GitHub Pages)

- Seletor de origem/destino com **busca por mais de 5.700 aeroportos** (sem precisar decorar código IATA)
- Abas por destino, com botão de remover **protegido por senha**
- Cards de estatística: melhor preço, companhia, preço médio, tendência (queda/alta %)
- Preço mais recente **pulsando** com botão direto de compra
- Tabela de histórico com badge da fonte do dado, e botão de remover oferta individual (também protegido por senha)
- Painel de ofertas pode ser **ocultado/reaberto**, com preferência salva no navegador
- Atualização automática a cada 60 segundos — sem precisar recarregar a página
- Fundo animado (aurora), glassmorphism, totalmente responsivo

---

## 🎨 Design System

Paleta escura própria, inspirada em produtos de viagem premium, com acento dourado e azul-céu.

### Paleta de cores

```css
/* Base — Navy */
--bg: #0B1220        /* Background principal */
--surface: #111B2E   /* Cards e superfícies */
--surface-2: #16243B /* Elementos elevados */
--line: #22314C      /* Bordas e divisores */

/* Accents */
--gold: #D4A843      /* Destaque principal — preços, CTAs */
--sky: #4FA3D1        /* Destaque secundário — links, dados */

/* Semânticos */
--good: #5CB88A       /* Queda de preço, sucesso */
--bad: #D16B6B        /* Alta de preço, erro */

/* Tipografia */
--text: #E8E6DF
--muted: #8B93A6
```

### Tipografia

- **Display / Títulos:** [Playfair Display](https://fonts.google.com/specimen/Playfair+Display) — serifada, 600/700
- **Body / UI:** [Inter](https://fonts.google.com/specimen/Inter) — 400 a 700
- **Code / Mono:** [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) — 500

### Componentes

- Cards com **glassmorphism** (fundo semitransparente + blur)
- Fundo com **aurora animada** (manchas de luz em movimento lento)
- Preço em destaque com **animação de pulso**
- Badges de fonte de dado e selos de tecnologia no rodapé
- Autocomplete de aeroportos com sugestões e busca fonética em português
- Grid responsivo com breakpoint em 640px

---

## 🛠️ Tecnologias

| Camada | Tecnologia | Uso |
|--------|------------|-----|
| **Frontend** | HTML5 + CSS3 + JavaScript vanilla | Dashboard estático, zero build step |
| **Hospedagem** | GitHub Pages | Deploy gratuito, direto do repositório |
| **Banco de dados** | Supabase (Postgres) | Histórico de ofertas, destinos, controle de cota |
| **Automação** | N8N (self-hosted) | Orquestração da varredura, IA e alertas |
| **Fonte de preço 1** | Travelpayouts (Data API) | Preços em cache, sem limite de requisições |
| **Fonte de preço 2** | Skyscanner (via RapidAPI) | Busca ao vivo, plano gratuito com cota controlada |
| **IA** | Claude (Anthropic) + ChatGPT (OpenAI) | Geração da mensagem de alerta, em cascata |
| **Notificação** | Telegram Bot API | Alertas simultâneos para dois agentes |
| **Dataset** | OpenFlights (aeroportos.js) | ~5.700 aeroportos com código IATA para autocomplete |

### Zero dependências no frontend

O dashboard não usa framework nenhum — HTML, CSS e JavaScript puro, com apenas o Chart.js via CDN para os gráficos de histórico. Isso significa deploy trivial (só precisa de arquivos estáticos) e nenhuma etapa de build.

---

## 📊 Métricas do Projeto

| Métrica | Valor |
|---------|-------|
| **Frequência de varredura** | 30 minutos |
| **Fontes de preço** | 2 (com fallback automático) |
| **Modelos de IA em cascata** | 2 (Claude + ChatGPT) + fallback de texto |
| **Aeroportos no autocomplete** | ~5.700 |
| **Canais de notificação** | 2 (Telegram Jarvis + Hermes) |
| **Dependências externas no frontend** | 1 (Chart.js via CDN) |
| **Custo de infraestrutura** | R$ 0 (todos os serviços em camada gratuita) |
| **Suporte a mobile** | ✅ Total (mobile-first) |
| **Suporte a dark mode** | ✅ Nativo (dark-only) |

---

## 🚀 Como usar localmente

### Opção 1 — Servidor Python (simples)

```bash
git clone https://github.com/cleybersilva/dashboardtravel.git
cd dashboardtravel
python3 -m http.server 8000
```

Acesse `http://localhost:8000` no navegador.

### Opção 2 — Node.js (http-server)

```bash
git clone https://github.com/cleybersilva/dashboardtravel.git
cd dashboardtravel
npx http-server -p 8000
```

### Opção 3 — Direto no navegador

Basta abrir o arquivo `index.html` no navegador (algumas fontes podem não carregar em `file://`).

> **Nota:** para funcionar de verdade (não só visualmente), o `index.html` precisa ter `SUPABASE_URL` e `SUPABASE_ANON_KEY` configurados, e o banco Supabase precisa ter o schema aplicado (veja os arquivos `supabase-schema.sql` e `supabase-migration-v*.sql` no repositório).

---

## 🌐 Deploy no GitHub Pages

O projeto já está configurado para GitHub Pages:

1. Fork ou clone o repositório
2. Vá em **Settings** → **Pages**
3. Em **Source**, escolha **Deploy from a branch**
4. Selecione **branch: main** e **folder: / (root)**
5. Aguarde 1–2 minutos para o deploy

O site estará disponível em `https://<seu-usuario>.github.io/dashboardtravel/`

---

## 🗺️ Roadmap

### v1.0 (atual) — Fundação

- ✅ Dashboard responsivo com design próprio
- ✅ Automação N8N com fallback entre 2 fontes de preço
- ✅ IA em cascata (Claude → ChatGPT → texto padrão)
- ✅ Alertas duplos via Telegram
- ✅ Proteção automática de cota de API
- ✅ Multi-destino dinâmico, com autocomplete de aeroportos

### v1.1 — Melhorias

- [ ] Notificação por e-mail como terceiro canal de alerta
- [ ] Gráfico comparativo entre destinos na mesma tela
- [ ] Exportar histórico de preços em CSV
- [ ] Modo claro (light theme) opcional

### v2.0 — Fontes adicionais

- [ ] Integração com Kiwi.com (Tequila API), sujeito à aprovação de parceria
- [ ] Suporte a busca por faixa de datas flexível (não só datas fixas)
- [ ] Histórico de preço por dia da semana (padrão de sazonalidade)

---

## 🤝 Como Contribuir

Contribuições são bem-vindas! Este é um projeto pessoal, mas aberto a melhorias.

### Reportar bugs

Abra uma [issue](https://github.com/cleybersilva/dashboardtravel/issues) descrevendo:
- Navegador e versão
- Passos para reproduzir
- Comportamento esperado vs observado

### Pull requests

1. Fork o repositório
2. Cria uma branch: `git checkout -b feature/minha-melhoria`
3. Commita: `git commit -m 'feat: adiciona nova funcionalidade'`
4. Push: `git push origin feature/minha-melhoria`
5. Abre um Pull Request

---

## 📚 Referências

- [Supabase Documentation](https://supabase.com/docs) — Postgres como serviço, REST automático
- [N8N Documentation](https://docs.n8n.io/) — Automação de workflows
- [Travelpayouts Data API](https://travelpayouts.github.io/slate/) — Dados de preços de passagens
- [RapidAPI Hub](https://rapidapi.com/hub) — Marketplace de APIs, incluindo Skyscanner
- [Anthropic Claude API](https://docs.claude.com/) — Geração de linguagem natural
- [OpenAI API](https://platform.openai.com/docs) — Geração de linguagem natural (fallback)
- [OpenFlights Airport Database](https://openflights.org/data.php) — Dataset de aeroportos

---

## 📄 Licença

Este projeto está licenciado sob a **MIT License** — veja o arquivo [LICENSE](LICENSE) para detalhes.

Você pode usar, modificar e distribuir livremente, desde que mantida a atribuição original.

---

## 👤 Autor

<div align="center">

<img src="https://github.com/cleybersilva.png" width="120" style="border-radius:50%" alt="Cleyber Silva"/>

### **Cleyber Gomes da Silva**

**AI Scientist / SRE Engineer** · TCS × Itaú Unibanco
**Aluno de MBA em Inteligência Artificial e Big Data** · ICMC / USP
**Doutorando em IA & Big Data** · USP · **Educação Sciences** · VCCU

📍 João Pessoa, Paraíba, Brasil

[![GitHub](https://img.shields.io/badge/GitHub-cleybersilva-181717?logo=github)](https://github.com/cleybersilva)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?logo=linkedin)](https://linkedin.com/in/cleyber-silva)

</div>

### Sobre o autor

AI Scientist / SRE Engineer com experiência em ambientes bancários regulados, atualmente alocado ao **Itaú Unibanco** via **TCS (Tata Consultancy Services)**. Simultaneamente, doutorando em **Inteligência Artificial e Big Data** pela **USP** e em **Ciências da Educação** pela **VCCU (Veni Creator Christian University)**, onde também atua como professor, pesquisador e gestor do VLE (Moodle).

Especialista em **Cloud-Native Infrastructure**, **GitOps**, **Observability** e **AI aplicada a operações**. Idealizador de projetos que unem SRE, DevSecOps e Inteligência Artificial — este dashboard nasceu como automação pessoal e evoluiu para uma vitrine de arquitetura orientada a IA com múltiplas camadas de resiliência (fallback de fontes, fallback de IA, proteção automática de cota).

**Formação simultânea:**
- 🎓 MBA em Inteligência Artificial e Big Data — ICMC/USP
- 🎓 Doutorado em IA & Big Data — USP
- 🎓 Doutorado em Ciências da Educação — VCCU
- 🎓 Pós-graduações concorrentes em DevOps, Cloud Architecture, SRE e .NET/Azure

---

<div align="center">

### ⭐ Se este projeto foi útil, considere dar uma estrela!

**Feito com ❤️ para nunca mais perder uma promoção de passagem**

---

**Dashboard Travel** · N8N + Supabase + IA · Monitor pessoal de voos

[✈️ Dashboard](https://cleybersilva.github.io/dashboardtravel/)

[⬆ Voltar ao topo](#️-dashboard-travel)

---

_"A automação boa é aquela que você esquece que existe — até o dia em que o Telegram avisa que o preço caiu."_

© 2026 · Cleyber Gomes da Silva · MIT License

</div>
