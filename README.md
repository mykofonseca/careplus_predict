
# CarePlus Predict

**Plataforma de Predição e Integração da Saúde com Inteligência Artificial Adaptativa**

[![FIAP](https://img.shields.io/badge/FIAP-Next%20Gen%201CCPS-blue)](https://www.fiap.com.br)
[![Care Plus](https://img.shields.io/badge/Parceiro-Care%20Plus%20%7C%20Bupa-blue)](https://www.careplus.com.br)
[![License](https://img.shields.io/badge/license-MIT-green)](./LICENSE)
[![Status](https://img.shields.io/badge/status-Em%20Desenvolvimento-yellow)]()

---

## Sobre o Projeto

O **CarePlus Predict** é uma plataforma integrada de saúde desenvolvida para unificar, monitorar e prever o estado de saúde de pacientes por meio de Inteligência Artificial adaptativa e coleta contínua de dados biométricos via wearables. O projeto é uma iniciativa acadêmica da **FIAP — Faculdade de Informática e Administração Paulista**, turma **1CCPS**, em parceria com a **Care Plus (Part of Bupa)**.

### Problemas Endereçados

- **Fragmentação de dados de saúde** — informações distribuídas em múltiplos sistemas sem integração, impossibilitando uma visão holística do paciente.
- **Ausência de ferramentas preditivas acessíveis** — a prevenção é relegada ao segundo plano por falta de tecnologia escalável e de baixo custo operacional.
- **Monitoramento descontinuado** — o acompanhamento da saúde ocorre apenas em consultas pontuais, sem continuidade entre os atendimentos.

### Pilares da Solução

| Pilar                   | Descrição                                                                                       |
| ----------------------- | ------------------------------------------------------------------------------------------------- |
| Plataforma Unificada    | Centralização dos dados de saúde do paciente em repositório único, seguro e acessível       |
| IA Preditiva Adaptativa | Modelos de ML treinados continuamente com dados reais do usuário para antecipar riscos de saúde |
| Monitoramento Contínuo | Coleta permanente de dados biométricos via wearables e APIs de saúde integradas                 |

---

## Arquitetura do Sistema

```
+----------------------------------------------------------+
|                     CAMADA DE COLETA                     |
|   Samsung Wearable -> Samsung Health SDK -> App Mobile   |
|   APIs de Prontuario -> APIs de Laboratorio              |
+---------------------------+------------------------------+
                            |
+---------------------------v------------------------------+
|              PIPELINE DE DADOS EM TEMPO REAL             |
|          Apache Kafka -> Apache Flink / Spark            |
|               TimescaleDB + Redis Cache                  |
+---------------------------+------------------------------+
                            |
+---------------------------v------------------------------+
|             FEATURE ENGINEERING (Python)                 |
|   Normalizacao -> Features Temporais -> Correlacao       |
|          Janelas: 1h | 6h | 24h | 7d | 30d              |
+---------------------------+------------------------------+
                            |
+---------------------------v------------------------------+
|              CAMADA DE MODELOS PREDITIVOS                |
|   Risco Cardiovascular (XGBoost)                         |
|   Deteccao de Anomalias (LSTM Autoencoder)               |
|   Qualidade do Sono (Random Forest + CNN)                |
|   Risco de Recaida (HealthFormer Transformer)            |
|   Estresse / Saude Mental (SVM + HRV features)           |
+---------------------------+------------------------------+
                            |
+---------------------------v------------------------------+
|          APRENDIZADO CONTINUO (Continual Learning)       |
|   Modelo Global Base -> Fine-Tuning Pessoal              |
|   Federated Learning + EWC                               |
+---------------------------+------------------------------+
                            |
+---------------------------v------------------------------+
|            ALERTAS, INSIGHTS E DASHBOARD                 |
|   Notificacoes Push | Relatorios Semanais (LLM)          |
|   Dashboard Clinico | Explicabilidade via SHAP           |
+----------------------------------------------------------+
```

---

## IA Adaptativa

Inspirado no modelo do **Google NotebookLM**, o CarePlus Predict cria um modelo de saúde personalizado por paciente, alimentado continuamente pelos dados coletados em tempo real:

| NotebookLM (referência)                         | CarePlus Predict                                     |
| ------------------------------------------------ | ---------------------------------------------------- |
| Documentos do usuário como base de conhecimento | Dados biométricos e histórico clínico do paciente |
| Responde perguntas com base nos documentos       | Prediz riscos com base no perfil individual          |
| Aprende com novos documentos adicionados         | Aprende com novos dados do wearable e consultas      |
| Modelo isolado por usuário                      | Modelo de saúde isolado e privado por paciente      |

### Modelos Preditivos

| Modelo                   | Algoritmo                  | Input Principal                    | Output                       | Precisão-Alvo   |
| ------------------------ | -------------------------- | ---------------------------------- | ---------------------------- | ---------------- |
| Risco Cardiovascular     | XGBoost                    | HR, HRV, SpO2, histórico clínico | Score de risco 0–100        | > 92% (F1-Score) |
| Detecção de Anomalias  | LSTM Autoencoder           | Séries temporais contínuas       | Alerta de desvio             | > 95%            |
| Qualidade do Sono        | Random Forest + CNN        | Acelerômetro, HR noturno          | Classificação de qualidade | > 90%            |
| Risco de Recaída        | Transformer (HealthFormer) | Histórico clínico + wearable     | Janela de risco              | > 88%            |
| Estresse e Saúde Mental | SVM + HRV features         | HRV, padrões de movimento         | Índice de estresse          | > 85%            |

### Ciclo de Aprendizado Contínuo

1. **Modelo Base Global** — treinado offline com datasets clínicos anonimizados (PhysioNet, MIMIC-III, dados parceiros CarePlus)
2. **Fine-Tuning Personalizado** — refinamento com dados específicos do usuário via transfer learning
3. **Atualização Incremental** — ciclos diários de 24h sem necessidade de retreinar do zero
4. **Elastic Weight Consolidation (EWC)** — previne esquecimento catastrófico de padrões anteriores
5. **Federated Learning** — modelos atualizados localmente no dispositivo; apenas gradientes trafegam para o servidor

---

## Integrações via API

| API / Sistema                | Dados Acessados                                | Protocolo     | Prioridade |
| ---------------------------- | ---------------------------------------------- | ------------- | ---------- |
| Samsung Health SDK 6.0+      | HR, HRV, SpO2, sono, composição corporal     | SDK Nativo    | Alta       |
| RNDS (Ministério da Saúde) | Sumário de alta, vacinação, resultados SADT | FHIR R4       | Alta       |
| DASA / Fleury                | Resultados de exames laboratoriais             | HL7 FHIR      | Alta       |
| Conexa Saúde                | Histórico de consultas, prescrições         | REST + OAuth2 | Alta       |
| Google Fit / Apple HealthKit | Dados de usuários iOS/Android não Samsung    | REST OAuth2   | Média     |
| OpenAQ / IQAir               | Qualidade do ar por geolocalização           | REST          | Baixa      |
| Receita Federal CPF API      | Validação de identidade do paciente          | REST          | Alta       |

**Padrão de interoperabilidade:** HL7 FHIR R4 com SMART on FHIR para autenticação e CDS Hooks para suporte à decisão clínica. Resources utilizados: `Patient`, `Observation`, `DiagnosticReport`, `MedicationRequest`, `Condition`, `Encounter`, `AllergyIntolerance`.

### API Gateway

- Rate Limiting e Throttling por endpoint
- Circuit Breaker Pattern para degradação graciosa em falhas externas
- Cache inteligente com TTL configurável para dados de baixa frequência de alteração
- Retry exponencial com backoff para falhas temporárias de rede
- Webhook Support para notificações push de sistemas externos

---

## Stack Tecnológica

### Front-End e Mobile

| Tecnologia                | Finalidade                                         |
| ------------------------- | -------------------------------------------------- |
| React Native 0.73+        | Framework unificado para Android e Web             |
| Expo SDK                  | Ferramentas de desenvolvimento e distribuição    |
| TanStack Query            | Gerenciamento de estado assíncrono e cache        |
| Victory Native / Recharts | Visualizações de dados biométricos e dashboards |

### Back-End

| Tecnologia        | Finalidade                                               |
| ----------------- | -------------------------------------------------------- |
| Node.js + Fastify | API RESTful de alta performance com suporte a WebSockets |
| Python + FastAPI  | Microsserviço dedicado aos modelos de ML                |
| Apache Kafka      | Ingestão de streams de dados do wearable                |
| Redis             | Cache de sessão, rate limiting e pub/sub para alertas   |

### Banco de Dados

| Tecnologia            | Finalidade                                                    |
| --------------------- | ------------------------------------------------------------- |
| PostgreSQL 16         | Dados relacionais: usuários, consentimentos, configurações |
| TimescaleDB           | Séries temporais biométricas (extensão do PostgreSQL)      |
| MongoDB               | Documentos FHIR e dados semi-estruturados de prontuários     |
| MinIO (S3-compatible) | Artefatos de modelo e dados brutos de wearable                |

### Machine Learning e IA

| Tecnologia                       | Finalidade                                              |
| -------------------------------- | ------------------------------------------------------- |
| PyTorch / TensorFlow             | Treinamento de modelos LSTM, Transformer e CNN          |
| Scikit-learn / XGBoost           | Gradient boosting para risco cardiovascular             |
| SHAP                             | Explicabilidade das predições (XAI)                   |
| Flower (flwr)                    | Framework de Federated Learning                         |
| MLflow                           | Rastreamento de experimentos e versionamento de modelos |
| ONNX Runtime                     | Otimização de modelos para execução on-device       |
| TensorFlow Lite / PyTorch Mobile | Inferência diretamente no dispositivo Android          |

---

## Segurança e Conformidade

O projeto adota uma abordagem **Privacy-by-Design** em todas as camadas do sistema.

| Aspecto                              | Implementação                                                         |
| ------------------------------------ | ----------------------------------------------------------------------- |
| Criptografia em trânsito            | TLS 1.3                                                                 |
| Criptografia em repouso              | AES-256 para dados biométricos                                         |
| Privacidade dos dados de treinamento | Federated Learning — dados brutos nunca deixam o dispositivo           |
| Consentimento                        | Granular, controlado pelo usuário e revogável a qualquer momento      |
| Arquitetura de acesso                | Zero Trust — autenticação e autorização verificadas em cada camada |
| Anonimização                       | Dados de treinamento global irrevertíveis ao paciente original         |
| Rastreabilidade                      | Audit Trail completo com log imutável de todas as operações          |
| Explicabilidade                      | SHAP values para justificar cada predição gerada (XAI)                |

### Regulamentações

| Regulamento                                | Status                                            |
| ------------------------------------------ | ------------------------------------------------- |
| LGPD (Lei 13.709/2018)                     | Conformidade total — DPO nomeado, RIPD elaborado |
| ANVISA RDC 657/2022 — SaMD Classe II      | Processo de certificação em andamento           |
| CFM Resolução 2.314/2022 — Telemedicina | Em adequação                                    |
| ISO 27001 — Segurança da Informação    | Roadmap para certificação em 12 meses           |
| HL7 FHIR R4                                | Implementado na arquitetura                       |
| RNDS (Portaria 1.434/2020)                 | Integração planejada para fase 2                |

---

## Métricas de Sucesso

| Métrica                                     | Meta                                    |
| -------------------------------------------- | --------------------------------------- |
| APIs integradas e funcionais                 | 3+ na fase 2; 8+ na fase 3              |
| Precisão preditiva — modelo cardiovascular | >= 92% (F1-Score)                       |
| Latência de resposta da API                 | < 2 segundos para 95% das requisições |
| Uptime da plataforma                         | >= 99,5% mensal                         |
| Cobertura de testes automatizados            | >= 80% do código de produção         |
| Ciclo de atualização do modelo pessoal     | 24 horas                                |
| Tamanho máximo do modelo on-device          | < 50 MB                                 |

---

## Roadmap de Desenvolvimento

| Fase                    | Período      | Entregas Principais                                         | Status       |
| ----------------------- | ------------- | ----------------------------------------------------------- | ------------ |
| Fase 1 — MVP           | Concluída    | App móvel, UI, simulações de dados, arquitetura técnica | Concluído   |
| Fase 2 — Integração  | Mar–Mai 2025 | APIs reais, modelos preditivos v1, conformidade LGPD        | Em andamento |
| Fase 3 — IA Adaptativa | Mai–Jul 2025 | Federated Learning, fine-tuning personalizado               | Planejado    |
| Fase 4 — Escala        | Jul–Ago 2025 | Certificação ANVISA, FHIR completo, novos wearables       | Planejado    |
| Fase 5 — Expansão     | Ago+          | B2B para hospitais, internacionalização                   | Conceito     |

---

## Equipe

| Nome                         | RM     |
| ---------------------------- | ------ |
| Gustavo Franzotti Gonçalves | 566983 |
| Lincoln Simão Pereira       | 567284 |
| Maykon Santana Fonseca       | 567041 |
| Nicolas Sakaue Nishimura     | 567752 |

**Scrum Master:** Allan Roberto
**Instituição:** FIAP — Faculdade de Informática e Administração Paulista
**Turma:** 1CCPS | Projeto Next Gen | 2025

---

## Licença

Este projeto está licenciado sob os termos descritos no arquivo [LICENSE](./LICENSE).
