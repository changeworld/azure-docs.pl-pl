---
title: Omówienie zestawu SDK sieci Web usługi Azure Mobile Engagement | Dokumentacja firmy Microsoft
description: Najnowsze aktualizacje i procedury dotyczące zestawu SDK sieci Web dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 5bbc2fda-0f3f-43d0-a73d-0f2c0f8dc25b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 10/18/2016
ms.author: piyushjo
ms.openlocfilehash: ad0a83e05c6a60953c2f8a0036f823e7ed497f45
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-web-sdk"></a>Azure Mobile Engagement Web SDK
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Szczegółowe informacje dotyczące sposobu integracji usługi Azure Mobile Engagement w aplikacji sieci web, zacznij tutaj. Jeśli chcesz go wypróbować przed rozpoczęciem pracy z aplikacją sieci web, zobacz nasze [samouczek 15 minut](mobile-engagement-web-app-get-started.md).

## <a name="integration-procedures"></a>Procedury integracji
1. Dowiedz się [jak zintegrowana usługa Mobile Engagement w aplikacji sieci web](mobile-engagement-web-integrate-engagement.md).
2. Tag planu wykonania, Dowiedz się [sposobu korzystania z zaawansowanych Mobile Engagement znakowanie interfejsu API w aplikacji sieci web](mobile-engagement-web-use-engagement-api.md).

## <a name="release-notes"></a>Informacje o wersji
### <a name="202-10182016"></a>2.0.2 (10/18/2016)
* Stałe awarii na InPrivate (Safari).
* Stałe awarii w przeglądarkach plików cookie jest wyłączona.

Wszystkie wersje, zobacz [ukończyć wersji](mobile-engagement-web-release-notes.md).

## <a name="upgrade-procedures"></a>Procedury uaktualniania
### <a name="upgrade-from-121-to-200"></a>Uaktualnienie z wersji 1.2.1 do 2.0.0
W poniższych sekcjach opisano sposób migracji integracji zestaw SDK usługi Mobile Engagement w sieci Web w usłudze Capptain oferowanych przez Capptain SAS, aplikację usługi Azure Mobile Engagement. W przypadku migracji z wersji starszej niż 1.2.1, zajrzyj do witryny sieci Web Capptain, aby przeprowadzić migrację do wersji 1.2.1, a następnie zastosuj poniższe procedury.

Ta wersja zestaw SDK usługi Mobile Engagement w sieci Web nie obsługuje Samsung inteligentne TV, Opera TV, webOS lub funkcji Reach.

> [!IMPORTANT]
> Capptain i usługi Azure Mobile Engagement nie są tej samej usługi, oraz poniższych procedur zaznacz tylko sposób migracji aplikacji klienckiej. Migrowanie Mobile Engagement Web SDK w aplikacji nie będą migrowane dane z serwera Capptain na serwerze usługi Mobile Engagement.
> 
> 

#### <a name="javascript-files"></a>Pliki JavaScript
Zastąp plik capptain-sdk.js plików azure-engagement.js, a następnie odpowiednio zaktualizować importów Twojego skryptu.

#### <a name="remove-capptain-reach"></a>Usuń Capptain Reach
Ta wersja zestaw SDK usługi Mobile Engagement w sieci Web nie obsługuje funkcji Reach. Jest zintegrowany Capptain Reach do aplikacji, należy go usunąć.

Usuń import osiągnąć CSS ze strony i usuń plik .css powiązane (capptain-reach.css, domyślnie).

Usuń następujące zasoby Reach: Zamknij obrazu (capptain-close.png, domyślnie) i ikonę marki (capptain powiadomienia ikona, domyślnie).

Usuń osiągnąć interfejsu użytkownika dla powiadomień w aplikacji. Domyślny układ wygląda następująco:

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Usuń osiągnąć interfejsu użytkownika dla tekstu i sieci web anonsów i sond. Domyślny układ wygląda następująco:

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Usuń `reach` obiekt z konfiguracji, jeśli istnieje. Wygląda następująco:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Usuń wszelkie inne dostosowania Reach, takich jak kategorii.

#### <a name="remove-deprecated-apis"></a>Usuń przestarzałe interfejsy API
Niektóre funkcje interfejsu API z Capptain są używane w sieci Web zestaw SDK usługi Mobile Engagement.

Usuń wszystkie wywołania API: `agent.connect`, `agent.disconnect`, `agent.pause`, i `agent.sendMessageToDevice`.

Usunąć żadnego z następujących wywołania zwrotne w konfiguracji Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, i `onPushMessageReceived`.

#### <a name="configuration"></a>Konfigurowanie
Usługa Mobile Engagement używa parametrów połączenia do konfigurowania zestawu SDK identyfikatorów, na przykład identyfikator aplikacji.

Zastąp identyfikator aplikacji parametrów połączenia. Należy pamiętać, że obiekt globalny dla konfiguracji SDK zmieni się z `capptain` do `azureEngagement`.

Przed migracją:

    window.capptain = {
      appId: ...,
      [...]
    };

Po zakończeniu migracji:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

Ciąg połączenia dla aplikacji jest wyświetlana w portalu Azure.

#### <a name="javascript-apis"></a>Interfejsy API języka JavaScript
Globalnego obiektu JavaScript `window.capptain` została zmieniona `window.azureEngagement`, ale można użyć `window.engagement` alias dla interfejsu API. Nie można użyć tego aliasu, aby zdefiniować konfigurację zestawu SDK.

Na przykład `capptain.deviceId` staje się `engagement.deviceId`, `capptain.agent.startActivity` staje się `engagement.agent.startActivity`i tak dalej.

Jeśli zintegrowano już program wcześniejszej wersji zestawu SDK usługi Azure Mobile Engagement sieci Web do aplikacji, przeczytaj informacje o [procedur uaktualniania](mobile-engagement-web-upgrade-procedure.md).

