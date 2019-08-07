---
title: Azure Monitor dla dostawców usług | Microsoft Docs
description: Azure Monitor może pomóc zarządzanym dostawcom usług (msp), dużymi przedsiębiorstwami, niezależnymi dostawcami oprogramowania (ISV) i dostawcami usług hostingu oraz monitorować serwery w infrastrukturze lokalnej lub w chmurze klienta.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: meirm
ms.openlocfilehash: 971757a4778dd50be486bead0c50fd6b3a25002e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839276"
---
# <a name="azure-monitor-for-service-providers"></a>Azure Monitor dla dostawców usług
Log Analytics obszary robocze w Azure Monitor mogą pomóc zarządzanym dostawcom usług (msp), dużymi przedsiębiorstwami, niezależnymi dostawcami oprogramowania (ISV) i dostawcami usług hostingowych oraz zarządzać serwerami w infrastrukturze lokalnej i w chmurze klienta. 

Duże przedsiębiorstwa mają wiele wspólnego z dostawcami usług, zwłaszcza w przypadku scentralizowane zespół IT, który jest odpowiedzialny za zarządzanie IT dla wielu różnych jednostek biznesowych. Dla uproszczenia w tym dokumencie użyto termin *usługodawcy* , ale te same funkcje jest również dostępna dla przedsiębiorstw i innych klientów.

