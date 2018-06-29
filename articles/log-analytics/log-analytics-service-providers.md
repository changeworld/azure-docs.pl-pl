---
title: Dziennika funkcji analizy dla dostawców usług | Dokumentacja firmy Microsoft
description: Analiza dzienników może pomóc zarządzanego dostawcy usług (MSPs) dużych przedsiębiorstw niezależni dostawcy oprogramowania (ISV) i dostawcy usług hostingowych zarządzanie i monitorowanie serwerów w lokalnym przez klienta lub infrastruktury chmury.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: meirm
ms.openlocfilehash: 97e36c624e865010ada67f5163af6d7f03de079f
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096574"
---
# <a name="log-analytics-features-for-service-providers"></a>Funkcje analizy dziennika dla dostawców usług
Analiza dzienników może pomóc dostawców usługi zarządzane (MSPs), dużych przedsiębiorstw niezależnym dostawcom oprogramowania (ISV) i dostawcy usług hostingowych zarządzanie i monitorowanie serwerów w lokalnym przez klienta lub infrastruktury chmury. 

Duże przedsiębiorstwa udostępniać wiele podobieństw dostawców usług, zwłaszcza w przypadku scentralizowane zespół IT, który jest odpowiedzialny za zarządzanie IT dla wielu różnych jednostek biznesowych. Dla uproszczenia tego dokumentu, używany jest termin *dostawcy usług* , ale te same funkcje jest również dostępny do przedsiębiorstwa i innych klientów.

Dla partnerów i dostawców usług, którzy należą do elementu [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program, analizy dzienników jest jednym z usług Azure, która jest dostępna w [subskrypcji Azure dostawcy usług Kryptograficznych](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Architektury dla dostawców usług

Obszary robocze analizy dziennika umożliwiają administratorowi sterowanie przepływem i izolacji dzienniki i utworzyć architekturę dziennika, która dotyczy jej potrzebami. [W tym artykule](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-manage-access) opisano ogólne zagadnienia dotyczące zarządzania obszaru roboczego. Dostawcy usług mają dodatkowe zagadnienia.

Istnieją trzy możliwe architektury dla dostawców usług dotyczących analizy dzienników obszarów roboczych:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Rozproszone — dzienniki są przechowywane w obszarach roboczych znajdujących się w dzierżawie klienta 

W ramach tej architektury obszaru roboczego zostanie wdrożona w dzierżawy przez klienta, która służy do wszystkie dzienniki tego klienta. Administratorzy dostawcy usług mają prawo dostępu do tego obszaru roboczego przy użyciu [gości usługi Azure Active Directory (B2B)](https://docs.microsoft.com/en-us/azure/active-directory/b2b/what-is-b2b). Administrator dostawcy usług należy przełączyć w portalu Azure do katalogu ich klienta, aby można było uzyskać dostępu do tych obszarów roboczych.

Zalety tej architektury są następujące:
* Klienta można zarządzać dostępem do dzienników przy użyciu własnych [dostępu opartej na rolach](https://docs.microsoft.com/en-us/azure/role-based-access-control/overview).
* Każdy klient może mieć różne ustawienia dla ich obszaru roboczego, takich jak przechowywania i danych są takie same.
* Izolację między klientami przepisami i zgodności.
* Opłata za każdym obszarze roboczym zostanie zawarta w subskrypcji klienta.
* Dzienniki mogą być zbierane z wszystkich typów zasobów, nie tylko agenta na podstawie. Na przykład inspekcji Azure.

Wady tej architektury są następujące:
* Jest trudniejsze dla dostawcy usług do zarządzania dużą liczbę klientów dzierżawcy na raz.
* Administratorzy dostawcy usług mają na potrzeby aprowizacji w katalogu klienta.
* Dostawca usług nie można przeanalizować danych przez jej klientów.

### <a name="2-central---logs-are-stored-in-workspace-located-in-the-service-provider-tenant"></a>2. Środkowo - dzienniki są przechowywane w obszarze roboczym znajduje się w dzierżawie dostawca usługi

W ramach tej architektury dzienniki nie są przechowywane w dzierżaw klienta, ale tylko w centralnej lokalizacji, w ramach jednej subskrypcji dostawcy usług. Agenci, którzy są instalowane na maszynach wirtualnych klienta są skonfigurowane do wysyłania dzienników do tego obszaru roboczego przy użyciu obszaru roboczego identyfikator i klucz tajny.

Zalety tej architektury są następujące:
* To ułatwia zarządzanie dużą liczbę klientów i ich integracji różnych systemów wewnętrznej bazy danych.
* Dostawcy usług ma pełne prawa własności przez dzienniki i różnych artefaktów, takich jak funkcje i zapisane kwerendy.
* Usługodawcy mogą wykonywać analizy dla wszystkich klientów.

Wady tej architektury są następujące:
* Trudno będzie do oddzielania danych między klientów. Jedynym dobrym metoda w tym celu jest do używania nazwy domeny komputera.
* Wszystkie dane ze wszystkich klientów będą przechowywane w tym samym regionie z jednym BOM i tego samego ustawienia przechowywania i konfiguracji.
* Sieci szkieletowej Azure i PaaS usług, takich jak diagnostyki Azure i inspekcji Azure wymaga obszar roboczy, aby być w tej samej dzierżawy jako zasób w związku z tym nie mogą wysyłać dzienniki do obszaru roboczego centralnej.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybrydowe — dzienniki są przechowywane w obszarze roboczym znajduje się w dzierżawie klienta i niektóre z nich są pobierane w centralnej lokalizacji.

Trzeci architektura łączyć się z dwóch opcji. Jest on oparty na pierwszym architektura rozproszona, których dzienniki znajdują się lokalnie do każdego klienta, ale tworzenie centralnym repozytorium dzienników przy użyciu mechanizmu. Część dzienniki są pobierane w centralnej lokalizacji dla raportowania i analiz. Ta część może być małą liczbą typów danych lub podsumowanie działań, takich jak statystyka codziennie.

Dostępne są dwie opcje do zaimplementowania centralnej lokalizacji w analizy dzienników:

1. Centralnej obszaru roboczego: dostawcę usługi można utworzyć obszaru roboczego w swojej dzierżawie i użyć skryptu, który używa [zapytania interfejsu API](https://dev.loganalytics.io/) z [interfejsu API kolekcji danych](log-analytics-data-collector-api.md) do przeniesienia danych z różnych obszarów roboczych do tego centralnej lokalizacji. Inną opcją niż skryptu jest użycie [aplikacji logiki Azure](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-overview).

2. Usługa Power BI jako lokalizacji centralnej: usługi Power BI może działać jako centralnej lokalizacji różnych obszarów roboczych eksportowania danych do niej przy użyciu integracji między analizy dzienników i [usługi Power BI](log-analytics-powerbi.md). 


## <a name="next-steps"></a>Następne kroki
* Zautomatyzować tworzenie i konfiguracja obszarów roboczych przy użyciu [szablonów Resource Manager](log-analytics-template-workspace-configuration.md)
* Zautomatyzować tworzenie obszarów roboczych przy użyciu [środowiska PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Użyj [alerty](log-analytics-alerts.md) do integracji z istniejącymi systemami
* Generowanie raportu podsumowania przy użyciu [usługi Power BI](log-analytics-powerbi.md)

