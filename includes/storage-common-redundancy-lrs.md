---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 58d81bd4c1ce4b3af91a335039f62df08b340576
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399995"
---
Magazyn lokalnie nadmiarowy (LRS) ma na celu zapewnienie co najmniej 99,999999999% (11 9) trwałości obiektów w danym roku przez replikowanie danych w jednostce skalowania magazynu. Jednostki skali magazynu znajduje się w centrum danych w regionie, w którym utworzono konto magazynu. Żądania zapisu do konta magazynu LRS wróci pomyślnie tylko wtedy, gdy dane zostały zapisane w wszystkich replik. Tych replik każdej znajdują się w oddzielnych domenach błędów i domenach w obrębie jednej jednostki skali magazynu aktualizacji.

Jednostki skali magazynu to zbiór stojakami węzłów magazynu. Domena błędów (FD) to grupa węzłów, które reprezentują fizyczną jednostkę awarii i może być traktowany jako węzły należące do tej samej fizycznej stojaka. Domeny uaktualnienia (UD) to grupa węzłów, które są ze sobą uaktualnienia w trakcie procesu uaktualniania usługi (wdrażanie). Repliki są dystrybuowane między domenami aktualizacji i błędów w w obrębie jednej jednostki skalowania magazynu. Tej architektury gwarantuje, że Twoje dane są dostępne, jeśli awaria sprzętowa ma wpływ na jednym stojaku, lub gdy węzły były uaktualniane w miarę wprowadzania aktualizacji.

Magazyn LRS jest najniższy koszt opcję replikacji i oferuje trwałość co najmniej w porównaniu do innych opcji. W przypadku awarii poziomie centrum danych (na przykład pożar lub przepełnieniu) wszystkie repliki może być utracone lub ulegnie nieodwracalnej awarii. Aby zmniejszyć to zagrożenie, firma Microsoft zaleca używanie magazynu strefowo nadmiarowego (ZRS) lub magazyn geograficznie nadmiarowy (GRS).

* Jeśli aplikacja przechowuje dane, które można łatwo odtworzone, jeśli dojdzie do utraty danych, użytkownik może zrezygnować dla magazynu LRS.
* Niektóre aplikacje są ograniczone do replikowania danych tylko w obrębie kraju ze względu na wymagania ładu danych. W niektórych przypadkach może być sparowane regiony, w których dane są replikowane do kont GRS, w innym kraju. Aby uzyskać więcej informacji na temat sparowane regiony, zobacz [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/).
