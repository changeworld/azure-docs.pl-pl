---
title: Wersje księgi usługi Azure Blockchain Service, poprawki, uaktualnienie &
description: Omówienie obsługiwanych wersji książek w usłudze Azure Blockchain, w tym zasad dotyczących poprawek systemów oraz uaktualnień zarządzanych przez system i zarządzanych przez użytkownika.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325187"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Obsługiwane wersje księgi usługi Azure Blockchain Service

Usługa Azure Blockchain service korzysta z księgi [kworum](https://www.goquorum.com/developers) opartej na Ethereum przeznaczonej do przetwarzania transakcji prywatnych w ramach grupy znanych uczestników, zidentyfikowanych jako konsorcjum w usłudze Azure Blockchain.

Obecnie usługa Azure Blockchain Service obsługuje [usługę Kworum w wersji 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) i [menedżera transakcji Tessera.](https://github.com/jpmorganchase/tessera)

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami

Przechowywanie wersji w Kworum odbywa się za pośrednictwem dużych, pomocniczych i poprawek. Na przykład jeśli wersja kworum jest 2.0.1, typ wydania będzie klasyfikowany w następujący sposób:

|Duży | Mały  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Usługa Azure Blockchain automatycznie aktualizuje poprawki dotyczące wersji Kworum do istniejących uruchomionych członków w ciągu 30 dni od udostępnienia ich z Kworum.

## <a name="availability-of-new-ledger-versions"></a>Dostępność nowych wersji księgi

Usługa Azure Blockchain service udostępnia najnowsze główne i pomocnicze wersje księgi kworum w ciągu 60 dni od udostępnienia ich u producenta kworum. Konsorcja mają do wyboru maksymalnie cztery mniejsze zwolnienia, z których można wybierać podczas tworzenia obsługi administracyjnej nowego członka i konsorcjum. Uaktualnianie z wersji głównej lub pomocniczej nie jest obecnie obsługiwane. Na przykład jeśli korzystasz z wersji 2.x, uaktualnienie do wersji 3.x nie jest obecnie obsługiwane. Podobnie, jeśli korzystasz z wersji 2.2, uaktualnienie do wersji 2.3 nie jest obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki

[Limity w usłudze Blockchain platformy Azure](limits.md)
