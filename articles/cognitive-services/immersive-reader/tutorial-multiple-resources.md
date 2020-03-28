---
title: 'Samouczek: Integracja wielu zasobów programu Immersive Reader'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz aplikację Node.js, która uruchamia program Immersive Reader przy użyciu wielu zasobów programu Immersive Reader.
author: skamal
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: skamal
ms.openlocfilehash: 3912d55b13f3977818e8d898efa651ffeb1a798a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046275"
---
# <a name="tutorial-integrate-multiple-immersive-reader-resources"></a>Samouczek: Integracja wielu zasobów programu Immersive Reader

W [przeglądzie](./overview.md)dowiedziałeś się o tym, czym jest Immersive Reader i jak wdraża sprawdzone techniki poprawy rozumienia czytania dla osób uczących się języków, nowych czytelników i uczniów z różnicami w uczeniu się. W programie [Node.js Quickstart](./quickstart-nodejs.md)opisano, jak korzystać z programu Immersive Reader z jednym zasobem. W tym samouczku opisano, jak zintegrować wiele zasobów immersive reader w tej samej aplikacji. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie wielu zasobów programu Immersive Reader w istniejącej grupie zasobów
> * Uruchom czytnik Immersive Reader przy użyciu wielu zasobów

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Postępuj zgodnie z [przewodnikiem Szybki start,](./quickstart-nodejs.md) aby utworzyć aplikację internetową, która uruchamia program Immersive Reader z nodejs. W tym przewodniku Szybki start można skonfigurować jeden zasób programu Immersive Reader. Będziemy budować na szczycie tego w tym samouczku.

## <a name="create-the-immersive-reader-resources"></a>Tworzenie zasobów programu Immersive Reader

Postępuj zgodnie [z tymi instrukcjami,](./how-to-create-immersive-reader.md) aby utworzyć każdy zasób programu Immersive Reader. Skrypt **Create-ImmersiveReaderResource** `ResourceName`ma `ResourceSubdomain`, `ResourceLocation` i jako parametry. Powinny one być unikatowe dla każdego tworzonego zasobu. Pozostałe parametry powinny być takie same, jak podczas konfigurowania pierwszego zasobu programu Immersive Reader. W ten sposób każdy zasób może być połączony z tą samą grupą zasobów platformy Azure i aplikacją usługi Azure AD.

W poniższym przykładzie pokazano, jak utworzyć dwa zasoby, jeden w WestUS i inny w U. EastUS. Zwróć uwagę na `ResourceName` `ResourceSubdomain`unikalne `ResourceLocation`wartości dla , i .

```azurepowershell-interactive
Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_wus `
  -ResourceSubdomain resource-subdomain-wus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation westus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>

Create-ImmersiveReaderResource
  -SubscriptionName <SUBSCRIPTION_NAME> `
  -ResourceName Resource_name_eus `
  -ResourceSubdomain resource-subdomain-eus `
  -ResourceSKU <RESOURCE_SKU> `
  -ResourceLocation eastus `
  -ResourceGroupName <RESOURCE_GROUP_NAME> `
  -ResourceGroupLocation <RESOURCE_GROUP_LOCATION> `
  -AADAppDisplayName <AAD_APP_DISPLAY_NAME> `
  -AADAppIdentifierUri <AAD_APP_IDENTIFIER_URI> `
  -AADAppClientSecret <AAD_APP_CLIENT_SECRET>
```

## <a name="add-resources-to-environment-configuration"></a>Dodawanie zasobów do konfiguracji środowiska

W przewodniku Szybki start utworzono plik `TenantId` `ClientId`konfiguracji `ClientSecret`środowiska `Subdomain` zawierający program , i parametry. Ponieważ wszystkie zasoby używają tej samej aplikacji usługi Azure AD, `ClientId`możemy `ClientSecret`użyć tych samych wartości dla `TenantId`programu , i . Jedyną zmianą, która musi zostać wytypo dokonywać listy każdej poddomeny dla każdego zasobu.

