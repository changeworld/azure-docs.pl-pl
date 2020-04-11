---
title: Wycofywanie interfejsów API wdrażania dla usługi Azure Monitor i skalowania automatycznego
description: Metryki i automatyczne skalowanie klasycznych interfejsów API, nazywanych również modelem wdrażania usługi Azure Service Management (ASM) lub RDFE, które są wycofywane
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/19/2018
ms.openlocfilehash: d9b6118bc165a9b0a78ae9c4da895719249b22b7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114228"
---
# <a name="azure-monitor-retirement-of-classic-deployment-model-apis-for-metrics-and-autoscale"></a>Wycofanie klasycznego interfejsu API modelu wdrażania usługi Azure Monitor dla metryk i skalowania automatycznego

Usługa Azure Monitor (dawniej Usługa Azure Insights po pierwszym wydaniu) oferuje obecnie możliwość tworzenia i zarządzania ustawieniami skalowania automatycznego dla i korzystania z metryk z klasycznych maszyn wirtualnych i klasycznych usług w chmurze. Oryginalny zestaw klasycznych interfejsów API opartych na modelu wdrażania zostanie **wycofany po 30 czerwca 2019 r.** we wszystkich chmurach publicznych i prywatnych platformy Azure we wszystkich regionach.   

Te same operacje były obsługiwane za pośrednictwem zestawu interfejsów API opartych na usłudze Azure Resource Manager przez ponad rok. Portal Azure używa nowych interfejsów API REST dla skalowania automatycznego i metryk. Dostępne są również nowe interfejsy SDK, PowerShell i CLI oparte na tych interfejsach API Menedżera zasobów. Nasze usługi partnerskie do monitorowania zużywają nowe interfejsy API REST oparte na Menedżerze zasobów w usłudze Azure Monitor.  

## <a name="who-is-not-affected"></a>Kogo to nie dotyczy

Jeśli zarządzasz skalowaniem automatycznym za pośrednictwem witryny Azure Portal, nowe szablony [zestawów SDK,](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)PowerShell, CLI lub Resource Manager firmy Azure, nie jest konieczne żadne działanie.  

Jeśli korzystasz z metryk za pośrednictwem witryny Azure portal lub za pośrednictwem różnych [usług partnerów monitorowania,](../../azure-monitor/platform/partners.md)nie jest konieczne żadne działanie. Firma Microsoft współpracuje z partnerami monitoruja w celu migracji do nowych interfejsów API.

## <a name="who-is-affected"></a>Kogo dotyczy problem

Ten artykuł dotyczy użytkownika, jeśli używasz następujących składników:

