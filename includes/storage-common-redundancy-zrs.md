---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ac3e87d7f921da2c1089eb6f2c7e61fc2c432f9f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463941"
---
Magazyn strefowo nadmiarowy (ZRS) replikuje dane synchronicznie przez trzy klastry magazynu w jednym regionie. Każdy klaster magazynu jest fizycznie oddzielony od innych i znajduje się w jego własnej strefie dostępności (AZ). Każda strefa dostępności&mdash;i klaster ZRS w tym&mdash;są autonomiczne i zawierają oddzielne narzędzia i funkcje sieciowe. Żądanie zapisu do konta magazynu ZRS zwraca się pomyślnie dopiero po zapisaniu danych we wszystkich replikach w trzech klastrach.

Gdy dane są przechowywane na koncie magazynu za pomocą replikacji ZRS, można nadal uzyskiwać dostęp do danych i zarządzać nimi, jeśli strefa dostępności stanie się niedostępna. ZRS zapewnia doskonałą wydajność i małe opóźnienia. ZRS oferuje takie same elementy docelowe skalowalności jak [Magazyn lokalnie nadmiarowy (LRS)](../articles/storage/common/storage-redundancy-lrs.md). Aby uzyskać więcej informacji o skalowalności dla kont magazynu w warstwie Standardowa, zobacz [elementy docelowe skalowalności dla kont magazynu w warstwie Standardowa](../articles/storage/common/scalability-targets-standard-account.md).

Weź pod uwagę ZRS scenariusze, które wymagają spójności, trwałości i wysokiej dostępności. Nawet jeśli awaria lub klęska żywiołowa nie jest dostępna dla niedostępności strefy dostępności, ZRS oferuje trwałość dla obiektów magazynu co najmniej 99,9999999999% (12 9) w danym roku.

Magazyn strefowo nadmiarowy (GZRS) (wersja zapoznawcza) replikuje dane synchronicznie w trzech strefach dostępności platformy Azure w regionie podstawowym, a następnie replikuje dane asynchronicznie do regionu pomocniczego. GZRS zapewnia wysoką dostępność wraz z maksymalną trwałością. GZRS zaprojektowano w celu udostępnienia co najmniej 99.99999999999999% (16 9) trwałości obiektów w danym roku. Aby uzyskać dostęp do odczytu do danych w regionie pomocniczym, Włącz strefę geograficzną z dostępem do odczytu (RA-GZRS). Aby uzyskać więcej informacji na temat GZRS, zobacz [Geograficznie nadmiarowy magazyn w celu zapewnienia wysokiej dostępności i maksymalnej trwałości (wersja zapoznawcza)](../articles/storage/common/storage-redundancy-gzrs.md).

Aby uzyskać więcej informacji na temat stref dostępności, zobacz [strefy dostępności Omówienie](https://docs.microsoft.com/azure/availability-zones/az-overview).
