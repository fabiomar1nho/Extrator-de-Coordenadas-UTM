# Extrator de Coordenadas UTM

Ferramenta web que encontra pares de coordenadas UTM (Easting e Northing) em memoriais descritivos, tabelas, listas e **fotos ou prints**, mostra o resultado em um **mapa de satélite** e exporta em **CSV** ou **KML** para o Google Earth.

Funciona 100% no navegador, em um único arquivo `index.html`. Não há servidor, instalação nem cadastro, e nenhum texto ou imagem é enviado para fora do seu computador.

![HTML5](https://img.shields.io/badge/HTML5-single%20file-0f6b5c)
![Client-side](https://img.shields.io/badge/100%25-client--side-0f6b5c)
![Tesseract.js](https://img.shields.io/badge/OCR-Tesseract.js%205-0f6b5c)
![Leaflet](https://img.shields.io/badge/Mapa-Leaflet%201.9-0f6b5c)

## Recursos

- **Extração automática** de pares Easting (6 dígitos) e Northing (7 dígitos) a partir de qualquer texto colado.
- **Formatos numéricos variados:** números soltos, com pontos de milhar (`9.518.974`, `498.416`) e com **múltiplos pontos** típicos de OCR ou memoriais mal formatados (`506.453.657`, `9.530.956.5097`).
- **Leitura de imagens (OCR)** com seleção visual estilo Google Lens: passe o mouse sobre o texto reconhecido, clique nas linhas desejadas e envie para a extração.
- **Várias imagens de uma vez**, por seleção de arquivos, arrastar e soltar ou **Ctrl+V** para colar prints.
- **Rotação de fotos** em passos de 90°, com o OCR refeito na nova orientação.
- **Texto vinculado à foto:** ao remover uma foto, apenas o texto que veio dela sai da caixa principal e a extração é atualizada.
- **Visualização no mapa** com imagem de satélite, polígono fechado, vértices numerados, perímetro e área aproximados.
- **Exportação em CSV e KML**, com conversão UTM para latitude/longitude e personalização de nome, cor e espessura da linha.
- **Modo claro e escuro**, que respeita a preferência do sistema e lembra sua escolha.
- **Layout responsivo**, do celular ao desktop, com foco visível no teclado e suporte a leitores de tela.

## Como usar

1. Abra o `index.html` em qualquer navegador moderno.
2. Cole o texto no campo **Texto de origem**, ou carregue imagens no bloco **Tem uma foto ou print?**.
3. Clique em **Extrair coordenadas**.
4. Em **Configurar exportação**, informe a **Zona UTM** e o **Hemisfério** da sua área (padrão: zona 24, hemisfério Sul) e, se quiser, o nome, a cor e a espessura da linha.
5. Clique em **Visualizar no mapa**, **Baixar CSV** ou **Baixar KML**.

> A zona e o hemisfério são usados na conversão para KML e para o mapa. Confira esses valores antes de exportar, pois uma zona errada desloca o polígono para outro lugar.

### Exemplo de texto aceito

```text
Do vértice P-01, de coordenadas N 9.518.974 e E 498.416, segue até o vértice P-02,
de coordenadas N 9.519.102 e E 498.530, ...
```

Também funcionam tabelas copiadas do Excel, Word ou PDF e listas com números separados por espaço ou quebra de linha. Letras e palavras são ignoradas.

## Leitura de imagens (OCR)

1. Arraste imagens para o bloco de OCR, escolha arquivos ou cole um print com **Ctrl+V** em qualquer lugar da página.
2. O leitor abre e processa cada imagem. As caixas aparecem sobre as linhas de texto reconhecidas.
3. As linhas que parecem coordenadas já vêm selecionadas. Ajuste clicando nas caixas ou use **Só linhas com números**, **Selecionar tudo** e **Limpar seleção**.
4. Se a foto estiver de lado ou de cabeça para baixo, use **Girar à esquerda** ou **Girar à direita**. A imagem é lida de novo na nova orientação.
5. Se necessário, corrija o texto no painel lateral, que é editável.
6. Clique em **Enviar e extrair coordenadas**. O texto vai para a caixa principal e a extração roda automaticamente.

**Pré-processamento:** antes do OCR, cada imagem é girada conforme a sua escolha, redimensionada (lado maior entre 1600 e 2600 px), convertida para escala de cinza e recebe alongamento de contraste com curva em S. Imagens escuras com texto claro são invertidas automaticamente.

**Remoção de fotos:** **Remover esta imagem** e **Remover todas** apagam da caixa principal somente o texto enviado por essas fotos, sem tocar no que você digitou ou colou à mão. Depois disso a extração é refeita com o que sobrou, ou os resultados são limpos se não restar texto. Reenviar uma foto substitui o trecho anterior dela em vez de duplicar. Se você alterar à mão, na caixa principal, um trecho já enviado, ele não pode ser localizado e a ferramenta avisa para você conferir o texto.

**Dependência de rede:** o [Tesseract.js](https://github.com/naptha/tesseract.js) 5.1.1 e os dados do idioma (`eng`) são baixados da CDN jsDelivr no primeiro uso do OCR. O restante da aplicação funciona offline, exceto o mapa.

## Visualização no mapa

O botão **Visualizar no mapa** abre uma janela com o [Leaflet](https://leafletjs.com/) 1.9.4 (carregado da cdnjs no primeiro uso) e a camada de satélite **Esri World Imagery**, com a atribuição "Tiles © Esri".

- O polígono é fechado e usa a cor e a espessura configuradas para o KML.
- Cada vértice é um marcador numerado. Ao clicar, abre um popup com Easting, Northing, latitude e longitude.
- A faixa superior mostra vértices, zona, perímetro e área (em hectares e m²). Esses valores são aproximados, calculados no plano UTM.
- O mapa enquadra o polígono com margem, chegando no máximo ao zoom 20. Os tiles nativos da Esri vão até o nível 17, e do 18 ao 20 a imagem é ampliada, o que evita tiles vazios.
- **Centralizar** reenquadra o polígono.

## Como funciona

### 1. Extração

O texto é analisado com expressões regulares em estratégias complementares:

- **Números puros:** sequências de exatamente 6 dígitos (Easting) e 7 dígitos (Northing). Dois números adjacentes, um de cada tipo, formam um par, em qualquer ordem.
- **Números com pontos de milhar:** padrões como `9.518.974` (Northing) e `498.416` (Easting), combinados pela proximidade no texto, ignorando o ruído entre eles.
- **Múltiplos pontos:** tokens como `506.453.657` ou `9.530.956.5097`. Os pontos de milhar formam a parte inteira (6 dígitos = Easting, 7 = Northing) e o último grupo é tratado como casas decimais, já que a vírgula decimal costuma ser lida como ponto. Assim, `506.453.657` vira 506453 e `9.530.956.5097` vira 9530956. Cada normalização aparece no **Registro de processamento**.

Pares duplicados são descartados. O resultado aparece na tabela e no registro.

### 2. Conversão UTM para graus decimais

Cada par é convertido para latitude e longitude pela série trigonométrica inversa da projeção UTM, usando o elipsoide WGS 84 (equivalente ao SIRGAS 2000 para esta finalidade) e o meridiano central da zona informada. O polígono é fechado repetindo o primeiro vértice no final, e os pontos seguem a ordem em que foram encontrados.

## Perguntas frequentes

O botão **Perguntas frequentes**, no topo da página, abre uma janela com a explicação do funcionamento da extração e dos formatos de texto aceitos.

## Executando localmente

Não é preciso instalar nada. Baixe o `index.html` e abra no navegador.

Se preferir servir por HTTP:

```bash
python3 -m http.server 8000
# acesse http://localhost:8000
```

## Publicado no GitHub Pages

https://fabiomar1nho.github.io/Extrator-de-Coordenadas-UTM/

## Estrutura

```text
.
├── index.html   # aplicação completa (HTML, CSS e JavaScript)
└── README.md
```

## Limitações

- A extração espera Easting com 6 dígitos e Northing com 7 dígitos. Coordenadas fora desse padrão são ignoradas.
- As casas decimais de tokens como `506.453.657` são descartadas, e todos os vértices usam metros inteiros.
- O número deve estar separado de letras por espaço (`N 9.530.956.5097`, e não `N9.530.956.5097`).
- O KML gera um único polígono com todos os pontos encontrados.
- A qualidade do OCR depende da nitidez da imagem. Fotos tortas, com reflexo ou texto muito pequeno podem gerar leituras erradas, por isso o texto é editável antes do envio e as fotos podem ser giradas.
- A orientação automática da foto (detecção de rotação) não está incluída. A rotação é manual.
- Formatos de imagem sem suporte nativo do navegador, como HEIC em alguns sistemas, podem não abrir.
- O mapa exige internet. Em áreas onde a Esri tem pouca cobertura detalhada, a imagem pode aparecer borrada.

## Tecnologias

- HTML, CSS e JavaScript puros, sem frameworks
- [Tesseract.js](https://github.com/naptha/tesseract.js) 5.1.1 para OCR
- [Leaflet](https://leafletjs.com/) 1.9.4 para o mapa
- Imagens de satélite da [Esri World Imagery](https://www.arcgis.com/home/item.html?id=10df2279f9684e4a9f6a7f08febac2a9)
- Fontes [Public Sans](https://fonts.google.com/specimen/Public+Sans) e [IBM Plex Mono](https://fonts.google.com/specimen/IBM+Plex+Mono) via Google Fonts

## Autor

Desenvolvido por **fabiomar1nho**.

- GitHub: [@fabiomar1nho](https://github.com/fabiomar1nho)
- Instagram: [@fabio_mar1nho](https://www.instagram.com/fabio_mar1nho/)

---

Copyright &copy; 2026 fabiomar1nho
