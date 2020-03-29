---
title: Buforowanie tokenu uwierzytelniania
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak buforować token uwierzytelniania.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: e652aa29b1c1935fcc4887dbe13ef9b683a8bd05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946167"
---
# <a name="how-to-cache-the-authentication-token"></a>Jak buforować token uwierzytelniania

W tym artykule pokazano, jak buforować token uwierzytelniania w celu zwiększenia wydajności aplikacji.

## <a name="using-aspnet"></a>Korzystanie z ASP.NET

Zaimportuj pakiet **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet, który jest używany do uzyskiwania tokenu. Następnie użyj następującego kodu, `AuthenticationResult`aby uzyskać program ,używając wartości uwierzytelniania, które zostały utworzone podczas [tworzenia zasobu Czytnik immersyjny.](./how-to-create-immersive-reader.md)

```csharp
private async Task<AuthenticationResult> GetTokenAsync()
{
    AuthenticationContext authContext = new AuthenticationContext($"https://login.windows.net/{TENANT_ID}");
    ClientCredential clientCredential = new ClientCredential(CLIENT_ID, CLIENT_SECRET);
    AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", clientCredential);
    return authResult;
}
```

Obiekt `AuthenticationResult` ma `AccessToken` właściwość, która jest rzeczywisty token, który będzie używany podczas uruchamiania Immersive Reader przy użyciu SDK. Posiada również `ExpiresOn` właściwość, która oznacza, kiedy token wygaśnie. Przed uruchomieniem Immersive Reader, można sprawdzić, czy token wygasł i uzyskać nowy token tylko wtedy, gdy wygasł.

## <a name="using-nodejs"></a>Korzystanie z node.js

Dodaj pakiet npm [**żądania**](https://www.npmjs.com/package/request) do projektu. Użyj następującego kodu, aby uzyskać token, używając wartości uwierzytelniania, które zostały utworzone podczas [tworzenia zasobu Immersive Reader](./how-to-create-immersive-reader.md).

```javascript
router.get('/token', function(req, res) {
    request.post(
        {
            headers: { 'content-type': 'application/x-www-form-urlencoded' },
            url: `https://login.windows.net/${TENANT_ID}/oauth2/token`,
            form: {
                grant_type: 'client_credentials',
                client_id: CLIENT_ID,
                client_secret: CLIENT_SECRET,
                resource: 'https://cognitiveservices.azure.com/'
            }
        },
        function(err, resp, json) {
            const result = JSON.parse(json);
            return res.send({
                access_token: result.access_token,
                expires_on: result.expires_on
            });
        }
    );
});
```

Właściwość `expires_on` jest datą i godziną wygaśnięcia tokenu, wyrażoną jako liczba sekund od 1 stycznia 1970 UTC. Użyj tej wartości, aby ustalić, czy token wygasł przed podjęciem próby uzyskania nowego.

```javascript
async function getToken() {
    if (Date.now() / 1000 > CREDENTIALS.expires_on) {
        CREDENTIALS = await refreshCredentials();
    }
    return CREDENTIALS.access_token;
}
```

## <a name="next-steps"></a>Następne kroki

* Poznaj [dokumentację immersyjnego sdk czytnika](./reference.md)