- **Klasyczny zestaw SDK usługi Azure Insights** — jeśli używasz [klasycznego sdk usługi Azure Insights,](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)przełącz się na nowy zestaw SDK usługi Azure Monitor dla [platformy .NET](https://github.com/azure/azure-libraries-for-net#download) lub [Java](https://github.com/azure/azure-libraries-for-java#download). Pobierz [pakiet NuGet zestawu SDK usługi Azure Monitor](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/).

- **Klasyczna skalowanie automatyczne** — jeśli wywołujesz [klasyczne interfejsy API ustawień automatycznego](https://msdn.microsoft.com/library/azure/mt348562.aspx) z narzędzi utworzonych na zamówienie lub przy użyciu [klasycznego zestawu Azure Insights SDK,](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/)należy przełączyć się na interfejs [API REST usługi Resource Manager Azure Monitor](https://docs.microsoft.com/rest/api/monitor/autoscalesettings).

- **Klasyczne metryki** — jeśli korzystasz z metryk przy użyciu [klasycznych interfejsów API REST](https://msdn.microsoft.com/library/azure/dn510374.aspx) lub [klasycznego zestawu SDK usługi Azure Insights](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/) z narzędzi niestandardowych, należy przełączyć się do korzystania z interfejsu API REST usługi Resource Manager Azure [Monitor.](https://docs.microsoft.com/rest/api/monitor/autoscalesettings) 

Jeśli nie masz pewności, czy kod lub narzędzia niestandardowe dzwonią do klasycznych interfejsów API, zapoznaj się z następującymi tematami:

- Przejrzyj identyfikator URI, do którego odwołuje się kod lub narzędzie. Klasyczne interfejsy API używają https://management.core.windows.netidentyfikatora URI . Należy używać nowszego identyfikatora URI dla interfejsów `https://management.azure.com/`API opartych na Menedżerze zasobów zaczyna się od .

- Porównaj nazwę złożenia na komputerze. Starszy klasyczny montaż https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring/jest w .

- Jeśli używasz uwierzytelniania certyfikatu, aby uzyskać dostęp do metryk lub skalowania automatycznego interfejsów API, używasz klasycznego punktu końcowego i biblioteki. Nowsze interfejsy API Menedżera zasobów wymagają uwierzytelniania usługi Azure Active Directory za pośrednictwem jednostki usługi lub jednostki głównej użytkownika.

- Jeśli używasz wywołań, do których odwołuje się w dokumentacji w dowolnym z poniższych łączy, używasz starszych klasycznych interfejsów API.

  - [Biblioteka klas windows.Azure.Management.Monitoring](https://docs.microsoft.com/previous-versions/azure/dn510414(v=azure.100))

  - [Monitorowanie (klasyczne) .NET](https://docs.microsoft.com/previous-versions/azure/reference/mt348562(v%3dazure.100))

  - [Interfejs IMetricOperations](https://docs.microsoft.com/previous-versions/azure/reference/dn802395(v%3dazure.100))

## <a name="why-you-should-switch"></a>Dlaczego warto przełączyć

Wszystkie istniejące możliwości skalowania automatycznego i metryki będą nadal działać za pośrednictwem nowych interfejsów API.  

Migracja do nowszych interfejsów API jest dostępna z możliwościami opartymi na Menedżerze zasobów, takimi jak obsługa spójnej kontroli dostępu opartej na rolach (RBAC) we wszystkich usługach monitorowania. Zyskujesz również dodatkowe funkcje dla danych: 

- podparcie dla wymiarów
- spójna 1-minutowa szczegółowość metryczna we wszystkich usługach 
- lepsze wyszukiwanie zapytań
- większe przechowywanie danych (93 dni danych vs. 30 dni) 

Ogólnie rzecz biorąc, podobnie jak wszystkie inne usługi na platformie Azure, interfejsy API usługi Azure Monitor oparte na usłudze Resource Manager są wyposażone w lepszą wydajność, skalowalność i niezawodność. 

## <a name="what-happens-if-you-do-not-migrate"></a>Co się stanie, jeśli nie zostaniesz zmigrowane

### <a name="before-retirement"></a>Przed przejściem na emeryturę

Nie będzie żadnego bezpośredniego wpływu na usługi platformy Azure lub ich obciążeń.  

### <a name="after-retirement"></a>Po przejściu na emeryturę

Wszystkie wywołania klasycznych interfejsów API wymienionych wcześniej zakończy się niepowodzeniem i zwróci komunikaty o błędach podobne do następujących komunikatów:

W przypadku skalowania automatycznego: *ten interfejs API został przestarzały. Użyj szablonów Witryny Azure Portal, Azure Monitor SDK, PowerShell, CLI lub Resource Manager do zarządzania ustawieniami skalowania automatycznego.*  

W przypadku metryk: *ten interfejs API został przestarzały. Użyj witryny Azure Portal, Azure Monitor SDK, PowerShell, CLI, aby zbadać metryki.*

## <a name="email-notifications"></a>Powiadomienia e-mail

Powiadomienie o przejściu na emeryturę zostało wysłane na adresy e-mail dla następujących ról konta: 

- Administratorzy kont i usług
- Coadministrators  

Jeśli masz jakieś pytania, MonitorClassicAPIhelp@microsoft.comskontaktuj się z nami pod adresem .  

## <a name="references"></a>Dokumentacja

- [Nowsze interfejsy API REST dla usługi Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) 
- [Nowszy zestaw SDK monitora platformy Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)
