---
title: 'Szybki start: Utwórz aplikację sieci web, która uruchamia Immersyjnych czytnika (C#)'
titlesuffix: Azure Cognitive Services
description: W tym przewodniku Szybki Start tworzenie aplikacji sieci web od podstaw i dodawać funkcje Immersyjnych czytnika.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 77d95383c801038c256ccb2bf386ddf06048cf78
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311808"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Szybki start: Utwórz aplikację sieci web, która uruchamia Immersyjnych czytnika (C#)

[Immersyjnych czytnika](https://www.onenote.com/learningtools) to włącznie zaprojektowane narzędzie, które implementuje sprawdzonych technik w celu zrozumienia odczytu.

W tym przewodniku Szybki Start, tworzenie aplikacji sieci web od podstaw i zintegrować Immersyjnych czytnika przy użyciu Immersyjnych SDK czytnika. Pełny przykład pracy tego przewodnika Szybki Start jest dostępny [tutaj](https://github.com/Microsoft/immersive-reader-sdk/samples/quickstart-csharp).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Klucz subskrypcji dla Immersyjnych czytnika. Pobierz, wykonując instrukcje [w instrukcjach](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

## <a name="create-a-web-app-project"></a>Tworzenie projektu aplikacji sieci web

Utwórz nowy projekt w programie Visual Studio przy użyciu szablonu aplikacji sieci Web programu ASP.NET Core za pomocą wbudowanych Model-View-Controller.

![Nowy projekt](./media/vswebapp.png)

![Nowa aplikacja internetowa ASP.NET Core](./media/vsmvc.png)

## <a name="acquire-an-access-token"></a>Uzyskiwanie tokenu dostępu

Potrzebujesz klucz subskrypcji oraz końcowy następnym kroku. Klucz subskrypcji można znaleźć na stronie klucze zasobu przeznaczonego czytnika w witrynie Azure portal. Punkt końcowy usługi można znaleźć na stronie Przegląd.

Kliknij prawym przyciskiem myszy nad projektem w _Eksploratora rozwiązań_ i wybierz polecenie **zarządzania wpisami tajnymi użytkowników**. Spowoduje to otwarcie pliku o nazwie _secrets.json_. Zastąp zawartość tego pliku przy użyciu następujących, podając swoje klucz subskrypcji i punktu końcowego, gdzie jest to odpowiednie.

```json
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
```

Otwórz _Controllers\HomeController.cs_i Zastąp `HomeController` klasy z następującym kodem.

```csharp
public class HomeController : Controller
{
    private readonly string SubscriptionKey;
    private readonly string Endpoint;

    public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
    {
        SubscriptionKey = configuration["SubscriptionKey"];
        Endpoint = configuration["Endpoint"];

        if (string.IsNullOrEmpty(Endpoint) || string.IsNullOrEmpty(SubscriptionKey))
        {
            throw new ArgumentNullException("Endpoint or subscriptionKey is null!");
        }
    }

    public IActionResult Index()
    {
        return View();
    }

    [Route("token")]
    public async Task<string> Token()
    {
        return await GetTokenAsync();
    }

    /// <summary>
    /// Exchange your Azure subscription key for an access token
    /// </summary>
    private async Task<string> GetTokenAsync()
    {
        using (var client = new System.Net.Http.HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
            using (var response = await client.PostAsync(Endpoint, null))
            {
                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
```

## <a name="add-sample-content"></a>Dodawanie przykładowej zawartości

Teraz dodamy kilka przykładowej zawartości do tej aplikacji sieci web. Otwórz _Views\Home\Index.cshtml_ i Zastąp automatycznie wygenerowany kod w tym przykładzie:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js'></script>
<script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
<script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        ImmersiveReader.launchAsync(token, content, { uiZIndex: 1000000 });
    }
</script>
}
```

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Na pasku menu wybierz **Debuguj > Rozpocznij debugowanie**, lub naciśnij **F5** do uruchomienia aplikacji.

W przeglądarce powinien zostać wyświetlony:

![Przykładowa aplikacja](./media/quickstart-result.png)

Po kliknięciu przycisku "Czytelnik Immersyjnych" zobaczysz Immersyjnych czytnik uruchomiona z zawartości na stronie.

![Wszechstronne czytnika](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Kolejne kroki

* Widok [samouczek](./tutorial.md) aby zobaczyć, co jeszcze można zrobić z zestawem SDK Immersyjnych czytnika
* Zapoznaj się z [Immersyjnych czytnika SDK](https://github.com/Microsoft/immersive-reader-sdk) i [Immersyjnych czytnika odwołanie do zestawu SDK](./reference.md)