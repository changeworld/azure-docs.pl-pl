---
title: Tworzenie elementów webhook w regułach w usłudze Azure IoT Central | Dokumentacja firmy Microsoft
description: Tworzenie elementów webhook w usłudze Azure IoT Central automatycznie powiadamiać innych aplikacji, gdy wyzwolenie reguły.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 291307fe8d8911a969e11a8d0e9d5b9078132eda
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213793"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Utwórz akcje elementu webhook na reguły w usłudze Azure IoT Central

*Ten temat dotyczy konstruktorów i administratorów.*

Elementy Webhook pozwalają połączyć aplikację IoT Central do innych aplikacji i usług, zdalne monitorowanie i powiadomienia. Elementy Webhook powiadamiają automatycznie, inne aplikacje i usługi, z którymi się łączysz zawsze wtedy, gdy reguła jest wyzwalana w swojej aplikacji IoT Central. Aplikację IoT Central wysyła żądanie POST do punktu końcowego HTTP innej aplikacji, przy każdym wyzwoleniu reguły. Ładunek zawiera szczegóły dotyczące urządzenia i wyzwalacza reguły.

## <a name="set-up-the-webhook"></a>Konfigurowanie elementu webhook

W tym przykładzie połączenie z narzędzia RequestBin powiadomieniom reguły wyzwalać przy użyciu elementów webhook.

1. Otwórz [RequestBin](http://requestbin.net/).

1. Utwórz nowe narzędzia RequestBin i kopiowania **URL pojemnika**.

1. Tworzenie [reguły telemetrii](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) lub [reguły zdarzenia](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Zapisać regułę, a następnie dodaj nową akcję.

    ![Ekran tworzenia elementu Webhook](media/howto-create-webhooks-experimental/webhookcreate.png)

1. Wybierz akcję elementu webhook i podać nazwę wyświetlaną i wklej adres URL pojemnika, jako adres URL wywołania zwrotnego.

1. Zapisać reguły.

Po wyzwoleniu reguły, spowoduje to wyświetlenie nowego żądania są wyświetlane w RequestBin.

## <a name="payload"></a>Ładunek

Po wyzwoleniu reguły żądanie HTTP POST wykonywane, aby adres URL wywołania zwrotnego zawierającego ładunek json z pomiarów, urządzenia, reguły i szczegóły dotyczące aplikacji. Reguły telemetrii ładunek wygląda podobnie do poniższego:

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

Obecnie nie ma sposobu anulowania subskrypcji/subskrypcji z tych elementów webhook przy użyciu interfejsu API.

Jeśli masz pomysły dotyczące poprawy tę funkcję, opublikuj swoje sugestie naszych [Uservoice forum](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Kolejne kroki

Skoro już wiesz, jak konfigurowanie i używanie elementów webhook, sugerowane następnym krokiem jest Eksplorowanie [tworzenia przepływów pracy w programie Microsoft Flow](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
