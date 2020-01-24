---
title: Wywoływanie internetowego interfejsu API z poziomu aplikacji demona — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje interfejsy API sieci Web
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5a234e0b5d3a88b722257aa80249f4d80182d12f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702236"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — wywołuje interfejs API sieci Web z aplikacji

Aplikacja demona może wywoływać interfejs API sieci Web z aplikacji demona platformy .NET lub wywołać kilka wstępnie zatwierdzonych interfejsów API sieci Web.

## <a name="calling-a-web-api-daemon-application"></a>Wywoływanie aplikacji demona interfejsu API sieci Web

Oto jak używać tokenu do wywoływania interfejsu API:

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

W przypadku aplikacji demonów interfejsy API sieci Web, które są wywoływane, muszą być wstępnie zatwierdzone. Nie będzie żadnej łącznej zgody z aplikacjami demona (nie ma interakcji z użytkownikiem). Administrator dzierżawy musi wstępnie wyrazić zgodę na aplikację i wszystkie uprawnienia interfejsu API. Jeśli chcesz wywołać kilka interfejsów API, musisz uzyskać token dla każdego zasobu, za każdym razem, gdy wywoła `AcquireTokenForClient`. MSAL użyje pamięci podręcznej tokenów aplikacji, aby uniknąć niepotrzebnych wywołań usługi.

## <a name="next-steps"></a>Następne kroki

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplikacja demona — Przenieś do środowiska produkcyjnego](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplikacja demona — Przenieś do środowiska produkcyjnego](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplikacja demona — Przenieś do środowiska produkcyjnego](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
