---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: cc5272ed133f4fa5c99eb3b3a397ac2c53b93b7c
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2018
ms.locfileid: "46473908"
---
## <a name="setting-up-your-web-server-or-project"></a>Konfigurowanie serwera sieci web lub projektu

> Chcesz zamiast tego Pobierz ten przykładowy projekt?
> - [Pobieranie projektu programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> lub
> - [Pobierz pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) dla lokalnego serwera internetowego, takie jak węzeł
>
> A następnie przejdź do [kroku konfiguracji](#register-your-application) do skonfigurowania przykładowego kodu przed jej wykonanie.

## <a name="prerequisites"></a>Wymagania wstępne
Serwer sieci web w lokalnych, takich jak [Node.js](https://nodejs.org/en/download/), [platformy .NET Core](https://www.microsoft.com/net/core), lub usług IIS Express integracji z usługą [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) jest wymagany do uruchomienia w tym samouczku.

Instrukcje w tym przewodniku są oparte na technologii Node.js i programu Visual Studio 2017, ale możesz użyć dowolnego środowiska programowania lub serwer sieci Web.

## <a name="create-your-project"></a>Utwórz projekt

> ### <a name="option-1-visual-studio"></a>Opcja 1: Visual Studio
> Jeśli używasz programu Visual Studio i tworzenia nowego projektu, wykonaj poniższe kroki, aby utworzyć nowe rozwiązanie programu Visual Studio:
> 1.    W programie Visual Studio:  `File` > `New` > `Project`
> 2.    W obszarze `Visual C#\Web`, wybierz opcję `ASP.NET Web Application (.NET Framework)`
> 3.    Nadaj nazwę aplikacji, a następnie kliknij przycisk *OK*
> 4.    W obszarze `New ASP.NET Web Application`, wybierz opcję `Empty`

<p/><!-- -->

> ### <a name="option-2-node-other-web-servers"></a>Opcja 2: Węzeł / inne serwery sieci web
> Upewnij się, że zainstalowano [Node.js](https://nodejs.org/en/download/), następnie wykonaj kroki opisane poniżej:
> - Utwórz folder do hostowania aplikacji.


## <a name="create-your-single-page-applications-ui"></a>Tworzenie interfejsu użytkownika aplikacji jednostronicowej
1.  Tworzenie *index.html* swoje SPA JavaScript w pliku. Jeśli używasz programu Visual Studio wybierz projekt (folder główny projektu), kliknij prawym przyciskiem myszy i wybierz pozycję: `Add`  >  `New Item`  >  `HTML page` i nadaj mu nazwę index.html
2.  Dodaj następujący kod do strony:
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended).
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
