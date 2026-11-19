# Levantamento de tecnologias para a plataforma CATI

## 1. Objetivo

Organizar as tecnologias identificadas por função, distinguindo frameworks, modelos, servidores de inferência, aplicações e serviços externos.

## 2. Tecnologias identificadas e categorizadas

### 2.1. Frameworks de orquestração de agentes de voz

| Tecnologia                                          | Tipo                 | Descrição — uma linha                                                                                                                                   |
| --------------------------------------------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Pipecat](https://github.com/pipecat-ai/pipecat)    | Framework Python     | Framework para criar agentes de voz em tempo real através de pipelines que integram transporte de áudio, reconhecimento de fala, LLMs e síntese de voz. |
| [LiveKit Agents](https://github.com/livekit/agents) | Framework de agentes | Framework para desenvolver agentes de voz em tempo real, com integração de STT, LLM e TTS e ligação à telefonia através da infraestrutura LiveKit.      |

### 2.2. Reconhecimento de fala e diarização — STT/ASR

| Tecnologia                                                                     | Tipo                               | Descrição — uma linha                                                                                                                             |
| ------------------------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| [MOSS-Transcribe-Diarize](https://github.com/OpenMOSS/MOSS-Transcribe-Diarize) | Modelo de transcrição e diarização | Modelo que transcreve áudio em vários idiomas, distingue os intervenientes e produz texto com marcas temporais e etiquetas de locutor.            |
| [Parakeet — achetronic](https://github.com/achetronic/parakeet)                | Servidor de inferência ASR         | Servidor em Go que disponibiliza o modelo NVIDIA Parakeet TDT através de ONNX Runtime e de uma API compatível com a API de transcrição da OpenAI. |

### 2.3. Síntese de voz — TTS

| Tecnologia                                                  | Tipo                              | Descrição — uma linha                                                                                                                        |
| ----------------------------------------------------------- | --------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| [Breeze TTS 2](https://github.com/breezeblue-ai/breeze-tts) | Modelo TTS e código de inferência | Modelo de síntese de voz em inglês e chinês com geração em streaming, clonagem de voz e controlo da voz por instruções em linguagem natural. |

### 2.4. Ferramentas de produção e experimentação com voz

| Tecnologia                                                    | Tipo              | Descrição — uma linha                                                                                                                             |
| ------------------------------------------------------------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Voicebox — jamiepine](https://github.com/jamiepine/voicebox) | Aplicação com API | Estúdio local de voz que integra vários motores TTS, clonagem de voz e transcrição, disponibilizando funcionalidades por interface gráfica e API. |

### 2.5. Serviços externos de chamadas automatizadas

| Tecnologia                                           | Tipo                        | Descrição — uma linha                                                                                                                           |
| ---------------------------------------------------- | --------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| [PlaceCall](https://github.com/voygr-tech/placecall) | Serviço externo com API/MCP | Serviço que permite a agentes efetuar chamadas para empresas nos EUA a partir de instruções e devolver o resultado, a transcrição e a gravação. |

## 3. Revisão no contexto do projeto

As notas seguintes constituem uma avaliação inicial de enquadramento.

| Tecnologia              | Enquadramento na arquitetura                                    | Observação relevante                                                                                                                                                                  |
| ----------------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Pipecat                 | Camada de agentes e integração entre STT, interpretação e TTS.  | Candidato para coordenar a conversa; inclui suporte para conversas estruturadas através de Pipecat Flows, mas a lógica específica do questionário CATI terá de ser implementada.      |
| LiveKit Agents          | Camada de agentes, sessões de áudio e integração com telefonia. | Outro candidato para a base conversacional; a integração telefónica depende da stack de telefonia LiveKit, não apenas da biblioteca Agents.                                           |
| MOSS-Transcribe-Diarize | Bloco Speech-to-Text e eventual análise das gravações.          | Orientado para transcrição de áudio longo com vários intervenientes; a adequação ao ciclo interativo da entrevista precisa de validação de latência.                                  |
| Parakeet — achetronic   | Serviço associado ao bloco Speech-to-Text.                      | O streaming documentado devolve texto incremental depois do envio integral do áudio; não equivale a receber continuamente o áudio de uma chamada.                                     |
| Breeze TTS 2            | Bloco Text-to-Speech.                                           | O suporte anunciado é inglês e chinês, pelo que não é uma opção principal para entrevistas em português; os pesos e as saídas self-hosted têm restrições de utilização não comercial. |
| Voicebox                | Ferramenta auxiliar para experimentar vozes e gerar áudio.      | Pode apoiar a prototipagem e expõe uma API, mas é uma aplicação que agrega motores de voz, não um modelo TTS nem um framework de telefonia.                                           |
| PlaceCall               | Referência de serviço completo de chamadas automatizadas.       | O foco em empresas nos EUA torna-o sobretudo uma referência comparativa, em vez de uma escolha direta para um CATI dirigido a Portugal.                                               |

## 4. Ligação à arquitetura

| Componente                    | Tecnologias candidatas ou relacionadas                                                             |
| ----------------------------- | -------------------------------------------------------------------------------------------------- |
| Agents                        | Pipecat ou LiveKit Agents, enquanto candidatos à camada de orquestração.                           |
| Speech-to-Text                | MOSS-Transcribe-Diarize ou o servidor Parakeet.                                                    |
| Text-to-Speech                | Breeze TTS 2, com as limitações linguísticas indicadas.                                            |
| Experimentação de voz         | Voicebox, numa categoria auxiliar de experimentação.                                               |
| Serviço externo de referência | PlaceCall, sem o confundir com um servidor SIP ou um fornecedor genérico de transporte telefónico. |

## 5. Projetos Interessantes

[MILAE](https://hellomilae.com/)

A MILAE é um exemplo de aplicação de inteligência artificial conversacional ao contacto telefónico automatizado, orientado para o acompanhamento de pessoas idosas. Através de chamadas regulares, procura perceber como se encontra a pessoa e identificar situações que possam exigir atenção dos serviços de apoio.

Este projeto apresenta uma relação direta com a minha dissertação, Programmable Voice Services for Automated Computer-Assisted Telephone Interviewing, pela utilização de uma interface de voz para automatizar interações telefónicas. A dissertação propõe integrar serviços de telecomunicações programáveis com reconhecimento de fala, interpretação de respostas, gestão do diálogo e síntese de voz, mantendo a possibilidade de intervenção humana em situações ambíguas ou excecionais.

A principal adaptação está na finalidade da conversa: enquanto a MILAE se centra no acompanhamento do bem-estar, o sistema proposto na dissertação destina-se à realização de entrevistas e questionários estruturados. Assim, a lógica de contacto e interação por voz pode servir de referência, mas o diálogo passa a ser orientado por um guião de entrevista, com perguntas, regras de ramificação e critérios de validação das respostas. O objetivo deixa de ser identificar necessidades de apoio e passa a ser recolher informação de forma estruturada, consistente e auditável.

Neste contexto, a MILAE constitui uma referência de aplicação, e não necessariamente uma arquitetura técnica a reproduzir. A sua relevância para a dissertação está no conceito de automatizar contactos telefónicos sem eliminar a supervisão humana, adaptando essa abordagem à recolha de dados e considerando requisitos como consentimento, privacidade e transparência perante os participantes.
