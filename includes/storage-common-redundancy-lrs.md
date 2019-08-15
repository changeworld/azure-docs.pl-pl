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
ms.openlocfilehash: b4b050befe160d69fbc44b7f0a2ebbbbad3d705a
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015953"
---
Magazyn lokalnie nadmiarowy (LRS) oferuje co najmniej 99,999999999% (11 dziewięciu) trwałości obiektów w danym roku. LRS zapewnia tę trwałość obiektu przez replikowanie danych do jednostki skalowania magazynu. Centrum danych znajdujące się w regionie, w którym utworzono konto magazynu, obsługuje jednostkę skalowania magazynu. Żądanie zapisu do konta magazynu LRS zwraca się pomyślnie tylko po zapisaniu danych we wszystkich replikach. Każda replika znajduje się w osobnych domenach błędów i uaktualnia domeny w ramach jednostki skalowania magazynu.

Jednostka skali magazynu jest kolekcją stojaków węzłów magazynu. Domena błędów (FD) jest grupą węzłów reprezentujących fizyczną jednostkę awarii. Domenę błędów należy traktować jako węzły należące do tego samego stojaka fizycznego. Domena uaktualnienia (UD) jest grupą węzłów, które są uaktualniane razem podczas procesu uaktualniania usługi (wdrażanie). Repliki są rozłożone na domenami błędów i w jednej jednostce skalowania. Ta architektura zapewnia dostępność danych, jeśli awaria sprzętowa ma wpływ na pojedynczy stojak lub gdy węzły są uaktualniane podczas uaktualniania usługi.

LRS to najniższa cena opcja replikacji i oferuje najmniejszą trwałość w porównaniu z innymi opcjami. Jeśli wystąpi awaria na poziomie centrum danych (na przykład pożar lub zalanie), wszystkie repliki mogą być tracone lub nieodwracalne. Aby zmniejszyć to ryzyko, firma Microsoft zaleca korzystanie z magazynu Strefowo nadmiarowego (ZRS), magazynu geograficznie nadmiarowego (GRS) lub geograficznie nadmiarowego magazynu (GZRS).

* Jeśli aplikacja przechowuje dane, które można łatwo odtworzyć w przypadku utraty danych, można wybrać opcję LRS.
* Niektóre aplikacje są ograniczone do replikowania danych tylko w obrębie kraju/regionu ze względu na wymagania dotyczące zarządzania danymi. W niektórych przypadkach sparowane regiony, w których dane są replikowane dla kont GRS, mogą znajdować się w innym kraju/regionie. Aby uzyskać więcej informacji na temat sparowanych regionów, zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/).
