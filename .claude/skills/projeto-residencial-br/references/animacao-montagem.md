# Animação de montagem, desmontagem e vista explodida

Mesmo esquema do render: caderno Colab que importa o IFC no Blender e anima, sem o usuário abrir o Blender. Sai um MP4 no Google Drive.

## Ordem da montagem por classe IFC

Agrupe os elementos importados nesta ordem (segue a sequência real da obra). Dentro de cada grupo, ordene por pavimento (`IfcBuildingStorey`) e depois de baixo para cima (coordenada Z) e, se quiser, por posição (de um lado ao outro).

| # | Etapa | Classes IFC típicas |
|---|---|---|
| 1 | Terreno e locação | `IfcSite`, `IfcGeographicElement` |
| 2 | Fundação | `IfcFooting`, `IfcPile`, vigas baldrame (`IfcBeam` no nível da fundação) |
| 3 | Contrapiso / piso térreo | `IfcSlab` do térreo (`PredefinedType` FLOOR/BASESLAB) |
| 4 | Estrutura | `IfcColumn`, `IfcBeam` |
| 5 | Paredes | `IfcWall`, `IfcWallStandardCase` |
| 6 | Lajes superiores | `IfcSlab` acima do térreo |
| 7 | Escadas | `IfcStair`, `IfcStairFlight`, `IfcRailing` |
| 8 | Cobertura | `IfcRoof`, `IfcSlab` ROOF, `IfcMember` (tesouras/caibros) |
| 9 | Instalações aparentes | `IfcFlowSegment`, `IfcFlowTerminal` etc., se o modelo tiver |
| 10 | Esquadrias | `IfcWindow`, `IfcDoor` |
| 11 | Revestimentos e acabamentos | `IfcCovering` (pisos, forros, revestimentos) |
| 12 | Móveis e louças | `IfcFurnishingElement`, `IfcSanitaryTerminal` |
| 13 | Externos | muros, calçada, vegetação |

Se o BeamCraft exportar classes genéricas (`IfcBuildingElementProxy`), use o nome do elemento, o pavimento e a altura para decidir o grupo, e avise o usuário quais ficaram incertos.

## Técnicas de animação (bpy, por keyframes)

Para cada elemento, no seu quadro de entrada:
- **Surgir caindo do alto** (padrão, fica bonito): começa 3–5 m acima, invisível; em ~12 quadros desce até a posição final com *ease out*. Visibilidade por keyframe em `hide_render`/`hide_viewport` ou escala 0→1.
- **Crescer do chão** (bom para paredes e pilares): escala Z de 0 a 1 com a origem na base do objeto.
- **Fade de material** (vidros e acabamentos): Alpha de 0 a 1 (use Principled BSDF + Alpha e modo de mistura adequado).
- Entre etapas, deixe ~1 s de pausa e mostre um **título da etapa** (objeto de texto 3D ou sobreposição no vídeo: "Fundação", "Estrutura"…).

**Desmontagem**: a mesma lista na ordem inversa (móveis saem primeiro, fundação por último), com o movimento ao contrário (sobe e some).

**Remontagem**: desmontagem seguida da montagem no mesmo vídeo, ou dois vídeos separados.

**Vista explodida**: cada pavimento/etapa se afasta no eixo Z (ex.: 2 m por grupo) e volta; ótimo para mostrar camadas (fundação, estrutura, paredes, telhado).

## Câmera e ritmo

- Câmera orbitando a casa devagar (um *Empty* no centro, câmera filha dele, rotação de 360° durante o vídeo), altura ~8–12 m, lente 35 mm.
- 24 ou 30 quadros por segundo; 45 s a 2 min de vídeo total.
- Distribua o tempo pelo número de elementos: etapas com muitas peças (paredes) aparecem em grupos de 3–10 por vez para não ficar longo.

## Render da animação no Colab

- Para prévia rápida: motor **EEVEE** ou Cycles com poucas amostras (32–64) + denoiser, 1280×720.
- Final: Cycles 64–128 amostras + denoiser, 1920×1080.
- Renderize em **sequência de PNG** dentro do Drive (se o Colab cair, continua de onde parou pulando quadros já existentes: `render.use_overwrite = False` e `use_placeholder = True`).
- Junte os quadros em MP4 com `ffmpeg` no Colab: `ffmpeg -framerate 30 -i quadro_%04d.png -c:v libx264 -pix_fmt yuv420p montagem.mp4`.
- Estime e informe o tempo: (segundos de quadro × total de quadros). Se passar do limite diário do Colab, divida em partes por etapa.
