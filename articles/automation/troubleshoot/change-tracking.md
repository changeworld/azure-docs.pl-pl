---
title: Rozwiązywanie problemów z Azure Change Tracking
description: Ten artykuł zawiera informacje na temat rozwiązywania problemów, śledzenia zmian
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 10/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 686a2c0ef0766176658d42d5ca53ea1ce6d3241c
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722942"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Rozwiązywanie problemów z Change Tracking and Inventory

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scenariusz: Rekordy śledzenia zmian nie są wyświetlane w witrynie Azure portal

#### <a name="issue"></a>Problem

Nie widzisz żadnych wyników spisu lub śledzenia zmian dla maszyn, które są dołączone do śledzenia zmian.

#### <a name="cause"></a>Przyczyna

Ten błąd może być spowodowany przez następujących przyczyn:

1. **Microsoft Monitoring Agent** nie jest uruchomiony
2. Konto usługi Automation komunikacja jest blokowana.
3. Pakiety administracyjne do śledzenia zmian nie są pobierane.
4. Maszyna wirtualna jest dołączona może pochodzić z klonowanym komputerze, który nie był Sysprep przy użyciu programu Microsoft Monitoring Agent zainstalowany.

#### <a name="resolution"></a>Rozwiązanie

1. Sprawdź **Microsoft Monitoring Agent** (HealthService.exe) działa na maszynie.
2. Odwiedź stronę, [Planowanie sieci](../automation-hybrid-runbook-worker.md#network-planning) Aby dowiedzieć się więcej o tym, jakie adresy i porty muszą być dozwolone dla śledzenia zmian do pracy.
3. Sprawdź, czy istnieją następujące pakiety administracyjne śledzenia zmian i spisu lokalnie:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
4. Jeśli przy użyciu klonowanego obrazu, program sysprep obrazu najpierw, a następnie zainstaluj agenta Microsoft Monitoring Agent w późniejszym czasie.

Jeśli te rozwiązania nie rozwiążą problemu, skontaktuj się z działem pomocy technicznej można uruchomić następujące polecenia, aby zbieranie diagnostyki agenta

Na maszynie agenta, przejdź do `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` i uruchom następujące polecenia:

```cmd
set stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Błąd domyślny śledzenie jest włączone, jeśli chcesz umożliwić pełne komunikaty, podobnie jak w poprzednim przykładzie użyj `VER` parametru. Ślady informacje, można użyć `INF` podczas wywoływania `StartTracing.cmd`.

## <a name="next-steps"></a>Kolejne kroki

Jeśli nie był widoczny problemu lub są w stanie rozwiązać problemu, odwiedź jedną z następujących kanałów obsługi więcej:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, mogą zgłaszać zdarzenia pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/) i wybierz **uzyskiwanie pomocy technicznej**.