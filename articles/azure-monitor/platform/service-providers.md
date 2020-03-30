---
title: Dzienniki usługi Azure Monitor dla dostawców usług | Dokumenty firmy Microsoft
description: Dzienniki usługi Azure Monitor mogą pomóc dostawcom usług zarządzanych(MSP), dużym przedsiębiorstwom, niezależnym dostawcom oprogramowania (ISV) i dostawcom usług hostingowych zarządzać serwerami i monitorować je w infrastrukturze lokalnej lub chmurowej klienta.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658884"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Dzienniki usługi Azure Monitor dla dostawców usług

Obszary robocze usługi Log Analytics w usłudze Azure Monitor mogą pomóc dostawcom usług zarządzanych(MSP), dużym przedsiębiorstwom, niezależnym dostawcom oprogramowania (ISV) i dostawcom usług hostingowych zarządzać serwerami i monitorować je w infrastrukturze lokalnej lub w chmurze klienta.

Duże przedsiębiorstwa mają wiele podobieństw do dostawców usług, szczególnie gdy istnieje scentralizowany zespół IT, który jest odpowiedzialny za zarządzanie działem IT dla wielu różnych jednostek biznesowych. Dla uproszczenia ten dokument używa terminu *dostawca usług,* ale ta sama funkcjonalność jest również dostępna dla przedsiębiorstw i innych klientów.

