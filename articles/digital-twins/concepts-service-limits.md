---
title: Limity usługi Azure Twins cyfrowego publicznych w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Świadomość, że Azure cyfrowego bliźniaczych reprezentacji publicznej wersji zapoznawczej limity usługi
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dwalthermsft
ms.openlocfilehash: 86ae75118dd1311ea2ae92fb718fe4c58b8e5673
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50961759"
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

- Jeden **IoTHub** zasobów.
- Jeden **EventHub** punktu końcowego dla typu zdarzenia **DeviceMessage**.
- Maksymalnie trzy **EventHub**, **ServiceBus**, lub **EventGrid** punktów końcowych typu zdarzenia **SensorChange**, **SpaceChange** , **TopologyOperation**, lub **UdfCustom**.

## <a name="management-api-limits"></a>Limity interfejsu API zarządzania

Limity szybkości żądania interfejsu API zarządzania są:

- 100 żądań na sekundę do interfejsu API zarządzania.
- Maksymalnie 1000 obiektów zwróconych przez kwerendę pojedynczy interfejs API zarządzania. 

> [!IMPORTANT]
> Jeśli przekroczysz limit 1000 obiekt, komunikat o błędzie i uprościć zapytanie.

## <a name="udf-rate-limits"></a>Limity szybkości funkcji zdefiniowanej przez użytkownika

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

Aby wypróbować przykładowe Twins cyfrowych platformy Azure, przejdź do [szybkiego startu, aby znaleźć dostępne pokoje](./quickstart-view-occupancy-dotnet.md).