---
title: Wywoływanie internetowego interfejsu API z aplikacji demona — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje internetowe interfejsy API
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e440628526dada7655cc71f63fc9fff006cc5ef5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885450"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Aplikacja Demon, która wywołuje internetowe interfejsy API - wywołaj internetowy interfejs API z aplikacji

Aplikacje demonów platformy .NET mogą wywoływać internetowy interfejs API. Aplikacje demonów .NET mogą również wywoływać kilka wstępnie zatwierdzonych interfejsów API sieci Web.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Wywoływanie internetowego interfejsu API z aplikacji demona

Oto jak używać tokenu do wywoływania interfejsu API:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

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

W przypadku aplikacji demonów interfejsy API sieci Web, które dzwonisz, muszą być wstępnie zatwierdzone. Nie ma przyrostowej zgody na aplikacje demonów. (Nie ma interakcji z użytkownikiem). Administrator dzierżawy musi wcześniej udzielić zgody na aplikację i wszystkie uprawnienia interfejsu API. Jeśli chcesz wywołać kilka interfejsów API, musisz uzyskać token dla `AcquireTokenForClient`każdego zasobu, za każdym razem wywołując . MSAL użyje pamięci podręcznej tokenu aplikacji, aby uniknąć niepotrzebnych wywołań usługi.

## <a name="next-steps"></a>Następne kroki

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplikacja Daemon - przejście do produkcji](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplikacja Daemon - przejście do produkcji](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplikacja Daemon - przejście do produkcji](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-production?tabs=java)

---
