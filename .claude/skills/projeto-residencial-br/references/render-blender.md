# Renderização realista: IFC → Blender → Colab

Objetivo: o usuário só coloca o `.ifc` no Google Drive e aperta "Executar tudo" num caderno Colab que você escreve. Nada de abrir o Blender.

## Estrutura do caderno Colab

Escreva o caderno com células curtas, cada uma com um título em português explicando o que faz:

1. **Conectar o Drive**: `from google.colab import drive; drive.mount('/content/drive')`. Pasta do projeto numa variável no topo (ex.: `PASTA = '/content/drive/MyDrive/Casa'`).
2. **Verificar GPU**: `!nvidia-smi`. Se não houver GPU, mostre a mensagem: "Vá em Ambiente de execução → Alterar o tipo de ambiente de execução → GPU T4 → Salvar" e pare.
3. **Baixar o Blender** (versão LTS estável, Linux x64, de download.blender.org) e descompactar em `/content/blender`. Rodar sempre em modo sem tela: `blender -b -P script.py`.
4. **Instalar IfcOpenShell/Bonsai** no Python do Blender (ou o add-on Bonsai pela extensão oficial). Confirme a versão compatível com a versão do Blender escolhida.
5. **Gravar o script de cena** (`%%writefile cena.py`) e **renderizar**.
6. **Salvar** as imagens em `PASTA/renders/` com nome claro (`fachada_frente.png`, `sala_dia.png`).

Teste cada versão do caderno antes de entregar quando a rede da sessão permitir; se não der, diga ao usuário que é a primeira versão e peça para mandar o erro (print) se aparecer.

## Script de cena (bpy) — o que ele deve fazer

1. Limpar a cena padrão; importar o IFC.
2. **Materiais por classe IFC** (procure pelo tipo do elemento/nome):
   - `IfcWall` externa: reboco pintado (cor do usuário), leve rugosidade, textura de reboco CC0.
   - `IfcWall` interna: tinta acrílica fosca.
   - `IfcSlab` piso: porcelanato/madeira conforme o ambiente; laje de cobertura: concreto ou telha.
   - `IfcRoof` / telhas: telha cerâmica ou fibrocimento com textura CC0.
   - `IfcWindow`: caixilho de alumínio + vidro (Glass BSDF, IOR 1.45, levemente esverdeado).
   - `IfcDoor`: madeira.
   - `IfcColumn` / `IfcBeam` aparentes: concreto.
   - Terreno: grama/terra; calçada: concreto.
   Texturas PBR grátis CC0: **Poly Haven** e **ambientCG** (baixar no próprio caderno). Sempre aplique mapeamento por *Box/Triplanar* ou coordenadas de objeto com escala real (texturas na escala de metros), senão os tijolos ficam gigantes.
3. **Luz**:
   - Céu: HDRI CC0 do Poly Haven (céu de dia limpo ou fim de tarde) no World, **ou** Sun + Sky (Nishita) com o sol na posição real (latitude da cidade e horário).
   - Interiores: além da luz das janelas, coloque luminárias (Area/Point) nas posições do projeto de iluminação, 2700–3000 K.
4. **Câmera** (o que mais dá cara de foto):
   - Altura dos olhos: **1,60 m**.
   - Lente: 24–35 mm para fachada, 18–24 mm para interiores.
   - **Paredes verticais retas**: câmera nivelada (rotação X = 90°) e use *shift* vertical para enquadrar, em vez de inclinar a câmera.
   - Fachada em perspectiva de dois pontos, ~30–45° da frente.
5. **Render**:
   - Motor **Cycles**, dispositivo **GPU** (CUDA/OptiX no Colab).
   - Amostras: 128–256 com *denoiser* (OpenImageDenoise ou OptiX) ligado.
   - Gerenciamento de cor **AgX** (ou Filmic em versões antigas), exposição ajustada.
   - Resolução: 1920×1080 para prévia, 3840×2160 para final.
6. **Realismo extra** (aplique quando houver tempo de GPU): vegetação simples, calçada e meio-fio, carro ao longe, rodapé e soleiras, leve imperfeição (bevel nas arestas de 1–2 mm), profundidade de campo sutil.

## Iteração com o usuário

- Mostre as imagens (o usuário manda o print ou você lê do Drive se houver acesso) e pergunte o que mudar em linguagem simples.
- Mudanças viram parâmetros no topo do caderno: `COR_FACHADA`, `HORA_DO_DIA`, `ESTILO`, lista de câmeras. Assim o usuário pode até trocar sozinho uma palavra.

## Limites do Colab grátis

- GPU T4, tempo limitado por dia e desconexão se ficar ocioso. Salve cada imagem no Drive assim que terminar.
- Renders 4K de interiores podem levar alguns minutos cada; animações levam mais (ver `animacao-montagem.md`).
