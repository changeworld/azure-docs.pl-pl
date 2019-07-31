---
title: 'Szybki start: Tworzenie aplikacji sieci Web, która uruchamia czytnik immersyjny zC#'
titlesuffix: Azure Cognitive Services
description: W tym przewodniku szybki start utworzysz aplikację internetową od podstaw i dodasz funkcję interfejsu API czytnika immersyjny.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 978b56f5c38a154c79544c7deb5d49396b1deff9
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688862"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Szybki start: Tworzenie aplikacji sieci Web, która uruchamia czytnik immersyjny (C#)

[Czytnik immersyjny](https://www.onenote.com/learningtools) jest specjalnie zaprojektowanym narzędziem, które implementuje sprawdzone techniki w celu zwiększenia czytelności.

W tym przewodniku szybki start utworzysz aplikację internetową od podstaw i zintegrujesz czytnik immersyjny przy użyciu zestawu SDK czytnika immersyjny. Pełny przykład pracy tego przewodnika Szybki Start jest dostępny [tutaj](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Zasób czytnika immersyjny skonfigurowany do uwierzytelniania Azure Active Directory (Azure AD). Postępuj zgodnie z [tymi instrukcjami](./azure-active-directory-authentication.md) , aby rozpocząć konfigurację. W przypadku konfigurowania przykładowych właściwości projektu konieczne będzie utworzenie niektórych wartości. Zapisz dane wyjściowe sesji w pliku tekstowym do użycia w przyszłości.

## <a name="create-a-web-app-project"></a>Tworzenie projektu aplikacji sieci Web

Utwórz nowy projekt w programie Visual Studio przy użyciu szablonu aplikacji sieci Web ASP.NET Core z wbudowanym kontrolerem widoku modelu.

![Nowy projekt](./media/vswebapp.png)

![Nowa aplikacja sieci Web ASP.NET Core](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>Uzyskiwanie tokenu uwierzytelniania usługi Azure AD

Wymagane są pewne wartości z kroku wymagań wstępnych konfiguracji uwierzytelniania usługi Azure AD dla tej części. Odwołaj się do pliku tekstowego zapisanego w tej sesji.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Kliknij prawym przyciskiem myszy projekt w _Eksplorator rozwiązań_ i wybierz polecenie **Zarządzaj kluczami tajnymi użytkownika**. Spowoduje to otwarcie pliku o nazwie Secret _. JSON_. Zastąp zawartość tego pliku następującym poleceniem, podając wartości właściwości niestandardowych z powyższych.

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

Otwórz plik _Controllers\HomeController.cs_i zastąp go następującym kodem.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
        private readonly string TenantId;     // Azure subscription TenantId
        private readonly string ClientId;     // Azure AD ApplicationId
        private readonly string ClientSecret; // Azure AD Application Service Principal password
        private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

        public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
        {
            TenantId = configuration["TenantId"];
            ClientId = configuration["ClientId"];
            ClientSecret = configuration["ClientSecret"];
            Subdomain = configuration["Subdomain"];

            if (string.IsNullOrWhiteSpace(TenantId))
            {
                throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientId))
            {
                throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(ClientSecret))
            {
                throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
            }

            if (string.IsNullOrWhiteSpace(Subdomain))
            {
                throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
            }
        }

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
        }

        /// <summary>
        /// Get an Azure AD authentication token
        /// </summary>
        private async Task<string> GetTokenAsync()
        {
            string authority = $"https://login.windows.net/{TenantId}";
            const string resource = "https://cognitiveservices.azure.com/";

            AuthenticationContext authContext = new AuthenticationContext(authority);
            ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

            AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

            return authResult.AccessToken;
        }
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Dodawanie pakietu NuGet Microsoft. IdentityModel. clients. ActiveDirectory

W powyższym kodzie są używane obiekty z pakietu NuGet **Microsoft. IdentityModel. clients. ActiveDirectory** , dlatego należy dodać odwołanie do tego pakietu w projekcie.

Otwórz konsolę Menedżera pakietów NuGet z **menu Narzędzia — > Menedżer pakietów NuGet — > konsolę Menedżera pakietów** i wpisz następujące:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

## <a name="add-sample-content"></a>Dodaj przykładową zawartość

Teraz dodamy przykładową zawartość do tej aplikacji sieci Web. Otwórz _Views\Home\Index.cshtml_ i Zastąp automatycznie wygenerowany kod tym przykładem:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
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

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
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
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
    }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Kompilowanie i uruchamianie aplikacji

Na pasku menu wybierz kolejno opcje **debuguj > Rozpocznij debugowanie**lub naciśnij klawisz **F5** , aby uruchomić aplikację.

W przeglądarce powinny zostać wyświetlone następujące tematy:

![Przykładowa aplikacja](./media/quickstart-result.png)

Po kliknięciu przycisku "czytnik immersyjny" zobaczysz czytnik immersyjny uruchamiany z zawartością na stronie.

![Czytnik immersyjny](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Kolejne kroki

* Wyświetl [samouczek](./tutorial.md) , aby zobaczyć, co jeszcze można zrobić za pomocą zestawu SDK czytnika immersyjny
* Poznaj [zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](./reference.md)
