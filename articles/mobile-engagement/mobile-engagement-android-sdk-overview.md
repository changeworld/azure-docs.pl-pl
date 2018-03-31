---
title: Integracja zestawu SDK systemu android dla usługi Azure Mobile Engagement
description: Opisuje sposób integrowania zestawu SDK usługi Azure Mobile Engagement w aplikacji systemu Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo;ricksal
ms.openlocfilehash: d02bcfc7521df9c18edc534e679e597ad2328e6f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Integracja zestawu SDK systemu android dla usługi Azure Mobile Engagement
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Platforma uniwersalna systemu Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

Ten dokument zawiera opis wszystkich integracji i konfiguracji dostępne opcje zestawem Azure Mobile Engagement Android SDK.

## <a name="prerequisites"></a>Wymagania wstępne
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>Funkcje zaawansowane
### <a name="reporting-features"></a>Funkcje raportowania
Można dodać następujące funkcje:

1. [Zaawansowane opcje raportowania](mobile-engagement-android-advanced-reporting.md)
2. [Opcje raportowania lokalizacji](mobile-engagement-android-location-reporting.md)
3. [Zaawansowane opcje konfiguracji](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>Powiadomienia:
[Integrowanie Reach (powiadomienia) w aplikacji systemu Android](mobile-engagement-android-integrate-engagement-reach.md)

1. Usługa Google Cloud Messaging (GCM): [Integrowanie usługi GCM z usługi Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
2. Usługi Amazon Device Messaging (ADM): [Integrowanie usługi ADM z usługi Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Tag plan implementacji:
[Jak używać zaawansowane usługi Mobile Engagement znakowanie interfejsu API w aplikacji systemu Android](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Informacje o wersji

### <a name="431-07172017"></a>4.3.1 (07/17/2017)
* Usuń awarii rzadko może się zdarzyć podczas wywoływania metody `EngagementAgentUtils.isInDedicatedEngagementProcess`, która jest już używana przez `EngagementApplication` klasy.

### <a name="430-06272017"></a>4.3.0 (06/27/2017)
* 8 Obsługa systemu android (poprzednie wersje zestawu SDK nie będą działać na 8 dla systemu Android).
* Nie więcej zależności biblioteki pomocy technicznej.
* Usuń `EngagementFragmentActivity` klasy.
* Ze względu na [limity wykonywania tła](https://developer.android.com/preview/features/background.html) na 8 dla systemu Android, może być opóźnione dzienniki w tle, dopóki użytkownik wchodzi w interakcję z urządzeniem, będzie to mieć wpływ na Push kampanii **dostarczone** i **powiadomienie systemowe wyświetlane** statystyki opóźnieniu, jeśli urządzenie zostało uśpiony (powiadomienia będą nadal wyświetlane, będzie pierścienia i Włącz wibrację w czasie rzeczywistym bez problemów).
* Ze względu na [ograniczenia lokalizacji tła](https://developer.android.com/preview/features/background-location-limits.html), w czasie rzeczywistym lokalizacji w tle nie zostanie zaktualizowany często na 8 dla systemu Android.

Wszystkie wersje, zobacz [ukończyć wersji](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Procedury uaktualniania
Jeśli już jest zintegrowany starszej wersji naszego zestawu SDK do aplikacji, należy skontaktować się [procedur uaktualniania](mobile-engagement-android-upgrade-procedure.md).

