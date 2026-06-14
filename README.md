# Local LLM Environment with Docker & LocalAI

Este repositório contém a infraestrutura básica e otimizada para rodar um Modelo de Linguagem (LLM) localmente utilizando **Docker** e **LocalAI**. O ambiente expõe uma API REST local 100% compatível com o formato da OpenAI, permitindo integração direta com ferramentas como Open WebUI, Dify, AnythingLLM ou scripts personalizados.

A configuração atual está otimizada para execução de alta performance em **CPU** (focada em processadores de 8 núcleos), utilizando o modelo **Llama 3.2 3B**.

---

## 🛠️ Requisitos Prévios

Antes de iniciar, certifique-se de ter instalado em sua máquina:
* [Docker Desktop](https://www.docker.com/products/docker-desktop/)
* Git (para clonar/gerenciar o repositório)

---

## 🚀 Como Configurar e Rodar

### 1. Clonar o Repositório
```bash
git clone [https://github.com/SEU_USUARIO/SEU_REPOSITORIO.git](https://github.com/SEU_USUARIO/SEU_REPOSITORIO.git)
cd gpt4all-docker

```

### 2. Baixar o Modelo de IA

Por motivos de performance e limitações de tamanho de arquivos no Git, o modelo de IA deve ser baixado manualmente e posicionado na pasta correta.

1. Baixe o modelo **Llama 3.2 3B Instruct** no formato `.gguf`:
* [Link Direto para Download (via GPT4All)](https://www.google.com/search?q=https://gpt4all.io/models/gguf/Llama-3.2-3B-Instruct-Q4_K_M.gguf) (~2.02 GB)


2. Mova o arquivo baixado para dentro da pasta `models/` do projeto.

Sua estrutura de arquivos deve ficar assim:

```text
gpt4all-docker/
├── models/
│   ├── .gitkeep
│   └── Llama-3.2-3B-Instruct-Q4_K_M.gguf  <-- O arquivo deve ficar aqui
├── .gitignore
├── docker-compose.yml
└── README.md

```

### 3. Iniciar o Servidor

Com o terminal aberto na raiz do projeto, execute o comando para subir o contêiner em segundo plano:

```bash
docker compose up -d

```

O LocalAI iniciará e mapeará o modelo automaticamente na porta `8080`.

---

## ⚡ Otimizações Aplicadas

O arquivo `docker-compose.yml` está configurado com variáveis de ambiente específicas para garantir que a inferência por CPU não sofra gargalos:

* `THREADS=6`: Configuração ideal para processadores de 8 núcleos físicos, deixando margem para o sistema operacional continuar fluido.
* `GALL_M_LOCK=MAINTAIN`: Trava o modelo na memória RAM para evitar paginação em disco lento.
* `MALLOC_TRIM_THRESHOLD_=-1`: Otimização de alocação de memória dinâmica.

---

## 🔌 Como Testar a API

Você pode validar se o ambiente está respondendo corretamente consumindo o endpoint via terminal.

### No PowerShell (Windows):

```powershell
(Invoke-RestMethod -Uri "http://localhost:8080/v1/chat/completions" -Method Post -ContentType "application/json" -Body '{"model": "Llama-3.2-3B-Instruct-Q4_K_M.gguf", "messages": [{"role": "user", "content": "Olá! Quem é você?"}]}').choices.message

```

### No Terminal (Linux/Mac ou Git Bash):

```bash
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "Llama-3.2-3B-Instruct-Q4_K_M.gguf",
    "messages": [{"role": "user", "content": "Olá! Quem é você?"}]
  }'

```

### Interface Visual (Swagger)

Para visualizar e testar todos os endpoints disponíveis na interface interativa, acesse no seu navegador:
👉 **`http://localhost:8080/docs`**

---

## 🛑 Como Parar o Ambiente

Para encerrar a execução do contêiner e liberar a memória RAM do seu computador, execute:

```bash
docker compose down

```