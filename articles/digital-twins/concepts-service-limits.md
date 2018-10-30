---
title: Limity usługi Azure Twins cyfrowego publicznych w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Understanding Azure cyfrowego Twins publicznej wersji zapoznawczej limity usługi
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dwalthermsft
ms.openlocfilehash: f9a3d934de47630ac3fd2356001014d006c2a4eb
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212271"
---
# <a name="public-preview-service-limits"></a>Limity usługi w publicznej wersji zapoznawczej

Podczas **publicznej wersji zapoznawczej**, Twins cyfrowych platformy Azure będzie mieć tymczasowe subskrypcji, wystąpienia i limity szybkości, które są opisane poniżej.

Te ograniczenia ma ułatwić uzyskiwanie informacji o nowej usługi i jego wiele funkcji.

> [!NOTE]
> Limity te będzie można zwiększyć i/lub usunięte przez **ogólnie** (**GA**).

## <a name="per-subscription-limits"></a>Limity subskrypcji

Podczas **publicznej wersji zapoznawczej**, każda subskrypcja platformy Azure można tworzyć lub uruchomione dokładnie jedno wystąpienie Twins cyfrowych platformy Azure w danym momencie.

> [!TIP]
> Usuwanie wystąpienia pozwala utworzyć nowy.

## <a name="per-instance-limits"></a>Limity wystąpień

Z kolei może mieć każde wystąpienie Twins cyfrowych platformy Azure:

- Jeden **IoTHub** zasobów
- Jeden **EventHub** punktu końcowego dla typu zdarzenia **DeviceMessage**
- Maksymalnie trzy **EventHub**, **ServiceBus**, lub **EventGrid** punktów końcowych typu zdarzenia **SensorChange**, **SpaceChange** , **TopologyOperation**, lub **UdfCustom**

## <a name="management-api-limits"></a>Limity interfejsu API zarządzania

Limity szybkości żądania interfejsu API zarządzania są:

- 100 żądań na sekundę do interfejsu API zarządzania
- Jedno zapytanie interfejsu API zarządzania może zwrócić maksymalnie 1000 obiektów

> [!IMPORTANT]
> Jeśli przekroczysz limit obiektu 1000, zostanie zwrócony błąd i, należy uprościć zapytanie.

## <a name="udf-rate-limits"></a>Limity szybkości funkcji zdefiniowanej przez użytkownika

Następujące limity Ustaw sumę wszystkich wywołań funkcji zdefiniowanej przez użytkownika do wystąpienia Twins cyfrowych platformy Azure:

- 400 wywołania biblioteki klienta na sekundę
- 100 **SendNotification** wywołania na sekundę

> [!NOTE]
> Następujące akcje mogą spowodować limitów szybkości dodatkowe, które mają być stosowane tymczasowo:
> - Zmiany metadanych obiektu topologii
> - Aktualizacje definicji funkcji zdefiniowanej przez użytkownika
> - Urządzenia wysyłają dane telemetryczne po raz pierwszy

## <a name="device-telemetry-limits"></a>Limity danych telemetrycznych z urządzeń

Limity poniżej limitu sumę wszystkich wiadomości urządzeń można wysyłać do Twojego wystąpienia Twins cyfrowych platformy Azure:

- 100 komunikatów na sekundę

## <a name="next-steps"></a>Kolejne kroki

Aby wypróbować przykładowe Twins cyfrowych platformy Azure, przejdź do [szybkiego startu, aby znaleźć dostępne pokoje](./quickstart-view-occupancy-dotnet.md).