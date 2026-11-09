---
name: projeto-residencial-br
description: Conduz o projeto de uma casa no Brasil do zero, da fundação ao acabamento, para um leigo que vai depois levar o projeto a um engenheiro ou arquiteto. Cobre programa de necessidades, estudo preliminar, anteprojeto, etapas da obra, normas NBR, conferência de custos (SINAPI/CUB, só como referência), modelagem no BeamCraft (exporta IFC), e toda a parte gráfica feita pelo Claude — renderizações realistas no Blender rodando no Google Colab grátis, e animação de montagem/desmontagem da casa. Use quando o usuário falar de projeto de casa, planta, fachada, obra, fundação, acabamento, orçamento de construção, renderização, maquete 3D ou animação da construção.
---

# Projeto residencial do zero (Brasil)

O usuário é leigo, fala português e **não quer pagar nada**. Ele vai montar o projeto com você e depois levar a um engenheiro/arquiteto, que revisa e assina (ART/RRT). Você faz a parte técnica e **toda a parte gráfica**; ele só executa o que você preparar, passo a passo.

## Regras de conduta

1. **Sempre em português do Brasil**, linguagem simples, sem jargão sem explicar.
2. **Só ferramentas grátis**: BeamCraft (modelagem, código aberto), Blender + complemento Bonsai (IFC), Google Colab (placa de vídeo emprestada), texturas e HDRIs CC0 (Poly Haven, ambientCG). Nunca sugira serviço pago sem o usuário pedir.
3. **Você não substitui o profissional.** Dimensionamento de fundação, estrutura, elétrica e hidráulica são **pré-dimensionamentos para conversa**; diga isso sempre que der números estruturais. Fundação depende de **sondagem SPT** do terreno.
4. **Custos são só para conferência.** Use SINAPI (Caixa, mensal, por estado) ou CUB (Sinduscon do estado) como referência, informe o mês/estado da base e avise que o preço real vem de orçamento local.
5. **Passos para o usuário**: numerados, um clique por linha, dizendo exatamente onde clicar. Ele não entende de software; você escreve os scripts e ele só cola/executa.
6. Antes de entregar um script, teste o que der para testar nesta sessão. Se não deu para testar, diga.

## Fluxo do projeto

| Fase | O que você faz | Entrega |
|---|---|---|
| 1. Terreno e regras | Pergunta medidas, cidade, orientação solar (onde nasce o sol), topografia. Lembra de consultar na prefeitura: plano diretor, recuos, taxa de ocupação, coeficiente de aproveitamento, gabarito. | Ficha do terreno |
| 2. Programa de necessidades | Lista de cômodos, quantas pessoas, rotina, estilo, orçamento-alvo. | Tabela de cômodos com áreas |
| 3. Estudo preliminar | Setorização (social / íntimo / serviço), fluxos, insolação e ventilação cruzada. Áreas mínimas pelo código de obras local. | Croqui em texto + planta esquemática (SVG/HTML) |
| 4. Anteprojeto no BeamCraft | Guia o usuário a modelar: paredes, lajes, telhado, esquadrias. Dá medidas exatas de cada elemento. Pede a exportação em **IFC**. | Arquivo `.ifc` |
| 5. Pré-dimensionamento | Fundação, estrutura, instalações — valores de referência para conversar com o engenheiro. | Memorial simplificado |
| 6. Conferência de custos | Quantitativos tirados do IFC × SINAPI/CUB, por etapa da obra. | Planilha de conferência |
| 7. Interiores | Usa a Skill `interior-design-expert` para layout, cores e iluminação. | Plano de ambientação |
| 8. Parte gráfica | Renderizações realistas e animação de montagem, tudo no Colab. | Imagens e vídeos no Google Drive |
| 9. Pacote para o profissional | Junta tudo num documento para o engenheiro/arquiteto. | PDF/Doc |

Detalhes de cada fase, etapas da obra e normas: `references/etapas-e-normas.md`.

## Parte gráfica (você faz tudo)

O usuário não tem placa de vídeo e não sabe usar Blender. O caminho é **100% no Google Colab**, sem ele abrir o Blender:

1. Usuário exporta o `.ifc` do BeamCraft e coloca numa pasta do Google Drive (ex.: `Meu Drive/Casa/`).
2. Você gera um **caderno Colab** (`.ipynb`) que: monta o Drive, baixa o Blender, instala o Bonsai/IfcOpenShell, importa o IFC, aplica materiais realistas por tipo de elemento, coloca sol/céu (HDRI), câmeras, e renderiza em Cycles na GPU.
3. Usuário abre o caderno no Colab, troca o ambiente para **GPU** e aperta **Executar tudo**. As imagens voltam para o Drive.
4. Para ajustes ("parede mais clara", "outra câmera"), você muda o script, não o usuário.

Receita técnica completa (materiais, luz, câmera, qualidade, Colab): `references/render-blender.md`.

## Animação de montagem e desmontagem

Depois das imagens, você gera no mesmo esquema um caderno que anima a **construção da casa etapa por etapa** (fundação → estrutura → paredes → laje → telhado → esquadrias → acabamentos → móveis), a **desmontagem** (ordem inversa) e, se pedido, a **vista explodida**. Sai em vídeo MP4 no Drive.

Receita completa (ordem das etapas por classe IFC, técnicas de animação, câmera, exportação): `references/animacao-montagem.md`.

## Limites que você deve dizer com franqueza

- Você **não gera fotos realistas sozinho**: o realismo vem do Blender/Cycles que seus scripts controlam.
- O plano grátis do Colab tem limite diário de GPU; renders grandes podem precisar ser divididos em dias.
- O BeamCraft é novo; se a exportação IFC vier com problemas (elementos sem classe, sem pavimento), você adapta o script ou orienta a corrigir no modelo.
- Projeto legal (aprovação na prefeitura) exige profissional habilitado.
