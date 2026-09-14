# 🛠️ Extrator de Coordenadas UTM

O **Extrator de Coordenadas UTM** é uma aplicação web estática voltada para agrimensores, engenheiros e analistas ambientais. A ferramenta automatiza a varredura de textos brutos, memoriais descritivos e relatórios técnicos para extrair pares de coordenadas UTM e convertê-los em arquivos georreferenciados (`.kml` e `.csv`) compatíveis com o Google Earth e softwares GIS.

---

## 🚀 Funcionalidades

* **Extração Inteligente:** Identifica padrões de coordenadas UTM em blocos de texto sem formatação prévia.


* **Conversão Geodésica:** Converte projeção plana UTM para Graus Decimais (Latitude/Longitude) em tempo real no padrão WGS 84 / SIRGAS 2000.


* **Exportação KML:** Gera arquivos de polígonos vetoriais para visualização no Google Earth, permitindo personalizar nome, cor e espessura da linha.


* **Exportação CSV:** Baixa a tabela estruturada contendo o número do ponto, coordenadas $X$ e $Y$ e o texto original.


* **Interface Dinâmica:** Alternância para *Dark Mode* e painel de logs para acompanhamento da extração.



---

## 📖 Passo a Passo de Uso

1. **Inserção dos Dados:** Cole o texto do memorial descritivo, tabela ou lista no campo de texto.


2. **Configuração da Zona:** Defina a **Zona UTM** (ex: `24`) e o **Hemisfério** (`Sul` ou `Norte`) correspondentes à área do imóvel/terreno.


3. **Extração:** Clique no botão **🔍 Extrair Coordenadas**. O sistema processará o texto e preencherá a tabela de resultados.


4. **Personalização do KML:** Escolha o nome do polígono, a cor do traçado e a espessura da linha no painel de exportação.


5. **Download:** Clique em **📄 CSV** para obter a planilha ou **🌍 Baixar KML** para exportar o arquivo do Google Earth.



---

## 💻 Estrutura e Funcionamento do Código

A aplicação é construída em arquivo único (`index.html`), rodando 100% no navegador do usuário (Client-Side) sem dependência de servidores ou bibliotecas externas.

### 1. Algoritmo de Extração Textual (`extrairCoordenadas`)

A leitura do texto é realizada em duas camadas utilizando Expressões Regulares (Regex):

* **Varredura por Dígitos:** Isola números de 6 dígitos (Easting / X) e 7 dígitos (Northing / Y) e analisa a adjacência entre eles no texto.


* **Varredura por Padrões Formatados:** Rastreia coordenadas com pontuação (ex: `X.XXX.XXX` para Northing e `XXX.XXX` para Easting), ignorando textos intercalados:


```javascript
const pattern1Regex = /\b(\d{1,3}\.\d{3}\.\d{3})(?:\.\d+)?\b/g; // 7 dígitos
const pattern2Regex = /\b(\d{3}\.\d{3})(?:\.\d+)?\b/g;           // 6 dígitos

```



### 2. Conversão de Projeção (`utmToLatLon`)

Como o formato KML não aceita coordenadas UTM em metros, a função `utmToLatLon()` realiza a conversão matemática matemática reversa da Projeção Transversa de Mercator para coordenadas geográficas:

* **Meridiano Central:** Calculado dinamicamente com base na Zona inserida:

$$\text{Meridiano Central} = (\text{Zona} - 1) \times 6 - 180 + 3$$


* **Geometria do Elipsoide:** Utiliza os parâmetros do WGS 84 (Raio Equatorial $a = 6378137.0\text{ m}$ e excentricidade $e^2 = 0.00669438$).
* **Saída:** Retorna a Latitude e Longitude com precisão de até 8 casas decimais.



### 3. Geração e Download dos Arquivos

A criação dos arquivos é feita diretamente em memória através de objetos `Blob` do JavaScript:

* **Conversão de Cor:** A cor escolhida no input HTML (`#RRGGBB`) é traduzida para a codificação hexadecimal do KML (`AABBGGRR`) via `hexParaKmlColor()`.


* **Fechamento do Polígono:** O código duplica automaticamente o primeiro ponto ao final da sequência dentro das tags `<LinearRing><coordinates>` para garantir que a figura geométrica feche perfeitamente no Google Earth.



---

## 👤 Autor

Desenvolvido por **Mar1nho**.

---

## 🤖 Divulgação do Uso de IA

Transparência e integridade são fundamentais para o desenvolvimento deste projeto. Ferramentas de Inteligência Artificial (IA) foram utilizadas no fluxo de trabalho do **Extrator de Coordenadas UTM**, atuando estritamente como assistentes técnicas para otimizar tarefas de estruturação, documentação e refatoração.

**Como a IA foi utilizada:**

* **Documentação e FAQ:** Auxílio na estruturação e redação técnica do arquivo, do FAQ interativo e na organização dos textos explicativos da ferramenta.


* **Refatoração e Regex:** Suporte na exploração e ajuste de Expressões Regulares para captura precisa de padrões de 6 e 7 dígitos e formatos com pontuação (`X.XXX.XXX` / `XXX.XXX`).


* **Apoio Algorítmico:** Consultas de sintaxe para a implementação das equações trigonométricas de conversão geodésica (UTM para Graus Decimais) e validação da estrutura das tags do arquivo XML/KML.


**Supervisão Humana:**

Embora a IA tenha servido para acelerar o processo de documentação e a resolução de dúvidas pontuais de sintaxe, **todas as decisões arquitetônicas, a validação das equações matemáticas, os testes de precisão cartográfica e a revisão final do código foram inteiramente conduzidos e aprovados por humanos**. A ferramenta reflete a lógica e o direcionamento técnico do seu desenvolvedor.
