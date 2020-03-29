---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/19/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: cdcbe993bd1100b2060a1f8d38eb82ac97121c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74851636"
---
Opłaty są naliczane za usługę Azure Storage na podstawie użycia konta magazynu. Wszystkie obiekty w koncie magazynu są rozliczane wspólnie jako grupa. 

Koszty magazynowania są obliczane zgodnie z następującymi czynnikami: 

* **Region** odnosi się do regionu geograficznego, w którym konto jest oparte.
* **Typ konta** odnosi się do typu używanego konta magazynu. 
* **Warstwa dostępu** odnosi się do wzorca użycia danych określonego dla konta magazynu ogólnego przeznaczenia w wersji 2 lub blob.
* **Pojemność** magazynu odnosi się do tego, jaka część przydziału konta magazynu jest używany do przechowywania danych.
* **Replikacja** określa, ile kopii danych są przechowywane w tym czasie i w jakich lokalizacjach.
* **Transakcje** odnoszą się do wszystkich operacji odczytu i zapisu do usługi Azure Storage.
* **Dane wychodzące** odnosi się do wszystkich danych przesyłanych z regionu platformy Azure. Gdy dane na koncie magazynu są dostępne przez aplikację, która nie jest uruchomiona w tym samym regionie, opłaty są naliczane za ruch wychodzący na dane. Aby uzyskać informacje dotyczące używania grup zasobów do grup danych i usług w tym samym regionie w celu ograniczenia opłat za ruch wychodzący, zobacz [Co to jest grupa zasobów platformy Azure?](https://docs.microsoft.com/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group). 

Strona [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) zawiera szczegółowe informacje o cenach w zależności od typu konta, pojemności magazynu, replikacji i transakcji. Artykuł [Szczegóły cennika transferów danych](https://azure.microsoft.com/pricing/details/data-transfers/) zawiera szczegółowe informacje o cenach za wyjście danych. Artykuł [Kalkulator cen usługi Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) zawiera informacje, które ułatwią szacowanie kosztów.

