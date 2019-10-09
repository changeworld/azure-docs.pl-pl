---
title: Tworzenie aplikacji systemu iOS przy użyciu funkcji Azure App Service Mobile Apps| Microsoft Docs
description: Postępuj zgodnie z tym samouczkiem, aby rozpocząć korzystanie z zapleczy usług mobilnych Azure na potrzeby opracowywania aplikacji systemu iOS w środowisku Objective-C lub Swift.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: cdfe395bca7ac73f00506da0180146310bd71d3d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027235"
---
# <a name="create-an-ios-app"></a>Tworzenie aplikacji dla systemu iOS

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center obsługuje kompleksowe i zintegrowane usługi centralne do tworzenia aplikacji mobilnych. Deweloperzy mogą używać usług **kompilowania**, **testowania** i **dystrybucji** , aby skonfigurować ciągłą integrację i potok dostarczania. Po wdrożeniu aplikacji deweloperzy mogą monitorować stan i użycie swojej aplikacji przy użyciu usług **analizy** i **diagnostyki** oraz angażować się z użytkownikami za pomocą usługi **wypychania** . Deweloperzy mogą również korzystać z **uwierzytelniania** w celu uwierzytelniania użytkowników i usługi **danych** w celu utrwalania i synchronizowania danych aplikacji w chmurze.
> Jeśli chcesz zintegrować usługi w chmurze w swojej aplikacji mobilnej, zarejestruj się w usłudze App Center [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) już dziś.

## <a name="overview"></a>Przegląd

W tym samouczku przedstawiono sposób dodawania [aplikacji mobilnych usługi Azure Mobile Apps](app-service-mobile-value-prop.md) (usługi zaplecza w chmurze) do aplikacji systemu iOS. Pierwszym krokiem jest utworzenie nowego zaplecza aplikacji mobilnej na platformie Azure. Następnie należy pobrać prostą przykładową aplikację systemu iOS *Lista zadań do wykonania*, która służy do przechowywania danych na platformie Azure.

Aby można było ukończyć ten samouczek, potrzebny jest komputer Mac i [konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>Tworzenie zaplecza nowej aplikacji mobilnej Azure

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Tworzenie połączenia z bazą danych i Konfigurowanie projektu klienta i serwera
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>Uruchamianie aplikacji systemu iOS

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
