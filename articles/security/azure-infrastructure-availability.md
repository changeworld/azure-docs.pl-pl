---
title: Dostępność infrastruktury platformy Azure
description: Artykuł opisuje poziomy nadmiarowość, aby zapewnić maksymalne dostępność danych klientów.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: db13e4835e483b4738074a71861737c4851d8dbc
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102513"
---
# <a name="availability-of-azure-infrastructure"></a>Dostępność infrastruktury platformy Azure
Platforma Azure udostępnia niezawodny dostępność w oparciu o szeroką gamę nadmiarowość osiągnięte dzięki technologii wirtualizacji. Platforma Azure oferuje wiele poziomów nadmiarowość, aby zapewnić maksymalne dostępność danych klientów.

## <a name="temporary-outages-and-natural-disaster"></a>Tymczasowych przestojów i klęski żywiołowej
Zespół infrastruktury chmury firmy Microsoft i operacje projektuje, tworzy działa i zabezpiecza infrastruktury chmury. Tego zespołu gwarantuje, że infrastruktura platformy Azure jest zapewnianie wysokiej dostępności i niezawodności, wysokiej wydajności, skalowalności inteligentne i chmury bezpieczną, prywatne i zaufanych.

Zasilacze i przeważająca banków baterii zapewnić energii elektrycznej ciągłej w przypadku krótkoterminowej zakłóceń zasilania.

Awaryjnych zapewnienia zasilacz dłuższych przestojów i planowanej konserwacji. Centrum danych jest świadczona z rezerw paliwa w siedzibie organizacji, jeśli występuje klęski żywiołowej.

Szybkie i niezawodne fiber sieci światłowodowe połączenie centrów danych z innych główne centra i użytkowników Internetu. Węzły obliczeniowe bliżej obciążeń hosta dla użytkowników końcowych w celu zmniejszenia opóźnień, zapewniają nadmiarowość geograficzna i zwiększyć ogólne usługi odporności. Zespół inżynierów przez całą dobę działa, aby upewnić się, że usługi są stale dostępne dla klientów.

Microsoft zapewnia wysoką dostępność za pośrednictwem zaawansowanego monitorowania i odpowiedzi na zdarzenia, obsługi i wykonaj kopię zapasową możliwości trybu failover. Geograficznie rozproszonych centrów operacji firmy Microsoft działają 24/7/365. Sieć platformy Azure jest jednym z największych na świecie. Sieć dystrybucji światłowodowe i zawartości włókien łączy centrów danych i węzłach Edge, aby zapewnić wysoką wydajność i niezawodność.

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii
Azure przechowuje dane klienta trwałe w dwóch lokalizacjach klientowi o możliwości, aby wybrać lokalizację kopii zapasowej lokacji. W obu lokalizacjach Azure obsługuje stale wiele replik dobrej kondycji (3) z danych klienta.

## <a name="database-availability"></a>Dostępność bazy danych
Azure zapewnia, że baza danych jest dostępna za pośrednictwem bramy internetowej o dostępności bazy danych przez Internet. Monitorowanie ocenia kondycję i stan aktywny baz danych w odstępach czasu 5 minut.

## <a name="storage-availability"></a>Dostępność magazynu
Azure oferuje magazynu za pośrednictwem usługi wysoce skalowalna i niezawodna magazynu, który zapewnia łączność punktów końcowych, dzięki któremu udostępniane bezpośrednio przez aplikację odbierającą. Za pomocą usługi magazynu przychodzących żądań magazynu będą przetwarzane wydajnie z integralnością transakcyjnych.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalne i zabezpieczenia fizyczne](azure-physical-security.md)
- [Składniki systemu Azure informacji i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieci Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń bazy danych SQL Azure firmy Microsoft](azure-infrastructure-sql.md)
- [Operacje Azure środowiska produkcyjnego i zarządzania](azure-infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](azure-infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klienta na platformie Azure](azure-protection-of-customer-data.md)
