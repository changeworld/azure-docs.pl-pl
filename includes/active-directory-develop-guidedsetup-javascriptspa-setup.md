---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 794fd51c38f66b24193c7da7a145d58f7a225b30
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32202540"
---
## <a name="setting-up-your-web-server-or-project"></a>Konfigurowanie serwera sieci web lub projektu

> Preferowane jest zamiast tego Pobierz ten przykładowy projekt? 
> - [Pobieranie projektu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> lub
> - [Pobierz pliki projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip) serwera sieci web z lokalnych, takich jak Python
>
> A następnie przejdź do [kroku konfiguracji](#register-your-application) skonfigurowaniu przykładowy kod przed jej wykonanie.

## <a name="prerequisites"></a>Wymagania wstępne
Serwer sieci web lokalnych, takich jak [Python http.server](https://www.python.org/downloads/), [serwera http](https://www.npmjs.com/package/http-server/), [.NET Core](https://www.microsoft.com/net/core), lub integracji usług IIS Express z [programu Visual Studio 2017](https://www.visualstudio.com/downloads/) jest wymagana do uruchamiania Instalatora z przewodnikiem. 

Instrukcje w tym przewodniku są oparte na Visual Studio 2017 r i Python, ale możesz użyć dowolnego środowiska programowania lub serwer sieci Web.

## <a name="create-your-project"></a>Tworzenie projektu 

> ### <a name="option-1-visual-studio"></a>Opcja 1: Visual Studio 
> Jeśli używasz programu Visual Studio i tworzenia nowego projektu, wykonaj poniższe kroki, aby utworzyć nowe rozwiązanie Visual Studio:
> 1.    W programie Visual Studio:  `File` > `New` > `Project`
> 2.    W obszarze `Visual C#\Web`, wybierz pozycję `ASP.NET Web Application (.NET Framework)`
> 3.    Nazwa aplikacji, a następnie kliknij przycisk *OK*
> 4.    W obszarze `New ASP.NET Web Application`, wybierz pozycję `Empty`

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>Opcja 2: Python / inne serwery w sieci web
> Upewnij się, że zainstalowano [Python](https://www.python.org/downloads/), wykonaj kroki opisane poniżej:
> - Utwórz folder na potrzeby hostowania aplikacji.


## <a name="create-your-single-page-applications-ui"></a>Tworzenie aplikacji jednej strony interfejsu użytkownika
1.  Utwórz *index.html* Twojego SPA JavaScript w pliku. Jeśli używasz programu Visual Studio, wybierz projekt (folder główny projekt), kliknij prawym przyciskiem myszy i wybierz: `Add`  >  `New Item`  >  `HTML page` i nadaj mu nazwę index.html
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
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.3/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
