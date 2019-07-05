---
title: Mapowania obszaru roboczego usługi Azure Automation i Log Analytics
description: W tym artykule opisano mapowania dozwolone między kontem usługi Automation i obszar roboczy usługi Log Analytics, pozwalające obsługiwać rozwiązanie
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9faa261f4b6293d778c5da685f630a21fd88f600
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478580"
---
# <a name="workspace-mappings"></a>Mapowania obszaru roboczego

Podczas włączania rozwiązań, takich jak zarządzanie aktualizacjami, śledzenie zmian i spisu lub uruchamianie/zatrzymywanie maszyn wirtualnych podczas szczytu rozwiązania, tylko w określonych regionach są obsługiwane w przypadku łączenia z obszarem roboczym usługi Log Analytics i konto usługi Automation. To mapowanie dotyczy tylko konto usługi Automation i obszaru roboczego usługi Log Analytics. Zasoby raporty do obszaru roboczego konto usługi Automation i Log Analytics może znajdować się w innych regionach.

## <a name="supported-mappings"></a>Obsługiwanych mapowań

W poniższej tabeli przedstawiono obsługiwane mapowania:

|**Regionu obszaru roboczego usługi log Analytics**|**Region usługi Azure Automation**|
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
|**Administracja USA**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> EastUS mapowania obszarów roboczych usługi Log Analytics do kont usługi Automation nie jest dokładnie mapowania regionu, ale jest poprawny mapowania.

<sup>2</sup> ze względu na ograniczenia wydajności region nie jest dostępna podczas tworzenia nowych zasobów. Obejmuje to obszary robocze kont usługi Automation i Log Analytics. Jednak istniejących połączonych zasobów w regionie powinny nadal działać.

## <a name="unlink-workspace"></a>Odłączanie obszaru roboczego

Jeśli zdecydujesz, że już nie chcesz zintegrować konta usługi Automation z obszarem roboczym usługi Log Analytics, możesz odłączyć konta bezpośrednio w witrynie Azure portal. Przed kontynuowaniem należy najpierw usunąć zarządzania aktualizacjami, śledzenie zmian i spisu lub uruchamianie/zatrzymywanie maszyn wirtualnych podczas szczytu rozwiązania, jeśli są używane. Jeśli nie usuniesz je, ten proces nie będą mogli kontynuować. Zapoznaj się z artykułem dla danego rozwiązania, które zostały zaimportowane, aby zrozumieć kroki wymagane do usunięcia go.

Po usunięciu tych rozwiązań, można wykonać poniższe kroki, aby odłączyć konto usługi Automation.

> [!NOTE]
> Niektóre rozwiązania, w tym wcześniejsze wersje rozwiązania do monitorowania usługi Azure SQL zostały utworzone zasoby usługi automation i może również muszą zostać usunięte przed odłączanie obszaru roboczego.

1. W witrynie Azure Portal otwórz konto usługi Automation, a na automatyzację konta wybierz stronę **połączony obszar roboczy** sekcji **powiązane zasoby** po lewej stronie.

2. Na stronie odłączania obszaru roboczego kliknij **odłączanie obszaru roboczego**. Zostanie wyświetlony monit sprawdzający, czy chcesz kontynuować.

3. Gdy usługa Azure Automation usiłuje odłączyć konto obszaru roboczego usługi Log Analytics, możesz śledzić postęp w obszarze **powiadomienia** z menu.

Jeśli używane jest rozwiązanie do zarządzania aktualizacjami, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Zaplanowanie aktualizacji — będzie mieć nazwy zgodne utworzonych wdrożeń aktualizacji)

* Grupy hybrydowych procesów roboczych utworzone dla rozwiązania — każda będzie miała podobnie do maszyna1.contoso.com_9ceb8108 - 26 c 9-4051-b6b3-227600d715c8).

Jeśli uruchamianie/zatrzymywanie maszyn wirtualnych jest używana podczas szczytu rozwiązania, opcjonalnie można usunąć następujące elementy, które nie są już potrzebne po usunięciu rozwiązania.

* Uruchamianie i zatrzymywanie maszyn wirtualnych elementu runbook harmonogramy
* Uruchamianie i zatrzymywanie elementów runbook maszyny Wirtualnej
* Zmienne

Alternatywnie można również odłączyć obszaru roboczego z konta usługi Automation z obszaru roboczego usługi Log Analytics. W obszarze roboczym wybierz **konta usługi Automation** w obszarze **powiązane zasoby**. Na stronie konta automatyzacji wybierz **odłączyć konto**.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak dołączyć następujących rozwiązań:

Aktualizowanie zarządzania i śledzenia zmian i spisu:

* Z [maszyny wirtualnej](../automation-onboard-solutions-from-vm.md)
* Z usługi [konta usługi Automation](../automation-onboard-solutions-from-automation-account.md)
* Gdy [przeglądania wielu maszyn](../automation-onboard-solutions-from-browse.md)
* Z [elementu runbook](../automation-onboard-solutions.md)

Uruchamianie lub zatrzymywanie maszyn wirtualnych po godzinach pracy

* [Wdrażanie uruchamianie/zatrzymywanie maszyn wirtualnych poza godzinami szczytu](../automation-solution-vm-management.md)