## Cleverson Passos, M.Sc.

Engenheiro eletricista e matemático, mestre em Computação Aplicada. Trabalho com diagnóstico de falhas, confiabilidade e manutenção preditiva de ativos elétricos e rotativos, e desenvolvo os sistemas que aplicam esse conhecimento em produção.

Atualmente sou cofundador da Smart Asset Control, onde respondo pela arquitetura e pelo desenvolvimento da plataforma. Minha trajetória inclui atuação como engenheiro de aplicação na Dynamox, com diagnóstico por análise de vibração em campo, e Product Owner da plataforma de gestão de frotas de motores da WEG.

Curitiba, PR · [LinkedIn](https://www.linkedin.com/in/cleverson-passos-m-sc-2b04383a/) · Português, inglês e espanhol

---

### Smart Asset Control

Plataforma SaaS multi-tenant para gestão de ativos elétricos industriais: ensaios dielétricos, inspeções de campo, diagnóstico, vida útil, confiabilidade e planejamento de manutenção. Inclui backend, aplicação web e aplicativo móvel. O código é proprietário; abaixo, um resumo do trabalho técnico.

**Diagnóstico de isolação de máquinas rotativas**

Sistema especialista que combina os resultados de diferentes ensaios em um diagnóstico rastreável. A base de conhecimento tem cerca de 60 hipóteses de falha, com regras que cruzam os ensaios entre si, por exemplo resistência de isolamento com tan δ e descargas parciais com capacitância, e regras de refutação para evidências contraditórias. A incerteza é tratada com fatores de certeza, priors em log-odds e lógica fuzzy, ponderados por um índice de qualidade dos dados. O sistema sugere o próximo ensaio pelo ganho de informação esperado e foi calibrado contra casos de referência (precision, recall, F1 e Brier score). As regras seguem a metodologia de G. C. Stone.

**Séries temporais e tendência**

Biblioteca estatística própria, com testes, aplicada ao histórico de ensaios de cada ativo:

- estatística robusta (mediana, MAD, z-score robusto, IQR) e regressão linear, quadrática e Theil-Sen;
- detecção de ponto de mudança e estatística circular para ângulos de fase;
- classificação da tendência em dez classes, com ajuste de confiança pela qualidade do histórico;
- projeção do tempo até a métrica atingir a próxima faixa normativa, usada para priorizar os ativos.

**Vida útil e confiabilidade**

Estimativa de vida útil combinando Weibull, modelo exponencial e atualização bayesiana, com fatores de envelhecimento térmico e dielétrico. Vida remanescente por cadeia de Markov sobre estados de saúde. Indicadores de confiabilidade (MTBF, MTTR, disponibilidade) com modos de falha baseados na OREDA e na ISO 14224.

**Ensaios elétricos**

| Ensaio | Referência |
|---|---|
| Resistência de isolamento, IP e DAR, com correção de temperatura | IEEE 43 |
| Tensão aplicada em degraus | IEEE 95 |
| Tan δ e tip-up | IEEE 286, IEC 60894 |
| Descargas parciais (PRPD) | IEC 60034-27-2, IEC 60270 |
| Resistência ôhmica | IEEE 118 |
| Capacitância e PDCA | — |

Para descargas parciais, os padrões PRPD são descritos por doze características por quadrante de fase e classificados por similaridade com protótipos de falha definidos por especialista, com indicação de risco e de confiança.

**Extração de laudos com LLM**

Leitura automática de laudos de ensaio em PDF com Google Gemini, com instruções específicas para cada tipo de ensaio, parsers determinísticos para formatos de diferentes fabricantes e validação por limites físicos antes da gravação. Nos documentos com camada de texto, cada valor extraído pelo modelo é conferido contra o texto original, o que evita gravar números trocados ou inexistentes. Há também um pipeline de OCR em Python (OpenCV e Tesseract) e um assistente que consulta os dados da plataforma por function calling.

**Engenharia**

- Backend em Node.js (Fastify), Prisma e PostgreSQL, com isolamento entre clientes por Row-Level Security.
- Infraestrutura no Google Cloud: Kubernetes (GKE), Cloud Storage, Pub/Sub e Redis. CI/CD com GitHub Actions.
- Observabilidade com OpenTelemetry, Prometheus e Grafana.
- Aplicação web em Next.js e TypeScript.
- Aplicativo móvel em React Native, com funcionamento offline e sincronização para técnicos em campo.
- Cerca de 570 arquivos de testes automatizados nas três aplicações.

---

### Experiência

**Smart Asset Control** · Cofundador · 2025 – atual

**WEG** · Product Owner, WEG Motor Fleet Management · 2022 – 2024
Responsável pela plataforma de monitoramento e gestão preditiva de ativos rotativos, usada em mineração, energia, óleo e gás, papel e celulose e siderurgia. Reestruturei o backlog, coordenei com o time de desenvolvimento a correção de falhas críticas e redesenhei os fluxos de diagnóstico de vibração e de monitoramento de condição.

**Dynamox** · Engenheiro de Aplicação, Confiabilidade e Monitoramento Preditivo · 2018 – 2022
Implantação de monitoramento baseado em condição com sensores sem fio de vibração e temperatura em plantas de mineração, papel e celulose, energia e siderurgia. Diagnóstico de falhas por análise de vibração e termografia (desbalanceamento, desalinhamento, folgas e rolamentos), análise de causa raiz em falhas críticas e elaboração de planos de manutenção preditiva.

Anteriormente, atuei com automação industrial, sensores e IIoT em funções técnicas e técnico-comerciais.

---

### Formação

- Mestrado em Computação Aplicada, Univali
- Engenharia Elétrica, PUC-PR
- Licenciatura em Matemática, UFPR
- Especialização em Engenharia da Manutenção (Manutenção 4.0), UFPR
- Pós-graduação em Análise de Dados e Matemática Aplicada, Ampli
- Pós-graduação em Engenharia de Software com IA Aplicada, UNIPDS (em andamento)

### Certificações

Analista de Vibração ISO 18436 Cat II/III · Google Cloud Associate Cloud Engineer · Vertex AI / Gemini AI Developer · Reliability-Centered Maintenance

### Competências

Python, NumPy, pandas, OpenCV · Node.js, TypeScript, PostgreSQL · Google Cloud, Kubernetes, Docker · análise de vibração, termografia, ensaios dielétricos · confiabilidade e RCA
