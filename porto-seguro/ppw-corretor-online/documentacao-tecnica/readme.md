![aunica](https://implementacaoaunica.github.io/client/aunica.jpg?raw=true)

> Área - Digital Analytics<br />
> Documento de Especificação Técnica

<br />

## Guia de estruturação HTML para tagueamento - PPW Corretor Online Unificado

> Última atualização: 19/04/2021 <br />

<br />

## Sumário

- [Objetivo](#objetivo)
- [Descrição Código Google Tag Manager](#descri&ccedil;&atilde;o-c&oacute;digo-google-tag-manager)
- [Objeto customData](#objeto-customdata)
- [Estrutura HTML](#estrutura-html)
- [Atributo data-gtm-type](#atributo-data-gtm-type)
- [Atributo data-gtm-clicktype](#atributo-data-gtm-clicktype)
- [Atributo data-gtm-formtype](#atributo-data-gtm-formtype)
- [Atributo data-gtm-name](#atributo-data-gtm-name)
- [Atributo data-gtm-subname](#atributo-data-gtm-subname)
- [Exemplos práticos](#exemplos-pr&aacute;ticos)


## Objetivo

<p style='text-align: justify;'>  
Neste documento são apresentados os requisitos necessários para que seja possível a implementação de uma estrutura otimizada e consistente de tagueamento no ambiente Corretor Online, diminuindo esforços com manutenção e evitando a perda de dados nas bases de Analytics.
</p>

<br />

### **Descrição Código Google Tag Manager**

<p style='text-align: justify;'>  
O `snippet` do Google Tag Manager é um pequeno trecho de código javascript ou non-javascript, através do uso de um iframe quando o javascript não está disponível, que é inserido nas páginas do site, tornando possível que a configuração das tags sejam realizadas via interface.
</p>

### **Posicionamento do Código - Google Tag Manager**

#### 1. Copie o seguinte JavaScript e cole-o o mais próximo da tag `<head>` de abertura possível em todas as páginas do seu site.

```html

<html>
  <head>
    <!-- Google Tag Manager -->
    <script>(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start': new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0], j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src='https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f); })(window,document,'script','dataLayer','GTM-XXXXXXX');</script>
    <!-- End Google Tag Manager -->
    //...
  </head>
```

#### 2. Copie o seguinte trecho e cole-o imediatamente após a marcação `<body>` de abertura em cada página do seu site.

```html
<body>
  <!-- Google Tag Manager (noscript) -->
  <noscript><iframe src="https://www.googletagmanager.com/ns.html?id=GTM-XXXXXXX" height="0" width="0" style="display:none;visibility:hidden"></iframe></noscript>
  <!-- End Google Tag Manager (noscript) -->
  //...
  </body>
</html>
```

Link de referência: [Documentação Oficial Google Tag Manager](https://developers.google.com/tag-manager/quickstart)

---

## Overview e Descrições Técnicas

### Camada de dados (DataLayer)

<p style='text-align: justify;'>  
É um array de objetos javascript utilizado pelo Google Tag Manager para receber em seus atributos, dados importantes do site.
Para implementar o dataLayer no site, o desenvolvedor pode utilizar formas diferentes para preencher os dados. Essas formas são dependentes da ação estabelecida na documentação e também do nível da interação.
</p>

**Instalação**<br />
Inserir a camada de dados antes do snippet de instalação do Google Tag Manager. Exemplo:


```html
<script>
	window.dataLayer = window.dataLayer || [];
	window.dataLayer.push({
		'atributo1': '[[valor1]]',
		'atributo2': '[[valor2]]'
	});
</script>
```

---


<br />

### Objeto customData

<p style='text-align: justify;'>  
O objeto customData é a fonte de alimentação de informações pertinentes ao disparo de tags da estrutura que foi desenvolvida. Trata-se de um objeto global que deve estar carregado e preenchido no momento do carregamento de todas as páginas do site, e caso haja a necessidade o seu conteúdo deve ser atualizado. Preferencialmente o customData deve ser carregado antes do GTM. Abaixo temos a indicação da estrutura do objeto e um descritivo de todos os seus atributos.
</p>

```html
<script>
customData = {
  site: {
    brand: '',
            versao: '',
    portal: ''
  },
      user: {
        id: '',
        id_usuario: '',
            tipo_usuario: '',
            saldo: '',
        susep: '',
            logado: ''
      }
}
</script>
```

| Atributo  | Descrição de preenchimento  | Tipo de dado | Valor padrão | Exemplo |
| :-------- | :--------------------       | :----------- | :----------- | :------ |
| customData.site  | Objeto destinado a armazenar informações sobre a página/site que está sendo acessada. | Objeto | ""| |
| customData.site.brand  | Deve indicar qual a marca do site. | Texto | ""| “portoseguro”|
| customData.site.versao  | Deve indicar qual variação/versão do site o usuário está acessando. | Texto | ""| “1.2”|
| customData.site.portal  | Deve indicar qual o tipo de produto. | Texto | ""| “PPW corretor online”|
| customData.user  | Objeto destinado a descrever as informações do usuário. O objeto deve ser trazido quando o usuário estiver identificado.| Objeto | ""| |
| customData.user.id  | Deve indicar o ID de usuário que a plataforma atribui. | Texto | ""| "ABC123"|
| customData.user.id_usuario  | CPF Criptografado  AESCryptography (Utilizada nos ambientes porto e azul) | Texto | ""| “a750c220a060fcf487f9519d3203035b”|
| customData.user.tipo_usuario  | Deve indicar a classificação do usuário em sua conta PortoSeguro | Texto | ""| “corretor”, “prestador-de-servico”,“cliente"|
| customData.user.saldo  | Deve indicar o saldo total disponível do usuário | Texto | ""| “31.257”, “12.345”|
| customData.user.susep  | Deve indicar o ID da Susep (quando se aplica). | Texto | ""| "COL10J"|
| ccustomData.user.logado  | Deve indicar se o usuário está já realizou o Login | Boolean | ""| "true", "false"|

### Estrutura HTML

<p style='text-align: justify;'>  
Foi desenvolvida uma categorização dos elementos HTML para que quando houverem interações do usuário na página, possamos identificar exatamente onde e qual foi a interação. Esse processo tem o objetivo de possibilitar que a captura de informações ocorra de forma automatizada, diminuindo o esforço necessário na implementação do mapeamento de chamadas.
Portanto, o primeiro requisito para a estrutura de tagueamento automático funcionar é o preenchimento do atributo data-gtm-type para que seja feita a categorização dos elementos da página e partir disso definir o comportamento de cada um para o disparo de tags. 
São 5 tipos de elementos, sendo que 3 possuem sub-categorias devido suas características.
É necessária a implementação desses itens em quaisquer elementos que possuam a necessidade de serem mensurados, pois baseado no preenchimento dos atributos data que as interações serão mapeadas pelo script.
</p>

* Check
* Click
   - filtro
   - accordion
   - menu
   - sub-menu
   - rodape
   - button
   - download
   - link
   - card
* Form
   - input
   - submit
* Select

---

> Todos os elementos do html que serão clicados, deverão ser mapeados recebendo os atributos com sua estrutura no item.

```html
<div  
  data-gtm-type= "click, form, select"
  data-gtm-clicktype= "accordion, filtro, button, card, download, link, menu, sub-menu, rodape"
  data-gtm-formtype= "input, submit"
  data-gtm-name= "nome-acao"
  data-gtm-subname= "sub-nome-acao"
 >
</div>
```

#### Atributo data-gtm-type

<p style='text-align: justify;'>  
Os valores click, form e select deverão ser preenchidos no atributo data-gtm-type.
</p>

#### Atributo data-gtm-clicktype

<p style='text-align: justify;'>
Os valores accordion, filtro, button, card, download, link, menu, sub-menu e rodape da categoria de elementos do tipo click deverão ser preenchidos no atributo data-gtm-clicktype.
</p>

#### Atributo data-gtm-formtype

<p style='text-align: justify;'>
Os valores input e submit do tipo form deverão ser preenchidos no atributo data-gtm-form.
</p>

#### Atributo data-gtm-name

<p style='text-align: justify;'>
Além disso, é necessário o preenchimento do atributo data-gtm-name para que possamos identificar em qual elemento ocorreu a interação. O atributo deve carregar um nome descritivo que representa a ação do botão, o nome do card, o nome do form, o nome do campo, etc.
</p>

#### Atributo data-gtm-subname

<p style='text-align: justify;'>
Caso exista a necessidade de especificar ainda mais o elemento que foi clicado é necessário indicar um no preenchimento do atributo data-gtm-subname para que possamos identificar em qual elemento ocorreu a interação.
</p>


---

### Exemplos práticos

- Localizar Documentos

![Localizar-doc](https://implementacaoaunica.github.io/client/prints/localizar-doc.png?raw=true)


- **Em Localizar Documentos**<br />

```html
<!-- Use se os atributos no elemento a ser clicado -->
<form 
  data-gtm-type="form"
  data-gtm-name= "localizar-documentos"
 >
</form>
```

<br />

- **Ao clicar no botão "Buscar" em Localizar Documentos**<br />

```html
<!-- Use se os atributos no elemento a ser clicado -->
<input 
  data-gtm-type= "submit"
  data-gtm-name= "buscar"
  data-gtm-subname= "localizar-documentos"
 >
</input>
```

<br />

- **Na interação com os itens do formulário em Localizar Documentos**<br />

```html
<!-- Use se os atributos no elemento a ser clicado -->
<input  
  data-gtm-form="input"
  data-gtm-name="[[campo]]"
 >
</input>
```

| Variavel  |  Descrição  | Exemplo |
| :-------- | :---------- | :------ | 
| [[campo]]  | Deve retornar o nome do campo que teve a interação | "numero", "nome-do-proponente" e etc |


<br />

- **No clique dos links em Localizar Documentos**<br />

```html
<!-- Use se os atributos no elemento a ser clicado -->
<div  
  data-gtm-type="click"
  data-gtm-clicktype="link"
  data-gtm-name="1500-protocolos-atualizados"
  data-gtm-subname="localizar-documentos"
 >
</div>
```


<br />

- **Na interação com os campos para selecionar uma opção em Localizar Documentos**<br />

```html
<!-- Use se os atributos no elemento a ser clicado -->
<div  
  data-gtm-type="select"
  data-gtm-name="[[campo-selecionado]]"
  data-gtm-subname="localizar-documentos"
 >
</div>
```

| Variavel  |  Descrição  | Exemplo |
| :-------- | :---------- | :------ | 
| [[campo-selecionado]]  | Deve retornar o nome do campo que selecionou alguma opção | "tipo:n-cia", "tipo:endosso", "situacao:calculado" e etc |



<br />

- Resultado Busca

![Resultado-busca](https://implementacaoaunica.github.io/client/prints/resultado-busca.png?raw=true)

- **No clique dos botões**<br />

```html
<!-- Use se os atributos no elemento a ser clicado -->
<div  
  data-gtm-type="click"
  data-gtm-clicktype="button"
  data-gtm-name="[[nome-button]]"
  data-gtm-subname="resultado-busca"
 >
</div>
```

| Variavel  |  Descrição  | Exemplo |
| :-------- | :---------- | :------ | 
| [[nome-button]]  | Deve retornar o nome do botão clicado | "orcamento", "proposta", "historico" e etc |



<br />

- **Na interação para abrir ou fechar algum resultado de busca**<br />

```html
<!-- Use se os atributos no elemento a ser clicado -->
<div  
  data-gtm-type="click"
  data-gtm-clicktype="link"
  data-gtm-name="[[acao]]"
  data-gtm-subname="resultado-busca"
  data-gtm-doc="[[doc]]"

 >
</div>
```

| Variavel  |  Descrição  | Exemplo |
| :-------- | :---------- | :------ | 
| [[acao]]  | Deve retornar a ação do usuário | "abrir" ou "fechar" |
| [[doc]]  | Deve retornar o numero do documento que se teve a ação | "95863254" e etc |



<br />

- **Ao selecionar algum resultado de busca**<br />

```html
<!-- Use se os atributos no elemento a ser clicado -->
<div  
  data-gtm-type="select"
  data-gtm-name="resultado-busca"
  data-gtm-doc="[[doc]]"
 >
</div>
```

| Variavel  |  Descrição  | Exemplo |
| :-------- | :---------- | :------ | 
| [[doc]]  | Deve retornar o numero do documento que se teve a ação | "95863254" e etc |



<br />

- **No clique do botão do chat do resultado de busca**<br />

```html
<!-- Use se os atributos no elemento a ser clicado -->
<div  
  data-gtm-type="click"
  data-gtm-clicktype="button"
  data-gtm-name="chat"
  data-gtm-subname="resultado-busca"
  data-gtm-doc="[[doc]]"
 >
</div>
```



| Variavel  |  Descrição  | Exemplo |
| :-------- | :---------- | :------ | 
| [[doc]]  | Deve retornar o numero do documento que se teve a ação | "95863254" e etc |



<br />

- Modal fale conosco

![Modal-fale-conosco](https://implementacaoaunica.github.io/client/prints/modal-fale-conosco.png?raw=true)

- **No clique dos botões**<br />

```html
<!-- Use se os atributos no elemento a ser clicado -->
<div  
  data-gtm-type="click"
  data-gtm-clicktype="button"
  data-gtm-name="[[nome-button]]"
  data-gtm-subname="modal-fale-conosco"
 >
</div>
```

| Variavel  |  Descrição  | Exemplo |
| :-------- | :---------- | :------ | 
| [[nome-button]]  | Deve retornar o nome do botão clicado | "inciar-chat", "fechar" |



<br />

- **Ao selecionar alguma opção no modal "Fale conosco"**<br />

```html
<!-- Use se os atributos no elemento a ser clicado -->
<div  
  data-gtm-type="select"
  data-gtm-name="[[opcao]]"
  data-gtm-subname="modal-fale-conosco"
 >
</div>
```

| Variavel  |  Descrição  | Exemplo |
| :-------- | :---------- | :------ | 
| [[opcao]]  | Deve retornar a opção selecionada | "help-desk-corretores", "auto-nucleo-de-negocios-emissao" e etc |



<br />


---

> Em caso de dúvidas, estamos a disposição.

<br />

<script> document.querySelector('h1').style.display = 'none' </script>

