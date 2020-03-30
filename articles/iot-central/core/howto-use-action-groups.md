---
title: Uruchamianie wielu akcji z reguły centrali usługi Azure IoT | Dokumenty firmy Microsoft
description: Uruchom wiele akcji z jednej reguły Centralnej IoT i utwórz grupy akcji wielokrotnego wyboru, które można uruchomić z wielu reguł.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157691"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Grupowanie wielu akcji do uruchomienia z jednej lub więcej reguł

*Ten artykuł dotyczy konstruktorów i administratorów.*

W usłudze Azure IoT Central tworzysz reguły do uruchamiania akcji po spełnieniu warunku. Reguły są oparte na danych telemetrycznych urządzenia lub zdarzeniach. Na przykład można powiadomić operatora, gdy temperatura urządzenia przekracza próg. W tym artykule opisano sposób używania *grup akcji* [usługi Azure Monitor](../../azure-monitor/overview.md) do dołączania wielu akcji do reguły centrali IoT. Grupę akcji można dołączyć do wielu reguł. [Grupa akcji](../../azure-monitor/platform/action-groups.md) to zbiór preferencji powiadomień zdefiniowanych przez właściciela subskrypcji platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacja utworzona przy użyciu standardowego planu cenowego
- Konto platformy Azure i subskrypcja w celu tworzenia grup akcji usługi Azure Monitor i zarządzania nimi

## <a name="create-action-groups"></a>Tworzenie grup akcji

Grupy akcji można [tworzyć i zarządzać nimi w witrynie Azure portal](../../azure-monitor/platform/action-groups.md) lub za pomocą [szablonu usługi Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Grupa akcji może:

- Wysyłaj powiadomienia, takie jak wiadomość e-mail, SMS lub nawiądź połączenie głosowe.
- Uruchom akcję, taką jak wywołanie elementu webhook.

Poniższy zrzut ekranu przedstawia grupę akcji, która wysyła powiadomienia e-mail i SMS i wywołuje element webhook:

![Grupa akcji](media/howto-use-action-groups/actiongroup.png)

Aby użyć grupy akcji w regule IoT Central, grupa akcji musi znajdować się w tej samej subskrypcji platformy Azure, co aplikacja IoT Central.

## <a name="use-an-action-group"></a>Używanie grupy akcji

Aby użyć grupy akcji w aplikacji IoT Central, najpierw utwórz regułę. Po dodaniu akcji do reguły wybierz grup **akcji Monitora Platformy Azure:**

![Wybieranie akcji](media/howto-use-action-groups/chooseaction.png)

Wybierz grupę akcji z subskrypcji platformy Azure:

![Wybieranie grupy akcji](media/howto-use-action-groups/chooseactiongroup.png)

Wybierz **pozycję Zapisz**. Grupa akcji pojawia się teraz na liście akcji do uruchomienia po wyzwoleniu reguły:

![Zapisana grupa akcji](media/howto-use-action-groups/savedactiongroup.png)

W poniższej tabeli podsumowano informacje wysyłane do obsługiwanych typów akcji:

| Typ akcji | Format wyjściowy |
| ----------- | -------------- |
| Adres e-mail       | Szablon wiadomości e-mail w centrum IoT |
| SMS         | Alert centrali Usługi Azure IoT: ${applicationName} — "${ruleName}" wyzwalany na "${deviceName}" z ${triggerDate} ${triggerTime} |
| Połączenia głosowe       | Alert centralna usługi Azure I.O.T: reguła "${ruleName}" wyzwalana na urządzeniu "${deviceName}" z ${triggerDate} ${triggerTime}, w aplikacji ${applicationName} |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[regularny ładunek elementu webhook](howto-create-webhooks.md#payload)}} |

Poniższy tekst jest przykładową wiadomością SMS z grupy akcji:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak korzystać z grup akcji z regułami, sugerowanym następnym krokiem jest nauczenie się [zarządzania urządzeniami.](howto-manage-devices.md)
