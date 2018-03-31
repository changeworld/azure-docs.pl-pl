---
title: Omówienie zestawu Silverlight SDK usługi Azure Mobile Engagement Windows Phone | Dokumentacja firmy Microsoft
description: Omówienie zestawu Windows Phone Silverlight SDK dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0e3d2420-0509-4952-8891-392e3dad9aaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: fe4df1f914e7b53f24a9855d5f96ecb193986b7f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Windows Phone Silverlight SDK — omówienie dla usługi Azure Mobile Engagement
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Zacznij tutaj uzyskać szczegółowe informacje dotyczące sposobu integracji usługi Azure Mobile Engagement w aplikacji Windows Phone Silverlight. Jeśli chcesz go wypróbować najpierw, upewnij się, należy wykonać naszych [samouczek 15 minut](mobile-engagement-windows-phone-get-started.md).

Kliknij, aby wyświetlić [zawartość zestawu SDK](mobile-engagement-windows-phone-sdk-content.md)

## <a name="integration-procedures"></a>Procedury integracji
1. Zacznij tutaj: [jak zintegrowana usługa Mobile Engagement w aplikacji Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
2. Dla powiadomień: [integrowanie Reach (powiadomienia) w aplikacji Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement-reach.md)
3. Tag plan implementacji: [sposobu korzystania z zaawansowanych Mobile Engagement znakowanie interfejsu API w aplikacji Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md)

## <a name="release-notes"></a>Informacje o wersji
###<a name="331-11032016"></a>3.3.1 (11/03/2016)
Część *MicrosoftAzure.MobileEngagement* pakietu Nuget **v3.4.1**

* Ulepszenia.

Dla starszych wersji zobacz [ukończyć informacje o wersji](mobile-engagement-windows-phone-release-notes.md)

## <a name="upgrade-procedures"></a>Procedury uaktualniania
Jeśli już jest zintegrowany starszej wersji naszego zestawu SDK do aplikacji, należy wziąć pod uwagę następujące kwestie, podczas uaktualniania zestawu SDK.

Należy wykonać kilka procedur, jeśli pominięte kilka wersji zestawu SDK. Zobacz pełną [procedur uaktualniania](mobile-engagement-windows-phone-upgrade-procedure.md). Na przykład w przypadku migrowania z 0.10.1 do 0.11.0, należy najpierw wykonać procedurę "od 0.9.0 do 0.10.1" następnie procedury "od 0.10.1 do 0.11.0".

### <a name="from-200-to-330"></a>Z 2.0.0 do 3.3.0
#### <a name="test-logs"></a>Dzienniki testów
Dzienniki konsoli utworzonego przez zestaw SDK można teraz włączone/wyłączone/odfiltrowane. Aby dostosować to, zaktualizuj właściwość `EngagementAgent.Instance.TestLogEnabled` do jednej z dostępnych wartości `EngagementTestLogLevel` wyliczenia, na przykład:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Uaktualnij ze starszych wersji
Zobacz [procedur uaktualniania](mobile-engagement-windows-phone-upgrade-procedure.md)

