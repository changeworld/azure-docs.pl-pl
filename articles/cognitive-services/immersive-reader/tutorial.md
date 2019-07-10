---
title: 'Samouczek: Uruchom Immersyjnych czytnika przy użyciu środowiska Node.js'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację w języku Node.js, która uruchamia Immersyjnych czytnika.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: f8697042ed46e0ff333f736454346908d76cf039
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718378"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Samouczek: Uruchamianie czytnika immersyjnego (Node.js)

W [Przegląd](./overview.md), omówiono Immersyjnych Czytelnik jest i jak go implementuje sprawdzonych technik w celu zrozumienia odczytu language learners, czytniki pojawiających się i uczniom i studentom uczenia różnice. W tym samouczku omówiono sposób tworzenia aplikacji internetowej Node.js uruchamiającego Immersyjnych czytnika. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji sieci web Node.js za pomocą platformy Express
> * Uzyskiwanie tokenu dostępu
> * Uruchom Immersyjnych czytnik z przykładowej zawartości
> * Wybierz język zawartości
> * Wybierz język interfejsu Immersyjnych czytnika
> * Uruchom Immersyjnych czytnika z zawartością matematyczne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Klucz subskrypcji dla Immersyjnych czytnika. Pobierz, wykonując instrukcje [w instrukcjach](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).
* [Node.js](https://nodejs.org/) i [Yarn](https://yarnpkg.com)
* Środowisko IDE, takie jak [programu Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Tworzenie aplikacji sieci web Node.js za pomocą platformy Express

Tworzenie aplikacji sieci web Node.js za pomocą `express-generator` narzędzia.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Zainstaluj zależności usługi yarn oraz Dodaj zależności `request` i `dotenv`, który będzie używany w dalszej części tego samouczka.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-access-token"></a>Uzyskiwanie tokenu dostępu

Następnie zapisz zaplecza interfejsu API w celu pobrania tokenu dostępu przy użyciu klucza subskrypcji. Potrzebujesz klucz subskrypcji oraz końcowy następnym kroku. Klucz subskrypcji można znaleźć na stronie klucze zasobu przeznaczonego czytnika w witrynie Azure portal. Punkt końcowy usługi można znaleźć na stronie Przegląd.

Po utworzeniu usługi klucz subskrypcji i punkt końcowy, Utwórz nowy plik o nazwie _ENV_i wklej następujący kod, zastępując `{YOUR_SUBSCRIPTION_KEY}` i `{YOUR_ENDPOINT}` klucz subskrypcji i punktu końcowego, odpowiednio.

```text
SUBSCRIPTION_KEY={YOUR_SUBSCRIPTION_KEY}
ENDPOINT={YOUR_ENDPOINT}
```

Upewnij się, nie można przekazać ten plik do kontroli źródła, ponieważ zawiera wpisy tajne, których nie powinni należeć do publicznych.

Następnie otwórz _app.js_ i Dodaj następujący kod do górnej części pliku. Spowoduje to załadowanie klucz subskrypcji i punktu końcowego jako zmienne środowiskowe do węzła.

```javascript
require('dotenv').config();
```

Otwórz _routes\index.js_ plik i że importowania w górnej części pliku:

```javascript
var request = require('request');
```

Następnie dodaj następujący kod bezpośrednio poniżej tego wiersza. Ten kod tworzy punkt końcowy interfejsu API, który uzyskuje token dostępu przy użyciu swój klucz subskrypcji, a następnie zwraca token.

```javascript
router.get('/token', function(req, res, next) {
  request.post({
    headers: {
        'Ocp-Apim-Subscription-Key': process.env.SUBSCRIPTION_KEY,
        'content-type': 'application/x-www-form-urlencoded'
    },
    url: process.env.ENDPOINT
  },
  function(err, resp, token) {
    return res.send(token);
  });
});
```

Ten punkt końcowy interfejsu API powinien zostać zabezpieczony za jakąś formę uwierzytelniania (na przykład [OAuth](https://oauth.net/2/)); czy pracy wykracza poza zakres tego samouczka.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Uruchom Immersyjnych czytnik z przykładowej zawartości

1. Otwórz _views\layout.pug_i Dodaj następujący kod w obszarze `head` tag przed `body` tagu. Te `script` załadować tagów [Immersyjnych SDK czytnika](https://github.com/Microsoft/immersive-reader-sdk) i bibliotece jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Otwórz _views\index.pug_i zastąp jego zawartość następującym kodem. Ten kod powoduje wypełnienie strony z zawartością niektóre przykładowe i dodaje przycisk, który uruchamia Immersyjnych czytnika.

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.
        function launchImmersiveReader() {
          // First, get a token using our /token endpoint
          $.ajax('/token', { success: token => {
            // Second, grab the content from the page
            const content = {
              title: document.getElementById('title').innerText,
              chunks: [ {
                content: document.getElementById('content').innerText + '\n\n',
                lang: 'en'
              } ]
            };

            // Third, launch the Immersive Reader
            ImmersiveReader.launchAsync(token, content);
          }});
        }
    ```

3. Nasza aplikacja sieci web jest teraz gotowy. Uruchom aplikację, uruchamiając:

    ```bash
    npm start
    ```

4. Otwórz przeglądarkę i przejdź do _http://localhost:3000_ . Na stronie powinno zostać wyświetlone powyższej zawartości. Kliknij przycisk **Immersyjnych czytnika** przycisk, aby uruchomić czytnik przeznaczonego do zawartości.

## <a name="specify-the-language-of-your-content"></a>Wybierz język zawartości

Wszechstronne czytnika zapewnia obsługę wielu różnych językach. Możesz określić język zawartości, wykonując poniższe kroki.

1. Otwórz _views\index.pug_ i Dodaj następujący kod poniżej `p(id=content)` tag dodanej w poprzednim kroku. Ten kod dodaje część zawartości hiszpański zawartość do strony.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. W kodzie JavaScript Dodaj następujący kod powyżej wywołanie `ImmersiveReader.launchAsync`. Ten kod przekazuje hiszpański zawartości do Immersyjnych czytnika.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Przejdź do _http://localhost:3000_ ponownie. Powinien zostać wyświetlony tekstu w języku hiszpańskim na stronie, a po kliknięciu **Immersyjnych czytnika**, zostanie wyświetlona do Immersyjnych czytnika.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Wybierz język interfejsu Immersyjnych czytnika

Domyślnie język interfejsu Immersyjnych czytnika odpowiada ustawień języka w przeglądarce. Można również określić język interfejsu Immersyjnych czytnika, z następującym kodem.

1. W _views\index.pug_, Zastąp wywołanie `ImmersiveReader.launchAsync(token, content)` z poniższym kodem.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, content, options);
    ```

2. Przejdź do strony _http://localhost:3000_ . Po uruchomieniu Immersyjnych czytnika, interfejs będą wyświetlane w języku francuskim.

## <a name="launch-the-immersive-reader-with-math-content"></a>Uruchom Immersyjnych czytnika z zawartością matematyczne

Może zawierać treść matematyczne w czytniku Immersyjnych przy użyciu [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Modyfikowanie _views\index.pug_ obejmujący następujący kod nad wywołanie `ImmersiveReader.launchAsync`:

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Przejdź do strony _http://localhost:3000_ . Po uruchomieniu czytnik wszechstronne i przewiń w dół, zobaczysz formuły matematyczne.

## <a name="next-steps"></a>Kolejne kroki

* Zapoznaj się z [Immersyjnych czytnika SDK](https://github.com/Microsoft/immersive-reader-sdk) i [Immersyjnych czytnika odwołanie do zestawu SDK](./reference.md)
* Wyświetl przykłady kodu na [GitHub](https://github.com/microsoft/immersive-reader-sdk/samples/advanced-csharp)