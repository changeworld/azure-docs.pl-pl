---
title: Utworzenie rozwiązania do zarządzania na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania obejmują scenariusze zarządzania spakowanych na platformie Azure, klienci można dodać do swojego obszaru roboczego usługi Log Analytics.  Ten artykuł zawiera szczegółowe informacje dotyczące tworzenia rozwiązania do zarządzania do użycia we własnym środowisku lub udostępniane klientom.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ef1af4d3d27bc098341a4de716e293557baa946a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595804"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Zaprojektować i zbudować rozwiązanie do zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> Jest to wersję wstępną dokumentacji dotyczące tworzenia rozwiązania do zarządzania na platformie Azure, które są obecnie w wersji zapoznawczej. Żadnego schematu opisanych poniżej ulec zmianie.

[Rozwiązania do zarządzania]( solutions.md) zapewniają pakietach zarządzania scenariusze klientów, można dodać do swojego obszaru roboczego usługi Log Analytics.  W tym artykule przedstawiono proces podstawowy, projektować i tworzyć rozwiązania do zarządzania, które jest odpowiednie dla najczęściej stawianych wymagań.  Jeśli dopiero zaczynasz tworzenie rozwiązań do zarządzania można użyć tego procesu jako punktu wyjścia i dopiero wówczas warto się z pojęciami dotyczącymi bardziej złożone rozwiązania w miarę ewolucji wymagań.

## <a name="what-is-a-management-solution"></a>Co to jest rozwiązanie do zarządzania?

Rozwiązania do zarządzania zawierają zasoby platformy Azure, które współpracują ze sobą, aby osiągnąć określony scenariusz zarządzania.  Są one implementowane jako [szablonami zarządzania zasobami](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) zawierających szczegółowe informacje o sposobie instalowania i konfigurowania ich zawartych zasobów, po zainstalowaniu rozwiązania.

Podstawowe strategia polega na rozwiązania do zarządzania na początek wbudowujemy poszczególne składniki w środowisku platformy Azure.  Gdy funkcja działa prawidłowo, następnie można uruchomić je do pakowania [plik rozwiązania zarządzania]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>Projektowanie własnego rozwiązania
Najczęstszym wzorcem rozwiązania do zarządzania jest wyświetlana na poniższym diagramie.  Różne składniki w tym wzorcu są omówione w poniżej.

