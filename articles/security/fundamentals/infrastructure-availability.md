---
title: Dostępność infrastruktury platformy Azure — zabezpieczenia platformy Azure
description: Ten artykuł zawiera informacje o tym, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure i zapewnić maksymalną dostępność danych klientów.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727229"
---
# <a name="azure-infrastructure-availability"></a>Dostępność infrastruktury platformy Azure
Ten artykuł zawiera informacje o tym, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure i zapewnić maksymalną dostępność danych klientów. Platforma Azure zapewnia niezawodną dostępność w oparciu o dużą nadmiarowość osiągniętą dzięki technologii wirtualizacji.

## <a name="temporary-outages-and-natural-disaster"></a>Tymczasowe przerwy w dostawie prądu i klęski żywiołowe
Zespół infrastruktury i operacji w chmurze firmy Microsoft projektuje, tworzy, obsługuje i poprawia bezpieczeństwo infrastruktury chmury. Ten zespół zapewnia, że infrastruktura platformy Azure zapewnia wysoką dostępność i niezawodność, wysoką wydajność i inteligentną skalowalność. Zespół zapewnia bezpieczniejszą, prywatną i zaufaną chmurę.

Zasilacze awaryjne i ogromne banki baterii zapewniają ciągłość energii elektrycznej w przypadku krótkotrwałego zakłócenia zasilania. Generatory awaryjne zapewniają zasilanie zapasowe w przypadku wydłużonych przestojów i planowanej konserwacji. W przypadku wystąpienia klęski żywiołowej centrum danych może używać rezerw paliwa na miejscu.

Szybkie i niezawodne sieci światłowodowe łączą centra danych z innymi głównymi centrami i użytkownikami Internetu. Węzły obliczeniowe hostują obciążenia bliżej użytkowników, aby zmniejszyć opóźnienia, zapewnić nadmiarowość geograficzną i zwiększyć ogólną odporność usług. Zespół inżynierów pracuje przez całą dobę, aby zapewnić stały dostęp do usług.

Firma Microsoft zapewnia wysoką dostępność dzięki zaawansowanemu monitorowaniu i reagowaniu na incydenty, obsłudze obsługi usług i możliwości wykonywania kopii zapasowych w trybie failover. Geograficznie rozproszone centra operacyjne firmy Microsoft działają 24/7/365. Sieć platformy Azure jest jedną z największych na świecie. Sieć dystrybucji światłowodowej i zawartości łączy centra danych i węzły brzegowe, aby zapewnić wysoką wydajność i niezawodność.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Platforma Azure zapewnia trwałość danych w dwóch lokalizacjach. Można wybrać lokalizację witryny kopii zapasowej. W obu lokalizacjach platforma Azure stale przechowuje trzy repliki danych w dobrej kondycji.

## <a name="database-availability"></a>Dostępność bazy danych
Platforma Azure zapewnia, że baza danych jest dostępna do Internetu za pośrednictwem bramy internetowej z trwałą dostępnością bazy danych. Monitorowanie ocenia kondycję i stan aktywnych baz danych w odstępach czasu pięciu minut.

## <a name="storage-availability"></a>Dostępność magazynu
Platforma Azure zapewnia magazyn za pośrednictwem wysoce skalowalnej i trwałej usługi magazynu, która zapewnia punkty końcowe łączności. Oznacza to, że aplikacja może uzyskać bezpośredni dostęp do usługi magazynu. Usługa magazynu przetwarza przychodzące żądania magazynu wydajnie, z integralnością transakcyjną.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tym, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure, zobacz:

- [Obiekty platformy Azure, pomieszczenia i zabezpieczenia fizyczne](physical-security.md)
- [Składniki i granice systemu informacyjnego platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Funkcje zabezpieczeń usługi Azure SQL Database](infrastructure-sql.md)
- [Operacje produkcyjne i zarządzanie platformą Azure](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](protection-customer-data.md)
