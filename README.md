# Encurtador de URLs com Observabilidade
## API Python/Flask, Prometheus e Grafana

## 📝 Descrição do Projeto

[cite_start]Este projeto implementa um microsserviço simples de **Encurtador de URLs** (URL Shortener) utilizando o framework **Flask** em Python[cite: 3, 4]. O serviço foi instrumentado com métricas de observabilidade (saúde, performance e métricas de negócio) utilizando a biblioteca `prometheus-flask-exporter`.

A pilha de monitoramento é orquestrada via **Docker Compose** e consiste em:
1.  [cite_start]**API Flask**: O alvo que expõe as métricas no endpoint `/metrics`[cite: 100, 89].
2.  [cite_start]**Prometheus**: Servidor de coleta que "raspa" (scrape) as métricas da API[cite: 10, 91].
3.  [cite_start]**Grafana**: Plataforma de visualização para construir um painel de controle em tempo real[cite: 6, 10, 126].

## 🎯 Objetivos da Atividade

[cite_start]Os principais objetivos da atividade eram[cite: 7]:

* [cite_start]Desenvolver uma API RESTful simples usando Python e Flask[cite: 8].
* [cite_start]Instrumentar a aplicação Flask para expor métricas no formato Prometheus[cite: 9].
* [cite_start]Configurar um ambiente local de monitoramento com Prometheus e Grafana usando Docker Compose[cite: 10].
* [cite_start]Construir um dashboard no Grafana para visualizar métricas de performance (latência, throughput) e métricas de negócio customizadas (links criados e redirecionados)[cite: 12].

## 🛠️ Arquivos e Configurações Chave

| Arquivo | Descrição | Detalhes Importantes |
| :--- | :--- | :--- |
| `app.py` | Contém a API Flask. | Define as métricas customizadas `links_criados_total` e `redirecionamentos_total`. |
| `requirements.txt` | Dependências Python. | Contém `flask` e `prometheus-flask-exporter`. |
| `prometheus.yml` | Configuração do Prometheus. | Configurado para raspar (scrape) o job `flask-url-shortener` na porta `5000` do host: `host.docker.internal:5000/metrics`. |
| `docker-compose.yml` | Orquestração dos serviços. | Sobe os containers `prometheus` (porta 9090) e `grafana` (porta 3000). |

## 🚀 Como Executar o Projeto

Certifique-se de ter **Python (e `pip`)** e **Docker/Docker Compose** instalados.

### 1. Iniciar a API Flask (O Alvo)

Em um terminal:

1.  [cite_start]**Instale as Dependências**[cite: 24]:
    ```bash
    pip install -r requirements.txt
    ```

2.  [cite_start]**Execute a Aplicação** (Manter este terminal rodando)[cite: 83]:
    ```bash
    python app.py
    ```
    A API estará disponível em `http://localhost:5000`.

### 2. Iniciar a Pilha de Monitoramento

Em um terminal diferente, na raiz do projeto:

1.  [cite_start]**Inicie os Containers**[cite: 122]:
    ```bash
    docker-compose up -d
    ```

2.  **Verifique o Prometheus** (Opcional):
    Acesse [http://localhost:9090](http://localhost:9090). [cite_start]Em **"Status" > "Targets"**, o job `flask-url-shortener` deve estar com o status **"UP"** (verde)[cite: 124].

3.  **Acesse o Grafana:**
    Acesse [http://localhost:3000](http://localhost:3000).
    * **Login**: `admin`
    * **Senha**: `admin`[cite: 129].

---

## 💻 Endpoints da Aplicação

| Endpoint | Método | Descrição |
| :--- | :--- | :--- |
| `/encurtar` | `POST` | Cria um novo link curto. Requer JSON: `{"url_longa": "..."}`. |
| `/<codigo_curto>` | `GET` | Redireciona para a URL longa associada. |
| `/api/links` | `GET` | Lista o "banco de dados" em memória. |
| `/metrics` | `GET` | [cite_start]Endpoint de métricas Prometheus (automático)[cite: 89, 100]. |

**Exemplo de Teste para Geração de Métrica `links_criados_total`:**
```bash
curl -X POST http://localhost:5000/encurtar -H "Content-Type: application/json" -d '{"url_longa": "[https://www.google.com](https://www.google.com)"}'
