---
title: Uruchom wiele akcji z reguły usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Uruchom wiele operacji na jednej regule IoT Central i Utwórz wielokrotnego użytku grupy akcji, które można uruchomić z wielu reguł.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58523010"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Wiele akcji, aby uruchamiała się z co najmniej jedną regułę grupy

*Ten artykuł dotyczy konstruktorów i administratorów.*

Usługa Azure IoT Central tworzenia reguł do uruchomienia działania, gdy warunek jest spełniony. Reguły są oparte na danych telemetrycznych z urządzenia lub zdarzeń. Możesz na przykład powiadomić operatorowi, gdy temperatura urządzenia przekroczy próg. W tym artykule opisano sposób używania [usługi Azure Monitor](../azure-monitor/overview.md) *grup akcji* można dołączyć wiele akcji do reguły usługi IoT Central. Grupy akcji można dołączyć wiele reguł. [Grupy akcji](../azure-monitor/platform/action-groups.md) to zbiór preferencje powiadamiania zdefiniowane przez właściciela subskrypcji platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacja płatność za rzeczywiste użycie
- Konto i Subskrypcja platformy do tworzenia i zarządzania usługi Azure Monitor grupy akcji

## <a name="create-action-groups"></a>Tworzenie grup akcji

Możesz [tworzenie grup i zarządzanie nimi akcji w witrynie Azure portal](../azure-monitor/platform/action-groups.md) lub [szablonu usługi Azure Resource Manager](../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Grupy akcji wykonywać następujące czynności:

- Wysyłanie powiadomień, takie jak wiadomości e-mail, wiadomość SMS lub nawiązania połączenia głosowego.
- Uruchamianie akcji takich jak wywoływanie elementu webhook.

Poniższy zrzut ekranu przedstawia grupę akcji, który wysyła wiadomości e-mail i powiadomienia SMS, a następnie wywołuje element webhook:

![Grupa akcji](media/howto-use-action-groups/actiongroup.png)

Aby użyć grupy akcji w regule IoT Central, grupy akcji musi być w tej samej subskrypcji platformy Azure jako aplikację IoT Central.

## <a name="use-an-action-group"></a>Użyj grupy akcji

Aby użyć grupy akcji w aplikacji IoT Central, należy najpierw utworzyć regułę telemetrii lub zdarzeń. Po dodaniu akcji do reguły wybierz **grup akcji usługi Azure Monitor**:

![Wybieranie akcji](media/howto-use-action-groups/chooseaction.png)

Wybierz grupę akcji z subskrypcji platformy Azure:

![Wybierz grupę akcji](media/howto-use-action-groups/chooseactiongroup.png)

Wybierz pozycję **Zapisz**. Grupa akcji pojawi się na liście Akcje do wykonania po wyzwoleniu reguły:

![Zapisane grupy akcji](media/howto-use-action-groups/savedactiongroup.png)

W poniższej tabeli przedstawiono informacje wysyłane do typów obsługiwanych akcji:

| Typ akcji | Format wyjściowy |
| ----------- | -------------- |
| Email       | Standardowy szablon wiadomości e-mail IoT Central |
| SMS         | Alert usługi Azure IoT Central: ${applicationName} — "${ruleName}" wyzwolone przez "${deviceName}" na ${triggerDate}, ${triggerTime} |
| Połączenia głosowe       | Azure alert I.O.T Central: reguła "${ruleName}" wyzwalane na urządzeniu "${deviceName}" na ${triggerDate}, ${triggerTime} w aplikacji, ${applicationName} |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "dane": {[ładunek elementu webhook regularne](#payload)}} |

Poniższy tekst to przykład wiadomość SMS z grupy akcji:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> Następujący kod JSON zawiera ładunek akcji elementu webhook przykład:

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>Kolejne kroki

Skoro już wiesz, jak korzystanie z grup akcji przy użyciu reguł, sugerowane następnym krokiem jest Dowiedz się, jak [zarz¹dzanie urz¹dzeniami](howto-manage-devices.md).
