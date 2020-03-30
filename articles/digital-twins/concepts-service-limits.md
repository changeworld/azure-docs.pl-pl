---
title: Limity publicznych usług podglądu — Azure Digital Twins | Dokumenty firmy Microsoft
description: Dowiedz się więcej o publicznych usługach w wersji zapoznawczej, subskrypcji, instancji i limitach stawek dla usługi Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 5e323d8faa19ceb0712aa6183df17740ce2a0a1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370387"
---
# <a name="public-preview-service-limits"></a>Limity usługi w publicznej wersji zapoznawczej

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Podczas publicznej wersji zapoznawczej usługa Azure Digital Twins ma następujące limity subskrypcji tymczasowej, wystąpienia i stawki dla istniejących klientów. Te ograniczenia istnieją, aby ułatwić poznawanie nowej usługi i jej wielu funkcji i zostaną zwiększone lub usunięte przez ogólną dostępność (GA).

## <a name="per-subscription-limits"></a>Limity dla subskrypcji

Podczas publicznej wersji zapoznawczej każda subskrypcja platformy Azure może tworzyć lub uruchamiać tylko jedno wystąpienie usługi Azure Digital Twins naraz. Jeśli usuniesz wystąpienie, możesz utworzyć nowe.

## <a name="per-instance-limits"></a>Limity dla wystąpień

Z kolei każde wystąpienie usługi Azure Digital Twins może mieć:

- Dokładnie jeden osadzony zasób **IoTHub,** który jest tworzony automatycznie podczas inicjowania obsługi administracyjnej usługi.
- Dokładnie jeden punkt końcowy **EventHub** dla typu zdarzenia **DeviceMessage**.
- Maksymalnie trzy punkty końcowe **EventHub**, **ServiceBus**lub **EventGrid** typu **Zdarzenie SensorChange**, **SpaceChange**, **TopologyOperation**lub **UdfCustom**.

> [!NOTE]
> Niektóre parametry, które są zwykle zdefiniowane podczas tworzenia powyższych jednostek IoT platformy Azure nie są wymagane podczas publicznej wersji zapoznawczej.
> - Najnowsze specyfikacje interfejsu API można znaleźć w [dokumentacji referencyjnej Swagger.](./how-to-use-swagger.md)

## <a name="azure-digital-twins-management-api-limits"></a>Limity interfejsu API zarządzania cyfrowymi urządzeniami usługi Azure Twins

Limity szybkości żądań dla interfejsu API zarządzania cyfrowymi urządzeniami usługi Azure dla obiektów i obrazów bliźniąt to:

- 100 żądań na sekundę do interfejsu API zarządzania cyfrowymi urządzeniami twins platformy Azure.
- Do 1000 obiektów zwróconych przez pojedyncze zapytanie interfejsu API zarządzania cyfrowymi pakietami cyfrowymi platformy Azure.

> [!IMPORTANT]
> Jeśli przekroczysz limit 1000 obiektów, zostanie wyświetlony błąd i należy uprościć kwerendę.

## <a name="user-defined-functions-rate-limits"></a>Limity szybkości funkcji zdefiniowanych przez użytkownika

Następujące limity ustawiają całkowitą liczbę wszystkich wywołań funkcji zdefiniowanych przez użytkownika w wystąpieniu usługi Azure Digital Twins:

- 400 wywołań biblioteki klienta na sekundę
- 100 **wywołań sendnotification** na sekundę

> [!NOTE]
> Następujące akcje mogą spowodować tymczasowe zastosowanie dodatkowych limitów stawek:
> - Zmiany wprowadzone w metadanych obiektu topologii
> - Aktualizacje wprowadzone do definicji funkcji zdefiniowanej przez użytkownika
> - Urządzenia, które po raz pierwszy wysyłają dane telemetryczne

## <a name="device-telemetry-limits"></a>Limity telemetrii urządzenia

Następujące limity ograniczają całkowitą liczbę wiadomości, które urządzenia mogą wysyłać do wystąpienia usługi Azure Digital Twins:

- 100 wiadomości na sekundę na wszystkich urządzeniach
-    25 wiadomości na sekundę na urządzenie

## <a name="next-steps"></a>Następne kroki

- Aby wypróbować przykład usługi Azure Digital Twins, przejdź do [przewodnika Szybki start, aby znaleźć dostępne pokoje.](./quickstart-view-occupancy-dotnet.md)
