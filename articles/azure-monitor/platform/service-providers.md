---
title: Azure Monitor dla dostawców usług | Microsoft Docs
description: Azure Monitor może pomóc zarządzanym dostawcom usług (msp), dużymi przedsiębiorstwami, niezależnymi dostawcami oprogramowania (ISV) i dostawcami usług hostingu oraz monitorować serwery w infrastrukturze lokalnej lub w chmurze klienta.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 08/06/2019
ms.openlocfilehash: b0f25d01421edd329b03d8f2b7e1aafaa2ba67d5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932061"
---
# <a name="azure-monitor-for-service-providers"></a>Azure Monitor dla dostawców usług
Log Analytics obszary robocze w Azure Monitor mogą pomóc zarządzanym dostawcom usług (msp), dużymi przedsiębiorstwami, niezależnymi dostawcami oprogramowania (ISV) i dostawcami usług hostingowych oraz zarządzać serwerami w infrastrukturze lokalnej i w chmurze klienta. 

Duże przedsiębiorstwa współużytkują wiele podobieństw z dostawcami usług, szczególnie w przypadku scentralizowanego zespołu IT, który jest odpowiedzialny za zarządzanie nim dla wielu różnych jednostek roboczych. Dla uproszczenia w tym dokumencie jest stosowany *dostawca usług* , ale te same funkcje są również dostępne dla przedsiębiorstw i innych klientów.

