---
name: resumos-upt-direito
description: "Formata resumos e apontamentos de Direito no template do Departamento de Direito da Universidade Portucalense (UPT): capa, índice, rodapé com logótipo, contracapa, tabelas e esquemas, em PDF. Usar para formatar um resumo no template UPT; não usar para resumir ou escrever conteúdo jurídico novo."
metadata:
  author: Gonçalo Pereira Ferreira
  version: '1.0'
---

# Resumos no template UPT — Departamento de Direito

## Regras de conteúdo (obrigatórias)
- Agir como puro formatador/editor: usar única e estritamente o texto fornecido pelo utilizador. Proibido completar, corrigir juridicamente ou inferir dados da base de conhecimento geral.
- Não resumir: preservar o texto integral, pela ordem original. Só correções ortográficas triviais; manter a redação, repetições e eventuais incoerências da fonte.
- Tabelas, caixas, esquemas e legendas só reorganizam o que o texto diz. Rótulos neutros (ex.: «Nota», «Exemplo», número do artigo citado) são permitidos.
- Todo o output em português de Portugal.
- Na mensagem final, não afirmar que o conteúdo foi validado ou corrigido juridicamente.

## Dados da capa
Pedir ao utilizador (ou usar os que der) e passar em `meta.json`: `titulo`, `subtitulo` (ex.: «Resumo»), `curso`, `ano_letivo`, `ano_curso`, `uc`, `docente`, `universidade`, `estudante`, `numero`. Campos não fornecidos ficam vazios e são omitidos — nunca inventar. Valores habituais do utilizador: curso «Direito», universidade «Universidade Portucalense», estudante «Gonçalo Pereira Ferreira», número «50205»; confirmar título, UC, ano letivo, ano do curso e docente para cada resumo.

## Fluxo
1. Extrair o texto da fonte (PDF/DOCX/texto colado) para um ficheiro de texto e lê-lo inteiro.
2. Ler `references/componentes.md` e escrever o corpo em HTML (`corpo.html`, pode ser dividido em vários ficheiros) com capítulos `<h2 class="ch" id="cN">`, caixas, tabelas e esquemas em HTML (nunca SVG com texto).
3. Escrever `meta.json` e correr:
   `python3 scripts/build.py --meta meta.json --body corpo.html --out Resumo-<tema>.pdf --workdir build`
   O script copia fontes e logótipos de `assets/`, gera capa, índice com números de página, rodapé (logótipo de Direito + «IMP.GE.208.3 | n.º»), contracapa em página par e paginação corrida (capítulo só começa em página nova se faltarem menos de ~8 cm).
4. Verificar a fidelidade: comparar as palavras do texto fonte com o texto do PDF (`pdftotext`) e repor qualquer frase em falta.
5. Renderizar as páginas em PNG e inspecionar todas: texto cortado, títulos órfãos, esquemas legíveis, páginas meio vazias. Corrigir e voltar ao passo 3.
6. Entregar o PDF.

## Ficheiros
- `templates/head.html` — CSS completo do template (Helvetica Neue, vermelho `#e40139`, cinzentos), capa e índice com marcadores `{{…}}`. Alterar só se o utilizador pedir outro estilo.
- `templates/back.html` — contracapa (logótipo UPT azul, upt.pt, ficha, quadrados vermelhos).
- `assets/fonts/` — Helvetica Neue (do template oficial DD_Templates-Documentos 2024-25).
- `assets/logos/` — `capa_dd.jpg` (fundo da capa com aspas e logótipo DD), `dd_rodape.png` (logótipo do rodapé), `upt_azul.png` (contracapa).
- `scripts/build.py` — montagem e geração do PDF (requer `weasyprint`, `pypdf`).

## Problemas conhecidos
- `url()` no CSS sem aspas extra: um erro de aspas faz as fontes falharem sem aviso; confirmar com `pdffonts` que só aparecem HN/HNL.
- Não usar a classe `toc` em títulos (força quebra de página); para entradas do índice usar `<h3 class="idx" id="…">`.
