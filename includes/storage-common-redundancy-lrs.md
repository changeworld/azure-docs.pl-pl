---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8908ded31b96aac50db1fc25e92c2c0a8e960453
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219042"
---
Magazyn lokalnie nadmiarowy (LRS) replikuje dane trzy razy w jednym centrum danych. LRS zapewnia co najmniej 99,999999999% (11 dziewięciu) trwałości obiektów w danym roku. LRS to najniższa cena opcja replikacji i oferuje najmniejszą trwałość w porównaniu z innymi opcjami.

Jeśli wystąpi awaria na poziomie centrum danych (na przykład pożar lub zalanie), wszystkie repliki na koncie magazynu przy użyciu LRS mogą zostać utracone lub nieodwracalne. Aby zmniejszyć to ryzyko, firma Microsoft zaleca korzystanie z magazynu Strefowo nadmiarowego (ZRS), magazynu geograficznie nadmiarowego (GRS) lub geograficznie nadmiarowego magazynu (GZRS).

Żądanie zapisu do konta magazynu LRS zwraca się pomyślnie tylko po zapisaniu danych we wszystkich trzech replikach.

Warto używać LRS w następujących scenariuszach:

* Jeśli aplikacja przechowuje dane, które można łatwo odtworzyć w przypadku utraty danych, można wybrać opcję LRS.
* Niektóre aplikacje są ograniczone do replikowania danych tylko w obrębie kraju/regionu ze względu na wymagania dotyczące zarządzania danymi. W niektórych przypadkach sparowane regiony, w których dane są replikowane dla kont GRS, mogą znajdować się w innym kraju/regionie. Aby uzyskać więcej informacji na temat sparowanych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).
