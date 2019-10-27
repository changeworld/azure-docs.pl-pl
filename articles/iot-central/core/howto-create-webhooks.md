---
title: Tworzenie elementów webhook dla reguł na platformie Azure IoT Central | Microsoft Docs
description: Tworzenie elementów webhook w usłudze Azure IoT Central w celu automatycznego powiadamiania innych aplikacji o pożaru reguł.
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5c2bef7f3eb8d6f8d6d78755d839a33556259b65
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953670"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Tworzenie akcji elementu webhook dla reguł na platformie Azure IoT Central

*Ten temat dotyczy konstruktorów i administratorów.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Elementy webhook umożliwiają łączenie aplikacji IoT Central z innymi aplikacjami i usługami na potrzeby zdalnego monitorowania i powiadomień. Elementy webhook automatycznie powiadamiają inne aplikacje i usługi, które są połączone przy każdym wyzwoleniu reguły w aplikacji IoT Central. Aplikacja IoT Central wysyła żądanie POST do punktu końcowego HTTP innej aplikacji przy każdym wyzwoleniu reguły. Ładunek zawiera szczegóły urządzenia i szczegóły wyzwalacza reguł.

## <a name="set-up-the-webhook"></a>Konfigurowanie elementu webhook

W tym przykładzie nawiążesz połączenie z usługą RequestBin, aby otrzymywać powiadomienia, gdy reguły wyzwalają korzystanie z elementów webhook.

1. Otwórz [RequestBin](https://requestbin.net/).

1. Utwórz nowy RequestBin i skopiuj **adres URL bin**.

1. Utwórz [regułę telemetrii](howto-create-telemetry-rules.md) lub [regułę zdarzenia](howto-create-event-rules.md). Zapisz regułę i Dodaj nową akcję.

    ![Ekran tworzenia elementu webhook](media/howto-create-webhooks/webhookcreate.png)

1. Wybierz akcję elementu webhook i podaj nazwę wyświetlaną, a następnie wklej adres URL bin jako adres URL wywołania zwrotnego.

1. Zapisz regułę.

Teraz, gdy reguła zostanie wyzwolona, zobaczysz nowe żądanie w RequestBin.

## <a name="payload"></a>Ładunku

Gdy reguła jest wyzwalana, żądanie HTTP POST jest wykonywane w adresie URL wywołania zwrotnego zawierającego ładunek JSON z danymi pomiarów, urządzeń, reguł i aplikacji. Dla reguły telemetrii ładunek wygląda następująco:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Znane ograniczenia

Obecnie nie ma możliwości programistycznego subskrybowania/anulowania subskrypcji z tych elementów webhook za pośrednictwem interfejsu API.

Jeśli masz pomysły dotyczące ulepszania tej funkcji, Opublikuj swoje sugestie na naszym [forum UserVoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować elementy webhook i korzystać z nich, sugerowanym następnym krokiem jest Eksplorowanie [przepływów pracy w Microsoft Flow](howto-add-microsoft-flow.md).
