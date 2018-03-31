---
title: Azure Mobile Engagement Web SDK procedur uaktualniania | Dokumentacja firmy Microsoft
description: Najnowsze aktualizacje i procedury dotyczące zestawu SDK sieci Web dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a20529b4-ec8d-4503-8ae9-09b5f0846d5b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: 42f78b5e6bfa028f3a798f6cb6c0dee3f17b3ebe
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Azure procedur uaktualniania zestaw SDK usługi Mobile Engagement w sieci Web
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Jeśli zintegrowano już program wcześniejszej wersji zestawu SDK usługi Azure Mobile Engagement sieci Web do aplikacji sieci web, należy wziąć pod uwagę następujące kwestie, po uaktualnieniu zestawu SDK.

Wiele wersji zestaw SDK usługi Mobile Engagement w sieci Web została pominięta, może być konieczne podczas procesu uaktualniania należy wykonać kilka procedury. Na przykład w przypadku migrowania z 1.4.0 do 1.6.0 najpierw wykonać procedury uaktualniania z 1.4.0 do 1.5.0. Następnie wykonaj procedury uaktualniania z 1.5.0 do 1.6.0.

Niezależnie od wersji uaktualnienia, Zamień jakakolwiek wcześniejsza wersja pliku azure-engagement.js najnowszej wersji pliku.

## <a name="upgrade-from-121-to-200"></a>Uaktualnienie z wersji 1.2.1 do 2.0.0
W tej sekcji opisano sposób migracji integracji zestaw SDK usługi Mobile Engagement w sieci Web w usłudze Capptain oferowanych przez Capptain SAS, aplikację usługi Azure Mobile Engagement. W przypadku migracji z wcześniejszej wersji, zajrzyj do witryny sieci Web Capptain najpierw migracji do wersji 1.2.1, a następnie zastosuj poniższe procedury.

Ta wersja zestaw SDK usługi Mobile Engagement w sieci Web nie obsługuje Samsung inteligentne TV, Opera TV, webOS lub funkcji Reach.

> [!IMPORTANT]
> Capptain i usługi Azure Mobile Engagement nie są tę samą usługę. Poniższa procedura zawiera wyróżnione tylko sposób migracji aplikacji klienckiej. Migrowanie Mobile Engagement Web SDK w aplikacji nie będą migrowane dane z serwera Capptain na serwerze usługi Mobile Engagement.
> 
> 

### <a name="javascript-files"></a>Pliki JavaScript
Zastąp plik capptain-sdk.js plików azure-engagement.js, a następnie odpowiednio zaktualizować importów Twojego skryptu.

### <a name="remove-capptain-reach"></a>Usuń Capptain Reach
Ta wersja zestaw SDK usługi Mobile Engagement w sieci Web nie obsługuje funkcji Reach. Jeśli Capptain Reach zintegrowana aplikacja, należy go usunąć.

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

### <a name="remove-deprecated-apis"></a>Usuń przestarzałe interfejsy API
Niektóre funkcje interfejsu API z Capptain są używane w sieci Web zestaw SDK usługi Mobile Engagement.

Usuń wszystkie wywołania API: `agent.connect`, `agent.disconnect`, `agent.pause`, i `agent.sendMessageToDevice`.

Usuń wszystkie wystąpienia elementu następujące wywołania zwrotne w konfiguracji Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, i `onPushMessageReceived`.

### <a name="configuration"></a>Konfigurowanie
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

### <a name="javascript-apis"></a>Interfejsy API języka JavaScript
Obiekt globalny JavaScript `window.capptain` została zmieniona `window.azureEngagement` , ale można użyć `window.engagement` alias dla interfejsu API. Nie można użyć aliasu, aby zdefiniować konfigurację zestawu SDK.

Na przykład `capptain.deviceId` staje się `engagement.deviceId`, `capptain.agent.startActivity` staje się `engagement.agent.startActivity`i tak dalej.

