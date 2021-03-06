---
title: Usługa Azure Security Center — pytania dotyczące istniejących agentów usługi Log Analytics
description: To często zadawane pytania zawiera odpowiedzi na pytania klientów, którzy już korzystają z agenta usługi Log Analytics i rozważają usługę Azure Security Center, produkt, który pomaga zapobiegać zagrożeniom, wykrywać ich i reagować na nie.
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
ms.openlocfilehash: f6384c1e9e14e38b4c44c5ac79a674839b43b4ca
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436163"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Często zadawane pytania dla klientów korzystających już z dzienników usługi Azure Monitor<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Czy Usługa Security Center zastępuje istniejące połączenia między maszynami wirtualnymi a obszarami roboczymi?

Jeśli maszyna wirtualna ma już agenta usługi Log Analytics zainstalowanego jako rozszerzenie platformy Azure, usługa Security Center nie zastępuje istniejącego połączenia obszaru roboczego. Zamiast tego Usługa Security Center używa istniejącego obszaru roboczego. Maszyna wirtualna będzie chroniona pod warunkiem, że rozwiązanie "Security" lub "SecurityCenterFree" zostało zainstalowane w obszarze roboczym, do którego jest raportowany. 

Rozwiązanie centrum zabezpieczeń jest instalowane w obszarze roboczym wybranym na ekranie Zbieranie danych, jeśli nie jest już obecny, a rozwiązanie jest stosowane tylko do odpowiednich maszyn wirtualnych. Po dodaniu rozwiązania jest ono domyślnie wdrażane automatycznie dla wszystkich agentów systemu Windows i systemu Linux połączonych z obszarem roboczym usługi Log Analytics. [Kierowanie na rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md) umożliwia zastosowanie zakresu do rozwiązań.

> [!TIP]
> Jeśli agent usługi Log Analytics jest zainstalowany bezpośrednio na maszynie wirtualnej (nie jako rozszerzenie platformy Azure), usługa Security Center nie instaluje agenta usługi Log Analytics, a monitorowanie zabezpieczeń jest ograniczone.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Czy usługa Security Center instaluje rozwiązania w istniejących obszarach roboczych usługi Log Analytics? Jakie są implikacje rozliczeń?
Gdy usługa Security Center stwierdzi, że maszyna wirtualna jest już połączona z utworzonym obszarem roboczym, usługa Security Center włącza rozwiązania w tym obszarze roboczym zgodnie z warstwą cenową. Rozwiązania są stosowane tylko do odpowiednich maszyn wirtualnych platformy Azure za pośrednictwem [kierowania rozwiązania,](../operations-management-suite/operations-management-suite-solution-targeting.md)więc rozliczenia pozostają takie same.

- **Warstwa bezpłatna** — usługa Security Center instaluje rozwiązanie "SecurityCenterFree" w obszarze roboczym. Nie zostanie naliczona naliczona naliczona warstwa Bezpłatna.
- **Warstwa standardowa** — usługa Security Center instaluje rozwiązanie "Zabezpieczenia" w obszarze roboczym.

   ![Rozwiązania w domyślnym obszarze roboczym](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Mam już obszary robocze w moim środowisku, czy mogę ich używać do zbierania danych zabezpieczeń?
Jeśli maszyna wirtualna ma już agenta usługi Log Analytics zainstalowanego jako rozszerzenie platformy Azure, usługa Security Center używa istniejącego połączonego obszaru roboczego. Rozwiązanie centrum zabezpieczeń jest zainstalowane w obszarze roboczym, jeśli nie jest już obecny, a rozwiązanie jest stosowane tylko do odpowiednich maszyn wirtualnych za pomocą [kierowania rozwiązania](../operations-management-suite/operations-management-suite-solution-targeting.md).

Gdy Usługa Security Center instaluje agenta usługi Log Analytics na maszynach wirtualnych, używa domyślnych obszarów roboczych utworzonych przez usługę Security Center.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Mam już rozwiązanie zabezpieczające w moich obszarach roboczych. Jakie są implikacje rozliczeń?
Rozwiązanie Do kontroli zabezpieczeń & służy do włączania funkcji warstwy standardowej usługi Security Center dla maszyn wirtualnych platformy Azure. Jeśli rozwiązanie Security & Audit jest już zainstalowane w obszarze roboczym, usługa Security Center używa istniejącego rozwiązania. Nie ma zmian w rozliczeniach.