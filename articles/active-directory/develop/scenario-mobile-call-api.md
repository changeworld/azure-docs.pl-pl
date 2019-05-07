---
title: Aplikacja mobilna wywołuje interfejsy API — w wywołuje internetowy interfejs API sieci web | Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje interfejsy API sieci Web (wywoływanie interfejsu API sieci Web)
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd65b9f97c373c55a3486e06e83fca7cf824cad
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075118"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Aplikacja mobilna, która wywołuje interfejsy API — w sieci web wywołania interfejsu API sieci web

Gdy aplikacja zostanie zalogowany użytkownik i odebrane tokeny, biblioteka MSAL udostępnia kilka rodzajów informacji o użytkowniku, ich środowiska i tokeny wystawione. Aplikacja umożliwia te wartości wywołanie interfejsu API sieci web lub wyświetlania komunikatu powitalnego do użytkownika.

Po pierwsze, przeanalizujemy wynik MSAL, a następnie sposób użycia tokenu dostępu z `AuthenticationResult` lub `result` do wywoływania chronionego internetowego interfejsu API.

## <a name="msal-result"></a>Wynik biblioteki MSAL

- `AccessToken`: Używany do wywoływania chronionego internetowego interfejsów API w żądaniu HTTP elementu nośnego.
- `IdToken`: Zawiera przydatne oświadczenia dotyczące zalogowanego użytkownika, takie jak ich nazwy głównej dzierżawy i unikatowy identyfikator dla magazynu.
- `ExpiresOn`: czas wygaśnięcia tokenu. Biblioteka MSAL obsługuje automatyczne odświeżanie dla aplikacji.
- `TenantId`: Identyfikator dzierżawy użytkownika użyte do zalogowania. Dla użytkowników-gości (Azure AD B2B) będzie to dzierżawy użytkownik zalogowany za pomocą nie ich dzierżawy głównej.  
- `Scopes`: zakresy, które zostały przyznane token. Może to być podzbiór użytkownik zażądał.

Ponadto biblioteka MSAL udostępnia abstrakcję do `Account`. Konto reprezentuje bieżący użytkownik zalogowany do konta.

- `HomeAccountIdentifier`: Identyfikator dzierżawy głównej użytkownika.
- `UserName`: Preferowany nazwy użytkownika. Może to być puste dla użytkowników usługi Azure AD B2C.
- `AccountIdentifier`: Identyfikator zalogowanego użytkownika. Są to taka sama jak `HomeAccountIdentifier` w większości przypadków, chyba że użytkownik jest gościa w innej dzierżawie.

## <a name="calling-an-api"></a>Wywoływanie interfejsu API

Po utworzeniu tokenu dostępu, gotowe, jest prosty do wywołania interfejsu API sieci web. Aplikacja proszę tokenu, konstruowania żądania HTTP i wykonaj go.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called graph, process data and send to UI 
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put Access Token in HTTP request 
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

        // Put Access token in HTTP Request
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

            // Successfully got data from Graph
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put Access token in HTTP request 
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Co kilka żądań interfejsu API

Jeśli potrzebne do wywoływania tego samego interfejsu API kilka razy lub wiele interfejsów API, istnieją pewne dodatkowe zagadnienia dotyczące podczas tworzenia aplikacji:

- ***Przyrostowe zgody***: Platforma tożsamości firmy Microsoft umożliwia aplikacjom uzyskanie zgody użytkownika, ponieważ uprawnienia są wymagane, a nie wszystkie z góry. Każdorazowo, gdy aplikacja jest gotowa do wywołania interfejsu API, powinien on zażądać zakresy, które zamierza użyć.
- ***Dostęp warunkowy***: W niektórych scenariuszach możesz uzyskać dodatkowe wymagania dotyczące dostępu warunkowego w przypadku wysyłania wielu żądań interfejsu API. Aby obsługiwać ten scenariusz, należy przechwytywania błędów z żądań w trybie dyskretnym i przygotuj interaktywne żądania. Może to nastąpić, jeśli pierwsze żądanie ma nie zastosowano żadnych zasad dostępu warunkowego, a aplikacja próbuje dyskretnie dostęp do nowego interfejsu API, który wymaga dostępu warunkowego. Aby dowiedzieć się więcej, zobacz [wskazówki dotyczące dostępu warunkowego](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-mobile-production.md)
