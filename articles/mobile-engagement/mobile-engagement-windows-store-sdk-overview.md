---
title: Usługi Azure Mobile Engagement Windows Universal SDK Integration | Dokumentacja firmy Microsoft
description: Integracja uniwersalnego systemu Windows dla zestawu SDK dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 9ded187d-5c07-4377-a41c-ce205dd38b50
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: 8c513d5b05afaee4c49d0d2e96a0dcda78761421
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Integracja uniwersalnego zestawu SDK systemu Windows dla usługi Azure Mobile Engagement
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Ten dokument zawiera opis wszystkich integracji i konfiguracji dostępne opcje zestawem Azure Mobile Engagement Windows Universal SDK.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem tego samouczka, należy najpierw wykonać naszych [samouczek 15 minut](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Funkcje zaawansowane
### <a name="reporting-features"></a>Funkcje raportowania
Można dodać następujące funkcje:

1. [Zaawansowane opcje raportowania](mobile-engagement-windows-store-advanced-reporting.md)
2. [Zaawansowane opcje konfiguracji](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Powiadomienia
[Integrowanie Reach (powiadomienia) w aplikacji uniwersalnych systemu Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Tag plan implementacji:
[Jak używać zaawansowane usługi Mobile Engagement znakowanie interfejsu API w aplikacji uniwersalnych systemu Windows](mobile-engagement-windows-store-use-engagement-api.md)

## <a name="release-notes"></a>Informacje o wersji
### <a name="341-11032016"></a>3.4.1 (11/03/2016)

* Ulepszenia.

W przypadku wcześniejszych wersji, zobacz [ukończyć informacje o wersji](mobile-engagement-windows-store-release-notes.md)

## <a name="upgrade-procedures"></a>Procedury uaktualniania
Jeśli już jest zintegrowany starszą wersję programu zaangażowania do aplikacji, należy wziąć pod uwagę następujące kwestie, podczas uaktualniania zestawu SDK.

Jeśli pominięte kilka wersji zestawu SDK może być konieczne procedurami. Zobacz pełną [procedur uaktualniania](mobile-engagement-windows-store-upgrade-procedure.md). Na przykład w przypadku migrowania z 0.10.1 do 0.11.0, należy najpierw wykonać procedurę "od 0.9.0 do 0.10.1" następnie procedury "od 0.10.1 do 0.11.0".

### <a name="from-330-to-340"></a>Z 3.3.0 do 3.4.0
#### <a name="test-logs"></a>Dzienniki testów
Dzienniki konsoli utworzonego przez zestaw SDK można teraz włączone/wyłączone/odfiltrowane. Aby dostosować, zaktualizuj właściwość `EngagementAgent.Instance.TestLogEnabled` do jednej z tych wartości, które są dostępne z `EngagementTestLogLevel` wyliczenia, na przykład:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### <a name="resources"></a>Zasoby
Udoskonalono nakładki Reach. Jest ona częścią zasoby pakietu NuGet zestawu SDK.

Podczas uaktualniania do nowej wersji zestawu SDK, można wybrać, czy chcesz zachować istniejące pliki z folderu nakładki zasobów lub nie:

* Poprzednie nakładki działa automatycznie, czy jest integrowany z `WebView` elementy ręcznie, następnie można zdecydujesz się zachować Twojej zamykanie plików, to nadal będzie działać.
* Aby zaktualizować nowe nakładce, Zastąp cały `overlay` folder z Twoich zasobów nową z pakietu SDK (aplikacji platformy uniwersalnej systemu Windows: po uaktualnieniu, możesz pobrać ze strony % USERPROFILE % nowy folder nakładki\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Przy użyciu nowego nakładki zastępuje wszelkie dostosowania z poprzedniej wersji.
> 
> 

### <a name="upgrade-from-older-versions"></a>Uaktualnij ze starszych wersji
Zobacz [procedur uaktualniania](mobile-engagement-windows-store-upgrade-procedure.md)

