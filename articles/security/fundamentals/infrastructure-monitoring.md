---
title: Monitorowanie infrastruktury platformy Azure
description: W tym artykule omówiono monitorowanie sieci produkcyjnej platformy Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: af9c157b4644156edc6dcdb1b53c141263576500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727151"
---
# <a name="azure-infrastructure-monitoring"></a>Monitorowanie infrastruktury platformy Azure   

## <a name="configuration-and-change-management"></a>Zarządzanie konfiguracją i zmianami
Platforma Azure co roku przegląda i aktualizuje ustawienia konfiguracji i konfiguracje linii bazowej sprzętu, oprogramowania i urządzeń sieciowych. Zmiany są opracowywane, testowane i zatwierdzane przed wejściem do środowiska produkcyjnego ze środowiska deweloperskiego i/lub testowego.

Konfiguracje linii bazowej, które są wymagane dla usług opartych na platformie Azure są przeglądane przez zespół zabezpieczeń i zgodności platformy Azure oraz przez zespoły usług. Przegląd zespołu usługi jest częścią testowania, który występuje przed wdrożeniem ich usługi produkcyjnej.

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach
Zarządzanie aktualizacjami zabezpieczeń pomaga chronić systemy przed znanymi lukami w zabezpieczeniach. Platforma Azure używa zintegrowanych systemów wdrażania do zarządzania dystrybucją i instalacją aktualizacji zabezpieczeń oprogramowania firmy Microsoft. Platforma Azure może również korzystać z zasobów Centrum odpowiedzi zabezpieczeń firmy Microsoft (MSRC). MSRC identyfikuje, monitoruje, reaguje i usuwa zdarzenia związane z bezpieczeństwem i luki w zabezpieczeniach w chmurze przez całą dobę, każdego dnia roku.

## <a name="vulnerability-scanning"></a>Skanowanie luk w zabezpieczeniach
Skanowanie luk w zabezpieczeniach jest wykonywane w systemach operacyjnych serwerów, bazach danych i urządzeniach sieciowych. Skanowanie luk w zabezpieczeniach jest wykonywane co najmniej co kwartał. Platformy Azure zawiera umowy z niezależnymi rzeczoznawcami w celu przeprowadzenia testów penetracyjnych granicy platformy Azure. Ćwiczenia red-team są również rutynowo wykonywane, a wyniki są wykorzystywane do poprawy bezpieczeństwa.

## <a name="protective-monitoring"></a>Monitoring ochronny
Zabezpieczenia platformy Azure zdefiniowały wymagania dotyczące aktywnego monitorowania. Zespoły serwisowe konfigurują aktywne narzędzia monitorowania zgodnie z tymi wymaganiami. Aktywne narzędzia monitorowania obejmują program Microsoft Monitoring Agent (MMA) i Program Operacyjny Centrum systemu. Te narzędzia są skonfigurowane w taki sposób, aby zapewniały alerty czasowe dla pracowników zabezpieczeń platformy Azure w sytuacjach wymagających natychmiastowych działań.

## <a name="incident-management"></a>Zarządzanie zdarzeniami
Firma Microsoft implementuje proces zarządzania zdarzeniami zabezpieczeń w celu ułatwienia skoordynowanej reakcji na incydenty, jeśli wystąpią.

Jeśli firma Microsoft dowie się o nieautoryzowanym dostępie do danych klienta przechowywanych na jej sprzęcie lub w jego obiektach lub dowie się o nieautoryzowanym dostępie do takiego sprzętu lub urządzeń, co powoduje utratę, ujawnienie lub zmianę danych klienta, Firma Microsoft wykonuje następujące czynności:

- Niezwłocznie powiadamia klienta o zdarzeniu związanym z zabezpieczeniami.
- Niezwłocznie bada zdarzenie zabezpieczeń i dostarcza klientom szczegółowych informacji o incydencie zabezpieczeń.
- Podejmuje rozsądne i szybkie kroki w celu złagodzenia skutków i zminimalizowania szkód wynikających z incydentu zabezpieczeń.

Ustanowiono strukturę zarządzania zdarzeniami, która definiuje role i przydziela obowiązki. Zespół zarządzania zdarzeniami zabezpieczeń platformy Azure jest odpowiedzialny za zarządzanie zdarzeniami związanymi z zabezpieczeniami, w tym eskalacją, i zapewnienie zaangażowania wyspecjalizowanych zespołów w razie potrzeby. Menedżerowie operacji platformy Azure są odpowiedzialni za nadzorowanie dochodzeń i rozwiązywania incydentów związanych z bezpieczeństwem i prywatnością.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o tym, co firma Microsoft robi, aby zabezpieczyć infrastrukturę platformy Azure, zobacz:

- [Obiekty platformy Azure, pomieszczenia i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacyjnego platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Sieć produkcyjna platformy Azure](production-network.md)
- [Funkcje zabezpieczeń usługi Azure SQL Database](infrastructure-sql.md)
- [Operacje produkcyjne i zarządzanie platformą Azure](infrastructure-operations.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](protection-customer-data.md)
