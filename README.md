# Extrator de Coordenadas UTM

Ferramenta web que encontra pares de coordenadas UTM (Easting e Northing) em memoriais descritivos, tabelas, listas e até em **fotos e prints**, e exporta o resultado em **CSV** ou **KML** para abrir no Google Earth.

Funciona 100% no navegador, em um único arquivo `index.html`. Não há servidor, instalação nem cadastro, e nenhum texto ou imagem é enviado para fora do seu computador.

![HTML5](https://img.shields.io/badge/HTML5-single%20file-0f6b5c)
![Client-side](https://img.shields.io/badge/100%25-client--side-0f6b5c)
![Tesseract.js](https://img.shields.io/badge/OCR-Tesseract.js%205-0f6b5c)

## Recursos

- **Extração automática** de pares Easting (6 dígitos) e Northing (7 dígitos) a partir de qualquer texto colado.
- **Leitura de imagens (OCR)** com seleção visual estilo Google Lens: passe o mouse sobre o texto reconhecido, clique nas linhas desejadas e envie para a extração.
- **Várias imagens de uma vez**, por seleção de arquivos, arrastar e soltar ou **Ctrl+V** para colar prints.
- **Exportação em CSV** com índice, Easting, Northing e texto original.
- **Exportação em KML** com conversão UTM para latitude/longitude e personalização de nome, cor e espessura da linha.
- **Modo claro e escuro**, que respeita a preferência do sistema e lembra sua escolha.
- **Layout responsivo**, do celular ao desktop, com foco visível no teclado e suporte a leitores de tela.

## Como usar

1. Abra o `index.html` em qualquer navegador moderno.
2. Cole o texto no campo **Texto de origem**, ou carregue imagens no bloco **Tem uma foto ou print?**.
3. Clique em **Extrair coordenadas**.
4. Em **Configurar exportação**, informe a **Zona UTM** e o **Hemisfério** da sua área. Os valores padrão são zona 24 e hemisfério Sul.
5. Clique em **Baixar CSV** ou **Baixar KML**.

> A zona e o hemisfério são usados apenas na conversão para KML. Confira esses valores antes de exportar, pois uma zona errada desloca o polígono no mapa.

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
4. Se necessário, corrija o texto no painel lateral, que é editável.
5. Clique em **Enviar e extrair coordenadas**. O texto vai para a caixa principal e a extração roda automaticamente.

**Pré-processamento:** antes do OCR, cada imagem é redimensionada (lado maior entre 1600 e 2600 px), convertida para escala de cinza e recebe alongamento de contraste com curva em S. Imagens escuras com texto claro são invertidas automaticamente.

**Dependência de rede:** o [Tesseract.js](https://github.com/naptha/tesseract.js) 5.1.1 e os dados do idioma (`eng`) são baixados da CDN jsDelivr no primeiro uso do OCR. O restante da aplicação funciona offline.

## Como funciona

### 1. Extração

O texto é analisado com expressões regulares em duas estratégias complementares:

- **Números puros:** sequências de exatamente 6 dígitos (Easting) e 7 dígitos (Northing). Dois números adjacentes, um de cada tipo, formam um par. A ordem entre eles não importa.
- **Números formatados:** padrões como `9.518.974` (Northing) e `498.416` (Easting), combinados pela proximidade no texto, ignorando o ruído entre eles.

Pares duplicados são descartados. O resultado aparece na tabela e no **Registro de processamento**.

### 2. Conversão UTM para graus decimais

Para o KML, cada par é convertido para latitude e longitude pela série trigonométrica inversa da projeção UTM, usando o elipsoide WGS 84 (equivalente ao SIRGAS 2000 para esta finalidade) e o meridiano central da zona informada. O polígono é fechado repetindo o primeiro vértice no final, e os pontos seguem a ordem em que foram encontrados.

## Executando localmente

Não é preciso instalar nada. Baixe o `index.html` e abra no navegador.

Se preferir servir por HTTP:

```bash
python3 -m http.server 8000
# acesse http://localhost:8000
```

## Estrutura

```text
.
├── index.html   # aplicação completa (HTML, CSS e JavaScript)
└── README.md
```

## Limitações

- A extração espera Easting com 6 dígitos e Northing com 7 dígitos. Coordenadas fora desse padrão são ignoradas.
- O KML gera um único polígono com todos os pontos encontrados.
- A qualidade do OCR depende da nitidez da imagem. Fotos tortas, com reflexo ou texto muito pequeno podem gerar leituras erradas, por isso o texto é editável antes do envio.
- Formatos de imagem sem suporte nativo do navegador, como HEIC em alguns sistemas, podem não abrir.

## Tecnologias

- HTML, CSS e JavaScript puros, sem frameworks
- [Tesseract.js](https://github.com/naptha/tesseract.js) 5.1.1 para OCR
- Fontes [Public Sans](https://fonts.google.com/specimen/Public+Sans) e [IBM Plex Mono](https://fonts.google.com/specimen/IBM+Plex+Mono) via Google Fonts

## Autor

Desenvolvido por **fabiomar1nho**.

- GitHub: [@fabiomar1nho](https://github.com/fabiomar1nho)
- Instagram: [@fabio_mar1nho](https://www.instagram.com/fabio_mar1nho/)

---

Copyright &copy; 2026 fabiomar1nho
