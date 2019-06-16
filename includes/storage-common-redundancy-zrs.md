---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d25efa8c666fa91c7bd652a7cf931ea6ca379aa2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133104"
---
Magazyn strefowo nadmiarowy (ZRS) replikuje Twoje dane synchronicznie w trzech klastrach magazynu w jednym regionie. Każdy klaster magazynu są fizycznie oddzielone od innych i znajduje się w jego własnej strefie dostępności (AZ). Każda strefa dostępności&mdash;i klastra magazynu ZRS w nim&mdash;jest autonomicznym i zawiera osobne narzędzia i funkcje sieciowe. Żądania zapisu do konta magazynu ZRS wróci pomyślnie tylko wtedy, gdy dane są zapisywane do wszystkich replik w trzech klastrach.

Gdy dane są przechowywane na koncie magazynu przy użyciu replikacji magazynu ZRS, można nadal dostęp do danych i zarządzanie nimi Jeśli strefa dostępności staje się niedostępny. Magazyn ZRS zapewnia doskonałą wydajność i małe opóźnienia. Magazyn ZRS zapewnia takie same [cele skalowalności](../articles/storage/common/storage-scalability-targets.md) jako [magazyn lokalnie nadmiarowy (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Magazyn ZRS należy wziąć pod uwagę scenariusze, które wymagają spójności, trwałości i wysokiej dostępności. Nawet w przypadku awarii lub klęski żywiołowej renderuje strefie dostępności niedostępny, Magazyn ZRS zapewnia trwałość obiektów magazynu co najmniej 99,9999999999% (12 9) w danym roku.

Aby uzyskać więcej informacji o strefach dostępności, zobacz [Przegląd stref dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview).