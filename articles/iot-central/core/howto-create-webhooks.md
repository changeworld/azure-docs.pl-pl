---
title: Tworzenie elementów webhook dla reguł na platformie Azure IoT Central | Microsoft Docs
description: Tworzenie elementów webhook w usłudze Azure IoT Central w celu automatycznego powiadamiania innych aplikacji o pożaru reguł.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: db4e48a7bff9127810b051a9ab63bbe9d78cf6da
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271618"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Tworzenie akcji elementu webhook dla reguł na platformie Azure IoT Central

*Ten temat dotyczy konstruktorów i administratorów.*

Elementy webhook umożliwiają łączenie aplikacji IoT Central z innymi aplikacjami i usługami na potrzeby zdalnego monitorowania i powiadomień. Elementy webhook automatycznie powiadamiają inne aplikacje i usługi, które są połączone przy każdym wyzwoleniu reguły w aplikacji IoT Central. Aplikacja IoT Central wysyła żądanie POST do punktu końcowego HTTP innej aplikacji przy każdym wyzwoleniu reguły. Ładunek zawiera szczegóły urządzenia i szczegóły wyzwalacza reguł.

## <a name="set-up-the-webhook"></a>Konfigurowanie elementu webhook

W tym przykładzie nawiążesz połączenie z usługą RequestBin, aby otrzymywać powiadomienia, gdy reguły wyzwalają korzystanie z elementów webhook.

1. Otwórz [RequestBin](https://requestbin.net/).

1. Utwórz nowy RequestBin i skopiuj **adres URL bin**.

1. Utwórz [regułę telemetrii](tutorial-create-telemetry-rules.md). Zapisz regułę i Dodaj nową akcję.

    ![Ekran tworzenia elementu webhook](media/howto-create-webhooks/webhookcreate.png)

1. Wybierz akcję elementu webhook i podaj nazwę wyświetlaną, a następnie wklej adres URL bin jako adres URL wywołania zwrotnego.

1. Zapisz regułę.

Teraz, gdy reguła zostanie wyzwolona, zobaczysz nowe żądanie w RequestBin.

## <a name="payload"></a>Ładunku

Gdy reguła jest wyzwalana, żądanie HTTP POST jest wykonywane do adresu URL wywołania zwrotnego zawierającego ładunek JSON z danymi telemetrycznymi, urządzeniem, regułą i aplikacją. Ładunek może wyglądać następująco:

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

Obecnie nie ma możliwości programistycznego subskrybowania/anulowania subskrypcji z tych elementów webhook za pośrednictwem interfejsu API.

Jeśli masz pomysły dotyczące ulepszania tej funkcji, Opublikuj swoje sugestie na naszym [forum użytkowników](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy już wiesz, jak skonfigurować elementy webhook i korzystać z nich, zalecanym następnym krokiem jest zapoznanie się z tematem [Konfigurowanie grup akcji Azure monitor](howto-use-action-groups.md).