![Omówienie rozwiązania zarządzania](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Źródła danych
Pierwszym krokiem projektowania rozwiązania jest określenie danych, które wymagają z repozytorium usługi Log Analytics.  Te dane mogą być zbierane przez [źródła danych](../../azure-monitor/platform/agent-data-sources.md) lub [inne rozwiązanie]( solutions.md), lub rozwiązania może być konieczne podanie proces się je zebrać.

Istnieje kilka sposobów źródeł danych, które mogą być zbierane w repozytorium usługi Log Analytics, zgodnie z opisem w [źródeł danych w usłudze Log Analytics](../../azure-monitor/platform/agent-data-sources.md).  Ta obejmuje zdarzenia w dzienniku zdarzeń Windows lub wygenerowane Syslog oprócz liczniki wydajności dla klientów systemów Windows i Linux.  Można także gromadzić dane z zasobów platformy Azure, zbieranych przez usługi Azure Monitor.  

Jeśli potrzebujesz danych, która nie jest dostępna za pośrednictwem dowolnych dostępnych źródeł danych, a następnie można użyć [interfejsu API modułu zbierającego dane HTTP](../../azure-monitor/platform/data-collector-api.md) pozwala zapisywać dane w repozytorium usługi Log Analytics za pomocą dowolnego klienta, który można wywołać interfejs API REST.  Najbardziej typowe oznacza, że zbieranie danych niestandardowych w rozwiązaniu do zarządzania jest utworzenie [elementu runbook w usłudze Azure Automation](../../automation/automation-runbook-types.md) który gromadzi informacje o wymaganych danych z zasobów platformy Azure lub zewnętrznych i używa interfejsu API modułu zbierającego dane do zapisu do repozytorium.  

### <a name="log-searches"></a>Wyszukiwanie w Dzienniku
[Przeszukiwanie dzienników](../../azure-monitor/log-query/log-query-overview.md) służą do wyodrębniania i analizować dane w repozytorium usługi Log Analytics.  Są one używane przez widoki i alerty, oprócz umożliwienia użytkownikowi przeprowadzenie analizy ad-hoc danych w repozytorium.  

Należy zdefiniować żadnych zapytań, które prawdopodobnie będą pomocne dla użytkowników, nawet jeśli nie są one używane przez wszystkie widoki i alerty.  Te będą dostępne dla nich jako zapisane wyszukiwania w portalu, a można również uwzględnić je w [części wizualizacji zapytań listy](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) w widoku niestandardowym.

### <a name="alerts"></a>Alerty
[Alerty w usłudze Log Analytics](../../azure-monitor/platform/alerts-overview.md) zidentyfikować problemy za pośrednictwem [dziennikach](#log-searches) względem danych w repozytorium.  One powiadomić użytkownika lub automatycznie uruchomić akcję w odpowiedzi. Należy zidentyfikować różnych warunków alertów dla aplikacji i umieścić odpowiednie reguły alertów w pliku rozwiązania.

Jeśli ten problem można rozwiązać potencjalnie przy użyciu zautomatyzowanego procesu, następnie zazwyczaj utworzysz element runbook w usłudze Azure Automation, aby to skorygować.  Większość usług platformy Azure można zarządzać za pomocą [poleceń cmdlet](/powershell/azure/overview) której element runbook będzie wykorzystywać do wykonywania takich funkcji.

Jeśli rozwiązanie wymaga funkcji zewnętrznych w odpowiedzi na alert, a następnie można użyć [odpowiedź elementu webhook](../../azure-monitor/platform/alerts-metric.md).  Umożliwia wywołanie zewnętrznej usługi internetowej wysyłanie informacji z poziomu alertu.

### <a name="views"></a>Widoki
Widoki w usłudze Log Analytics są używane do wizualizacji danych z repozytorium usługi Log Analytics.  Każde z tych rozwiązań zwykle będzie zawierać pojedynczy widok przy użyciu [Kafelek](../../azure-monitor/platform/view-designer-tiles.md) , jest wyświetlany na głównym pulpicie nawigacyjnym użytkownika.  Widok może zawierać dowolną liczbę [części wizualizacji](../../azure-monitor/platform/view-designer-parts.md) zapewnienie różnych wizualizacji zebranych danych do użytkownika.

Możesz [Tworzenie niestandardowych widoków przy użyciu projektanta widoku](../../azure-monitor/platform/view-designer.md) która później można wyeksportować do włączenia w pliku rozwiązania.  


## <a name="create-solution-file"></a>Utwórz plik rozwiązania
Po skonfigurowaniu i przetestowane składniki, które będą częścią rozwiązania, możesz [Utwórz plik rozwiązania]( solutions-solution-file.md).  Wdroży składniki rozwiązania [szablonu usługi Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md) zawierającej [zasobów rozwiązania]( solutions-solution-file.md#solution-resource) z relacjami do innych zasobów w pliku.  


## <a name="test-your-solution"></a>Przetestowanie rozwiązania
Podczas opracowywania rozwiązania, należy zainstalować i przetestować go w obszarze roboczym.  Można to zrobić przy użyciu dowolnej z dostępnych metod w celu [testu i zainstalować szablonów usługi Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Publikowanie rozwiązania
Po wprowadzeniu i przetestowane rozwiązanie można udostępnić go klientom za pośrednictwem następujących źródeł.

- **Szablony szybkiego startu platformy Azure**.  [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) to zbiór szablonów usługi Resource Manager pochodzących od społeczności za pośrednictwem witryny GitHub.  Możesz udostępnić swoje rozwiązanie przez następujące informacje w [przewodniku po współtworzeniu](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  [Portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) umożliwia dystrybucji i sprzedawać swoje rozwiązanie innych deweloperów niezależnych dostawców oprogramowania i profesjonalistów IT.  Możesz dowiedzieć się, jak opublikować swoje rozwiązanie w portalu Azure Marketplace w [sposób publikowania i zarządzania nimi oferty w portalu Azure Marketplace](../../marketplace/marketplace-publishers-guide.md).



## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [utworzenie pliku rozwiązania]( solutions-solution-file.md) dla rozwiązania do zarządzania.
* Poznaj lepiej [tworzenia usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
* Wyszukiwanie [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates) przykłady różnych szablonów usługi Resource Manager.
