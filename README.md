# Olá, eu sou o Cleverson Passos 👋

**Engenheiro Eletricista, Matemático e M.Sc. em Computação Aplicada · Cofundador da Smart Asset Control**
**Diagnóstico, séries temporais e manutenção preditiva de ativos elétricos e rotativos**

Construo sistemas que transformam medições de campo em decisão de manutenção: diagnóstico explicável, análise de tendência e vida remanescente, extração de laudos com LLM e plataformas de produção que rodam isso em escala.

Trabalho há mais de uma década com ativos industriais críticos. Na **Dynamox** diagnostiquei falhas por análise de vibração e implantei monitoramento preditivo em plantas de mineração, energia e celulose. Na **WEG** fui Product Owner da plataforma de gestão de frotas de motores. Hoje eu mesmo implemento esses sistemas, de ponta a ponta: sei interpretar um padrão PRPD anômalo e também escrever o código que o classifica.

📍 Curitiba, PR · 🔗 [LinkedIn](https://www.linkedin.com/in/cleverson-passos-m-sc-2b04383a/) · 🗣️ Português (nativo) · Inglês (profissional) · Espanhol (fluente)

---

## 🔧 Smart Asset Control: o que eu venho construindo

SaaS multi-tenant para **gestão de ativos elétricos industriais**: ensaios dielétricos, inspeções de campo, diagnóstico, vida útil, confiabilidade e PCM. Tem backend, aplicação web e app mobile, todos em produção. O código é proprietário; abaixo descrevo **o que foi implementado e como**.

### 🧠 Sistema especialista de diagnóstico explicável
Diagnóstico cruzado de máquinas rotativas que combina evidências de vários ensaios e gera uma conclusão técnica rastreável.

- **Base de conhecimento:** cerca de 60 hipóteses de falha organizadas em famílias, com regras cruzadas entre ensaios (IR × tan δ, Capacitância × tan δ, PRPD × tan δ × Cap, Step Voltage × IR × PDCA, ôhmica × dielétrico) e **regras de refutação** para tratar evidências contraditórias.
- **Raciocínio sob incerteza:**
  - fatores de certeza no estilo **MYCIN**;
  - priors em **log-odds**;
  - **lógica fuzzy** com funções triangular, trapezoidal, sigmoide e gaussiana;
  - **índice de qualidade de dados** (completude, validade, consistência e repetibilidade).
- **Recomendação do próximo ensaio** por **entropia de Shannon e ganho de informação**: o sistema sugere o ensaio que mais reduz a incerteza do diagnóstico.
- **Calibração do motor:**
  - métricas precision, recall, F1, balanced accuracy e **Brier score**;
  - pesos otimizados por **grid search** contra casos de referência.
- **Health score hierárquico** em três níveis (ensaio, mecanismo e ativo), com penalidade dominante e penalidades secundárias limitadas.

### 📈 Séries temporais, anomalias e tendência
Biblioteca estatística própria, com testes unitários, aplicada ao histórico de ensaios de cada ativo:

- **Estatística robusta:** mediana, MAD, z-score robusto, IQR e percentis.
- **Regressão:** linear, quadrática e **Theil-Sen** (robusta a outliers).
- **Correlação:** Pearson, Spearman e correlação cruzada.
- **Suavização:** médias móveis simples, ponderada e exponencial (EMA).
- **Detecção de ponto de mudança (change-point):** minimiza a soma dos erros de duas regressões.
- **Estatística circular:** para ângulos de fase de descargas parciais.
- **Detecção de anomalias:** z-score e IQR com níveis de severidade.
- **Classificador de tendência em 10 classes** (degradação, recuperação, estável…). Combina slope e R², compara janela curta com janela longa e verifica monotonicidade, ajustando a confiança pela qualidade e pela comparabilidade do histórico.
- **Projeção até o limite normativo:** taxa de degradação mensal e **meses até a métrica cruzar a próxima faixa** (IEEE/IEC). Os ativos são ranqueados por urgência.

### ⏳ Vida útil, RUL e confiabilidade
- **Ensemble de vida útil:**
  - **Weibull:** R(t), taxa de falha e vidas B10/B50;
  - **exponencial;**
  - **bayesiano conjugado Normal-Normal**, com prior vindo de curvas de referência e intervalo de confiança de 95%.
- **Fatores de envelhecimento:** térmico (regra de Arrhenius simplificada) e dielétrico.
- **Curva P-F.**
- **RUL por cadeia de Markov:** matriz de transição entre estados de saúde, com sugestão da data do próximo ensaio.
- **Confiabilidade:** MTBF, MTTR, disponibilidade e OEE, com modos de falha baseados na base **OREDA** e taxonomia **ISO 14224**.
- **Risk score de ativos críticos:** combina health score, tempo desde o último ensaio e criticidade.

### ⚡ Domínio: ensaios elétricos e normas
Um analisador por ensaio, com regras rastreáveis até a norma:

| Ensaio | Análises implementadas | Normas |
|---|---|---|
| Resistência de isolamento | IP, DAR, correção de temperatura, modelo de absorção, platô, R1 mínimo | IEEE 43 |
| Tensão em degraus (Step Voltage) | Não linearidade por degrau | IEEE 95 |
| Tan δ / Fator de dissipação | Tip-up e tip-up incremental, tendência histórica | IEEE 286, IEC 60894 |
| Capacitância | ΔC%, dC/dV, d²C/dV², tensão de início | — |
| Descargas parciais (PRPD) | Qmax, Q95, NQN, assimetria de polaridade, clusters, PDIV/PDEV, **classificação de padrões de falha** | IEC 60034-27-2, IEC 60270 |
| PDCA | Ajuste de decaimento em lei de potência | — |
| Resistência ôhmica | Correção de temperatura do cobre, desbalanço entre fases | IEEE 118 |

**Classificação de padrões PRPD:** um vetor de 12 features por quadrante de fase (magnitude média por quadrante, assimetrias de magnitude e densidade, concentração angular, dispersão) é normalizado e comparado por **distância euclidiana a protótipos de falha** definidos por especialista (microvazios, delaminação, descarga de ranhura etc.). O resultado inclui risco e confiança. Há também um classificador heurístico por quadrante baseado no Anexo A da IEC 60034-27-2.

As regras de diagnóstico da isolação seguem a metodologia de G. C. Stone (*Electrical Insulation for Rotating Machines*). Inspeções de campo cobrem estator, rotor, mancais (incluindo marcas de descarga), excitação, porta-escovas, **mapa corona**, refrigeração e lubrificação.

### 🤖 LLM e extração de documentos
- **Extração de laudos em PDF com Google Gemini:**
  - prompts específicos para cada tipo de ensaio, com temperatura baixa;
  - parsers determinísticos para laudos de três fabricantes diferentes, com detecção automática do formato;
  - **validação por regras físicas** antes de gravar no banco.
- **Conferência anti-alucinação:** todo número devolvido pelo modelo precisa aparecer literalmente na camada de texto do PDF. Assim, um dígito trocado ou um valor inventado é detectado antes de virar dado.
- **Pipeline em Python:** OpenCV (limiarização, morfologia e contornos para detectar tabelas) com Tesseract, e LLM como alternativa quando o OCR clássico não resolve.
- **Análise de imagens multimodal** com Gemini Vision para as fotos de inspeção de campo.
- **Conclusões técnicas geradas por LLM** sobre os resultados dos analisadores determinísticos (tan δ, step voltage, PDCA e inspeções).
- **SmartBot:** assistente com **function calling**, que consulta cerca de 30 ferramentas de diferentes áreas do sistema e responde com gráficos e ações.

### 🏗️ Engenharia de produção
- **Backend:**
  - Node.js (Fastify), Prisma e PostgreSQL, com cerca de 355 modelos de dados e 31 domínios;
  - **multi-tenant com Row-Level Security** e testes de isolamento entre tenants.
- **Infra:**
  - Kubernetes (GKE) com ambientes dev, staging e production, HPA e PDB;
  - CI/CD em GitHub Actions, com guarda de migrations e varredura de segredos;
  - GCS, Pub/Sub, Redis e padrão outbox.
- **Observabilidade:** OpenTelemetry, Prometheus, Grafana e Alertmanager.
- **Qualidade:**
  - cerca de **570 arquivos de teste** nas três aplicações (Jest, Supertest, Testing Library e Playwright com regressão visual);
  - ESLint com regras de segurança e SonarQube.
- **Web:**
  - Next.js e TypeScript, TanStack Query, ECharts (camada própria de gráficos com bandas normativas e regressão);
  - interface em pt-BR e en.
- **Mobile:**
  - React Native com Expo e Clean Architecture;
  - **offline-first**: fila persistente com idempotência, caixa de conflitos e storage particionado por tenant;
  - leitura de QR code, captura de fotos com fila de upload e push com FCM.

---

## 🧭 Trajetória em manutenção preditiva

**Smart Asset Control** · Cofundador, arquitetura e desenvolvimento · *2025 – hoje*
Plataforma descrita acima, construída do zero: da arquitetura e infraestrutura ao diagnóstico e ao app de campo.

**WEG** · Product Owner, WEG Motor Fleet Management (MFM) · *2022 – 2024*
- Fui Product Owner da plataforma de monitoramento e gestão preditiva de ativos rotativos, usada por indústrias de mineração, energia, óleo e gás, papel e celulose e siderurgia.
- Reestruturei o backlog por valor de negócio e liderei, com o time de desenvolvimento, a correção de falhas críticas.
- Redesenhei os fluxos de diagnóstico de vibração e de monitoramento de condição, seguindo a ISO 10816.
- Traduzi a necessidade de grandes contas industriais em requisitos, a partir de visitas técnicas em campo.

**Dynamox** · Engenheiro de Aplicação, Confiabilidade e Monitoramento Preditivo · *2018 – 2022*
- Conduzi projetos completos de monitoramento baseado em condição (CbM) com sensores wireless de vibração e temperatura em motores, redutores, ventiladores, bombas e compressores.
- Diagnostiquei falhas por análise de vibração e termografia: desbalanceamento, desalinhamento, folgas e defeitos de rolamento (ISO 10816 / ISO 20816).
- Apliquei RCA e FMEA em falhas críticas e criei planos de manutenção baseada em condição no lugar de estratégias por tempo.
- Treinei equipes de manutenção dos clientes na interpretação de espectros de vibração.

Antes disso, trabalhei com automação industrial, sensores e IIoT (suporte técnico e desenvolvimento técnico-comercial).

---

## 🎓 Formação e certificações

- **Mestrado em Computação Aplicada** · Univali
- **Engenharia Elétrica** · PUC-PR
- **Licenciatura em Matemática** · UFPR
- **Especialização em Engenharia da Manutenção (Manutenção 4.0)** · UFPR
- **Pós-graduação em Análise de Dados e Matemática Aplicada** · Ampli
- **Pós em Engenharia de Software com IA Aplicada** · UNIPDS *(em curso)*

**Certificações:** Analista de Vibração ISO 18436 Cat II/III · Google Cloud Associate Cloud Engineer · Vertex AI / Gemini AI Developer · Reliability-Centered Maintenance

---

## 🛠️ Stack

**Dados e IA:** Python · NumPy · pandas · OpenCV · Tesseract · Google Gemini (LLM, function calling) · estatística robusta · inferência bayesiana · sistemas especialistas

**Engenharia:** Node.js · TypeScript · Fastify · Prisma · PostgreSQL · Redis · Next.js · React Native/Expo

**Infra:** GCP · Kubernetes (GKE) · Docker · GitHub Actions · OpenTelemetry · Prometheus · Grafana

**Domínio:** análise de vibração (ISO 10816/20816) · termografia · RCA/FMEA · IEEE 43 · IEEE 95 · IEEE 286 · IEC 60034-27 · IEC 60270 · ISO 14224 · confiabilidade (Weibull, MTBF/MTTR) · PCM

---

## 📌 Em andamento

- Projeto público de **detecção de falhas em rolamentos por vibração**: features espectrais, validação temporal, monitoramento de drift e API de inferência. *(em breve)*
