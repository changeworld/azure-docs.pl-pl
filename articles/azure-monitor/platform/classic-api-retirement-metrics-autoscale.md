---
title: Azure Monitor procedury wycofywania klasycznego modelu wdrażania interfejsów API dla metryki i automatycznego skalowania
description: Metryki i automatyczne skalowanie klasyczne interfejsy API, nazywane również usługi Azure Service Management (ASM) lub modelu wdrażania przy użyciu frontonu REDDOG zostanie wycofana
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: robb
ms.openlocfilehash: ce54b63aa7831ed40a8592d536c43fc83fdc5567
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60709987"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Azure Monitor procedury wycofywania klasycznego modelu wdrażania interfejsów API dla metryki i automatycznego skalowania

Usługa Azure Monitor (dawniej Azure Insights po raz pierwszy zwolnieniu) obecnie oferuje możliwość tworzenia i Zarządzaj ustawieniami automatycznego skalowania i wykorzystywania metryki z klasycznych maszyn wirtualnych i klasycznej usługi w chmurze. Oryginalny zestaw będzie interfejsów API opartych na modelu wdrożenia klasycznego **wycofane po 30 czerwca 2019** w wszystkich publicznych i prywatnych chmurach Azure we wszystkich regionach.   

Te same operacje są obsługiwane za pośrednictwem interfejsów API dla na podstawie zestawu możliwości usługi Azure Resource Manager w ciągu roku. Witryny Azure portal korzysta z nowych interfejsów API REST dla skalowania automatycznego i metryki. Nowy zestaw SDK, PowerShell i interfejsu wiersza polecenia platformy w oparciu o te interfejsy API usługi Resource Manager są również dostępne. Nasze usługi partnera do monitorowania używać nowego Menedżera zasobów na podstawie interfejsów API REST w usłudze Azure Monitor.  

## <a name="who-is-not-affected"></a>Kto jest nie dotyczy problem

Jeśli zarządzasz automatycznego skalowania w witrynie Azure portal, [nowy zestaw SDK usługi Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/), szablonów programu PowerShell, interfejsu wiersza polecenia lub usługi Resource Manager, są wymagane żadne akcje.  

Jeśli używanym metryki w witrynie Azure portal lub za pośrednictwem różnych [monitorowania usług dla partnerów](../../azure-monitor/platform/partners.md), są wymagane żadne akcje. Firma Microsoft współpracuje z partnerami, aby migrować do nowych interfejsów API monitorowania.

## <a name="who-is-affected"></a>Kto dotyczy problem

W tym artykule mają zastosowanie do Ciebie, jeśli używane są następujące składniki:

- **Klasyczne zestawu SDK usługi Azure Insights** — Jeśli używasz [klasycznego zestawu SDK usługi Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), przełącz się do nowej usługi Azure Monitor zestawem SDK dla [.NET](https://github.com/azure/azure-libraries-for-net#download) lub [Java](https://github.com/azure/azure-libraries-for-java#download). Pobierz [pakietu NuGet zestawu SDK usługi Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Automatyczne skalowanie klasycznego** — Jeśli w przypadku wywoływania [ustawień automatycznego skalowania klasyczne interfejsy API](https://msdn.microsoft.com/library/azure/mt348562.aspx) z Twoimi narzędziami niestandardowej lub za pomocą [klasycznego zestawu SDK usługi Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/), należy przełączyć się do korzystania z [ Interfejs API REST Menedżera zasobów usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Metryki klasycznego** — Jeśli w przypadku używania metryki za pomocą [klasycznych interfejsów API REST](https://msdn.microsoft.com/library/azure/dn510374.aspx) lub [klasycznego zestawu SDK usługi Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) z niestandardowym narzędzi, powinien przełączasz się do korzystania z [ Interfejs API REST Menedżera zasobów usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/autoscalesettings). 

Jeśli wiesz, czy kodu lub narzędzia niestandardowe wywołania dotyczą klasyczne interfejsy API, sprawdź następujące czynności:

- Sprawdź identyfikator URI odwołania w kodzie albo narzędzia. Klasyczne interfejsy API, użyj identyfikatora URI https://management.core.windows.net. Należy używać nowszej identyfikator URI dla Menedżera zasobów na podstawie interfejsów API, który rozpoczyna się od https://management.azure.com/.

- Porównaj nazwę zestawu na swojej maszynie. Starsze klasycznego zestawu wynosi https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/.

- Dostęp do metryk ani funkcja automatycznego skalowania interfejsów API za pomocą uwierzytelniania certyfikatów, używasz klasycznej punktu końcowego i biblioteki. Na nowsze interfejsy API usługi Resource Manager wymaga uwierzytelniania usługi Azure Active Directory za pomocą nazwy głównej usługi lub nazwy głównej użytkownika.

- Jeśli używasz wywołań, do którego odwołuje się z dokumentacją w miejscach występowania dowolnego z poniższych linków, używasz starszej klasyczne interfejsy API.

  - [Biblioteka klas Windows.Azure.Management.Monitoring](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Monitorowanie .NET (model klasyczny)](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [Interfejs IMetricOperations](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Dlaczego należy przełączyć

Wszystkie istniejące możliwości automatycznego skalowania i metryki, będą w dalszym ciągu działać przy użyciu nowych interfejsów API.  

Migrowanie za pośrednictwem do nowszych interfejsów API są dostarczane z możliwości usługi Resource Manager, na podstawie, takie jak obsługa spójne opartej na rolach kontrola dostępu (RBAC) dla wszystkich usług monitorowania. Uzyskasz także dodatkowe funkcje metryki: 

- Obsługa wymiarów
- spójne 1 minutę metryki poziom szczegółowości dla wszystkich usług 
- lepiej zapytań
- wyższe przechowywanie danych (93 dni vs metryki. 30 dni) 

Ogólnie, podobnie jak inne usługi na platformie Azure Resource Manager, na podstawie usługi Azure Monitor, API dołączone lepszą wydajność, skalowalność i niezawodność. 

## <a name="what-happens-if-you-do-not-migrate"></a>Co się stanie, jeśli nie są migrowane

### <a name="before-retirement"></a>Przed wycofywania

Nie będzie żadnych bezpośredni wpływ na usługi platformy Azure lub ich obciążeń.  

### <a name="after-retirement"></a>Po wycofaniu

Wszelkie wywołania do klasycznego interfejsów API wymienionych wcześniej będzie się niepowodzeniem i zwróci komunikaty o błędach podobne poniższych:

Dla skalowania automatycznego: *Ten interfejs API jest przestarzały. Użycie witryny Azure portal, szablonów zestawu SDK usługi Azure Monitor, programu PowerShell, interfejsu wiersza polecenia lub usługi Resource Manager do zarządzania ustawieniami skalowania automatycznego*.  

Aby uzyskać metryki: *Ten interfejs API jest przestarzały. Użyj witryny Azure portal, zestawu SDK usługi Azure Monitor programu PowerShell, interfejsu wiersza polecenia, aby wykonać zapytanie dotyczące metryk*.

## <a name="email-notifications"></a>Powiadomienia e-mail

Powiadomienie o wycofaniu została wysłana na adresy dla następujących ról konta e-mail: 

- Administratorzy usługi oraz konta
- Współadministratorów  

Jeśli masz jakieś pytania, skontaktuj się z nami pod adresem MonitorClassicAPIhelp@microsoft.com.  

## <a name="references"></a>Dokumentacja

- [Nowsze interfejsy API REST dla usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) 
- [Monitor Azure nowszej zestawu SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
