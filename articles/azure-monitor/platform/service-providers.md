---
title: Dzienniki Azure Monitor dla dostawców usług | Microsoft Docs
description: Dzienniki Azure Monitor mogą pomóc zarządzanym dostawcom usług (msp), dużymi przedsiębiorstwami, niezależnymi dostawcami oprogramowania (ISV) i dostawcami usług hostingowych oraz zarządzać serwerami w infrastrukturze lokalnej lub w chmurze klienta.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658884"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Dzienniki Azure Monitor dla dostawców usług

Log Analytics obszary robocze w Azure Monitor mogą pomóc zarządzanym dostawcom usług (msp), dużymi przedsiębiorstwami, niezależnymi dostawcami oprogramowania (ISV) i dostawcami usług hostingowych oraz zarządzać serwerami w infrastrukturze lokalnej i w chmurze klienta.

Duże przedsiębiorstwa mają wiele wspólnego z dostawcami usług, zwłaszcza w przypadku scentralizowane zespół IT, który jest odpowiedzialny za zarządzanie IT dla wielu różnych jednostek biznesowych. Dla uproszczenia w tym dokumencie jest stosowany *dostawca usług* , ale te same funkcje są również dostępne dla przedsiębiorstw i innych klientów.

W przypadku partnerów i dostawców usług, którzy są częścią programu [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , Log Analytics w Azure monitor to jedna z usług platformy Azure dostępnych w ramach subskrypcji CSP platformy Azure.

Log Analytics w Azure Monitor może być również używany przez dostawcę usług do zarządzania zasobami klienta za pomocą funkcji zarządzania zasobami delegowanymi platformy Azure w [usłudze Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="architectures-for-service-providers"></a>Architektury dla dostawców usług

Log Analytics obszary robocze umożliwiają administratorowi sterowanie przepływem i izolacją danych [dziennika](data-platform-logs.md) oraz tworzenie architektury, która odpowiada konkretnym potrzebom biznesowym. W [tym artykule](design-logs-deployment.md) wyjaśniono zagadnienia dotyczące projektowania, wdrażania i migracji dla obszaru roboczego, a w artykule [Zarządzanie dostępem](manage-access.md) omówiono sposób stosowania uprawnień i zarządzania nimi w celu uzyskania danych dziennika. Dostawcy usług mają dodatkowe zagadnienia.

Istnieją trzy możliwe architektury dla dostawców usług dotyczących obszarów roboczych usługi Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. dzienniki rozproszone są przechowywane w obszarach roboczych znajdujących się w dzierżawie klienta

W ramach tej architektury obszar roboczy jest wdrażany w dzierżawie klienta, używany dla wszystkich dzienników z tego klienta.

Istnieją dwa sposoby, które Administratorzy dostawcy usług mogą uzyskać dostęp do obszaru roboczego Log Analytics w dzierżawie klienta:

- Klient może dodawać poszczególnych użytkowników od usługodawcy jako [Azure Active Directory użytkowników-Gości (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Administratorzy dostawcy usług będą musieli zalogować się do katalogu każdego klienta w Azure Portal, aby mogli uzyskać dostęp do tych obszarów roboczych. Wymaga to również od klientów zarządzania indywidualnym dostępem każdego administratora dostawcy usług.
- Aby zapewnić lepszą skalowalność i elastyczność, dostawcy usług mogą korzystać z funkcji [zarządzania zasobami delegowanymi przez platformę Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) [Lighthouse na platformie Azure](https://docs.microsoft.com/azure/lighthouse/overview) w celu uzyskania dostępu do dzierżawy klienta. Ta metoda polega na tym, że Administratorzy dostawcy usług są włączeni do grupy użytkowników usługi Azure AD w dzierżawie dostawcy usług, a podczas procesu dołączania dla każdego klienta zostaje udzielony dostęp do tej grupy. Ci Administratorzy mogą następnie uzyskiwać dostęp do obszarów roboczych każdego klienta z własnej dzierżawy usługodawcy, zamiast logować się do każdej dzierżawy każdego klienta osobno. W ten sposób uzyskuje się dostęp do zasobów obszarów roboczych Log Analytics w ten sposób, co zmniejsza pracę wymaganą przez klienta i może ułatwić zbieranie i analizowanie danych przez wielu klientów zarządzanych przez tego samego dostawcę usług za pośrednictwem takich narzędzi, jak [Azure monitor skoroszytów](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview). Aby uzyskać więcej informacji, zobacz [monitorowanie zasobów klienta w odpowiedniej skali](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale).

Zalety architektury rozproszonej są następujące:

* Klient może potwierdzić określone poziomy uprawnień za pośrednictwem funkcji [zarządzania zasobami delegowanymi przez platformę Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)lub zarządzać dostępem do dzienników przy użyciu własnego [dostępu opartego na rolach](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Dzienniki mogą być zbierane z wszystkich typów zasobów, a nie tylko z danych maszyn wirtualnych opartych na agentach. Na przykład dzienników inspekcji platformy Azure.
* Każdy klient może mieć różne ustawienia dla ich obszarze roboczym, takich jak przechowywanie i danych są takie same.
* Izolację między klientów przepisów i zgodności.
* Opłaty za każdy obszar roboczy zostanie wycofana do subskrypcji klienta.

Wady architektury rozproszonej są następujące:

* Centralne wizualizacje i analizowanie danych w dzierżawach klientów dzięki narzędziom, takim jak skoroszyty Azure Monitor mogą powodować wolniejsze środowiska, szczególnie podczas analizowania danych w ponad 50 obszarach roboczych.
* Jeśli klienci nie zostaną dołączeni do zarządzania zasobami delegowanymi przez platformę Azure, Administratorzy dostawcy usług muszą być obsługiwani w katalogu klienta i trudniejsze jest użycie dostawcy usług w celu jednoczesnego zarządzania dużą liczbą dzierżawców klientów.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. dzienniki centralne są przechowywane w obszarze roboczym znajdującym się w dzierżawie dostawcy usług

W tej architekturze dzienniki nie są przechowywane w dzierżawach klienta, ale tylko w centralnej lokalizacji, w ramach jednej subskrypcji do dostawcy usług. Agenci, którzy są instalowane na maszynach wirtualnych klienta są skonfigurowane do wysyłania dzienników do tego obszaru roboczego za pomocą Identyfikatora obszaru roboczego i klucz tajny.

Zalety scentralizowanej architektury są następujące:

* To ułatwia zarządzanie dużą liczbą klientów i zintegrowanie ich do różnych systemów zaplecza.
* Dostawcy usług mają pełne prawa własności w zakresie dzienników oraz różnych artefaktów, takich jak funkcje i zapisane kwerendy.
* Usługodawcy mogą wykonywać analizy we wszystkich jej klientów.

Wady centralnej architektury są następujące:

* Ta architektura ma zastosowanie tylko do danych opartej o agentów maszyny Wirtualnej, nie zapewnią źródeł danych sieci szkieletowej PaaS i SaaS oraz platformy Azure.
* Może być trudny do oddzielania danych od klientów, gdy są one scalane w jeden obszar roboczy. Jedynym dobrym metody, w tym celu jest użycie komputera w pełni kwalifikowaną nazwę domeny (FQDN) lub za pośrednictwem subskrypcji platformy Azure identyfikatora. 
* Wszystkie dane ze wszystkich klientów będą przechowywane w tym samym regionie, z jednym rachunku i te same ustawienia przechowywania i konfiguracji.
* Sieci szkieletowej platformy Azure i usług PaaS usług, takich jak usługi Azure Diagnostics i dzienników inspekcji platformy Azure wymaga obszar, aby być w tej samej dzierżawie, co zasób, dlatego nie mogą wysyłać dzienniki do obszaru roboczego centralnej.
* Wszyscy agenci maszyn wirtualnych wszyscy klienci będą uwierzytelniani w centralnym obszarze roboczym przy użyciu tego samego identyfikatora i klucza obszaru roboczego. Nie istnieje metoda blokowania dzienników z określonego klienta bez przerywania pracy innych klientów.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. dzienniki hybrydowe są przechowywane w obszarze roboczym znajdującym się w dzierżawie klienta, a niektóre z nich są ściągane do lokalizacji centralnej.

Architektura trzeci łączyć się z dwóch opcji. Jest on oparty na pierwszym architektura rozproszona, gdzie dzienniki są lokalne w poszczególnych klientów, ale tworzenie centralne repozytorium dzienników przy użyciu mechanizmu. Część dzienników jest pobierane do centralnej lokalizacji w celu raportowania i analizy. Ta część może być małą liczbą typów danych lub podsumowanie działań, takich jak statystyki dzienny.

Istnieją dwie opcje implementowania dzienników w centralnej lokalizacji:

1. Centralny obszar roboczy: dostawca usług może utworzyć obszar roboczy w swojej dzierżawie i użyć skryptu, który korzysta z [interfejsu API zapytania](https://dev.loganalytics.io/) z [interfejsem API zbierania danych](../../azure-monitor/platform/data-collector-api.md) , aby przenieść dane z różnych obszarów roboczych do tej centralnej lokalizacji. Inną opcją, inną niż skrypt, jest użycie [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI jako centralnej lokalizacji: Power BI może pełnić rolę centralnej lokalizacji, gdy różne obszary robocze eksportują do niego dane przy użyciu integracji między obszarem roboczym Log Analytics a [Power BI](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Następne kroki

* Automatyzowanie tworzenia i konfigurowania obszarów roboczych przy użyciu [szablonów Menedżer zasobów](template-workspace-configuration.md)

* Automatyzowanie tworzenia obszarów roboczych przy użyciu [programu PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Korzystanie z [alertów](../../azure-monitor/platform/alerts-overview.md) w celu integracji z istniejącymi systemami

* Generowanie raportów podsumowujących przy użyciu [Power BI](../../azure-monitor/platform/powerbi.md)

* Dołączanie klientów do [zarządzania zasobami delegowanymi przez platformę Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management).
