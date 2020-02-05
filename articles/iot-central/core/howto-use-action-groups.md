---
title: Uruchamianie wielu akcji z poziomu reguły IoT Central platformy Azure | Microsoft Docs
description: Uruchom wiele akcji z jednej reguły IoT Central i Utwórz grupy akcji wielokrotnego użytku, które można uruchamiać z wielu reguł.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 1992b8925d5d9ba59c36452187f5b6eb510e72dc
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990814"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Grupuj wiele akcji do uruchomienia z jednej lub kilku reguł

*Ten artykuł dotyczy konstruktorów i administratorów.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

W usłudze Azure IoT Central tworzysz reguły uruchamiania akcji po spełnieniu warunku. Reguły są oparte na danych telemetrycznych lub zdarzeniach urządzeń. Na przykład można powiadomić operatora, gdy temperatura w urządzeniu przekroczy wartość progową. W tym artykule opisano, jak używać *grup akcji* [Azure monitor](../../azure-monitor/overview.md) do dołączania wielu akcji do reguły IoT Central. Grupę akcji można dołączyć do wielu reguł. [Grupa akcji](../../azure-monitor/platform/action-groups.md) to zbiór preferencji powiadomień definiowanych przez właściciela subskrypcji platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Aplikacja utworzona przy użyciu standardowego planu cenowego
- Konto platformy Azure i subskrypcja do tworzenia grup akcji Azure Monitor i zarządzania nimi

## <a name="create-action-groups"></a>Tworzenie grup akcji

Można [tworzyć grupy akcji i zarządzać nimi w Azure Portal](../../azure-monitor/platform/action-groups.md) lub z [szablonem Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Grupa akcji może:

- Wysyłaj powiadomienia, takie jak wiadomość e-mail, wiadomość SMS lub nawiązać połączenie głosowe.
- Uruchom akcję, taką jak wywołanie elementu webhook.

Poniższy zrzut ekranu przedstawia grupę akcji, która wysyła wiadomości e-mail i powiadomienia SMS oraz wywołuje element webhook:

![Grupa akcji](media/howto-use-action-groups/actiongroup.png)

Aby użyć grupy akcji w regule IoT Central, Grupa akcji musi znajdować się w tej samej subskrypcji platformy Azure co aplikacja IoT Central.

## <a name="use-an-action-group"></a>Korzystanie z grupy akcji

Aby użyć grupy akcji w aplikacji IoT Central, należy najpierw utworzyć telemetrię lub regułę zdarzenia. Po dodaniu akcji do reguły wybierz pozycję **Azure monitor grupy akcji**:

![Wybieranie akcji](media/howto-use-action-groups/chooseaction.png)

Wybierz grupę akcji z subskrypcji platformy Azure:

![Wybierz grupę akcji](media/howto-use-action-groups/chooseactiongroup.png)

Wybierz pozycję **Zapisz**. Grupa akcji zostanie teraz wyświetlona na liście akcji do uruchomienia, gdy reguła zostanie wyzwolona:

![Zapisano grupę akcji](media/howto-use-action-groups/savedactiongroup.png)

Poniższa tabela zawiera podsumowanie informacji wysyłanych do obsługiwanych typów akcji:

| Typ akcji | Format danych wyjściowych |
| ----------- | -------------- |
| Adres e-mail       | Szablon wiadomości e-mail w warstwie Standardowa IoT Central |
| SMS         | Alert IoT Central platformy Azure: $ {applicationName}-"$ {RuleName}" wyzwolony dla "$ {DeviceName}" w $ {triggerDate} $ {triggerTime} |
| Głos       | Alert dotyczący platformy Azure I. O. T Central: reguła "$ {RuleName}" została wyzwolona na urządzeniu "$ {DeviceName}" pod adresem $ {triggerDate} $ {triggerTime} w aplikacji $ {applicationName} |
| Webhook     | {"schemaId": "AzureIoTCentralRuleWebhook", "Data": {[regularny ładunek elementu webhook](#payload)}} |

Następujący tekst to przykładowy komunikat SMS z grupy akcji:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a>Poniższy kod JSON pokazuje przykład ładunku akcji elementu webhook:

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

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak używać grup akcji z regułami, sugerowanym następnym krokiem jest zapoznanie się z tematem [Zarządzanie urządzeniami](howto-manage-devices.md).
