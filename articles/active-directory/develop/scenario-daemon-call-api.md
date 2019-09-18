---
title: Aplikacja demona wywołująca interfejsy API sieci Web (wywołujące interfejsy API sieci Web) — Microsoft Identity platform
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje interfejsy API sieci Web (wywołując interfejsy API sieci Web)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eacb574f20abeb63a9d0ab8caf534eb7abb9784
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056360"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — wywołuje interfejs API sieci Web z aplikacji

Aplikacja demona może wywoływać interfejs API sieci Web z aplikacji demona platformy .NET lub wywołać kilka wstępnie zatwierdzonych interfejsów API sieci Web.

## <a name="calling-a-web-api-from-a-net-daemon-application"></a>Wywoływanie interfejsu API sieci Web z aplikacji demona platformy .NET

Oto jak używać tokenu do wywoływania interfejsu API

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
endpoint = "url to the API" 
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>Wywoływanie kilku interfejsów API

W przypadku aplikacji demonów interfejsy API sieci Web, które są wywoływane, muszą być wstępnie zatwierdzone. Nie będzie żadnej łącznej zgody z aplikacjami demona (nie ma interakcji z użytkownikiem). Administrator dzierżawy musi wstępnie wyrazić zgodę na aplikację i wszystkie uprawnienia interfejsu API. Jeśli chcesz wywołać kilka interfejsów API, musisz uzyskać token dla każdego zasobu, przy każdym wywołaniu `AcquireTokenForClient`. MSAL użyje pamięci podręcznej tokenów aplikacji, aby uniknąć niepotrzebnych wywołań usługi.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja demona — Przenieś do środowiska produkcyjnego](./scenario-daemon-production.md)