Nowy plik __.env__ powinien teraz wyglądać następująco:

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN_WUS={YOUR_WESTUS_SUBDOMAIN}
SUBDOMAIN_EUS={YOUR_EASTUS_SUBDOMAIN}
```

Pamiętaj, aby nie zatwierdzać tego pliku do kontroli źródła, ponieważ zawiera on wpisy tajne, które nie powinny być upublicznione.

Następnie zmodyfikujemy plik _routes\index.js,_ który utworzyliśmy w celu obsługi naszych wielu zasobów. Zastąp jego zawartość następującym kodem.

Podobnie jak poprzednio, ten kod tworzy punkt końcowy interfejsu API, który uzyskuje token uwierzytelniania usługi Azure AD przy użyciu hasła jednostkowego usługi. Tym razem umożliwia użytkownikowi określenie lokalizacji zasobu i przekazanie jej jako parametru zapytania. Następnie zwraca obiekt zawierający token i odpowiednią poddomenę.

```javascript
var express = require('express');
var router = express.Router();
var request = require('request');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        request.post({
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
        function(err, resp, tokenResult) {
            if (err) {
                console.log(err);
                return res.status(500).send('CogSvcs IssueToken error');
            }

            var tokenResultParsed = JSON.parse(tokenResult);

            if (tokenResultParsed.error) {
                console.log(tokenResult);
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }

            var token = tokenResultParsed.access_token;

            var subdomain = "";
            var region = req.query && req.query.region;
            switch (region) {
                case "eus":
                    subdomain = process.env.SUBDOMAIN_EUS
                    break;
                case "wus":
                default:
                    subdomain = process.env.SUBDOMAIN_WUS
            }

            return res.send({token, subdomain});
        });
    } catch (err) {
        console.log(err);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

Punkt końcowy interfejsu API **getimmersivereaderlaunchparams** powinien być zabezpieczony za jakąś formą uwierzytelniania (na przykład [OAuth),](https://oauth.net/2/)aby uniemożliwić nieautoryzowanym użytkownikom uzyskanie tokenów do użycia w usłudze Immersive Reader i rozliczeniach; że praca wykracza poza zakres tego samouczka.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Uruchom czytnik Immersive Reader z przykładową zawartością

1. Otwórz _views\index.mops_i zastąp jego zawartość następującym kodem. Ten kod wypełnia stronę próbką i dodaje dwa przyciski uruchamiające program Immersive Reader. Jeden do uruchomienia Immersive Reader dla zasobu EastUS, a drugi dla zasobu WestUS.

    ```pug
    doctype html
    html
        head
            title Immersive Reader Quickstart Node.js

            link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

            // A polyfill for Promise is needed for IE11 support.
            script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

            script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js')
            script(src='https://code.jquery.com/jquery-3.3.1.min.js')

            style(type="text/css").
                .immersive-reader-button {
                background-color: white;
                margin-top: 5px;
                border: 1px solid black;
                float: right;
                }
        body
            div(class="container")
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("wus")') WestUS Immersive Reader
                button(class="immersive-reader-button" data-button-style="icon" data-locale="en" onclick='handleLaunchImmersiveReader("eus")') EastUS Immersive Reader

                h1(id="ir-title") About Immersive Reader
                div(id="ir-content" lang="en-us")
                p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

                    ul
                        li Shows content in a minimal reading view
                        li Displays pictures of commonly used words
                        li Highlights nouns, verbs, adjectives, and adverbs
                        li Reads your content out loud to you
                        li Translates your content into another language
                        li Breaks down words into syllables

                h3 The Immersive Reader is available in many languages.

                p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
                p(lang="zh-cn") 沉浸式阅读器支持许多语言
                p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
                p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

    script(type="text/javascript").
    function getTokenAndSubdomainAsync(region) {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "/GetTokenAndSubdomain",
                    type: "GET",
                    data: {
                        region: region
                    },
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }

        function handleLaunchImmersiveReader(region) {
            getTokenAndSubdomainAsync(region)
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
                    // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }

        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    ```

3. Nasza aplikacja internetowa jest już gotowa. Uruchom aplikację, uruchamiając:

    ```bash
    npm start
    ```

4. Otwórz przeglądarkę i [http://localhost:3000](http://localhost:3000)przejdź do pliku . Powyższe treści powinny być widoczne na stronie. Kliknij przycisk **EastUS Immersive Reader** lub **WestUS Immersive Reader,** aby uruchomić czytnik Immersive Reader przy użyciu odpowiednich zasobów.

## <a name="next-steps"></a>Następne kroki

* Poznaj [immersyjny moduł SDK czytnika](https://github.com/microsoft/immersive-reader-sdk) i [immersyjny moduł SDK czytnika](./reference.md)
* Wyświetlanie przykładów kodu w [usłudze GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp)