---
title: 'Samouczek: Uruchom program Immersive Reader za pomocą pliku Node.js'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację Node.js, która uruchamia czytnik Immersive.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 139dd2ebdabbc91a6de3b0a1eb921b110d47c3f3
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842031"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>Samouczek: Uruchom immersyjny czytnik (node.js)

W [przeglądzie](./overview.md)dowiedziałeś się o tym, czym jest Immersive Reader i jak wdraża sprawdzone techniki poprawy rozumienia czytania dla osób uczących się języków, nowych czytelników i uczniów z różnicami w uczeniu się. W tym samouczku opisano sposób tworzenia aplikacji sieci web Node.js, która uruchamia program Immersive Reader. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie aplikacji sieci Web Node.js za pomocą aplikacji Express
> * Uzyskiwanie tokenu dostępu
> * Uruchom czytnik Immersive Reader z przykładową zawartością
> * Określanie języka zawartości
> * Określanie języka interfejsu czytnika Immersive Reader
> * Uruchom czytnik Immersive Reader z zawartością matematyczną

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Zasób programu Immersive Reader skonfigurowany do uwierzytelniania usługi Azure Active Directory. Postępuj zgodnie [z tymi instrukcjami,](./how-to-create-immersive-reader.md) aby skonfigurować. Podczas konfigurowania właściwości środowiska potrzebne będą niektóre wartości utworzone w tym miejscu. Zapisz dane wyjściowe sesji w pliku tekstowym do wykorzystania w przyszłości.
* [Node.js](https://nodejs.org/) i [Przędza](https://yarnpkg.com)
* Ide, takie jak [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-nodejs-web-app-with-express"></a>Tworzenie aplikacji sieci Web Node.js za pomocą aplikacji Express

Utwórz aplikację internetową Node.js za `express-generator` pomocą narzędzia.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Zainstaluj zależności przędzy i dodać `request` zależności `dotenv`i , które będą używane w dalszej części samouczka.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Uzyskiwanie tokenu uwierzytelniania usługi Azure AD

Następnie napisz interfejs API wewnętrznej bazy danych, aby pobrać token uwierzytelniania usługi Azure AD.

Potrzebujesz kilka wartości z kroku wstępnego konfiguracyjnej eru usługi Azure AD dla tej części. Odsyłanie z powrotem do pliku tekstowego zapisanego w tej sesji.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Po uzyskaniu tych wartości utwórz nowy plik o nazwie _.env_i wklej do niego następujący kod, podając wartości właściwości niestandardowych z góry. Nie należy dołączać cudzysłowów ani znaków "{" i "}".

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Pamiętaj, aby nie zatwierdzać tego pliku do kontroli źródła, ponieważ zawiera on wpisy tajne, które nie powinny być upublicznione.

Następnie otwórz _plik app.js_ i dodaj następujące elementy w górnej części pliku. Spowoduje to załadowanie właściwości zdefiniowanych w pliku env jako zmiennych środowiskowych do nodea.

```javascript
require('dotenv').config();
```

Otwórz plik _routes\index.js_ i zastąp jego zawartość następującym kodem.

Ten kod tworzy punkt końcowy interfejsu API, który uzyskuje token uwierzytelniania usługi Azure AD przy użyciu hasła jednostkowego usługi. Pobiera również poddomenę. Następnie zwraca obiekt zawierający token i poddomenę.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
                headers: {
                    'content-type': 'application/x-www-form-urlencoded'
                },
                url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
                form: {
                    grant_type: 'client_credentials',
                    client_id: process.env.CLIENT_ID,
                    client_secret: process.env.CLIENT_SECRET,
                    resource: 'https://cognitiveservices.azure.com/'
                }
        },
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

Punkt końcowy interfejsu API **getimmersivereaderlaunchparams** powinien być zabezpieczony za jakąś formą uwierzytelniania (na przykład [OAuth),](https://oauth.net/2/)aby uniemożliwić nieautoryzowanym użytkownikom uzyskanie tokenów do użycia w usłudze Immersive Reader i rozliczeniach; że praca wykracza poza zakres tego samouczka.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Uruchom czytnik Immersive Reader z przykładową zawartością

1. Otwórz _views\layout.mops_i dodaj następujący `head` kod pod `body` tagiem przed tagiem. Tagi te `script` załadować [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) i jQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Otwórz _views\index.mops_i zastąp jego zawartość następującym kodem. Ten kod wypełnia stronę próbną zawartością i dodaje przycisk uruchamiający czytnik Immersive Reader.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. Nasza aplikacja internetowa jest już gotowa. Uruchom aplikację, uruchamiając:

    ```bash
    npm start
    ```

4. Otwórz przeglądarkę i _http://localhost:3000_przejdź do pliku . Powyższe treści powinny być widoczne na stronie. Kliknij przycisk **Czytnik wciągający,** aby uruchomić czytnik Immersive Reader ze swoją zawartością.

## <a name="specify-the-language-of-your-content"></a>Określanie języka zawartości

Czytnik Immersive Reader obsługuje wiele różnych języków. Możesz określić język zawartości, wykonując poniższe czynności.

1. Otwórz _views\index.mops_ i dodaj poniższy kod poniżej tagu `p(id=content)` dodanego w poprzednim kroku. Ten kod dodaje niektóre treści hiszpańskiej zawartości do strony.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. W kodzie JavaScript dodaj powyższe `ImmersiveReader.launchAsync`połączenie do . Ten kod przekazuje zawartość hiszpańską do czytnika Immersive Reader.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Przejdź _http://localhost:3000_ do ponownie. Powinieneś zobaczyć hiszpański tekst na stronie, a po kliknięciu na **Immersive Reader**, pojawi się również w Immersive Reader.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Określanie języka interfejsu czytnika Immersive Reader

Domyślnie język interfejsu czytnika Immersive Reader jest zgodny z ustawieniami języka przeglądarki. Można również określić język interfejsu Immersive Reader z następującym kodem.

1. W _views\index.mops_, zastąp wywołanie poniższym `ImmersiveReader.launchAsync(token, subdomain, content)` kodem.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Przejdź _http://localhost:3000_do pliku . Po uruchomieniu czytnika Immersive Reader interfejs będzie wyświetlany w języku francuskim.

## <a name="launch-the-immersive-reader-with-math-content"></a>Uruchom czytnik Immersive Reader z zawartością matematyczną

Zawartość matematyczną można dołączyć do programu Immersive Reader za pomocą [pliku MathML](https://developer.mozilla.org/en-US/docs/Web/MathML).

1. Zmodyfikuj _views\index.mops,_ aby `ImmersiveReader.launchAsync`uwzględnić następujący kod powyżej wywołania:

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

2. Przejdź _http://localhost:3000_do pliku . Po uruchomieniu czytnika Immersive Reader i przewinięciu do dołu zobaczysz formułę matematyczną.

## <a name="next-steps"></a>Następne kroki

* Poznaj [immersyjny moduł SDK czytnika](https://github.com/microsoft/immersive-reader-sdk) i [immersyjny moduł SDK czytnika](./reference.md)
* Wyświetlanie przykładów kodu w [usłudze GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)
