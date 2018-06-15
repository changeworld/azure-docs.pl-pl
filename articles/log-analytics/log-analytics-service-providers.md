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
ms.author: richrund
ms.openlocfilehash: 6934e92df562099122eaede39fd26cf51cf1ee44
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31593053"
---
# <a name="log-analytics-features-for-service-providers"></a>Funkcje analizy dziennika dla dostawców usług
Analiza dzienników może pomóc dostawców usługi zarządzane (MSPs), dużych przedsiębiorstw niezależnym dostawcom oprogramowania (ISV) i dostawcy usług hostingowych zarządzanie i monitorowanie serwerów w lokalnym przez klienta lub infrastruktury chmury. 

Duże przedsiębiorstwa udostępniać wiele podobieństw dostawców usług, zwłaszcza w przypadku scentralizowane zespół IT, który jest odpowiedzialny za zarządzanie IT dla wielu różnych jednostek biznesowych. Dla uproszczenia tego dokumentu, używany jest termin *dostawcy usług* , ale te same funkcje jest również dostępny do przedsiębiorstwa i innych klientów.

## <a name="cloud-solution-provider"></a>Cloud Solution Provider
Dla partnerów i dostawców usług, którzy należą do elementu [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) program, analizy dzienników jest jednym z usług Azure, która jest dostępna w [subskrypcji Azure dostawcy usług Kryptograficznych](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

Dla analizy dzienników, następujące funkcje są włączone w *Cloud Solution Provider* subskrypcji.

Jako *Cloud Solution Provider* można wykonywać następujące czynności:

* Tworzenie obszarów roboczych usługi Analiza dzienników w ramach subskrypcji dzierżawy (klienta).
* Dostęp do obszarów roboczych utworzonych przez dzierżawców. 
* Dodawanie i usuwanie dostępu użytkownika do obszaru roboczego przy użyciu zarządzania użytkowników platformy Azure. Jeśli w obszarze roboczym dzierżawcy w portalu OMS strony zarządzania użytkownikami, w obszarze Ustawienia nie jest dostępny
  * Analiza dzienników nie obsługuje dostępu opartej na rolach jeszcze - nadanie użytkownika `reader` uprawnienia w portalu Azure pozwala na zmianę konfiguracji w portalu OMS

Aby zalogować się do subskrypcji dzierżawcy, należy określić identyfikator dzierżawy. Identyfikator dzierżawy jest często ostatnia część adres e-mail używany do logowania.

* W portalu OMS dodać `?tenant=contoso.com` w adresie URL portalu. Na przykład: `mms.microsoft.com/?tenant=contoso.com`
* W programie PowerShell, użyj `-Tenant contoso.com` parametr przy użyciu `Connect-AzureRmAccount` polecenia cmdlet
* Identyfikator dzierżawy jest automatycznie dodawane, gdy używasz `OMS portal` łącza z portalu Azure, aby otworzyć i zaloguj się do portalu OMS dla wybranego obszaru roboczego

Jako *klienta* programu Cloud Solution Provider można wykonywać następujące czynności:

* Tworzenie dziennika analytics obszarów roboczych w ramach subskrypcji dostawcy usług Kryptograficznych
* Dostęp do obszarów roboczych utworzone przez dostawcę usług Kryptograficznych
  * Użyj `OMS portal` łącza z portalu Azure, aby otworzyć i zaloguj się do portalu OMS dla wybranego obszaru roboczego
* Wyświetlanie i używanie strony zarządzania użytkownikami w obszarze Ustawienia w portalu OMS

> [!NOTE]
> Uwzględnione rozwiązania w zakresie tworzenia kopii zapasowych i odzyskiwania lokacji dla analizy dzienników, nie będą mogły nawiązać połączenia z magazynu usług odzyskiwania i nie można skonfigurować w ramach subskrypcji dostawcy usług Kryptograficznych. 
> 
> 

## <a name="managing-multiple-customers-using-log-analytics"></a>Zarządzanie wielu klientów przy użyciu analizy dzienników
Zalecane jest tworzenie obszaru roboczego analizy dzienników dla poszczególnych klientów, którym zarządzasz. Udostępnia obszaru roboczego analizy dzienników:

* Lokalizację geograficzną dla przechowywania danych. 
* Poziom szczegółowości rozliczeń 
* Izolacja danych 
* Unikatowy konfiguracji

Tworząc obszar roboczy na odbiorcy, jest możliwość przechowywania danych poszczególnych klientów oddzielne i również śledzić wykorzystanie każdego klienta.

Więcej informacji na temat kiedy i dlaczego można utworzyć wiele obszarów roboczych jest opisany w [zarządzanie dostępem do dziennika analizy](log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Tworzenie i Konfiguracja klienta obszarów roboczych można zautomatyzować za pomocą [PowerShell](log-analytics-powershell-workspace-configuration.md), [szablonów Resource Manager](log-analytics-template-workspace-configuration.md), lub za pomocą [interfejsu API REST](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

Korzystanie z szablonów usługi Resource Manager dla obszaru roboczego konfiguracji umożliwia wzorca konfiguracji, który może służyć do tworzenia i konfigurowania obszarów roboczych. Można mieć pewność, że podczas tworzenia obszarów roboczych dla klientów, a ich są automatycznie konfigurowane do wymagań. Po zaktualizowaniu wymagań szablon zostanie zaktualizowana i ponownie zastosować istniejących obszarów roboczych. Daje to pewność, że nawet istniejących obszarów roboczych spełniających określone standardy nowe.    

Podczas zarządzania wiele obszarów roboczych usługi Analiza dzienników, firma Microsoft zaleca integrowanie każdego obszaru roboczego z istniejącego systemu obsługi biletów / za pomocą konsoli operacje [alerty](log-analytics-alerts.md) funkcji. Dzięki integracji z istniejących systemów, zespół pomocy technicznej mogą nadal postępuj zgodnie z ich znanych procesów. Analiza dzienników regularnie sprawdza każdego obszaru roboczego kryteriów alertu, które określisz i generuje alert, gdy potrzebny jest akcja.

Dostosowanych widoków danych, można użyć [pulpitu nawigacyjnego](../azure-portal/azure-portal-dashboards.md) możliwości w portalu Azure.  

Wykonawczego poziomu raportów, które Podsumuj dane między obszarami roboczymi umożliwia integrację między usługą analizy dzienników i [PowerBI](log-analytics-powerbi.md). Jeśli chcesz zintegrować z innego systemu raportowania, można użyć interfejsu API Search (za pośrednictwem programu PowerShell lub [REST](log-analytics-log-search-api.md)) do wykonywania kwerend i eksportowanie wyników wyszukiwania.

## <a name="next-steps"></a>Następne kroki
* Zautomatyzować tworzenie i konfiguracja obszarów roboczych przy użyciu [szablonów Resource Manager](log-analytics-template-workspace-configuration.md)
* Zautomatyzować tworzenie obszarów roboczych przy użyciu [środowiska PowerShell](log-analytics-powershell-workspace-configuration.md) 
* Użyj [alerty](log-analytics-alerts.md) do integracji z istniejącymi systemami
* Generowanie raportu podsumowania przy użyciu [usługi Power BI](log-analytics-powerbi.md)

