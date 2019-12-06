---
title: Rozwiązywanie problemów z usługą Azure Change Tracking
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów Change Tracking
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 482b263b7c8427276d4fdb8681372c2be1ae69ee
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849398"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Rozwiązywanie problemów z rozwiązaniami Change Tracking i Inventory

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scenariusz: rekordy Change Tracking nie są wyświetlane dla maszyn z systemem Windows

#### <a name="issue"></a>Problem

Nie widzisz żadnych spisów ani Change Tracking wyników dla maszyn z systemem Windows, które zostały dołączone do Change Tracking.

#### <a name="cause"></a>Przyczyna

Przyczyną tego błędu mogą być następujące przyczyny:

1. **Microsoft Monitoring Agent** nie jest uruchomiona
2. Trwa blokowanie komunikacji z powrotem do konta usługi Automation.
3. Nie pobrano pakietów administracyjnych dla Change Tracking.
4. Dołączana maszyna wirtualna może pochodzić ze sklonowanego komputera, który nie został Sysprep z zainstalowanym Microsoft Monitoring Agent.

#### <a name="resolution"></a>Rozdzielczość

1. Sprawdź, czy na komputerze jest uruchomiony **Microsoft Monitoring Agent** (HealthService. exe).
1. Sprawdź **Podgląd zdarzeń** na maszynie i Wyszukaj wszystkie zdarzenia, które mają słowo `changetracking`.
1. Odwiedź stronę [Planowanie sieci](../automation-hybrid-runbook-worker.md#network-planning) , aby dowiedzieć się, które adresy i porty muszą być dozwolone, aby Change Tracking działały.
1. Sprawdź, czy następujące Change Tracking i pakiety administracyjne spisu istnieją lokalnie:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. W przypadku używania sklonowanego obrazu program Sysprep najpierw zainstaluje go, a następnie instaluje agenta Microsoft Monitoring Agent po tym fakcie.

Jeśli te rozwiązania nie rozwiążą problemu i skontaktujesz się z pomocą techniczną, możesz uruchomić następujące polecenia, aby zebrać dane diagnostyczne agenta

Na maszynie agenta przejdź do `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` i uruchom następujące polecenia:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Domyślnie śledzenie błędów jest włączone, jeśli chcesz włączyć pełne komunikaty o błędach podobne do powyższego przykładu, użyj `VER` parametru. Aby uzyskać informacje na temat śladów informacji, użyj `INF` podczas wywoływania `StartTracing.cmd`.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów w zakresie platformy Azure na [forach dotyczących platformy Azure](https://azure.microsoft.com/support/forums/)
* Połącz się z kontem [@AzureSupport](https://twitter.com/azuresupport) — oficjalnym kontem platformy Microsoft Azure utworzonym w celu podniesienia jakości obsługi klientów przez połączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.
* Jeśli potrzebujesz więcej pomocy, możesz obsłużyć zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
