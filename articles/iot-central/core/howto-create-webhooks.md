---
title: Tworzenie zestawów webhook na regułach w usłudze Azure IoT Central | Dokumenty firmy Microsoft
description: Tworzenie zestawów webhook w usłudze Azure IoT Central, aby automatycznie powiadamiać inne aplikacje, gdy reguły są uruchamiane.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: d97bd7a3c6de92f22a9880040f407960d5257f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158099"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Tworzenie akcji elementu webhook dla reguł w usłudze Azure IoT Central

*Ten temat dotyczy konstruktorów i administratorów.*

Elementów Webhook umożliwiają łączenie aplikacji IoT Central z innymi aplikacjami i usługami do zdalnego monitorowania i powiadomień. Elementów Webhook automatycznie powiadamia inne aplikacje i usługi, które łączysz się za każdym razem, gdy reguła jest wyzwalana w aplikacji IoT Central. Aplikacja IoT Central wysyła żądanie POST do punktu końcowego HTTP innej aplikacji za każdym razem, gdy reguła jest wyzwalana. Ładunek zawiera szczegóły urządzenia i szczegóły wyzwalacza reguły.

## <a name="set-up-the-webhook"></a>Konfigurowanie elementu webhook

W tym przykładzie można połączyć się z RequestBin, aby otrzymywać powiadomienia, gdy reguły są uruchamiane za pomocą elementów webhook.

1. Otwórz [plik RequestBin](https://requestbin.net/).

1. Utwórz nowy Plik RequestBin i skopiuj **adres URL pojemnika**.

1. Tworzenie [reguły telemetrii](tutorial-create-telemetry-rules.md). Zapisz regułę i dodaj nową akcję.

    ![Ekran tworzenia elementu Webhook](media/howto-create-webhooks/webhookcreate.png)

1. Wybierz akcję elementu webhook i podaj nazwę wyświetlaną i wklej adres URL pojemnika jako adres URL wywołania zwrotnego.

1. Zapisz regułę.

Teraz, gdy reguła jest wyzwalana, pojawi się nowe żądanie w RequestBin.

## <a name="payload"></a>Ładunku

Po wyzwoleniu reguły żądanie HTTP POST jest składane z adresu URL wywołania zwrotnego zawierającego ładunek json z danymi telemetrii, urządzenia, reguły i aplikacji. Ładowność może wyglądać następująco:

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Znane ograniczenia

Obecnie nie istnieje programowy sposób subskrybowania/anulowania subskrypcji z tych łączy webhook za pośrednictwem interfejsu API.

Jeśli masz pomysły na ulepszenie tej funkcji, opublikuj swoje sugestie na naszym [forum głosowych użytkownika.](https://feedback.azure.com/forums/911455-azure-iot-central)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować i używać zestawów webhook, sugerowanym następnym krokiem jest [zbadanie konfigurowania grup akcji monitora Azure.](howto-use-action-groups.md)
