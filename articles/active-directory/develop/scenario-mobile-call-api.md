---
title: Aplikacja mobilna wywołuje interfejsy API — w wywołuje internetowy interfejs API sieci web | Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje internetowych interfejsów API (wywoływanie interfejsu web API)
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
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44b6c203583a082228c2ba1f4c5f6fdb04d059be
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962388"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Aplikacja mobilna, która wywołuje interfejsy API — w sieci web wywołania interfejsu API sieci web

Po aplikacji został zalogowany użytkownik i odebrane tokeny, biblioteka MSAL udostępnia kilka rodzajów informacji dotyczących użytkownika, środowiska użytkownika i tokeny wystawione. Aplikacja umożliwia te wartości wywołanie interfejsu API sieci web lub wyświetlania komunikatu powitalnego do użytkownika.

Najpierw przyjrzymy wynik MSAL. Następnie omówimy sposób użycia tokenu dostępu z `AuthenticationResult` lub `result` do wywoływania chronionego internetowego interfejsu API.

## <a name="msal-result"></a>Wynik biblioteki MSAL
Biblioteka MSAL zawiera następujące wartości: 

- `AccessToken`: Używany do wywoływania chronionego internetowego interfejsów API w żądaniu HTTP elementu nośnego.
- `IdToken`: Zawiera przydatne informacje dotyczące zalogowanego użytkownika, takie jak nazwa użytkownika, głównej dzierżawy i unikatowy identyfikator dla magazynu.
- `ExpiresOn`: Czas wygaśnięcia tokenu. Biblioteka MSAL obsługuje automatyczne odświeżanie dla aplikacji.
- `TenantId`: Identyfikator dzierżawy, w której użytkownik jest zalogowany przy użyciu. Dla użytkowników-gości (Azure Active Directory B2B) ta wartość będzie identyfikować dzierżawy, który użytkownik zalogowany za pomocą nie dzierżawy macierzystego użytkownika.  
- `Scopes`: Zakresy, które zostały przyznane token. Udzielone zakresy może być podzbiorem zakresy, których szukasz.

Biblioteka MSAL również udostępnia abstrakcję do `Account`. `Account` Reprezentuje konto logowania bieżącego użytkownika.

- `HomeAccountIdentifier`: Identyfikator dzierżawy głównej użytkownika.
- `UserName`: Nazwa użytkownika preferowany przez użytkownika. Może to być pusty dla użytkowników usługi Azure Active Directory B2C.
- `AccountIdentifier`: Identyfikator zalogowanego użytkownika. Ta wartość będzie taka sama jak `HomeAccountIdentifier` wartość w większości przypadków, chyba że użytkownik Gość w innej dzierżawie.

## <a name="call-an-api"></a>Wywoływanie interfejsu API

Po utworzeniu tokenu dostępu, jest łatwy do wywołania interfejsu API sieci web. Twoja aplikacja będzie używać tokenu do konstruowania żądania HTTP, a następnie uruchom żądanie.

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

```CSharp
httpClient = new HttpClient();

// Put access token in HTTP request.
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Co kilka żądań interfejsu API

Czy potrzebne do wywoływania tego samego interfejsu API kilka razy, czy należy wywołać wiele interfejsów API, wziąć pod uwagę następujące pod uwagę podczas tworzenia aplikacji:

- **Przyrostowe zgody**: Platforma tożsamości firmy Microsoft umożliwia aplikacjom uzyskanie zgody użytkownika, jakie uprawnienia są wymagane, a nie wszystkie na początku. Każdorazowo, gdy aplikacja jest gotowa do wywołania interfejsu API powinien go zażądać zakresy, trzeba go użyć.
- **Dostęp warunkowy**: W niektórych scenariuszach możesz otrzymać wymagania dotyczące dostępu warunkowego dodatkowe po wprowadzeniu kilka żądań interfejsu API. Może to nastąpić, jeśli pierwsze żądanie ma nie zastosowano żadnych zasad dostępu warunkowego, a aplikacja próbuje dyskretnie dostęp do nowego interfejsu API, który wymaga dostępu warunkowego. Aby obsługiwać ten scenariusz, należy przechwytywania błędów z żądań w trybie dyskretnym i przygotuj interaktywne żądania.  Aby dowiedzieć się więcej, zobacz [wskazówki dotyczące dostępu warunkowego](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przenoszenie do środowiska produkcyjnego](scenario-mobile-production.md)
