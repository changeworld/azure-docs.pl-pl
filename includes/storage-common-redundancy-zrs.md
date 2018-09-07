---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4a43966180850645584043690b1be9d6ae232f6e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027451"
---
Magazyn strefowo nadmiarowy (ZRS) replikuje Twoje dane synchronicznie w trzech klastrach magazynu w jednym regionie. Każdy klaster magazynu są fizycznie oddzielone od innych i znajduje się w jego własnej strefie dostępności (AZ). Każda strefa dostępności i klaster magazynu ZRS, to jest autonomicznego, przy użyciu osobnych narzędzi i funkcji sieciowych.

Przechowywanie danych w ramach konta magazynu ZRS zapewnia będą mieć dostęp i Zarządzaj danymi w przypadku, gdy strefa jest niedostępna. Magazyn ZRS zapewnia doskonałą wydajność i małe opóźnienia. Magazyn ZRS zapewnia takie same [cele skalowalności](../articles/storage/common/storage-scalability-targets.md) jako [magazyn lokalnie nadmiarowy (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Magazyn ZRS należy wziąć pod uwagę scenariusze, które wymagają wysokiego poziomu spójności, silne trwałości i wysokiej dostępności, nawet w przypadku awarii lub klęski żywiołowej powoduje wyświetlenie centrum danych strefowych niedostępny. Magazyn ZRS zapewnia trwałość obiektów magazynu co najmniej 99,9999999999% (12 9) w danym roku.

Aby uzyskać więcej informacji o strefach dostępności, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview).