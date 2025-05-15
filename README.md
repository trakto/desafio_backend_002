Okay, aqui está uma proposta de desafio técnico que cobre todos os pontos solicitados. Ele é projetado para avaliar as habilidades de um desenvolvedor backend pleno/sênior em diversas áreas relevantes.

---

## Desafio Técnico: Serviço de Otimização de Imagens em Fila

**Objetivo:**
Desenvolver um serviço backend que recebe imagens, as otimiza para reduzir seu tamanho, gera múltiplas versões e gerencia o processo de forma assíncrona utilizando uma fila de mensagens.

**Contexto:**
Em muitas aplicações web, a otimização de imagens é crucial para melhorar o tempo de carregamento e a experiência do usuário. Este desafio simula a criação de um microserviço focado nessa tarefa.

**Requisitos Funcionais:**

1.  **API de Upload:**
    *   Deve haver um endpoint `POST` (ex: `/upload`) que aceite o upload de uma imagem (`multipart/form-data`).
    *   A API deve receber a imagem e enfileirar uma tarefa para processamento.
    *   A resposta imediata da API deve ser um identificador único para a tarefa (ex: `task_id`) e um status inicial (ex: `PENDING`).

2.  **Processamento Assíncrono com Fila (RabbitMQ):**
    *   Ao receber uma imagem, uma mensagem contendo as informações necessárias para o processamento (ex: caminho para a imagem temporária ou os dados da imagem, nome original do arquivo) deve ser enviada para uma fila no RabbitMQ.
    *   Um ou mais "workers" (processos consumidores) devem escutar essa fila.

3.  **Otimização e Geração de Versões:**
    *   O worker, ao consumir uma mensagem da fila, deve:
        *   Realizar a otimização da imagem original com o objetivo principal de reduzir o tamanho do arquivo, mantendo a melhor qualidade visual possível.
        *   Gerar 3 (três) versões da imagem:
            *   **Baixa qualidade:** Uma versão com dimensões menores e/ou maior compressão (ex: largura máxima de 320px).
            *   **Média qualidade:** Uma versão com dimensões médias e compressão moderada (ex: largura máxima de 800px).
            *   **Alta qualidade (original otimizada):** A imagem com as dimensões originais, mas otimizada para redução de tamanho sem perda perceptível de qualidade (ou com a menor perda possível).
        *   As imagens processadas devem ser salvas em um local acessível (para o escopo do desafio, pode ser um diretório local no servidor/container do worker).

4.  **Registro de Status e Metadados (MongoDB):**
    *   Para cada imagem processada, o sistema deve registrar o status e metadados em um banco de dados MongoDB.
    *   Informações a serem salvas:
        *   `task_id` (o mesmo retornado pela API).
        *   `original_filename` (nome do arquivo original).
        *   `status` (ex: `PENDING`, `PROCESSING`, `COMPLETED`, `FAILED`).
        *   `original_metadata`:
            *   `width` (largura original).
            *   `height` (altura original).
            *   `mimetype` (tipo MIME original).
            *   `exif` (dados EXIF originais, se disponíveis. Capturar os principais já é suficiente).
        *   `processed_at` (timestamp de quando o processamento foi concluído ou falhou).
        *   `error_message` (em caso de falha).
        *   `versions`: Um array/objeto contendo informações sobre as versões geradas (ex: caminho/nome do arquivo, novas dimensões, novo tamanho em bytes para cada versão: `low`, `medium`, `high_optimized`).

5.  **API de Consulta de Status (Opcional, mas recomendado):**
    *   Um endpoint `GET` (ex: `/status/{task_id}`) que permita consultar o status atual de uma tarefa de processamento.

**Requisitos Não Funcionais (a serem considerados):**

*   **Qualidade de Código:** Código limpo, bem organizado, com boas práticas de programação.
*   **Tratamento de Erros:** O sistema deve ser resiliente e tratar erros de forma adequada (ex: falha no processamento da imagem, indisponibilidade do RabbitMQ/MongoDB).
*   **Configuração:** Facilitar a configuração de parâmetros (ex: credenciais do RabbitMQ/MongoDB, parâmetros de otimização) através de variáveis de ambiente ou arquivos de configuração.
*   **Documentação:** O `README.md` deve ser claro e completo.

**Stack Tecnológica Sugerida (o candidato pode escolher alternativas, desde que justifique):**

*   **Linguagem Backend:** Node.js (com Express.js/Fastify).
*   **Fila de Mensagens:** RabbitMQ (obrigatório).
*   **Banco de Dados:** MongoDB (obrigatório).
*   **Bibliotecas de Manipulação de Imagem:**
    *   Node.js: Sharp, Jimp.
*   **Containerização (Fortemente Recomendado):** Docker e Docker Compose para facilitar a execução do ambiente completo (aplicação, RabbitMQ, MongoDB).

**Entrega:**

1.  Um repositório Git (pode ser no GitHub, GitLab, Bitbucket) contendo:
    *   Todo o código fonte da solução.
    *   `Dockerfile` e `docker-compose.yml` (se utilizar Docker).
2.  Um arquivo `README.md` detalhado contendo:
    *   Breve descrição da solução e arquitetura.
    *   Instruções claras sobre como configurar o ambiente e executar o projeto (incluindo dependências, variáveis de ambiente necessárias, etc.).
    *   Como executar os testes (se houver).
    *   Exemplos de como usar a API (ex: comandos `curl`).
    *   Quaisquer decisões de design importantes ou trade-offs feitos.

**Critérios de Avaliação:**

*   **Funcionalidade:** O sistema atende a todos os requisitos funcionais?
*   **Qualidade do Código:** Organização, legibilidade, manutenibilidade, uso de boas práticas.
*   **Design e Arquitetura:** Escolhas de design, separação de responsabilidades, escalabilidade da solução (pensando no uso de workers).
*   **Uso de RabbitMQ:** Implementação correta do padrão produtor/consumidor.
*   **Uso de MongoDB:** Estrutura do schema, eficiência das interações.
*   **Qualidade da Otimização:** As imagens são efetivamente otimizadas e as versões são geradas corretamente?
*   **Tratamento de Erros:** Robustez da aplicação.
*   **Documentação (README):** Clareza e completude das instruções.
*   **Configuração e Execução:** Facilidade para rodar o projeto. O uso de Docker/Docker Compose será um grande diferencial.

**Pontos Bônus:**

*   Implementação de testes unitários e/ou de integração.
*   Implementação da API de consulta de status (`/status/{task_id}`).
*   Suporte a diferentes formatos de saída (ex: WebP).
*   Mecanismos de retry para tarefas que falham devido a problemas temporários.
*   Logging estruturado.

**Prazo:**
7 dias corridos.

**Dúvidas:**
Entrar em contato através do e-mail marinatorres@trakto.io
