# Pantanal Vivo — Modelagem DPSIR e Mapa Cognitivo Fuzzy Participativo

**Aplicativo:** https://ernandes-sobreira.github.io/pantanal-vivo/

Aplicação científica colaborativa para discutir o Pantanal como sistema socioecológico, com salas por código, rede causal dirigida, matriz de influências, indicadores, avaliação independente de pesos e análise exploratória de consensos.

## Trabalho em grupo

- Crie salas independentes com a chave de facilitador e compartilhe o código de 8 caracteres ou QR Code.
- Adicione conceitos e temas centrais múltiplos (por exemplo, Água, Carbono no solo, Carbono na biomassa aérea).
- Classifique variáveis não centrais em D (forças motrizes), P (pressões), S (estado), I (impactos) e R (respostas).
- Crie setas causais com sinal positivo ou negativo e intensidade de 0 a 100. O peso armazenado equivale à intensidade dividida por 100.
- Arraste as bolinhas, organize o conjunto em torno dos temas centrais, analise influências diretas e caminhos exploratórios de duas etapas.
- Salas e dados persistem no Supabase. Salve versões, exporte CSV, PNG ou JSON e restaure um projeto quando necessário.

## Métricas e redução da arbitrariedade

A aba **Métricas** implementa um protocolo inicial de registro:

1. **Indicador da variável:** registre definição operacional, valor observado, unidade, período e fonte, e opcionalmente limites de referência. A normalização é `(valor - mínimo)/(máximo - mínimo)`, limitada ao intervalo 0–1 para visualização. Os limites precisam ser declarados, não são inferidos.
2. **Avaliação de cada ligação:** cada aparelho pode enviar uma intensidade 0–100, sinal +/−, confiança ordinal de 1 a 3, natureza da evidência (percepção, conhecimento local, literatura, dados) e referência/contexto.
3. **Resumo robusto:** exibe número de avaliações, acordo sobre o sinal, mediana de intensidade **entre avaliações com sinal predominante**, dispersão Q1–Q3 calculada **sobre todas as intensidades**, confiança mediana e contagem declarada de tipos de evidência.
4. **Adoção pelo facilitador:** para transformar as avaliações em peso de rede exige pelo menos **3 avaliações e ≥ 2/3 de concordância de sinal**, contando notas de intensidade zero como *ausência de influência*, não como votos a favor de qualquer sinal. O sistema não adota automaticamente o consenso e bloqueia a adoção quando não há concordância suficiente.

**Limitação:** avaliações são identificadas por token local do navegador, não por autenticação pessoal. Uma pessoa com múltiplos navegadores pode avaliar mais de uma vez; a quantidade representa *avaliações por identificador de navegador*, não necessariamente indivíduos distintos. Os pesos são julgamentos estruturados, não efeitos causais identificados. A concordância entre participantes não elimina viés de seleção, confundidores hidrológicos ou efeitos espaciais/temporais. Dados, revisão de literatura e desenhos apropriados de validação continuam necessários.

A normalização de variável e o peso causal são objetos distintos. O sinal positivo significa que o aumento da origem tende a aumentar o destino; não significa benefício ecológico.

## Abas

- **Rede:** conceitos, múltiplos centros, relações dirigidas, edição e análise focal.
- **Matriz:** influência assinada de −100 a +100; valor `null` no JSON = ligação ausente, valor 0 = relação registrada com peso zero.
- **DPSIR:** classificação dos conceitos comuns; os temas centrais aparecem em seção separada.
- **Resultados:** rankings de intensidades e participação, visualizações e versões arquivadas.
- **Métricas:** fichas quantitativas, votação de peso e consenso.
- **Sobre:** instruções e limitações.

## Exportação e preservação

O JSON conserva `nodes`, `edges`, a matriz reconstruível, resumo das métricas e, **quando exportado pelo facilitador após consultar o servidor**, avaliações individuais pseudonimizadas (`ratings`) que podem ser restauradas. Versões arquivadas no banco incluem avaliações e valores observados. Arquivos de versões antigas continuam compatíveis (sem novas colunas).

Não use valores demonstrativos de treinamento como parâmetros empíricos. Registre o recorte espacial/temporal e evidências sempre que possível.

## Infraestrutura e segurança

- Frontend hospedado no GitHub Pages a partir da branch `main`, por GitHub Actions.
- Backend: Supabase Edge Function `pantdpsir`, tabelas `pantdpsir_rooms`, `pantdpsir_nodes`, `pantdpsir_edges`, `pantdpsir_ratings`, `pantdpsir_snapshots`.
- A API valida o código de cada sala e controla operações exclusivas do facilitador. RLS está habilitada nas tabelas e o acesso direto anônimo às tabelas não está habilitado.
- **A chave privada do facilitador não deve aparecer no código público nem em QR Codes dos alunos**.
- Códigos de salas funcionam como convites de edição compartilhada, não como autenticação pessoal segura. Não registre dados pessoais sensíveis.
- Recomenda-se exportar JSON periodicamente; hospedagem estática não armazena os registros e o projeto Supabase pode ficar indisponível.

**Publicação:** confira **Settings → Pages → Build and deployment → GitHub Actions** e a aba **Actions** do repositório. O workflow de publicação está em `.github/workflows/pages.yml`. O código estar commitado não confirma, por si só, que o site já esteja online.
