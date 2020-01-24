---
title: Aplikacja przenoszenia demona wywołująca interfejsy API sieci Web do produkcji — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak przenieść aplikację demona, która wywołuje interfejsy API sieci Web w środowisku produkcyjnym
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
ms.openlocfilehash: 990273e84bfceb9f4a19eae8bf5890e8303a5857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702270"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — Przenieś do środowiska produkcyjnego

Teraz, gdy wiesz już, jak uzyskać i używać tokenu dla wywołania Service to Service, Dowiedz się, jak przenieść aplikację do środowiska produkcyjnego.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Wdrożenie — sprawa aplikacji demona wielodostępna

Jeśli jesteś niezależnym dostawcą oprogramowania i utworzysz aplikację demona, która może być uruchamiana w kilku dzierżawcach, należy upewnić się, że administratorzy dzierżawy:

- Inicjuje obsługę nazwy głównej usługi dla aplikacji
- Przyznaje zgodę na aplikację

Należy wyjaśnić swoim klientom sposób wykonywania tych operacji. Aby uzyskać więcej informacji, zobacz [żądanie zgody dla całej dzierżawy](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Następne kroki

Oto kilka linków, aby dowiedzieć się więcej:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- Jeśli jeszcze tego nie zrobiono, wypróbuj Szybki Start [token i Wywołaj interfejs API Microsoft Graph z aplikacji konsolowej przy użyciu tożsamości aplikacji](./quickstart-v2-netcore-daemon.md).
- Dokumentacja referencyjna dla:
  - Tworzenie wystąpienia [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Wywoływanie [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Inne przykłady/samouczki:
  - [program Microsoft-Identity-platform-Console DAEMON](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) oferuje prostą aplikację konsolową demona programu .NET Core, która wyświetla użytkowników dzierżawcy z przeszukiwaniem Microsoft Graph.

    ![topology](media/scenario-daemon-app/daemon-app-sample.svg)

    Ten sam przykład ilustruje również zmiany z certyfikatami.

    ![topology](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-platform-ASPNET-webapp-demos](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) ASP.NET MVC Web Application, która synchronizuje dane z Microsoft Graph przy użyciu tożsamości aplikacji zamiast w imieniu użytkownika. Przykład ilustruje również proces wyrażania zgody administratora.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

Wypróbuj Szybki Start [token i Wywołaj interfejs API Microsoft Graph z aplikacji konsolowej języka Python przy użyciu tożsamości aplikacji](./quickstart-v2-python-daemon.md).

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [MSAL Java dev Samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
