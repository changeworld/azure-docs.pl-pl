---
title: Monitorowanie infrastruktury platformy Azure
description: W tym artykule omówiono monitorowania sieci platformy Azure środowiska produkcyjnego.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 88bc76116392140c533f67402642c68d714227c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591642"
---
# <a name="azure-infrastructure-monitoring"></a>Monitorowanie infrastruktury platformy Azure   

## <a name="configuration-and-change-management"></a>Konfiguracja i zarządzanie zmianami
Platforma Azure przeglądów i aktualizuje ustawienia konfiguracji i linii bazowej konfiguracji sprzętu, oprogramowania i urządzeń sieciowych rocznie. Zmiany są opracowane, przetestowane i zatwierdzone przed rozpoczęciem w środowisku produkcyjnym przy użyciu środowiska tworzenia i/lub test.

Konfiguracje odniesienia, które są wymagane dla usług opartych na platformie Azure są przeglądane przez zespół zabezpieczeń i zgodności platformy Azure i zespoły usług. Przegląd zespołu usług jest częścią testowania, występujący przed wdrożeniem jej usługi do produkcji.

## <a name="vulnerability-management"></a>Zarządzanie lukami w zabezpieczeniach
Zarządzanie aktualizacjami zabezpieczeń ułatwia ochronę systemów przed znanymi lukami w zabezpieczeniach. Usługa Azure korzysta zintegrowane systemy wdrażania do zarządzania dystrybucją i instalacją aktualizacji zabezpieczeń oprogramowania firmy Microsoft. Platforma Azure to także możliwość korzystania z zasobów Microsoft Security Response Center (MSRC). Centrum MSRC identyfikuje, monitoruje, odpowiada i jest rozpoznawana jako zdarzenia związane z bezpieczeństwem i w chmurze luk w zabezpieczeniach, przez całą dobę, każdego dnia roku.

## <a name="vulnerability-scanning"></a>Skanowanie luk w zabezpieczeniach
Skanowanie luk w zabezpieczeniach jest wykonywana na systemy operacyjne serwera, bazy danych i urządzeń sieciowych. Skanowanie luk w zabezpieczeniach są wykonywane co kwartał, co najmniej. Usługa Azure umów z niezależnych rzeczoznawców przeprowadzenie testów penetracyjnych granicy platformy Azure. Czerwony zespołu testującego są również rutynowo wykonywane, a wyniki są używane w celu ulepszenia zabezpieczeń.

## <a name="protective-monitoring"></a>Monitorowanie ochrony
Zdefiniowane wymagania dotyczące monitorowania aktywnych zabezpieczenia platformy Azure. Zespoły usług Konfigurowanie narzędzi do monitorowania aktywnych zgodnie z tymi wymaganiami. Narzędzia do aktywnego monitorowania zawierają Microsoft Monitoring Agent (MMA) i System Center Operations Manager. Te narzędzia są skonfigurowane do zapewnienia alerty w czasie do personelu zabezpieczeń platformy Azure w sytuacjach wymagających natychmiastowego działania.

## <a name="incident-management"></a>Zarządzanie zdarzeniami
Firma Microsoft implementuje procesu zarządzania zdarzeniami zabezpieczeń w celu ułatwienia skoordynowany sposób reagowania na zdarzenia, powinien wystąpić jeden.

Czy Microsoft uzyska informacje o nieautoryzowanego dostępu do danych klienta, który jest przechowywany na swoich urządzeniach lub w jego obiektach, staje się znane nieautoryzowany dostęp do takich urządzeń lub urządzeń, powodujące utraty, ujawnienia lub modyfikacją danych klienta Firma Microsoft wykonuje następujące czynności:

- Niezwłocznie powiadomi klienta zdarzeń naruszenia zabezpieczeń.
- Szybko bada zdarzenia zabezpieczeń oraz zapewnia klientom szczegółowe informacje na temat zdarzeń naruszenia zabezpieczeń.
- Trwa uzasadnione i Szybkie kroki, aby łagodzenia skutków i zminimalizować potencjalne szkody wynikające z zdarzeń naruszenia zabezpieczeń.

Platforma zarządzania zdarzeniami została określona w taki sposób, który definiuje role i obowiązki przydziela. Zespół zarządzania zdarzeniami zabezpieczeń platformy Azure jest odpowiedzialny za zarządzanie zdarzeniami naruszenia zabezpieczeń, w tym eskalacji i zapewnienie zaangażowaniu zespołów specjalistyczne, gdy jest to konieczne. Menedżerowie ds. operacji platformy Azure są odpowiedzialni za nadzorowanie badania i rozwiązywania problemów, zdarzeń zabezpieczeń i prywatności.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalnie i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Usługi Azure information składników systemu i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieć platformy Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń w usłudze Azure SQL Database](azure-infrastructure-sql.md)
- [Operacje platformy Azure środowiska produkcyjnego i zarządzanie](azure-infrastructure-operations.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klientów platformy Azure](azure-protection-of-customer-data.md)
