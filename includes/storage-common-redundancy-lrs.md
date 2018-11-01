---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/24/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a948a40e638b5f6e042c62ab58c2b7b65a49cd4e
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741984"
---
Magazyn lokalnie nadmiarowy (LRS) zawiera co najmniej 99,999999999% (11 nines) trwałości obiektów w danym roku. Magazyn LRS zawiera trwałości tego obiektu dzięki replikacji danych w jednostce skalowania magazynu. Centrum danych, znajdującego się w regionie, w którym utworzono konto magazynu obsługuje jednostki skali magazynu. Żądania zapisu do konta magazynu LRS wróci pomyślnie tylko wtedy, gdy dane są zapisywane do wszystkich replik. Każda replika znajduje się w oddzielnych domenach błędów i domenach aktualizacji w jednostce skalowania magazynu.

Jednostki skali magazynu to zbiór stojakami węzłów magazynu. Domeny błędów (FD) jest grupą węzły, które reprezentują fizyczną jednostkę awarii. Domena błędów należy traktować jako węzły należące do tej samej fizycznej stojaka. Domeny uaktualnienia (UD) to grupa węzłów, które są ze sobą uaktualnienia w trakcie procesu uaktualniania usługi (wdrażanie). Repliki są dystrybuowane między domenami aktualizacji i błędów w w obrębie jednej jednostki skalowania magazynu. Ta architektura zapewnia, że dane są dostępne, jeśli awaria sprzętowa ma wpływ na jednym stojaku, lub gdy węzły były uaktualniane podczas uaktualniania usługi.

Magazyn LRS jest dla opcji replikacji najniższym koszcie i oferuje trwałości co najmniej w porównaniu do innych opcji. W przypadku awarii poziomie centrum danych (na przykład pożar lub przepełnieniu) wszystkie repliki może być utracone lub ulegnie nieodwracalnej awarii. Aby zmniejszyć to zagrożenie, firma Microsoft zaleca używanie magazynu strefowo nadmiarowego (ZRS) lub magazyn geograficznie nadmiarowy (GRS).

* Jeśli aplikacja przechowuje dane, które można łatwo odtworzone, jeśli dojdzie do utraty danych, użytkownik może zrezygnować dla magazynu LRS.
* Niektóre aplikacje są ograniczone do replikowania danych tylko w obrębie kraju ze względu na wymagania ładu danych. W niektórych przypadkach może być sparowane regiony, w których dane są replikowane do kont GRS, w innym kraju. Aby uzyskać więcej informacji na temat sparowane regiony, zobacz [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/).
