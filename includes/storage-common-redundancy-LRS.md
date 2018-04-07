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
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
Magazyn lokalnie nadmiarowy (LRS) ma na celu zapewnienie co najmniej 99.999999999% (11 na 9) trwałość obiektów w danym roku przez replikowanie danych w ramach jednostki skalowania magazynu. Jednostki skalowania magazynu znajduje się w centrum danych w regionie, w którym utworzono konto magazynu. Żądanie zapisu do konta magazynu LRS pomyślnie zwraca tylko wtedy, gdy dane został zapisany do wszystkich replik. Te repliki każdego znajdują się w oddzielnych domen błędów i zaktualizuj domen w jedną jednostkę skalowania magazynu.

Jednostki skalowania magazynu to zbiór stojakami węzłów magazynu. Domeny błędów (FD) to grupa węzłów, które reprezentują fizyczną jednostkę awarii i mogą być uważane za węzłach należących do tej samej fizycznej stojaku. Domeny uaktualnienia (UD) to grupa węzłów, które są ze sobą uaktualniony podczas procesu uaktualniania usługi (wdrażanie). Repliki są rozkładane między UDs i FDs w jedną jednostkę skalowania magazynu. Taka architektura zapewnia dane są dostępne, jeśli jednym stojaku ma wpływ awarii sprzętu lub po uaktualnieniu węzłów podczas wdrażania.

Magazyn LRS jest najniższy koszt opcję replikacji i oferuje co najmniej trwałości w porównaniu do innych opcji. W przypadku awarii poziomie centrum danych (na przykład fire lub zalewania) wszystkie repliki może być utracone lub ich nieodwracalnej utraty. Aby zmniejszyć to zagrożenie, firma Microsoft zaleca używanie magazyn strefowo nadmiarowy (ZRS) lub magazyn geograficznie nadmiarowy (GRS).

* Jeśli aplikacja przechowuje dane, które można łatwo odtworzyć w przypadku utraty danych, można wybrać opcję LRS.
* Niektóre aplikacje są ograniczone do replikowanie danych tylko w obrębie kraju ze względu na wymagania ładu danych. W niektórych przypadkach sparowanego regionów, w których dane są replikowane do kont GRS może być w innym kraju. Aby uzyskać więcej informacji na sparowanej regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).
