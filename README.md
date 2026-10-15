# FAQ Chatbot - Spotify Premium

Este é um chatbot construído com arquitetura RAG (Retrieval-Augmented Generation) para responder a dúvidas sobre os planos do Spotify de forma segura, utilizando modelos Open Source rodando 100% localmente, sem dependência de APIs pagas.

## Arquitetura e Stack Tecnológica
- **Backend:** Python + FastAPI
- **Frontend:** Streamlit
- **LLM Local:** Ollama (Modelo: Llama 3)
- **Banco Vetorial:** ChromaDB
- **Framework IA:** LangChain
- **Orquestração:** Docker & Docker Compose

## Como funciona

1. `populate_db.py` lê o FAQ em `data/faq_spotify.md`, divide o texto em trechos e gera embeddings com um modelo multilíngue do Sentence Transformers, salvando tudo no ChromaDB.
2. Quando o usuário pergunta algo no Streamlit, o backend em FastAPI busca os 8 trechos mais relevantes no ChromaDB.
3. Os trechos entram no prompt do Llama 3 (temperatura 0), instruído a responder apenas com base no contexto. Se a informação não estiver no FAQ, o bot responde que não tem essa informação, o que reduz alucinações.
4. A resposta volta para a interface junto com a fonte consultada.

## Preparação (obrigatória nas duas formas de execução)

O banco vetorial não é versionado no Git. Antes da primeira execução, gere a base a partir do FAQ:

```bash
pip install -r requirements.txt
python populate_db.py
```

Isso cria a pasta `chroma_db/` na raiz do projeto.

## Como executar o projeto (via Docker)

Certifique-se de ter o [Docker Desktop](https://www.docker.com/) instalado na sua máquina.

1. Clone este repositório e faça a preparação acima.
2. Abra o terminal na raiz do projeto.
3. Execute o comando de orquestração:

   ```bash
   docker-compose up --build
   ```

4. Acesse a interface em `http://localhost:8501`.

## Como executar o projeto (localmente, sem Docker)

> **Importante, configuração da IA:** ao executar o projeto nativamente, o sistema não utiliza a ponte de rede do Docker. Antes de iniciar os servidores, abra o arquivo `src/backend/rag_engine.py` e altere o parâmetro `base_url` do modelo de `http://host.docker.internal:11434` para `http://localhost:11434`.

Certifique-se de ter o Python e o Ollama instalados, além de ter baixado o modelo Llama 3 (`ollama run llama3`).

1. Clone este repositório.
2. Abra o terminal na raiz do projeto.
3. Crie e ative um ambiente virtual:

   ```bash
   python -m venv venv
   .\venv\Scripts\activate
   ```

4. Instale as bibliotecas e gere a base vetorial:

   ```bash
   pip install -r requirements.txt
   python populate_db.py
   ```

5. Inicie o servidor do backend (FastAPI):

   ```bash
   uvicorn src.backend.main:app --reload
   ```

6. Em um novo terminal na raiz do projeto, ative o ambiente virtual novamente e inicie o frontend (Streamlit):

   ```bash
   streamlit run src/frontend/app.py
   ```
