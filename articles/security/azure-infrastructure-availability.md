---
title: Dostępność infrastruktury platformy Azure — zabezpieczenia platformy Azure
description: Ten artykuł zawiera informacje o jakie firma Microsoft używa do zabezpieczania infrastruktury platformy Azure i zapewnienia maksymalnej dostępności danych klientów.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: d13c79152afe00cb326f69b523e9fd7f0c3550b0
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872605"
---
# <a name="azure-infrastructure-availability"></a>Dostępność infrastruktury platformy Azure
Ten artykuł zawiera informacje o jakie firma Microsoft używa do zabezpieczania infrastruktury platformy Azure i zapewnienia maksymalnej dostępności danych klientów. System Azure oferuje niezawodne dostępność, oparte na rozbudowane nadmiarowości osiągane przy użyciu technologii wirtualizacji.

## <a name="temporary-outages-and-natural-disaster"></a>Tymczasowe awarii i klęskami żywiołowymi
Zespół infrastruktury chmury firmy Microsoft i operacje projektuje, tworzy, działa i zwiększa bezpieczeństwo infrastruktury chmurowej. Ten zespół zapewnia udostępnia infrastrukturę platformy Azure o wysokiej dostępności i niezawodności, wysokiej wydajności i skalowalności inteligentne. Zespół zapewnia chmura bezpieczniejsze, prywatne i zaufanych.

Zasilacze i szeroki banki baterii zapewnić energii elektrycznej ciągłe sytuacji krótkoterminowej przerw w działaniu usługi power. Awaryjnych Podaj zasilacz dłuższych przestojów i planowanej konserwacji. W przypadku klęski żywiołowej centrum danych można użyć rezerwy we wprowadzaniu na miejscu.

Szybkie i niezawodne działanie fiber światłowodowe sieci łączenia centrów danych z innych głównych koncentratorów i użytkowników Internetu. Węzły obliczeniowe hosta obciążeń bliżej do użytkowników w celu zmniejszenia opóźnień, zapewniają geograficzną nadmiarowość i zwiększyć ogólną usługi odporności. Zespół inżynierów działa nieustannie upewnij się, że stale dostępnych usług.

Firma Microsoft zapewnia wysoką dostępność dzięki zaawansowanego monitorowania i reagowania na zdarzenia, obsługi i możliwości tworzenia kopii zapasowej trybu failover. Geograficznie rozproszonych centrów operacji firmy Microsoft działają 24/7/365. Sieć platformy Azure jest jedną z największych na świecie. Sieć światłowodowe i zawartości dystrybucji fiber łączy się z węzłami centrów danych i urządzeniami brzegowymi w celu zapewnienia o wysokiej wydajności i niezawodności.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Azure przechowuje dane w trwałe w dwóch lokalizacjach. Możesz wybrać lokalizację kopii zapasowej lokacji. W obu lokalizacjach platformy Azure obsługuje stale dobrej kondycji trzech replik danych.

## <a name="database-availability"></a>Dostępność bazy danych
Platforma Azure zapewnia, że baza danych jest dostępny za pośrednictwem bramy internetowej o dostępności bazy danych przez internet. Monitorowanie ocenia kondycję i stan aktywnych baz danych w 5 minutowymi interwałami.

## <a name="storage-availability"></a>Dostępność magazynu
System Azure zapewnia magazynu za pośrednictwem usługi wysoko skalowalnego i trwałego magazynu, która zapewnia łączność punktów końcowych. Oznacza to, że aplikacji może bezpośrednio uzyskać dostęp do usługi magazynu. Usługi storage przetwarza przychodzące żądania magazynu wydajnie, za pomocą zapewniający integralność transakcji;.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się, co firma Microsoft używa do zabezpieczania infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne](azure-physical-security.md)
- [Usługi Azure information składników systemu i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieć platformy Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń w usłudze Azure SQL Database](azure-infrastructure-sql.md)
- [Operacje platformy Azure środowiska produkcyjnego i zarządzanie](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](azure-protection-of-customer-data.md)
