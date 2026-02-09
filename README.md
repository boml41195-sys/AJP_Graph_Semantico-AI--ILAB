# 🕷️ AJP-Graph: Motor de Automação Judicial Inteligente

**Edição Judiciária Brasileira do ScrapeGraphAI**

[English](README.en.md) | **Português (Brasil)**

[![PyPI Downloads](https://static.pepy.tech/personalized-badge/scrapegraphai?period=total&units=INTERNATIONAL_SYSTEM&left_color=BLACK&right_color=GREEN&left_text=downloads)](https://pepy.tech/projects/scrapegraphai)
[![linting: pylint](https://img.shields.io/badge/linting-pylint-yellowgreen?style=for-the-badge)](https://github.com/pylint-dev/pylint)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

---

## 🏛️ Sobre o Projeto

O **AJP-Graph** é um fork especializado do [ScrapeGraphAI](https://scrapegraphai.com) — uma biblioteca Python de web scraping que utiliza LLMs (Large Language Models) e lógica de grafos direcionados para criar pipelines de extração de dados de websites e documentos locais (XML, HTML, JSON, Markdown, etc.).

Este projeto foi customizado especificamente para o **ecossistema judiciário brasileiro**, sendo desenvolvido e mantido pelo **Lab de Inovação Jurídica** como o braço executor do **AJP (Agent Judicial Protocol)**. Nossa missão é fornecer uma camada de extração de dados **soberana, segura e resiliente** capaz de superar as barreiras de interoperabilidade dos sistemas de tribunais brasileiros.

### ⚖️ Por que AJP-Graph?

O cenário jurídico brasileiro apresenta desafios únicos que a versão upstream do ScrapeGraphAI não aborda diretamente:

#### **Sistemas Fragmentados**
- **SAJ** (Sistema de Automação da Justiça)
- **EPROC** (Processo Eletrônico)
- **PJe** (Processo Judicial Eletrônico)
- **Projudi** (Processo Judicial Digital)
- **SEEU** (Sistema Eletrônico de Execução Unificado)
- Dezenas de sistemas regionais e proprietários

#### **Requisitos de Conformidade**
- **Soberania de Dados**: Processamento local obrigatório para dados sob segredo de justiça
- **Certificação Digital**: Suporte a certificados ICP-Brasil (A1/A3) para autenticação de fé pública
- **Resoluções do CNJ**: Conformidade com diretrizes sobre uso de IA no Judiciário
- **Trilhas de Auditoria**: Rastreabilidade completa de acessos e extrações

#### **Desafios Técnicos**
- Sistemas legados com interfaces instáveis
- CAPTCHAs e mecanismos anti-bot agressivos
- Layouts que mudam frequentemente entre tribunais
- Ausência de APIs padronizadas

---

## 🛡️ Arquitetura de Defesa em Camadas

O AJP-Graph implementa uma estratégia de **resiliência multi-nível** para garantir a extração de dados mesmo em cenários adversos:

### **🎯 Camada 1: Extração Semântica (LLM-Powered)**
Utiliza modelos de linguagem para interpretar o DOM de forma inteligente, extraindo informações mesmo quando há mudanças superficiais no layout. Ignora alterações cosméticas e foca no conteúdo semântico.

**Tecnologias:**
- Integração nativa com **Ollama** para processamento local
- Suporte a modelos OpenAI, Groq, Azure, Gemini
- Parsing semântico adaptativo

### **🔗 Camada 2: Protocolo AJP Padronizado**
Traduz a extração bruta em esquemas **JPC (JSON Protocol Call)** padronizados, garantindo interoperabilidade entre diferentes sistemas judiciais.

**Recursos:**
- Schemas Pydantic para validação rigorosa
- Mapeamento automático de entidades processuais
- Normalização de formatos regionais

### **🧩 Camada 3: Resolução de Obstáculos**
Módulos especializados em contornar travas de acesso, com suporte a **Human-in-the-Loop (HITL)** para intervenção manual quando necessário.

**Capacidades:**
- Sistema de pausa e retomada de estado
- Interface de espelhamento para resolução manual de CAPTCHAs
- Fallback para automação de interface bruta (RPA híbrido)
- Gestão inteligente de sessões e cookies

---

## 🚀 Instalação Rápida

```bash
# Instale o AJP-Graph
pip install ajp-graph

# IMPORTANTE: Instale o Playwright para buscar conteúdo web
playwright install

# Para processamento local (recomendado para dados sensíveis)
# Instale o Ollama: https://ollama.com/
ollama pull llama3.2
```

> **⚠️ Recomendação**: Instale sempre em um ambiente virtual para evitar conflitos de dependências.

---

## 💻 Uso Básico

### Exemplo 1: Extração de Dados de Portal de Tribunal

```python
from ajp_graph.graphs import SmartScraperGraph

# Configuração para processamento local (On-Premise)
graph_config = {
    "llm": {
        "model": "ollama/llama3.2",
        "model_tokens": 8192,
        "format": "json",
    },
    "verbose": True,
    "headless": False,
    "ajp_protocol": True,  # Ativa o protocolo AJP
    "tribunal": "TJSP",    # Especifica o tribunal
}

# Cria o pipeline de extração
smart_scraper_graph = SmartScraperGraph(
    prompt="Extraia o número do processo, partes, movimentações e situação atual",
    source="https://esaj.tjsp.jus.br/cpopg/show.do?processo.codigo=XXXXX",
    config=graph_config
)

# Executa a extração
resultado = smart_scraper_graph.run()

import json
print(json.dumps(resultado, indent=4, ensure_ascii=False))
```

### Exemplo 2: Extração com Autenticação (Certificado Digital)

```python
from ajp_graph.graphs import SmartScraperGraph
from ajp_graph.auth import CertificadoICPBrasil

# Configura autenticação com certificado A3
certificado = CertificadoICPBrasil(
    tipo="A3",
    caminho="/caminho/para/certificado.pfx",
    senha="senha_segura"
)

graph_config = {
    "llm": {
        "model": "ollama/llama3.2",
    },
    "auth": certificado,
    "ajp_protocol": True,
    "tribunal": "TRF3",
}

scraper = SmartScraperGraph(
    prompt="Extraia dados de intimações e petições pendentes",
    source="https://eproc.trf3.jus.br/...",
    config=graph_config
)

resultado = scraper.run()
```

### Exemplo 3: Extração Multi-Página com Resiliência

```python
from ajp_graph.graphs import SmartScraperMultiGraph

fontes = [
    "https://esaj.tjsp.jus.br/cpopg/show.do?processo.codigo=123",
    "https://esaj.tjsp.jus.br/cpopg/show.do?processo.codigo=456",
    "https://esaj.tjsp.jus.br/cpopg/show.do?processo.codigo=789",
]

graph_config = {
    "llm": {"model": "ollama/llama3.2"},
    "ajp_protocol": True,
    "tribunal": "TJSP",
    "resilience": {
        "max_retries": 3,
        "fallback_to_rpa": True,
        "hitl_on_captcha": True,  # Pausa para resolução manual de CAPTCHA
    }
}

multi_scraper = SmartScraperMultiGraph(
    prompt="Extraia número, partes e última movimentação de cada processo",
    source=fontes,
    config=graph_config
)

resultados = multi_scraper.run()
```

---

## 📊 Pipelines Disponíveis

O AJP-Graph herda todos os pipelines do ScrapeGraphAI e adiciona variantes especializadas:

| Pipeline | Descrição | Caso de Uso Judicial |
|----------|-----------|----------------------|
| `SmartScraperGraph` | Extração de página única com prompt do usuário | Consulta de processo individual |
| `SearchGraph` | Extração dos top N resultados de busca | Jurisprudência e precedentes |
| `SmartScraperMultiGraph` | Extração paralela de múltiplas páginas | Monitoramento de lote de processos |
| `ScriptCreatorGraph` | Gera script Python de extração | Automação repetível e auditável |
| `SpeechGraph` | Extração com geração de áudio | Acessibilidade e relatórios narrados |
| `JudicialDocumentGraph` *(AJP)* | Extração estruturada de documentos processuais | Petições, sentenças, acórdãos |
| `TimelineGraph` *(AJP)* | Linha do tempo processual | Histórico cronológico de movimentações |

> **💡 Paralelização**: Todas as variantes possuem versão multi para chamadas LLM em paralelo.

---

## 🗂️ Estrutura do Projeto (Extensões AJP)

```
ajp-graph/
├── ajp_graph/
│   ├── drivers/          # Blueprints de navegação por tribunal
│   │   ├── tjsp.py       # Driver para SAJ/ESAJ (TJSP)
│   │   ├── pje.py        # Driver para PJe (CNJ)
│   │   ├── eproc.py      # Driver para EPROC (TRF)
│   │   └── ...
│   ├── auth/             # Autenticação e certificados digitais
│   │   ├── icp_brasil.py # Gestão de certificados A1/A3
│   │   └── oauth.py      # Tokens OAuth2 para APIs modernas
│   ├── schema/           # Esquemas Pydantic para protocolo AJP
│   │   ├── processo.py   # Entidades processuais padronizadas
│   │   └── jpc.py        # JSON Protocol Call definitions
│   ├── hitl/             # Human-in-the-Loop
│   │   ├── captcha.py    # Interface de resolução de CAPTCHA
│   │   └── mirror.py     # Espelhamento de sessão para intervenção
│   └── resilience/       # Estratégias de fallback
│       ├── rpa_hybrid.py # Automação RPA como último recurso
│       └── state.py      # Persistência de estado para retomada
├── tests/
├── docs/
└── README.md
```

---

## 🔐 Soberania e Conformidade

### **Processamento Local (On-Premise)**

O AJP-Graph foi projetado para **nunca enviar dados sensíveis para a nuvem**:

```python
# ✅ Configuração segura para dados sob segredo de justiça
graph_config = {
    "llm": {
        "model": "ollama/llama3.2",  # Modelo local via Ollama
        "base_url": "http://localhost:11434",  # Servidor local
    },
    "data_residency": "on-premise",  # Força processamento local
    "encryption": "AES-256",         # Criptografia de dados em repouso
}
```

### **Trilha de Auditoria**

Todas as operações geram logs estruturados compatíveis com requisitos do CNJ:

```python
# Exemplo de log de auditoria
{
    "timestamp": "2026-02-08T14:32:11Z",
    "user": "servidor.tribunal@tjsp.jus.br",
    "action": "extração_dados_processo",
    "processo": "1234567-89.2024.8.26.0100",
    "tribunal": "TJSP",
    "certificado": "A3:12345678901234567890",
    "ip_origem": "192.168.1.100",
    "hash_dados": "sha256:abc123...",
}
```

---

## 🌐 Integrações

O AJP-Graph mantém compatibilidade com todas as integrações do ScrapeGraphAI:

### **APIs e SDKs**
- **API REST**: [Documentação](https://docs.scrapegraphai.com/introduction)
- **Python SDK**: [scrapegraph-py](https://github.com/ScrapeGraphAI/scrapegraph-sdk/tree/main/scrapegraph-py)
- **Node.js SDK**: [scrapegraph-js](https://github.com/ScrapeGraphAI/scrapegraph-sdk/tree/main/scrapegraph-js)

### **Frameworks LLM**
- [Langchain](https://docs.scrapegraphai.com/integrations/langchain)
- [Llama Index](https://docs.scrapegraphai.com/integrations/llamaindex)
- [Crew.ai](https://docs.scrapegraphai.com/integrations/crewai)
- [Agno](https://docs.scrapegraphai.com/integrations/agno)
- [CamelAI](https://github.com/camel-ai/camel)

### **Plataformas Low-Code**
- [Pipedream](https://pipedream.com/apps/scrapegraphai)
- [Bubble](https://bubble.io/plugin/scrapegraphai-1745408893195x213542371433906180)
- [Zapier](https://zapier.com/apps/scrapegraphai/integrations)
- [n8n](http://localhost:5001/dashboard)
- [Dify](https://dify.ai)
- [Toolhouse](https://app.toolhouse.ai/mcp-servers/scrapegraph_smartscraper)

### **Servidor MCP**
- [Smithery MCP Server](https://smithery.ai/server/@ScrapeGraphAI/scrapegraph-mcp)

---

## 📖 Documentação

- **Documentação Oficial ScrapeGraphAI**: [ReadTheDocs](https://scrapegraph-ai.readthedocs.io/en/latest/)
- **Docusaurus**: [docs-oss.scrapegraphai.com](https://docs-oss.scrapegraphai.com/)
- **Documentação API**: [docs.scrapegraphai.com](https://docs.scrapegraphai.com)
- **Documentação AJP**: *(em desenvolvimento)*

### **Tutoriais**
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1sEZBonBMGP44CtO6GQTwAlL0BGJXjtfd?usp=sharing)

---

## 🔥 Benchmark

De acordo com o benchmark da Firecrawl, o ScrapeGraphAI (base do AJP-Graph) é o **melhor fetcher do mercado**:

![Benchmark](https://raw.githubusercontent.com/VinciGit00/Scrapegraph-ai/main/docs/assets/histogram.png)

---

## 🤝 Governança e Contribuição

Este projeto é mantido pelo **Lab de Inovação Jurídica**. Encorajamos desenvolvedores de:

- **Tribunais de Justiça**
- **Ministério Público**
- **Defensorias Públicas**
- **Advocacia Pública**

...a contribuírem com novos **drivers de sistemas regionais**.

### **Como Contribuir**

1. Faça um fork do repositório
2. Crie uma branch para sua feature (`git checkout -b feature/driver-tjrs`)
3. Commit suas mudanças (`git commit -m 'Adiciona driver para TJRS'`)
4. Push para a branch (`git push origin feature/driver-tjrs`)
5. Abra um Pull Request

Consulte nosso [Guia de Contribuição](CONTRIBUTING.md) para mais detalhes.

### **Comunidade**

[![Discord](https://skillicons.dev/icons?i=discord)](https://discord.gg/uJN7TYcpNa)
[![LinkedIn](https://skillicons.dev/icons?i=linkedin)](https://www.linkedin.com/company/scrapegraphai/)
[![Twitter](https://skillicons.dev/icons?i=twitter)](https://twitter.com/scrapegraphai)

> **Nota**: Este fork mantém **paridade com o upstream** do ScrapeGraphAI, mas prioriza **estabilidade** e **conformidade** com as resoluções do CNJ sobre uso de IA.

---

## 📈 Telemetria

Coletamos métricas de uso **anônimas** para melhorar a qualidade do pacote e a experiência do usuário. Os dados nos ajudam a priorizar melhorias e garantir compatibilidade.

Para **desativar**, defina a variável de ambiente:

```bash
export SCRAPEGRAPHAI_TELEMETRY_ENABLED=false
```

Mais informações: [Documentação de Telemetria](https://scrapegraph-ai.readthedocs.io/en/latest/scrapers/telemetry.html)

---

## ❤️ Contribuidores

[![Contributors](https://contrib.rocks/image?repo=VinciGit00/Scrapegraph-ai)](https://github.com/VinciGit00/Scrapegraph-ai/graphs/contributors)

---

## 🎓 Citação Acadêmica

Se você utilizou nossa biblioteca para fins de pesquisa, por favor cite-nos:

```bibtex
@misc{ajp-graph,
    author = {Lab de Inovação Jurídica},
    title = {AJP-Graph: Motor de Automação Judicial Inteligente},
    year = {2026},
    url = {https://github.com/lab-inovacao/ajp-graph},
    note = {Fork especializado do ScrapeGraphAI para o ecossistema judiciário brasileiro}
}

@misc{scrapegraph-ai,
    author = {Lorenzo Padoan, Marco Vinciguerra},
    title = {Scrapegraph-ai},
    year = {2024},
    url = {https://github.com/VinciGit00/Scrapegraph-ai},
    note = {A Python library for scraping leveraging large language models}
}
```

---

## 👥 Autores

### **AJP-Graph (Fork Judicial)**

| Nome | Contato |
|------|---------|
| Lab de Inovação Jurídica | [![LinkedIn](https://img.shields.io/badge/-LinkedIn-blue?style=flat&logo=Linkedin&logoColor=white)](#) |

### **ScrapeGraphAI (Upstream)**

| Nome | Contato |
|------|---------|
| Marco Vinciguerra | [![LinkedIn](https://img.shields.io/badge/-LinkedIn-blue?style=flat&logo=Linkedin&logoColor=white)](https://www.linkedin.com/in/marco-vinciguerra-7ba365242/) |
| Lorenzo Padoan | [![LinkedIn](https://img.shields.io/badge/-LinkedIn-blue?style=flat&logo=Linkedin&logoColor=white)](https://www.linkedin.com/in/lorenzo-padoan-4521a2154/) |

---

## 📜 Licença

O AJP-Graph é distribuído sob a **Licença MIT**. Veja o arquivo [LICENSE](LICENSE) para mais informações.

Este fork mantém a mesma licença do projeto upstream [ScrapeGraphAI](https://github.com/VinciGit00/Scrapegraph-ai).

---

## 🙏 Agradecimentos

- **ScrapeGraphAI Team**: Pela incrível base tecnológica que tornou este projeto possível
- **Comunidade Open Source**: Pelo apoio contínuo
- **CNJ (Conselho Nacional de Justiça)**: Pelas diretrizes de transformação digital
- **Tribunais Parceiros**: Por fornecerem feedback e casos de uso reais

---

## ⚠️ Aviso Legal

O AJP-Graph é destinado **exclusivamente** para:

- ✅ Exploração de dados públicos para fins legítimos
- ✅ Pesquisa acadêmica e jurídica
- ✅ Automação de atividades autorizadas por servidores públicos
- ✅ Integração oficial de sistemas judiciais

**Não nos responsabilizamos** por uso indevido da biblioteca, incluindo:

- ❌ Acesso não autorizado a sistemas
- ❌ Violação de segredos de justiça
- ❌ Uso comercial sem autorização
- ❌ Sobrecarga maliciosa de servidores

---

**Feito com ❤️ pelo Lab de Inovação Jurídica**

**Baseado em [ScrapeGraphAI](https://scrapegraphai.com) com ❤️**

---

*Última atualização: Fevereiro de 2026*
