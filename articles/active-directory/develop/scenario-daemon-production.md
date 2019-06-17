---
title: Wywołanie aplikacji demona internetowych interfejsów API (przenoszenie do środowiska produkcyjnego) — Platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację demona wywołuje interfejsy API (przenoszenie do środowiska produkcyjnego) sieci web
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 627dab0cb23800664c5fb5b3df9c61f5071d4b87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545406"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Demon aplikacji, która wywołuje sieci web interfejsy API — przenoszenie do środowiska produkcyjnego

Teraz gdy wiesz, jak pobrać i używać tokenu dla wywołania do usługi, Dowiedz się, jak przenieść aplikację do środowiska produkcyjnego.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Wdrażanie — przypadek aplikacje demona z wieloma dzierżawami

Jeśli jesteś niezależnym dostawcą oprogramowania i tworzenie aplikacji demona, który można uruchomić w kilka dzierżaw, musisz upewnić się, że Administratorzy dzierżawy:

- Aprowizuje nazwy głównej usługi dla aplikacji
- Przyznaje zgoda na aplikację

Konieczne będzie opisują klientom sposób wykonywania tych operacji. Aby uzyskać więcej informacji, zobacz [żądanie zgody na całej dzierżawie](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Kolejne kroki

Oto kilka linków, aby dowiedzieć się więcej:

### <a name="net"></a>.NET

- Jeśli jeszcze tego nie zrobiono, spróbuj tego przewodnika Szybki Start [uzyskania tokenu i wywołania interfejsu API Microsoft Graph z poziomu aplikacji konsolowej przy użyciu tożsamości aplikacji](./quickstart-v2-netcore-daemon.md).
- Dokumentacja referencyjna:
  - Utworzenie wystąpienia [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Wywoływanie [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Inne przykłady/samouczki:
  - [Microsoft identity platformy konsoli demonów](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) funkcji prostą aplikację do konsoli demona platformy .NET Core, która wyświetla użytkowników dzierżawy, wykonywania zapytań programu Microsoft Graph.

    ![topology](media/scenario-daemon-app/daemon-app-sample.svg)

    Tej samej próbki ilustruje odmiany za pomocą certyfikatów.

    ![topology](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-platform-ASPNET-webapp-Daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) funkcji aplikację sieci web platformy ASP.NET MVC, która synchronizuje dane z programu Microsoft Graph przy użyciu tożsamości aplikacji zamiast w imieniu użytkownika. Przykład ilustruje także procesu zgody administratora.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

### <a name="python"></a>Python

Python Biblioteka MSAL jest obecnie dostępna w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [przykładem w repozytorium poświadczeń klienta MSAL Python](https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/dev/sample/client_credentials_sample.py).

### <a name="java"></a>Java

Python Biblioteka MSAL jest obecnie dostępna w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [MSAL Java w repozytorium przykładów](https://github.com/AzureAD/azure-activedirectory-library-for-java/tree/dev/src/samples).