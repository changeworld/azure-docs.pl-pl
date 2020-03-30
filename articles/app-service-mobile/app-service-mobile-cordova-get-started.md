---
title: Tworzenie aplikacji Cordova
description: Postępuj zgodnie z tym samouczkiem, aby rozpocząć korzystanie z zapleczy usług mobilnych Azure na potrzeby opracowywania aplikacji dla oprogramowania Apache Cordova.
keywords: cordova,javascript,mobile,client
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 99fb4a4b07ecbd4a85abbc62ec52a0f5960654c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461576"
---
# <a name="create-an-apache-cordova-app"></a>Tworzenie aplikacji oprogramowania Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Omówienie
W tym samouczku przedstawiono sposób dodawania usługi zaplecza opartej na chmurze do aplikacji mobilnej oprogramowania Apache Cordova przy użyciu zaplecza aplikacji mobilnej Azure.  Utworzysz nowe zaplecze aplikacji mobilnej oraz prostą aplikację oprogramowania Apache Cordova typu *Lista czynności do wykonania*, która przechowuje dane aplikacji na platformie Azure.

Wykonanie kroków tego samouczka jest wymagane w przypadku wszystkich innych samouczków z zakresu oprogramowania Apache Cordova dotyczących używania funkcji Mobile Apps w usłudze Azure App Service.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Komputer z programem [Visual Studio Community 2017] lub nowszym.
* [Visual Studio Tools for Apache Cordova].
* [Aktywne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

Można również pominąć program Visual Studio i używać bezpośrednio wiersza polecenia programu Apache Cordova.  Korzystanie z wiersza polecenia jest przydatne w przypadku wykonywania kroków samouczka na komputerze Mac.  Kompilowanie aplikacji klienckich oprogramowania Apache Cordova za pomocą wiersza polecenia nie zostało opisane w tym samouczku.

## <a name="create-an-azure-mobile-app-backend"></a>Tworzenie zaplecza aplikacji mobilnej Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Tworzenie połączenia z bazą danych i konfigurowanie projektu klienta i serwera
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Pobieranie i uruchamianie aplikacji oprogramowania Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Narzędzia programu Visual Studio dla Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
