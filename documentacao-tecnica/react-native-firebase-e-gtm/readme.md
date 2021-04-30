![aunica](https://implementacaoaunica.github.io/client/aunica.jpg?raw=true)

> Área - Digital Analytics<br />
> Documento de Especificação Técnica

<br />

## Guia de Estruturação para Tagueamento React Native + Firebase e GTM


> Última atualização: 30/04/2021 <br />

<br />

## Sumário

- [Introdução](#introdu&ccedil;&atilde;o)
- [Instalação do SDK do Firebase](#instala&ccedil;&atilde;-do-sdk-do-firebase)
- [Instalação via NPM](##instala&ccedil;&atilde;-via-npm)
- [Para iOS](#para-ios)
- [Configuração Android](#configura&ccedil;&atilde;o-android)
- [Configuração iOS](#configura&ccedil;&atilde;o-ios)
- [Firebase Analytics](#firebase-analytica)
- [Solution Design](#solution-design)
- [Eventos](#eventos)
- [Visualização de Tela](#visualiza&ccedil;&atilde;o-de-tela)
- [User ID](#user-id)
- [Propriedades de Usuário](#propriedades-de-usu&aacute;rio)
- [Contêiner GTM](#cont&ecirc;iner-gtm)
- [Adicionando o contêiner GTM ao projeto](#adicionando-o-cont&ecirc;iner-gtm-ao-projeto)


## Introdução

<p style='text-align: justify;'>  
Esse documento contém uma breve explicação sobre como deve ser configurada a integração entre GTM (Google Tag Manager) e o Firebase Analytics.
A última versão da SDK do Firebase Analytics possui uma integração nativa com a plataforma de gerenciamento de tags do Google (GTM). Esta integração nos dá autonomia de controlar os disparos de eventos de comportamento de usuário não só no Google Analytics mas também no Firebase Analytics, evitando que tenhamos sempre a necessidade de acionar a T.I. responsável pela aplicação caso sejam necessárias alterações.
Utilizamos como base para esse documento o material do Firebase para React Native: https://rnfirebase.io/
Após a implementação da SDK do Firebase Analytics, são necessários alguns passos para que um contêiner do GTM esteja apto a capturar os eventos disparados pela SDK do Firebase que serão descritos abaixo.</p>

<br />

---

### Instalação do SDK do Firebase

<p style='text-align: justify;'>  
Instalar o Firebase para React Native Firebase requer alguns passos: instalar o módulo NPM, adicionar os arquivos de configuração do Firebase e realizar o rebuilding da aplicação.
</p>

### Instalação via NPM

- Usando NPM
npm install --save @react-native-firebase/app
npm install --save @react-native-firebase/analytics

- Usando Yarn
yarn add @react-native-firebase/app
yarn add @react-native-firebase/analytics

### Para iOS
cd ios/ && pod install


---

### Configuração Android

<p style='text-align: justify;'>Adicionar o arquivo google-services.json (baixado a partir do Console do Firebase) na raiz da pasta /android/app/ -> /android/app/google-services.json.
Adicione a dependëncia do plugin do google-services dentro do arquivo /android/build.gradle:</p>

```html
buildscript {
  dependencies {
    // ... other dependencies
    classpath 'com.google.gms:google-services:4.2.0'
    // Add me --- /\
  }
}
```

<p style='text-align: justify;'>Por fim, execute o plug-in adicionando o seguinte na parte inferior do seu arquivo /android/app/build.gradle:</p>

```html
apply plugin: 'com.google.gms.google-services'
```

--- 

### Configuração iOS

<p style='text-align: justify;'>Usando o Xcode, abra o arquivo /ios/{projectName}.xcodeproj (ou /ios/{projectName}.xcworkspace caso esteja usando Pods).
Clique com o botão direito no nome do projeto e "Add files" ao projeto, como demonstrado abaixo:</p>


![imagem](https://implementacaoaunica.github.io/client/prints/imagem.png?raw=true)

- <p style='text-align: justify;'>Selecione o arquivo GoogleService-Info.plist (baixado através do Console do Firebase), e garanta que o checkbox "Copy items if needed" está marcado.</p>


![imagem2](https://implementacaoaunica.github.io/client/prints/imagem2.png?raw=true)

Abra o arquivo /ios/{projectName}/AppDelegate.m, e adicione ao topo o arquivo o import do Firebase SDK:

```html
#import <Firebase.h>
```

- <p style='text-align: justify;'>No método didFinishLaunchingWithOptions existente, adicione o seguinte na parte superior do método:</p>

```html
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
  // Add me --- \/
  if ([FIRApp defaultApp] == nil) {
    [FIRApp configure];
  }
  // Add me --- /\
  // ...
}
```

--- 

### Firebase Analytics

Ao utilizar o Firebase Anallytics podemos coletar:

- **Eventos:** <p style='text-align: justify;'>o que está acontecendo no seu aplicativo, como ações do usuário, eventos do sistema ou erros.</p>
- **Propriedades do usuário:** <p style='text-align: justify;'>atributos que você define para descrever segmentos da sua base de usuários, como preferência de idioma ou localização geográfica.</p>


#### Solution Design

<p style='text-align: justify;'>O documento Solution Design visa descrever exatamente quais são os momentos que precisamos do disparo das chamadas tags que nos permitem entender o comportamento do usuário.
O documento consiste de uma descrição do momento da interação do usuário com o aplicativo, e a descrição dos parâmetros que devem se enviados junto com os eventos.
O(s) devenvolvedor(es) responsável pela implementação do tagueamento deve olhar a aba ‘Mapa de eventos’, onde teremos a coluna ‘TIPO DE DISPARO DO FIREBASE’ discriminando o evento que deve ser disparado, a coluna ‘DESCRIÇÃO’ descrevendo o momento que o evento deve ser disparado, e as colunas adicionais são parâmetros que devem ou não ser preenchidos de acordo com a linha do evento.</p>


#### Eventos

Exemplo de disparo de eventos, utilizando o método LogEvent:

import react, { useEffect } from 'react';
import { View, Button } from 'react-native';
import analytics from '@react-native-firebase/analytics';

```html
function App() {
  return (
    <View>
      <Button
        title="Continuar"
        onPress={() =>
          analytics().logEvent('select_content', {
            ev_category: 'app:demo:home',
            ev_action: 'click:continuar',
            screen: 'home'
          })
        }
      />
    </View>
  );
}
```

#### Visualização de Tela

Os disparos de visualização de tela devem ser implementados com dois métodos: setCurrentScreen e logEvent:

```html
analytics().setCurrentScreen('home', 'home');
```

```html
analytics().logEvent('view_screen', {screen: 'home'})
```


#### User ID

Após o usuário ser autenticado, pode-se definir o userID da seguinte forma:

```html
analytics().setUserId('123');
```

#### Propriedades de Usuário

Caso seja necessário também se pode definir propriedades de usuário:

```html
analytics().setUserProperty('chave','valor');
```

---

### Contêiner GTM

<p style='text-align: justify;'>Os eventos disparados pelo método logEvent além de serem enviados para a base do Firebase Analytics, serão paralelamente capturados pelo GTM juntamente com seus parâmetros, possibilitando o envio dos dados à outras propriedades do Google Analytics.
Além disso, nesse modelo de tagueamento é possível, pelo GTM, cancelar ou modificar a parametrização dos eventos que são enviados ao Firebase Analytics, tudo isso pelo tag manager, sem necessidade de gerar uma nova build da aplicação.
Para adicionar o contêiner do GTM no projeto, é necessário ter o arquivo .json referente ao mesmo que foi enviado junto com este documento.</p> 

### Adicionando o contêiner GTM ao projeto

- Android

1. No seu arquivo Gradle (geralmente app/build.gradle), adicione a dependência abaixo;

```html
dependencies {
  // ...
  compile 'com.google.android.gms:play-services-tagmanager:11.0.4'
}
```

2. Obtenha o arquivo .json do contêiner GTM que deve ser adicionado no app;
3. Crie a pasta app/main/assets/containers caso ela não exista. Copie o arquivo.json do contêiner para dentro do diretório.

- IOS

1. Adicione o Cocoapod do Google Tag Manager no seu projeto;
- No Terminal, rode o seguinte comando para instalar o Cocoapods:

```html
$ sudo gem install cocoapods
```

- Mude o diretório para a pasta do seu projeto;
- Execute o seguinte comando para criar um arquivo chamado ‘Podfile’
```html
$ sudo gem install cocoapods
```

- No ‘Podfile’, adicione o seguinte (argumento?):
```html
pod 'GoogleTagManager', '~> 6.0'
```

- Execute o seguinte comando para baixar e instalar todas as dependências do Google Tag Manager no seu projeto.

```html
$ pod install
```

2. Obtenha o arquivo .json do contêiner GTM que deve ser adicionado no app;
3. Adicione o arquivo .json obtido ao seu projeto.

- Copie o arquivo .json dentro de uma pasta chamada ‘container’ na raíz do seu projeto do XCode;
- Abre o XCode e dentro do menu ‘File’ selecione a opção ‘Add files to…’;
- Selecione a pasta ‘container’ e clique em ‘Options’ e garanta que a opção ‘Create folder references’

4. Crie, se ainda não existir, a pasta “app/main/assets/containers” dentro do local onde o código fonte de seu aplicativo se encontra;
5. Copie os arquivos de container providos pelo containers.zip, e cole-os na pasta de projeto do seu respectivo aplicativo.



> Em caso de dúvidas, estamos a disposição.

<br />

<script> document.querySelector('h1').style.display = 'none' </script>

