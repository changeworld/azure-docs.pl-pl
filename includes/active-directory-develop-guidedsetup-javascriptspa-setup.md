---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 5ce0f18c1ec7a0fcb6465ab20e774976552687f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133686"
---
## <a name="set-up-your-web-server-or-project"></a>Konfigurowanie serwera sieci web lub projektu

> Chcesz zamiast tego Pobierz ten przykładowy projekt? Wykonaj jedną z następujących czynności:
> 
> - Aby uruchomić projekt za pomocą lokalnego serwera internetowego, takich jak Node.js, [pobierania plików projektu](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - (Opcjonalnie) Aby uruchomić projekt na serwerze usług IIS [pobrać projekt programu Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> A następnie skonfigurować przykładowego kodu, zanim ją wykonać, przejdź do [kroku konfiguracji](#register-your-application).

## <a name="prerequisites"></a>Wymagania wstępne

* Aby uruchomić ten samouczek, konieczne lokalnego serwera internetowego, takich jak [Node.js](https://nodejs.org/en/download/), [platformy .NET Core](https://www.microsoft.com/net/core), lub usług IIS Express integracji z usługą [programu Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Jeśli używasz środowiska Node.js, aby uruchomić projekt, zainstaluj zintegrowanym środowisku programistycznym (IDE), takie jak [programu Visual Studio Code](https://code.visualstudio.com/download), aby edytować pliki projektu.

* Instrukcje w tym przewodniku są oparte na technologii Node.js i programu Visual Studio 2017, ale można użyć dowolnego środowiska programowania lub serwer sieci web.

## <a name="create-your-project"></a>Utwórz projekt

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Opcja 1: Node.js lub inne serwery sieci web
> Upewnij się, że zainstalowano [Node.js](https://nodejs.org/en/download/), a następnie wykonaj następujące czynności:
> - Utwórz folder do hostowania aplikacji.
>
> ### <a name="option-2-visual-studio"></a>Opcja 2: Visual Studio
> Jeśli używasz programu Visual Studio i tworzenia nowego projektu, wykonaj następujące czynności:
> 1. W programie Visual Studio wybierz pozycje **Plik** > **Nowy** > **Projekt**.
> 1. W pozycji **Visual C#\Internet** wybierz opcję **Aplikacja internetowa ASP.NET (.NET Framework)** .
> 1. Wprowadź nazwę aplikacji, a następnie wybierz **OK**.
> 1. W obszarze **Nowa aplikacja internetowa ASP.NET**, wybierz opcję **pusty**.

## <a name="create-the-spa-ui"></a>Tworzenie interfejsu użytkownika SPA
1. Tworzenie *index.html* swoje SPA JavaScript w pliku. Jeśli używasz programu Visual Studio wybierz projekt (folder główny projektu), kliknij prawym przyciskiem myszy i wybierz **Dodaj** > **nowy element** > **strony HTML**, i Nadaj plikowi nazwę *index.html*.

1. W *index.html* plików, Dodaj następujący kod:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Wersja MSAL.js w poprzednim skrypcie można zastąpić najnowszą wersję oficjalną w obszarze [zwalnia MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).
