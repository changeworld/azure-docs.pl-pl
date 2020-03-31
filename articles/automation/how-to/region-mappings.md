---
title: Mapowania obszarów roboczych automatyzacji i usługi Log Analytics platformy Azure
description: W tym artykule opisano mapowania dozwolone między kontem automatyzacji a obszarem roboczym usługi Log Analytics w celu obsługi rozwiązania
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849517"
---
# <a name="workspace-mappings"></a>Mapowania obszaru roboczego

Podczas włączania rozwiązań, takich jak zarządzanie aktualizacjami, śledzenie zmian i zapasów lub maszyny wirtualne start/stop podczas rozwiązania poza godzinami pracy, tylko niektóre regiony są obsługiwane do łączenia obszaru roboczego usługi Log Analytics i konta automatyzacji. To mapowanie dotyczy tylko konta automatyzacji i obszaru roboczego usługi Log Analytics. Zasoby raportowania do konta automatyzacji lub obszaru roboczego usługi Log Analytics może rezydować w innych regionach.

## <a name="supported-mappings"></a>Obsługiwane mapowania

W poniższej tabeli przedstawiono obsługiwane mapowania:

|**Region obszaru roboczego analizy dzienników**|**Region automatyzacji platformy Azure**|
|---|---|
|**USA**||
|Okręg wyborczy EastUS<sup>1</sup>|Okręg wyborczy EastUS2|
|Okręg wyborczy WestUS2|Okręg wyborczy WestUS2|
|Okręg wyborczy WestCentralUS<sup>2</sup>|Okręg wyborczy WestCentralUS<sup>2</sup>|
|**Kanada**||
|KanadaCentral|KanadaCentral|
|**Azja i Pacyfik**||
|AustraliaSoutheast|AustraliaSoutheast|
|Południowo-wschodnia Azja|Południowo-wschodnia Azja|
|CentralIndia ( CentralIndia )|CentralIndia ( CentralIndia )|
|JaponiaJast|JaponiaJast|
|**Europa**||
|UkSouth (polski)|UkSouth (polski)|
|WestEurope|WestEurope|
|**US Gov**||
|UsGovVirginia|UsGovVirginia|

<sup>1</sup> Mapowanie EastUS dla obszarów roboczych usługi Log Analytics do kont automatyzacji nie jest dokładne mapowanie regionu do regionu, ale jest poprawne mapowanie.

<sup>2</sup> Ze względu na ograniczenia wydajności region nie jest dostępny podczas tworzenia nowych zasobów. Obejmuje to konta automatyzacji i obszary robocze usługi Log Analytics. Jednak wcześniej powiązanych zasobów w regionie powinny nadal działać.

## <a name="unlink-workspace"></a>Unlink workspace (Odłączanie obszaru roboczego)

Jeśli zdecydujesz, że nie chcesz już integrować konta automatyzacji z obszarem roboczym usługi Log Analytics, możesz odłączyć swoje konto bezpośrednio z witryny Azure portal. Przed kontynuowaniem należy najpierw usunąć zarządzanie aktualizacjami, śledzenie zmian i zapasy lub maszyny wirtualne start/stop podczas rozwiązań poza godzinami pracy, jeśli ich używasz. Jeśli ich nie usuniesz, proces ten nie będzie mógł się kontynuować. Zapoznaj się z artykułem dla określonego rozwiązania, które zostały zaimportowane, aby zrozumieć kroki wymagane do jego usunięcia.

Po usunięciu tych rozwiązań można wykonać następujące kroki, aby odłączyć konto automatyzacji.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania do monitorowania SQL platformy Azure, mogły utworzyć zasoby automatyzacji i mogą również wymagać usunięcia przed odłączeniem obszaru roboczego.

1. W witrynie Azure portal otwórz konto automatyzacji, a na stronie konta automatyzacji wybierz **pozycję Połączony obszar roboczy** w sekcji **Powiązane zasoby** po lewej stronie.

2. Na stronie Odłącz obszar roboczy kliknij pozycję **Odłącz obszar roboczy**. Otrzymasz monit z weryfikacją, którą chcesz kontynuować.

3. Podczas gdy usługa Azure Automation próbuje odłączyć konto obszaru roboczego usługi Log Analytics, można śledzić postęp w obszarze **Powiadomienia** z menu.

Jeśli użyto rozwiązania do zarządzania aktualizacjami, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Harmonogramy aktualizacji — każda z nich będzie miała nazwy zgodne z utworzonymi wdrożeniami aktualizacji)

* Hybrydowe grupy robocze utworzone dla rozwiązania — `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`każda z nich będzie nazywana podobnie do ).

Jeśli używano start/stop maszyn wirtualnych podczas poza godzinami pracy rozwiązania, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywania harmonogramów ujeżdnych maszyn wirtualnych
* Uruchamianie i zatrzymywania obrażeń ekwształtów maszyn wirtualnych
* Zmienne

Alternatywnie można również odłączyć obszar roboczy od konta automatyzacji z obszaru roboczego usługi Log Analytics. W obszarze roboczym wybierz pozycję **Konto automatyzacji** w obszarze **Zasoby pokrewne**. Na stronie Konto automatyzacji wybierz pozycję **Odłącz konto**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak włączyć następujące rozwiązania:

Aktualizowanie zarządzania i śledzenia zmian oraz zasobów reklamowych:

* Z [maszyny wirtualnej](../automation-onboard-solutions-from-vm.md)
* Z [konta Automation](../automation-onboard-solutions-from-automation-account.md)
* Podczas [przeglądania wielu maszyn](../automation-onboard-solutions-from-browse.md)
* Z [podręcznika runbook](../automation-onboard-solutions.md)

Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy

* [Wdrażanie maszyn wirtualnych start/stop poza godzinami pracy](../automation-solution-vm-management.md)
