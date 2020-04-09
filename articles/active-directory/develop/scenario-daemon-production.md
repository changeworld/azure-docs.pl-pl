---
title: Przenoszenie aplikacji demona, która wywołuje internetowe interfejsy API do produkcji — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak przenieść aplikację demona, która wywołuje internetowe interfejsy API do produkcji
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
ms.openlocfilehash: e5d25169aa3daee33a9f02e605cbff91776dc7f1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885433"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Aplikacja Demon, która wywołuje internetowe interfejsy API - przejście do produkcji

Teraz, gdy wiesz, jak uzyskać i używać tokenu dla połączenia usługi do usługi, dowiedz się, jak przenieść aplikację do produkcji.

## <a name="deployment---multitenant-daemon-apps"></a>Wdrażanie — wielodostępne aplikacje demonów

Jeśli jesteś niezależną aplikacją isv tworzącą aplikację demona, która może działać w kilku dzierżawach, musisz upewnić się, że administrator dzierżawy:

- Rezerwy jednostki usługi dla wniosku.
- Wyraża zgodę na wniosek.

Musisz wyjaśnić klientom, jak wykonać te operacje. Aby uzyskać więcej informacji, zobacz [Żądanie zgody dla całej dzierżawy.](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Następne kroki

Oto kilka linków, które pomogą Ci dowiedzieć się więcej:

# <a name="net"></a>[.NET](#tab/dotnet)

- Szybki start: [uzyskaj token i wywołaj interfejs API programu Microsoft Graph z aplikacji konsoli przy użyciu tożsamości aplikacji](./quickstart-v2-netcore-daemon.md).
- Dokumentacja referencyjna dla:
  - Tworzenie wystąpienia [confidentialclientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Wywołanie [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Inne przykłady/samouczki:
  - [Microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) jest wyposażony w prostą aplikację konsoli demona .NET Core, która wyświetla użytkownikom dzierżawy kwerendy microsoft Graph.

    ![Przykładowa topologia aplikacji demona](media/scenario-daemon-app/daemon-app-sample.svg)

    Ten sam przykład ilustruje również odmianę z certyfikatami:

    ![Przykładowa topologia aplikacji demonów - certyfikaty](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) zawiera ASP.NET aplikację internetową MVC, która synchronizuje dane z programu Microsoft Graph przy użyciu tożsamości aplikacji, a nie w imieniu użytkownika. W tym przykładzie przedstawiono również proces zgody administratora.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Wypróbuj szybki start [Uzyskaj token i zadzwoń do interfejsu API programu Microsoft Graph z aplikacji konsoli Python przy użyciu tożsamości aplikacji](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

Msal Java jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [msal java dev samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
