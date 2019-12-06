---
title: Mapowania obszaru roboczego Azure Automation i Log Analytics
description: W tym artykule opisano mapowania dozwolone między kontem usługi Automation i obszarem roboczym Log Analytics do obsługi rozwiązania
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 965d6b206bb64e90fe59798ce0c37ccf029117f5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849517"
---
# <a name="workspace-mappings"></a>Mapowania obszaru roboczego

Podczas włączania rozwiązań takich jak Update Management, Change Tracking i spisu lub rozwiązania Start/Stop VMs during off-hours do łączenia obszaru roboczego Log Analytics i konta usługi Automation są obsługiwane tylko niektóre regiony. To mapowanie ma zastosowanie tylko do konta usługi Automation i obszaru roboczego Log Analytics. Zasoby raportowane do konta usługi Automation lub obszaru roboczego Log Analytics mogą znajdować się w innych regionach.

## <a name="supported-mappings"></a>Obsługiwane mapowania

W poniższej tabeli przedstawiono obsługiwane mapowania:

|**Log Analytics region obszaru roboczego**|**Region Azure Automation**|
|---|---|
|**USA**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Kanada**||
|CanadaCentral|CanadaCentral|
|**Azja i Pacyfik**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Europa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> mapowanie Wschodnie dla obszarów roboczych log Analytics na konta usługi Automation nie jest dokładnym mapowaniem regionu do regionu, ale jest poprawnym mapowaniem.

<sup>2</sup> ze względu na ograniczenia pojemności region nie jest dostępny podczas tworzenia nowych zasobów. Obejmuje to konta usługi Automation i Log Analytics obszary robocze. Jednak istniejące połączone zasoby w regionie powinny być nadal wykonywane.

## <a name="unlink-workspace"></a>Odłącz obszar roboczy

Jeśli zdecydujesz, że nie chcesz już integrować konta usługi Automation z obszarem roboczym Log Analytics, możesz odłączyć swoje konto bezpośrednio od Azure Portal. Przed kontynuowaniem należy najpierw usunąć Update Management, Change Tracking i spis lub Start/Stop VMs during off-hours rozwiązania, jeśli są używane. Jeśli ich nie usuniesz, ten proces nie zostanie zablokowany. Zapoznaj się z artykułem dotyczącym określonego rozwiązania, które zostało zaimportowane, aby poznać kroki wymagane do jego usunięcia.

Po usunięciu tych rozwiązań możesz wykonać następujące czynności, aby odłączyć konto usługi Automation.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania Azure SQL monitoring, mogły utworzyć zasoby automatyzacji i mogą być również musiały zostać usunięte przed odłączeniem obszaru roboczego.

1. W Azure Portal Otwórz konto usługi Automation, a na stronie konto usługi Automation wybierz opcję **połączony obszar roboczy** w sekcji **zasoby powiązane** z pozostałą częścią.

2. Na stronie Odłącz obszar roboczy kliknij pozycję **Odłącz obszar roboczy**. Zostanie wyświetlony monit o zweryfikowanie, czy chcesz kontynuować.

3. Mimo że Azure Automation próbuje odłączyć konto do obszaru roboczego Log Analytics, możesz śledzić postęp w obszarze **powiadomienia** z menu.

Jeśli użyto rozwiązania Update Management, opcjonalnie możesz chcieć usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Harmonogramy aktualizacji — każda z nich będzie mieć nazwy zgodne z utworzonymi wdrożeniami aktualizacji)

* Grupy hybrydowych procesów roboczych utworzone dla rozwiązania — każdy z nich zostanie nazwany podobnie do `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`).

Jeśli użyto rozwiązania Start/Stop VMs during off-hours, opcjonalnie możesz chcieć usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywanie harmonogramów elementów Runbook maszyny wirtualnej
* Uruchamianie i zatrzymywanie elementów Runbook maszyny wirtualnej
* Zmienne

Możesz również odłączyć obszar roboczy od konta usługi Automation z poziomu obszaru roboczego Log Analytics. W obszarze roboczym wybierz pozycję **konto usługi Automation** w obszarze **powiązane zasoby**. Na stronie konto usługi Automation wybierz opcję **Odłącz konto**.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak dołączyć następujące rozwiązania:

Update Management i Change Tracking i spisu:

* Z [maszyny wirtualnej](../automation-onboard-solutions-from-vm.md)
* Na [koncie usługi Automation](../automation-onboard-solutions-from-automation-account.md)
* Podczas [przeglądania wielu maszyn](../automation-onboard-solutions-from-browse.md)
* Z [elementu Runbook](../automation-onboard-solutions.md)

Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy

* [Wdróż Start/Stop VMs during off-hours](../automation-solution-vm-management.md)
