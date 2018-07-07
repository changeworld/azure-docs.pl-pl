---
title: Dostępność infrastruktury platformy Azure
description: W artykule opisano poziomy nadmiarowości w celu zapewnienia maksymalnej dostępności danych klientów.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 18c6b87c9926b93eec26cca4028a38e472912c46
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902581"
---
# <a name="azure-infrastructure-availability"></a>Dostępność infrastruktury platformy Azure
System Azure oferuje niezawodne dostępności oparte na rozbudowane nadmiarowości osiągane przy użyciu technologii wirtualizacji. System Azure oferuje wiele poziomów nadmiarowości w celu zapewnienia maksymalnej dostępności danych klientów.

## <a name="temporary-outages-and-natural-disaster"></a>Tymczasowe awarii i klęskami żywiołowymi
Zespół infrastruktury chmury firmy Microsoft i operacje projektuje, tworzy, działa i zabezpiecza infrastruktury chmury. Ten zespół gwarantuje, że infrastruktura platformy Azure jest dostarczanie wysokiej dostępności i niezawodności, wysokiej wydajności, skalowalności inteligentne i bezpieczne, prywatne i zaufanej chmury.

Zasilacze i szeroki banki baterii zapewnić energii elektrycznej ciągłe sytuacji krótkoterminowej przerw w działaniu usługi power.

Awaryjnych Podaj zasilacz dłuższych przestojów i planowanej konserwacji. Centrum danych jest świadczona za pomocą rezerwy we wprowadzaniu na miejscu sytuacji klęskami żywiołowymi.

Szybkich i niezawodnych fiber światłowodowe sieci łączenia centrów danych z innymi główne centra i użytkowników Internetu. Węzły obliczeniowe bliżej hostować obciążenia przez użytkowników końcowych w celu zmniejszenia opóźnienia zapewnia nadmiarowość geograficzna i zwiększyć ogólną usługi odporności. Zespół inżynierów działa nieustannie upewnij się, że usługi są stale dostępne dla klientów.

Firma Microsoft zapewnia wysoką dostępność, za pomocą zaawansowanego monitorowania i reagowania na zdarzenia, obsługi i Utwórz kopię zapasową możliwości trybu failover. Geograficznie rozproszonych centrów operacji firmy Microsoft działają 24/7/365. Sieć platformy Azure jest jedną z największych na świecie. Sieci dystrybucji światłowodowe i zawartości fiber łączy się z centrami danych i węzłami krawędzi w celu zapewnienia wysokiej wydajności i niezawodności.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Azure przechowuje dane klienta trwałe w dwóch lokalizacjach z klientem, mających możliwość wybierz lokalizację kopii zapasowej lokacji. W obu lokalizacjach platformy Azure obsługuje stale wiele replik dobrej kondycji (3) z danych klientów.

## <a name="database-availability"></a>Dostępność bazy danych
Platforma Azure zapewnia, że baza danych jest dostępny za pośrednictwem bramy internetowej o dostępności bazy danych przez Internet. Monitorowanie ocenia kondycję i stan aktywnych baz danych w 5-minutowymi interwałami.

## <a name="storage-availability"></a>Dostępność magazynu
System Azure zapewnia magazynu za pośrednictwem usługi wysoko skalowalnego i trwałego magazynu, która zapewnia łączność punktów końcowych, co pozwala na udostępnienie bezpośrednio przez aplikacja odbierająca komunikaty. Za pomocą usługi storage przychodzących żądań magazynu będą przetwarzane wydajnie z zapewniający integralność transakcji.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne](azure-physical-security.md)
- [Usługi Azure information składników systemu i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieć platformy Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Operacje platformy Azure środowiska produkcyjnego i zarządzanie](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klientów na platformie Azure](azure-protection-of-customer-data.md)
