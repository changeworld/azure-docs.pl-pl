---
title: Aplikacja mobilna, która wywołuje interfejsy API sieci Web — wywoływanie interfejsu API sieci Web | Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację mobilną wywołującą interfejsy API sieci Web (wywołując internetowy interfejs API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76f0cddfa889376d3795726e74d82e53417b31f1
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413579"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Aplikacja mobilna, która wywołuje interfejsy API sieci Web — wywołuje interfejs API sieci Web

Po podpisaniu aplikacji w użytkowniku i otrzymaniu tokenów MSAL uwidacznia kilka informacji o użytkowniku, środowisku użytkownika i wystawionych tokenach. Twoja aplikacja może używać tych wartości do wywoływania internetowego interfejsu API lub wyświetlania użytkownikowi komunikatu powitalnego.

Po pierwsze Przyjrzyjmy się wynikowi MSAL. Następnie dowiesz się, `AuthenticationResult` jak używać tokenu dostępu z lub `result` do wywoływania chronionego internetowego interfejsu API.

## <a name="msal-result"></a>Wynik MSAL
MSAL udostępnia następujące wartości: 

- `AccessToken`: Służy do wywoływania chronionych interfejsów API sieci Web w żądaniu HTTP okaziciela.
- `IdToken`: Zawiera użyteczne informacje o zalogowanym użytkowniku, takie jak nazwa użytkownika, dzierżawa główna i unikatowy identyfikator magazynu.
- `ExpiresOn`: Czas wygaśnięcia tokenu. MSAL obsługuje Autoodświeżanie aplikacji.
- `TenantId`: Identyfikator dzierżawy, za pomocą której zalogowany jest użytkownik. Dla użytkowników-Gości (Azure Active Directory B2B) ta wartość będzie identyfikować dzierżawcę, za pomocą którego zalogowano się użytkownik, a nie dzierżawy głównej użytkownika.  
- `Scopes`: Zakresy, które zostały przyznane z tokenem. Przyznane zakresy mogą być podzbiorem żądanych zakresów.

MSAL udostępnia również streszczenie dla `Account`. `Account` Reprezentuje konto zalogowanego bieżącego użytkownika.

- `HomeAccountIdentifier`: Identyfikator dzierżawy głównej użytkownika.
- `UserName`: Preferowana nazwa użytkownika. Może to być puste dla Azure Active Directory B2C użytkowników.
- `AccountIdentifier`: Identyfikator zalogowanego użytkownika. Ta wartość będzie taka sama jak `HomeAccountIdentifier` wartość w większości przypadków, chyba że użytkownik jest gościem w innej dzierżawie.

## <a name="call-an-api"></a>Wywoływanie interfejsu API

Po uzyskaniu tokenu dostępu można łatwo wywołać internetowy interfejs API. Aplikacja będzie używać tokenu do konstruowania żądania HTTP, a następnie uruchamiania żądania.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put access token in HTTP request.
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph.
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="making-several-api-requests"></a>Tworzenie kilku żądań interfejsu API

Jeśli konieczne jest Wywołaj ten sam interfejs API kilka razy lub jeśli musisz wywołać wiele interfejsów API, weź pod uwagę następujące kwestie podczas kompilowania aplikacji:

- Poprzednia zgoda: Platforma tożsamości firmy Microsoft umożliwia aplikacjom uzyskanie zgody użytkownika, ponieważ są wymagane uprawnienia, a nie wszystkie na początku. Za każdym razem, gdy aplikacja jest gotowa do wywołania interfejsu API, powinna zażądać tylko zakresów, których należy użyć.
- **Dostęp warunkowy**: W niektórych scenariuszach po wprowadzeniu kilku żądań interfejsu API mogą wystąpić dodatkowe wymagania dotyczące dostępu warunkowego. Taka sytuacja może wystąpić, jeśli pierwsze żądanie nie ma stosowanych zasad dostępu warunkowego, a aplikacja próbuje uzyskać dostęp do nowego interfejsu API, który wymaga dostępu warunkowego. Aby obsłużyć ten scenariusz, należy przechwycić błędy z żądań dyskretnych i przygotować się do przeprowadzenia żądania interaktywnego.  Aby dowiedzieć się więcej, zobacz [wskazówki dotyczące dostępu warunkowego](conditional-access-dev-guide.md).

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Wywoływanie kilku interfejsów API w programie Xamarin lub platformy UWP — wyrażanie zgody i dostępu warunkowego

Jeśli musisz wywołać kilka interfejsów API dla tego samego użytkownika, po uzyskaniu tokenu dla użytkownika, możesz uniknąć wielokrotnego monitowania użytkownika o poświadczenia, wywołując `AcquireTokenSilent` się w celu uzyskania tokenu.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Sytuacje, w których wymagane jest interakcja, to:

- Użytkownik wyraził zgodę na pierwszy interfejs API, ale teraz musi powyrazić zgody na więcej zakresów (powiększanie zgody)
- Pierwszy interfejs API nie wymagał uwierzytelniania wieloskładnikowego, ale Następna z nich robi.

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przenieś do środowiska produkcyjnego](scenario-mobile-production.md)
