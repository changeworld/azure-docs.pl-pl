---
title: Zaawansowana konfiguracja naboru uniwersalnych aplikacji systemu Windows SDK
description: Zaawansowane opcje konfiguracji dla usługi Azure Mobile Engagement z uniwersalnych aplikacji systemu Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6d85dd5d-ac07-43ba-bbe4-e91c3a17690b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: c0f666a0dd3441dfe3de0e82953b78c139091672
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Zaawansowana konfiguracja naboru uniwersalnych aplikacji systemu Windows SDK
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!div class="op_single_selector"]
> * [Platforma uniwersalna systemu Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
> 
> 

W tej procedurze opisano sposób konfigurowania różnych opcji konfiguracji dla usługi Azure Mobile Engagement aplikacji systemu Android.

## <a name="prerequisites"></a>Wymagania wstępne
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana
### <a name="disable-automatic-crash-reporting"></a>Wyłącz automatyczne zgłaszanie awarii
Możesz wyłączyć automatyczne awarii, funkcję zaangażowania raportowania. Następnie gdy wystąpi nieobsługiwany wyjątek, Engagement nie działa.

> [!WARNING]
> Jeśli wyłączyć tę funkcję, a następnie w przypadku wystąpienia awarii nieobsługiwany w aplikacji zaangażowania nie wysyła awarii **i** nie zamknięcia sesji i zadania.
> 
> 

Aby wyłączyć automatyczne zgłaszanie awarii, Dostosuj konfigurację, w zależności od sposobu należy zadeklarować jako:

#### <a name="from-engagementconfigurationxml-file"></a>Z `EngagementConfiguration.xml` pliku
Ustaw awarii raport `false` między `<reportCrash>` i `</reportCrash>` tagów.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Z `EngagementConfiguration` obiektu w czasie wykonywania
Ustaw awarii raportu przy użyciu obiektu EngagementConfiguration wartość false.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Wyłącz raportowanie w czasie rzeczywistym
Domyślnie raporty usługi Engagement rejestruje się w czasie rzeczywistym. Jeśli aplikacja zgłasza często dzienniki, lepiej jest buforu dzienniki i raportuj je w całości na podstawie czasu regularne. Jest on nazywany "Tryb szybki".

Aby to zrobić, należy wywołać metodę:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Argument jest wartością w **milisekund**. Zawsze, gdy chcesz ponownie uaktywnić rejestrowania w czasie rzeczywistym, wywołaj metodę bez parametrów, lub wartość 0.

Tryb serii nieco zwiększa czas pracy baterii, ale ma wpływ na monitorowanie usługi Engagement: wszystkie czas trwania sesji i zadań są zaokrąglane do serii wartość progową (w związku z tym sesji i zadania krótsze niż próg serii może nie być widoczna). Firma Microsoft zaleca używanie próg w serii się niż 30000 (30s). Zapisane dzienniki są ograniczone do 300 elementów. W przypadku wysyłania jest za długa, może spowodować utratę niektórych dzienników.

> [!WARNING]
> Próg serii nie można skonfigurować do okresu mniej niż 1 sekunda. Jeśli tak zrobisz, zestawu SDK zawiera śledzenia z powodu błędu i automatycznie przywraca wartość domyślna to zero sekund. Spowoduje to zainicjowanie zestaw SDK, aby zgłosić dzienniki w czasie rzeczywistym.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