W przypadku partnerów i dostawców usług, którzy są częścią programu [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , Log Analytics w Azure monitor to jedna z usług platformy Azure dostępnych w ramach [subskrypcji CSP platformy Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Architektury dla dostawców usług

Log Analytics obszary robocze umożliwiają administratorowi sterowanie przepływem i izolacją danych [dziennika](data-platform-logs.md) oraz tworzenie architektury, która odpowiada konkretnym potrzebom biznesowym. W [tym artykule](design-logs-deployment.md) wyjaśniono zagadnienia dotyczące projektowania, wdrażania i migracji dla obszaru roboczego, a w artykule [Zarządzanie dostępem](manage-access.md) omówiono sposób stosowania uprawnień i zarządzania nimi w celu uzyskania danych dziennika. Dostawcy usług mają dodatkowe uwagi.

Istnieją trzy możliwe architektury dla dostawców usług, które dotyczą Log Analytics obszarów roboczych:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. dzienniki rozproszone są przechowywane w obszarach roboczych znajdujących się w dzierżawie klienta 

W tej architekturze obszar roboczy jest wdrażany w dzierżawie klienta, który jest używany dla wszystkich dzienników tego klienta. Administratorzy dostawcy usług uzyskują dostęp do tego obszaru roboczego przy użyciu [Azure Active Directory użytkowników-Gości (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Administratorzy dostawcy usług będą musieli przełączyć się do katalogu klienta w Azure Portal, aby mogli uzyskać dostęp do tych obszarów roboczych.

Zalety tej architektury są następujące:
* Klient może zarządzać dostępem do dzienników przy użyciu własnego [dostępu opartego na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Każdy klient może mieć różne ustawienia dla swojego obszaru roboczego, takie jak przechowywanie danych i ich limit.
* Izolacja klientów na potrzeby regulacji i zgodności.
* Opłata za poszczególne obszary robocze zostanie przeniesiona na subskrypcję klienta.
* Dzienniki mogą być zbierane z wszystkich typów zasobów, a nie tylko na podstawie agenta. Na przykład dzienniki inspekcji platformy Azure.

Wady tej architektury są następujące:
* W przypadku dostawcy usług trudniejsze jest zarządzanie dużą liczbą dzierżawców klientów jednocześnie.
* Administratorzy dostawcy usług muszą być obsługiwani w katalogu klienta.
* Dostawca usług nie może analizować danych w swoich klientach.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. dzienniki centralne są przechowywane w obszarze roboczym znajdującym się w dzierżawie dostawcy usług

W tej architekturze dzienniki nie są przechowywane w dzierżawach klienta, ale tylko w centralnej lokalizacji w ramach subskrypcji dostawcy usług. Agenci zainstalowani na maszynach wirtualnych klienta są skonfigurowani do wysyłania dzienników do tego obszaru roboczego przy użyciu identyfikatora obszaru roboczego i klucza tajnego.

Zalety tej architektury są następujące:
* Zarządzanie dużą liczbą klientów i integrowanie ich z różnymi systemami zaplecza jest proste.

* Dostawca usług ma pełną własność w odniesieniu do dzienników i różnych artefaktów, takich jak funkcje i zapisane zapytania.

* Dostawca usług może przeprowadzić analizę we wszystkich swoich klientach.

Wady tej architektury są następujące:
* Ta architektura ma zastosowanie tylko do danych maszyn wirtualnych opartych na agencie, ale nie obejmuje źródeł danych PaaS, SaaS i Azure Fabric.

* Po scaleniu z jednym obszarem roboczym może być trudno rozdzielić dane między klientami. Jedyną dobrą metodą jest użycie w pełni kwalifikowanej nazwy domeny (FQDN) komputera lub identyfikatora subskrypcji platformy Azure. 

* Wszystkie dane ze wszystkich klientów będą przechowywane w tym samym regionie z pojedynczym rachunkiem i tymi samymi ustawieniami przechowywania i konfiguracji.

* Usługi Azure Fabric i PaaS Services, takie jak Diagnostyka Azure i dzienniki inspekcji platformy Azure, wymagają, aby obszar roboczy znajdował się w tej samej dzierżawie co zasób, dlatego nie może wysyłać dzienników do centralnego obszaru roboczego.

* Wszyscy agenci maszyn wirtualnych wszyscy klienci będą uwierzytelniani w centralnym obszarze roboczym przy użyciu tego samego identyfikatora i klucza obszaru roboczego. Nie ma metody blokowania dzienników od określonego klienta bez zakłócania pracy przez innych klientów.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. dzienniki hybrydowe są przechowywane w obszarze roboczym znajdującym się w dzierżawie klienta, a niektóre z nich są ściągane do lokalizacji centralnej.

Trzecia architektura między tymi dwiema opcjami. Jest on oparty na pierwszej architekturze rozproszonej, w której dzienniki są lokalne dla każdego klienta, ale przy użyciu pewnego mechanizmu można utworzyć centralne repozytorium dzienników. Część dzienników jest pobierana do centralnej lokalizacji na potrzeby raportowania i analizy. Ta część może być małą liczbą typów danych lub podsumowaniem działania, na przykład Statystyka dzienna.

Istnieją dwie opcje implementowania dzienników w centralnej lokalizacji:

1. Centralny obszar roboczy: dostawca usług może utworzyć obszar roboczy w swojej dzierżawie i użyć skryptu, który korzysta z [interfejsu API zapytania](https://dev.loganalytics.io/) z [interfejsem API zbierania danych](../../azure-monitor/platform/data-collector-api.md) , aby przenieść dane z różnych obszarów roboczych do tej centralnej lokalizacji. Inną opcją, inną niż skrypt, jest użycie [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI jako centralnej lokalizacji: Power BI może pełnić rolę centralnej lokalizacji, gdy różne obszary robocze eksportują do niego dane przy użyciu integracji między obszarem roboczym Log Analytics a [Power BI](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Następne kroki

* Automatyzowanie tworzenia i konfigurowania obszarów roboczych przy użyciu [szablonów Menedżer zasobów](template-workspace-configuration.md)

* Automatyzowanie tworzenia obszarów roboczych przy użyciu [programu PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Korzystanie z [alertów](../../azure-monitor/platform/alerts-overview.md) w celu integracji z istniejącymi systemami

* Generowanie raportów podsumowujących przy użyciu [Power BI](../../azure-monitor/platform/powerbi.md)

* Przegląd procesu [konfigurowania log Analytics i Power BI do monitorowania wielu klientów programu CSP](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