Dla partnerów i dostawców usług, którzy są częścią programu [dostawcy rozwiązań w chmurze (CSP),](https://partner.microsoft.com/Solutions/cloud-reseller-overview) usługa Log Analytics w usłudze Azure Monitor jest jedną z usług platformy Azure dostępnych w subskrypcjach usługi Azure CSP.

Usługa Log Analytics w usłudze Azure Monitor może być również używana przez dostawcę usług zarządzającego zasobami klientów za pośrednictwem funkcji zarządzania zasobami delegowanymi platformy Azure w [latarni morskiej platformy Azure.](https://docs.microsoft.com/azure/lighthouse/overview)

## <a name="architectures-for-service-providers"></a>Architektury dla usługodawców

Obszary robocze usługi Log Analytics zapewniają administratorowi metodę kontrolowania przepływu i izolacji danych [dziennika](data-platform-logs.md) i tworzenia architektury, która odpowiada na określone potrzeby biznesowe. [W tym artykule](design-logs-deployment.md) opisano zagadnienia dotyczące projektowania, wdrażania i migracji dla obszaru roboczego, a w artykule [dotyczącym zarządzania dostępem](manage-access.md) omówiono sposób stosowania i zarządzania uprawnieniami do rejestrowania danych. Dostawcy usług mają dodatkowe względy.

Istnieją trzy możliwe architektury dla dostawców usług dotyczących obszarów roboczych usługi Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Rozproszone — dzienniki są przechowywane w przestrzeniach roboczych znajdujących się w dzierżawie klienta

W tej architekturze obszar roboczy jest wdrażany w dzierżawie klienta, który jest używany dla wszystkich dzienników tego klienta.

Administratorzy usług mogą uzyskać dostęp do obszaru roboczego usługi Log Analytics w dzierżawie klienta na dwa sposoby:

- Klient może dodawać poszczególnych użytkowników od dostawcy usług jako [użytkowników-gościa usługi Azure Active Directory (B2B).](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) Administratorzy dostawcy usług będą musieli zalogować się do katalogu każdego klienta w witrynie Azure Portal, aby uzyskać dostęp do tych obszarów roboczych. Wymaga to również od klientów zarządzania indywidualnym dostępem dla każdego administratora dostawcy usług.
- Aby uzyskać większą skalowalność i elastyczność, dostawcy usług mogą korzystać z funkcji [zarządzania zasobami delegowanymi platformy Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) [w celu](https://docs.microsoft.com/azure/lighthouse/overview) uzyskania dostępu do dzierżawy klienta. Dzięki tej metodzie administratorzy dostawcy usług są uwzględniani w grupie użytkowników usługi Azure AD w dzierżawie dostawcy usług, a ta grupa ma dostęp podczas procesu dołączania dla każdego klienta. Administratorzy ci mogą następnie uzyskać dostęp do obszarów roboczych każdego klienta z poziomu ich dzierżawy dostawcy usług, zamiast logować się do dzierżawy każdego klienta indywidualnie. Dostęp do zasobów obszarów roboczych usługi Log Analytics klientów w ten sposób zmniejsza pracę wymaganą po stronie klienta i może ułatwić gromadzenie i analizowanie danych przez wielu klientów zarządzanych przez tego samego dostawcę usług za pomocą narzędzi, takich jak [skoroszyty usługi Azure Monitor.](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview) Aby uzyskać więcej informacji, zobacz [Monitorowanie zasobów klientów na dużą skalę](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale).

Zalety architektury rozproszonej to:

* Klient może potwierdzić określone poziomy uprawnień za pośrednictwem [usługi Azure zarządzania zasobami delegowanymi](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)lub zarządzać dostępem do dzienników przy użyciu własnego [dostępu opartego na rolach.](https://docs.microsoft.com/azure/role-based-access-control/overview)
* Dzienniki mogą być zbierane ze wszystkich typów zasobów, nie tylko danych maszyn wirtualnych opartych na agentach. Na przykład dzienniki inspekcji platformy Azure.
* Każdy klient może mieć różne ustawienia dla swojego obszaru roboczego, takie jak przechowywanie i ograniczanie danych.
* Izolacja między klientami w zakresie regulacji i zgodności.
* Opłata za każdy obszar roboczy zostanie przewinięta do subskrypcji klienta.

Wady architektury rozproszonej są:

* Centralne wizualizacje i analizowanie danych między dzierżawcami klientów za pomocą narzędzi, takich jak skoroszyty usługi Azure Monitor, może spowodować wolniejsze środowisko , szczególnie podczas analizowania danych w ponad ponad 50 obszarach roboczych.
* Jeśli klienci nie są dołączane do zarządzania zasobami delegowanymi platformy Azure, administratorzy dostawcy usług muszą być aprowiowane w katalogu klientów i jest trudniejsze dla dostawcy usług do zarządzania dużą liczbę dzierżawców klientów na raz.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Central - Dzienniki są przechowywane w obszarze roboczym znajdującym się w dzierżawie usługodawcy

W tej architekturze dzienniki nie są przechowywane w dzierżawców klienta, ale tylko w centralnej lokalizacji w ramach jednej z subskrypcji dostawcy usług. Agenci, którzy są instalowani na maszynach wirtualnych klienta są skonfigurowane do wysyłania swoich dzienników do tego obszaru roboczego przy użyciu identyfikatora obszaru roboczego i klucz tajny.

Zalety scentralizowanej architektury to:

* Łatwo jest zarządzać dużą liczbą klientów i integrować ich z różnymi systemami zaplecza.
* Dostawca usług ma pełną własność nad dziennikami i różnymi artefaktami, takimi jak funkcje i zapisane zapytania.
* Dostawca usług może przeprowadzać analizy we wszystkich swoich klientach.

Wady scentralizowanej architektury to:

* Ta architektura ma zastosowanie tylko do danych maszyn wirtualnych opartych na agentach, nie będzie obejmować paaS, SaaS i źródła danych sieci szkieletowej platformy Azure.
* Może być trudne do oddzielenia danych między klientami, gdy są one scalane w jednym obszarze roboczym. Jedyną dobrą metodą jest użycie w pełni kwalifikowanej nazwy domeny komputera (FQDN) lub za pośrednictwem identyfikatora subskrypcji platformy Azure. 
* Wszystkie dane od wszystkich klientów będą przechowywane w tym samym regionie z jednym rachunkiem i tymi samymi ustawieniami przechowywania i konfiguracji.
* Usługi sieci szkieletowej platformy Azure i PaaS, takie jak diagnostyka platformy Azure i dzienniki inspekcji platformy Azure, wymagają, aby obszar roboczy był w tej samej dzierżawie co zasób, w związku z czym nie mogą wysyłać dzienników do centralnego obszaru roboczego.
* Wszyscy agenci maszyn wirtualnych od wszystkich klientów zostaną uwierzytelnione do centralnego obszaru roboczego przy użyciu tego samego identyfikatora obszaru roboczego i klucza. Nie ma metody blokowania dzienników od określonego klienta bez przerywania innych klientów.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybrydowe — dzienniki są przechowywane w obszarze roboczym znajdującym się w dzierżawie klienta, a niektóre z nich są pobierane do centralnej lokalizacji.

Trzecia architektura miesza się między tymi dwoma opcjami. Jest on oparty na pierwszej architekturze rozproszonej, gdzie dzienniki są lokalne dla każdego klienta, ale przy użyciu pewnego mechanizmu do tworzenia centralnego repozytorium dzienników. Część dzienników jest pobierana do centralnej lokalizacji do raportowania i analizy. Ta część może być niewielka liczba typów danych lub podsumowanie działania, takich jak codzienne statystyki.

Istnieją dwie opcje implementacji dzienników w centralnej lokalizacji:

1. Centralny obszar roboczy: dostawca usług może utworzyć obszar roboczy w dzierżawie i użyć skryptu, który wykorzystuje [interfejs API kwerendy](https://dev.loganalytics.io/) z [interfejsem API zbierania danych,](../../azure-monitor/platform/data-collector-api.md) aby przenieść dane z różnych obszarów roboczych do tej centralnej lokalizacji. Inną opcją, inną niż skrypt, jest użycie [usługi Azure Logic Apps.](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)

2. Usługa Power BI jako centralna lokalizacja: usługa Power BI może działać jako centralna lokalizacja, gdy różne obszary robocze eksportują do niej dane przy użyciu integracji między obszarem roboczym usługi Log Analytics a [usługą Power BI](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Następne kroki

* Automatyzacja tworzenia i konfigurowania obszarów roboczych przy użyciu [szablonów Menedżera zasobów](template-workspace-configuration.md)

* Automatyzacja tworzenia obszarów roboczych przy użyciu programu [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Integracja z [istniejącymi systemami za](../../azure-monitor/platform/alerts-overview.md) pomocą alertów

* Generowanie raportów podsumowujących przy użyciu [usługi Power BI](../../azure-monitor/platform/powerbi.md)

* Klienci dołączani do [zarządzania zasobami delegowanymi platformy Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).
