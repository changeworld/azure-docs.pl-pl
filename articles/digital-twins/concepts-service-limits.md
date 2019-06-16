---
title: Limity usługi Azure Twins cyfrowego publicznych w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, że Azure cyfrowego bliźniaczych reprezentacji publicznej wersji zapoznawczej limity usług.
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: dwalthermsft
ms.openlocfilehash: cc873ad441c93a7fce54c275e9f7d52f0b044319
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60927557"
---
# <a name="public-preview-service-limits"></a>Limity usługi w publicznej wersji zapoznawczej

Okresie publicznej wersji zapoznawczej Twins cyfrowych platformy Azure ma następującą subskrypcję tymczasowe, wystąpienie i limitów szybkości.

Te ograniczenia ma ułatwić uzyskiwanie informacji o nowej usługi i jego wiele funkcji.

> [!NOTE]
> Limity te będzie można zwiększyć lub usunięty przez ogólnie dostępne (GA).

## <a name="per-subscription-limits"></a>Limity subskrypcji

W publicznej wersji zapoznawczej Każda subskrypcja platformy Azure można utworzyć lub jednocześnie uruchomione tylko jedno wystąpienie Twins cyfrowych platformy Azure.

> [!TIP]
> Jeśli usuniesz wystąpienia, możesz utworzyć nowy.

## <a name="per-instance-limits"></a>Limity wystąpień

Z kolei może mieć każde wystąpienie Twins cyfrowych platformy Azure:

- Dokładnie jeden osadzone **IoTHub** zasób, który jest tworzony automatycznie podczas aprowizowania usługi.
- Dokładnie jeden **EventHub** punktu końcowego dla typu zdarzenia **DeviceMessage**.
- Maksymalnie trzy **EventHub**, **ServiceBus**, lub **EventGrid** punktów końcowych typu zdarzenia **SensorChange**, **SpaceChange** , **TopologyOperation**, lub **UdfCustom**.

> [!NOTE]
> Niektóre parametry, które zwykle są zdefiniowane w tworzenie powyżej jednostek usługi Azure IoT nie są wymagane w publicznej wersji zapoznawczej.
> - Zapoznaj się z [dokumentację referencyjną programu Swagger](./how-to-use-swagger.md) na najnowszej specyfikacji interfejsu API.

## <a name="azure-digital-twins-management-api-limits"></a>Limity cyfrowego Twins interfejsu API zarządzania platformy Azure

Limity szybkości żądania, dla usługi Azure cyfrowego Twins interfejsu API zarządzania są:

- 100 żądań na sekundę do cyfrowego Twins zarządzania interfejsu API usługi Azure.
- Maksymalnie 1000 obiektów zwróconych przez jedno zapytanie cyfrowego Twins interfejsu API zarządzania Azure.

> [!IMPORTANT]
> Jeśli przekroczysz limit 1000 obiekt, komunikat o błędzie i uprościć zapytanie.

## <a name="user-defined-functions-rate-limits"></a>Limity szybkości funkcje zdefiniowane przez użytkownika

Następujące limity Ustaw sumę wszystkich wywołań funkcji zdefiniowanej przez użytkownika do wystąpienia Twins cyfrowych platformy Azure:

- 400 wywołania biblioteki klienta na sekundę
- 100 **SendNotification** wywołania na sekundę

> [!NOTE]
> Następujące akcje może spowodować limitów szybkości dodatkowe, które mają być stosowane tymczasowo:
> - Edycje metadane obiektu topologii
> - Aktualizacje wprowadzone do definicji funkcji zdefiniowanej przez użytkownika
> - Urządzenia, które wysyłają dane telemetryczne po raz pierwszy

## <a name="device-telemetry-limits"></a>Limity danych telemetrycznych z urządzeń

Następujące limity limit sumę wszystkich wiadomości wysyłanych przez urządzenia do Twojego wystąpienia Twins cyfrowych platformy Azure:

- 100 komunikatów na sekundę

## <a name="next-steps"></a>Kolejne kroki

- Aby wypróbować przykładowe Twins cyfrowych platformy Azure, przejdź do [szybkiego startu, aby znaleźć dostępne pokoje](./quickstart-view-occupancy-dotnet.md).