W przypadku partnerów i dostawców usług, którzy są częścią programu [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , Log Analytics w Azure monitor to jedna z usług platformy Azure dostępnych w ramach [subskrypcji CSP platformy Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Architektury dla dostawców usług

Log Analytics obszary robocze umożliwiają administratorowi sterowanie przepływem i izolacją danych [dziennika](data-platform-logs.md) oraz tworzenie architektury, która odpowiada konkretnym potrzebom biznesowym. W [tym artykule](design-logs-deployment.md) wyjaśniono zagadnienia dotyczące projektowania, wdrażania i migracji dla obszaru roboczego, a w artykule [Zarządzanie dostępem](manage-access.md) omówiono sposób stosowania uprawnień i zarządzania nimi w celu uzyskania danych dziennika. Dostawcy usług mają dodatkowe zagadnienia.

Istnieją trzy możliwe architektury dla dostawców usług dotyczących obszarów roboczych usługi Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Rozproszone — dzienniki są przechowywane w obszarach roboczych znajdujących się w dzierżawie klienta 

W ramach tej architektury obszar roboczy jest wdrażany w dzierżawie klienta, używany dla wszystkich dzienników z tego klienta. Administratorzy dostawcy usług mają dostęp do tego obszaru roboczego przy użyciu [użytkowników-gości usługi Azure Active Directory (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Administratorzy dostawcy usług musi przełączyć się do katalogu swoich klientów w witrynie Azure portal, aby mieć możliwość dostępu do tych obszarów roboczych.

Zalety tej architekturze są następujące:
* Klient może zarządzać dostępem do dzienników przy użyciu ich własnych [dostępu opartej na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Każdy klient może mieć różne ustawienia dla ich obszarze roboczym, takich jak przechowywanie i danych są takie same.
* Izolację między klientów przepisów i zgodności.
* Opłaty za każdy obszar roboczy zostanie wycofana do subskrypcji klienta.
* Dzienniki mogą być zbierane z wszystkich typów zasobów, nie tylko oparte na agentach. Na przykład dzienników inspekcji platformy Azure.

Dostępne są następujące wady tej architektury:
* Jest trudniejsze dla dostawcy usług do zarządzania dużą liczbą dzierżaw klientów jednocześnie.
* Administratorzy dostawcy usług mają do udostępnienia w katalogu klienta.
* Dostawca usług nie mogą analizować dane w swoim klientom.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Środkowo - dzienniki są przechowywane w obszarze roboczym znajduje się w dzierżawie dostawca usługi

W tej architekturze dzienniki nie są przechowywane w dzierżawach klienta, ale tylko w centralnej lokalizacji, w ramach jednej subskrypcji do dostawcy usług. Agenci, którzy są instalowane na maszynach wirtualnych klienta są skonfigurowane do wysyłania dzienników do tego obszaru roboczego za pomocą Identyfikatora obszaru roboczego i klucz tajny.

Zalety tej architekturze są następujące:
* To ułatwia zarządzanie dużą liczbą klientów i zintegrowanie ich do różnych systemów zaplecza.

* Dostawcy usług mają pełne prawa własności w zakresie dzienników oraz różnych artefaktów, takich jak funkcje i zapisane kwerendy.

* Usługodawcy mogą wykonywać analizy we wszystkich jej klientów.

Dostępne są następujące wady tej architektury:
* Ta architektura ma zastosowanie tylko do danych opartej o agentów maszyny Wirtualnej, nie zapewnią źródeł danych sieci szkieletowej PaaS i SaaS oraz platformy Azure.

* Może być trudny do oddzielania danych od klientów, gdy są one scalane w jeden obszar roboczy. Jedynym dobrym metody, w tym celu jest użycie komputera w pełni kwalifikowaną nazwę domeny (FQDN) lub za pośrednictwem subskrypcji platformy Azure identyfikatora. 

* Wszystkie dane ze wszystkich klientów będą przechowywane w tym samym regionie, z jednym rachunku i te same ustawienia przechowywania i konfiguracji.

* Sieci szkieletowej platformy Azure i usług PaaS usług, takich jak usługi Azure Diagnostics i dzienników inspekcji platformy Azure wymaga obszar, aby być w tej samej dzierżawie, co zasób, dlatego nie mogą wysyłać dzienniki do obszaru roboczego centralnej.

* Wszyscy agenci maszyn wirtualnych wszyscy klienci będą uwierzytelniani w centralnym obszarze roboczym przy użyciu tego samego identyfikatora i klucza obszaru roboczego. Nie istnieje metoda blokowania dzienników z określonego klienta bez przerywania pracy innych klientów.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybrydowe — dzienniki są przechowywane w obszarze roboczym znajduje się w dzierżawie klienta, a niektóre z nich są pobierane w centralnej lokalizacji.

Architektura trzeci łączyć się z dwóch opcji. Jest on oparty na pierwszym architektura rozproszona, gdzie dzienniki są lokalne w poszczególnych klientów, ale tworzenie centralne repozytorium dzienników przy użyciu mechanizmu. Część dzienników jest pobierane do centralnej lokalizacji w celu raportowania i analizy. Ta część może być małą liczbą typów danych lub podsumowanie działań, takich jak statystyki dzienny.

Istnieją dwie opcje implementowania dzienników w centralnej lokalizacji:

1. Centralny obszar roboczy: Dostawca usług może utworzyć obszar roboczy w swojej dzierżawie i użyć skryptu, który korzysta z [interfejsu API zapytania](https://dev.loganalytics.io/) z [interfejsem API zbierania danych](../../azure-monitor/platform/data-collector-api.md) , aby przenieść dane z różnych obszarów roboczych do tej centralnej lokalizacji. Inną opcją niż skryptu, jest użycie [usługi Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI jako centralnej lokalizacji: Power BI może pełnić rolę centralnej lokalizacji, gdy różne obszary robocze eksportują do niego dane przy użyciu integracji między obszarem roboczym Log Analytics i [Power BI](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Następne kroki

* Automatyzować tworzenie i konfigurowanie obszarami roboczymi przy użyciu [szablonów usługi Resource Manager](template-workspace-configuration.md)

* Automatyzacja tworzenia obszarów roboczych przy użyciu [programu PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Użyj [alerty](../../azure-monitor/platform/alerts-overview.md) do integracji z istniejącymi systemami

* Generowanie raportu podsumowania przy użyciu [usługi Power BI](../../azure-monitor/platform/powerbi.md)

* Przegląd procesu [konfigurowania usługi Log Analytics i usługą Power BI do monitorowania wielu klientów programu CSP](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
