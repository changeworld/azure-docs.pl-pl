---
title: Tworzenie aplikacji systemu Android przy użyciu funkcji Azure App Service Mobile Apps | Microsoft Docs
description: Postępuj zgodnie z tym samouczkiem, aby rozpocząć korzystanie z zapleczy usług mobilnych Azure na potrzeby opracowywania aplikacji dla systemu Android.
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: emalani
ms.openlocfilehash: 0f79d2e7112847dbc5553e60f12bc872a5c0b5d5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027641"
---
# <a name="create-an-android-app"></a>Tworzenie aplikacji dla systemu Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi centralne do tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług **kompilowania**, **testowania** i **dystrybucji** , aby skonfigurować ciągłą integrację i potok dostarczania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie swojej aplikacji przy użyciu usług **analizy** i **diagnostyki** oraz angażować się z użytkownikami za pomocą usługi **wypychania** . Deweloperzy mogą również korzystać z **uwierzytelniania** w celu uwierzytelniania użytkowników i usługi **danych** w celu utrwalania i synchronizowania danych aplikacji w chmurze.
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze App Center [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd
W tym samouczku przedstawiono sposób dodawania usługi zaplecza opartej na chmurze do aplikacji mobilnej systemu Android przy użyciu zaplecza aplikacji mobilnej Azure.  Będziesz tworzyć nowe zaplecze aplikacji mobilnej oraz prostą aplikację systemu Android typu *Lista czynności do wykonania*, która przechowuje dane aplikacji na platformie Azure.

Wykonanie kroków tego samouczka jest wymagane w przypadku wszystkich innych samouczków z zakresu systemu Android dotyczących używania funkcji Mobile Apps w usłudze Azure App Service.

## <a name="prerequisites"></a>Wymagania wstępne
Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Narzędzia dla deweloperów systemu Android](https://developer.android.com/sdk/index.html), w tym zintegrowane środowisko projektowe Android Studio i najnowsza platforma systemu Android.
* Android SDK Azure Mobile.
* [Aktywne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Tworzenie zaplecza nowej aplikacji mobilnej Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Tworzenie połączenia z bazą danych i Konfigurowanie projektu klienta i serwera
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>Uruchamianie aplikacji systemu Android
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
