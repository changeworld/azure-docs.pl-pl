---
title: Monitorowanie infrastruktury platformy Azure
description: W tym artykule omówiono monitorowania sieci Azure środowiska produkcyjnego.
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
ms.openlocfilehash: 17e7183ff56725462dc43cba21db418a86d86b51
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102546"
---
# <a name="monitoring-of-azure-infrastructure"></a>Monitorowanie infrastruktury platformy Azure   

## <a name="configuration-and-change-management"></a>Konfiguracja i zarządzanie zmianami
Microsoft Azure przegląda i aktualizuje ustawienia konfiguracji i linii bazowej konfiguracji sprzętu, oprogramowania i urządzeń sieciowych co roku. Zmiany są rozwinięte, przetestowane i zatwierdzone przed wprowadzeniem w środowisku produkcyjnym ze środowiska programowania i/lub testu.

Konfiguracje podstawowe wymagane dla usług opartych na platformie Azure jest rozpatrywana przez zespół Azure zabezpieczeń i zgodności i zespoły usługi. Zespół usługi przeglądu jest częścią testowanie przed wdrożeniem usługi ich produkcji.

## <a name="vulnerability-management"></a>Zarządzanie luki w zabezpieczeniach
Zarządzanie aktualizacjami zabezpieczeń pomaga chronić systemy ze znanych luk w zabezpieczeniach. Platforma Azure zintegrowane wdrożenia systemy do zarządzania dystrybucji i instalowanie aktualizacji zabezpieczeń oprogramowania firmy Microsoft. Azure może także korzystać z zasobów Centrum odpowiedź zabezpieczeń firmy Microsoft (MSRC). MSRC identyfikuje, monitoruje odpowiada i jest rozpoznawany jako przypadki naruszenia zabezpieczeń i w chmurze luk w zabezpieczeniach przez całą dobę, każdego dnia roku.

## <a name="vulnerability-scanning"></a>Luki w zabezpieczeniach skanowania
Skanowanie luki w zabezpieczeniach odbywa się na systemy operacyjne serwera baz danych i urządzeń sieciowych. Luki w zabezpieczeniach skanowania są wykonywane co kwartał, co najmniej. Umowy Microsoft Azure z niezależnych ekspertów do testowania penetracji granic Microsoft Azure. Ćwiczenia zespołu czerwony również regularnie są wykonywane i wyników użyć w celu dokonania poprawek zabezpieczeń.

## <a name="protective-monitoring"></a>Monitorowanie ochrony
Microsoft Azure Security zostały zdefiniowane wymagania aktywnego monitorowania. Zespoły obsługi skonfiguruj active narzędzi monitorowania zgodnie z tych wymagań. Narzędzia do aktywnego monitorowania obejmują agenta monitorowania (MA) i System Center Operations Manager. Te narzędzia są skonfigurowane do dostarczania czasu alerty do personelu zabezpieczeń firmy Microsoft Azure w sytuacjach, które wymagają natychmiastowego działania.

## <a name="incident-management"></a>Zarządzanie zdarzeniami
Microsoft implementuje procesu zarządzania zdarzeniami zabezpieczeń w celu ułatwienia skoordynowany sposób reagowania na zdarzenia, powinien wystąpić jeden.

Jeśli Microsoft staje się znane nieautoryzowanego dostępu do danych klienta przechowywanych na swoich urządzeniach lub w jego urządzenia lub nieautoryzowany dostęp do takich urządzeń lub urządzeń powodujące utratę, ujawnienia lub zmiany danych klienta, Microsoft przyjmuje następujące akcje:

- Szybko poinformowała klienta zdarzenia zabezpieczeń
- Szybko bada zdarzenia zabezpieczeń i zapewnia szczegółowe informacje dotyczące zdarzenia zabezpieczeń klienta
- Trwa uzasadnione i Szybkie kroki w celu ograniczenia efektów i zminimalizować szkody wynikające z naruszeniem bezpieczeństwa.

Określone role i obowiązki przydzielone została ustanowiona framework zarządzania zdarzeniami. Zespół zarządzania zdarzeniami zabezpieczeń systemu Windows Azure (WASIM) jest odpowiedzialny za zarządzanie zdarzeniami zabezpieczeń, w tym eskalacji i zapewnienie zaangażowania zespoły specjalistyczne, gdy jest to konieczne. Personel Azure jest odpowiedzialny za nadzór nad badanie i rozwiązywanie zdarzeń zabezpieczeń i prywatności.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat firmy Microsoft jest zapewnienie infrastruktury platformy Azure, zobacz:

- [Urządzenia platformy Azure, lokalne i zabezpieczenia fizyczne](azure-physical-security.md)
- [Dostępność infrastruktury platformy Azure](azure-infrastructure-availability.md)
- [Składniki systemu Azure informacji i granice](azure-infrastructure-components.md)
- [Architektura sieci platformy Azure](azure-infrastructure-network.md)
- [Sieci Azure środowiska produkcyjnego](azure-production-network.md)
- [Funkcje zabezpieczeń bazy danych SQL Azure firmy Microsoft](azure-infrastructure-sql.md)
- [Operacje Azure środowiska produkcyjnego i zarządzania](azure-infrastructure-operations.md)
- [Integralność infrastruktury platformy Azure](azure-infrastructure-integrity.md)
- [Ochrona danych klienta na platformie Azure](azure-protection-of-customer-data.md)
