---
title: Azure Security Center często zadawane pytania — pytania dotyczące istniejących modelami
description: Te często zadawane pytania odpowiadają za korzystanie z Microsoft Monitoring Agent i rozważanie Azure Security Center, produktu, który pomaga zapobiegać zagrożeniom, wykrywać je i reagować na nie.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 528ff47be2b18cb7d9b938e988383a9e81be67fb
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599511"
---
# Często zadawane pytania dotyczące klientów korzystających już z dzienników Azure Monitor<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Usługa Security Center, powoduje zastąpienia istniejące połączenia między maszynami wirtualnymi i obszary robocze?

Jeśli maszyna wirtualna ma już zainstalowany program Microsoft Monitoring Agent jako rozszerzenie platformy Azure, usługa Security Center nie zastępuje istniejące połączenie z obszaru roboczego. Zamiast tego usługa Security Center korzysta z istniejącego obszaru roboczego. Maszyna wirtualna będzie chroniona pod warunkiem, że rozwiązanie "Security" lub "SecurityCenterFree" zostało zainstalowane w obszarze roboczym, w którym jest raportowane. 

Rozwiązanie Security Center jest instalowane w obszarze roboczym wybranym na ekranie zbierania danych, jeśli nie istnieje, a rozwiązanie jest stosowane tylko do odpowiednich maszyn wirtualnych. Po dodaniu rozwiązania, automatycznie są wdrażane domyślnie wszyscy agenci Windows i Linux, połączonego z obszarem roboczym usługi Log Analytics. [Określanie celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md) pozwala zastosować zakres do rozwiązań.

Jeśli program Microsoft Monitoring Agent jest zainstalowany bezpośrednio na maszynie Wirtualnej (a nie jako rozszerzenie platformy Azure), usługa Security Center nie instaluje program Microsoft Monitoring Agent oraz monitorowanie zabezpieczeń jest ograniczony.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Czy usługa Security Center zainstalowanie rozwiązania na moich istniejących obszarów roboczych usługi Log Analytics Jakie są skutki rozliczeń?
Gdy usługa Security Center zidentyfikuje, że maszyny Wirtualnej jest już połączony obszar roboczy, który został utworzony, usługa Security Center umożliwia rozwiązań na ten obszar roboczy, zgodnie z warstwy cenowej. Rozwiązania są stosowane tylko do odpowiednich maszyn wirtualnych platformy Azure, za pośrednictwem [określania wartości docelowej rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md), więc rozliczenia pozostają takie same.

- **Warstwa Bezpłatna** — Security Center instaluje rozwiązanie "SecurityCenterFree" w obszarze roboczym. Nie zostanie naliczona opłata za warstwę bezpłatna.
- **Warstwa standardowa** — Security Center instaluje rozwiązanie "zabezpieczenia" w obszarze roboczym.

   ![Rozwiązania na domyślny obszar roboczy][1]

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Mam już obszarów roboczych w mojej środowisku, można z nich korzystać do zbierania danych zabezpieczeń?
Jeśli maszyna wirtualna ma już zainstalowany program Microsoft Monitoring Agent jako rozszerzenie platformy Azure, usługa Security Center korzysta z istniejącego obszaru roboczego połączonych. Rozwiązanie Security Center jest zainstalowane w obszarze roboczym, jeśli nie istnieje, a rozwiązanie jest stosowane tylko do odpowiednich maszyn wirtualnych za pośrednictwem [określania celu rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md).

Gdy usługa Security Center instaluje program Microsoft Monitoring Agent na maszynach wirtualnych, używane domyślne wszystkich obszarach roboczych, utworzonych przez usługę Security Center.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Mam już rozwiązania zabezpieczeń na mój obszar roboczy. Jakie są skutki rozliczeń?
Rozwiązanie zabezpieczenia i inspekcja umożliwia włączanie funkcje warstwy standardowej usługi Security Center dla maszyn wirtualnych platformy Azure. Jeśli rozwiązanie zabezpieczenia i inspekcja jest już zainstalowany w obszarze roboczym, usługa Security Center korzysta z istniejącego rozwiązania. Nie ma zmian w rozliczeń.


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/solutions.png