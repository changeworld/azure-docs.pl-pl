---
title: Tworzenie rozwiązania do zarządzania na platformie Azure | Dokumenty firmy Microsoft
description: Rozwiązania do zarządzania obejmują scenariusze zarządzania pakietami na platformie Azure, które klienci mogą dodać do swojego obszaru roboczego usługi Log Analytics.  Ten artykuł zawiera szczegółowe informacje na temat tworzenia rozwiązań do zarządzania, które mają być używane w środowisku lub udostępniane klientom.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f1605597c7716ba6a896c7ecdae968f07d66027b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663219"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> Jest to wstępna dokumentacja do tworzenia rozwiązań do zarządzania na platformie Azure, które są obecnie w wersji zapoznawczej. Każdy schemat opisany poniżej może ulec zmianie.

[Rozwiązania do zarządzania]( solutions.md) zapewniają scenariusze zarządzania pakietami, które klienci mogą dodać do swojego obszaru roboczego usługi Log Analytics.  W tym artykule przedstawiono podstawowy proces projektowania i tworzenia rozwiązania do zarządzania, które jest odpowiednie dla najbardziej typowych wymagań.  Jeśli dopiero zaczynasz tworzenie rozwiązań do zarządzania, możesz użyć tego procesu jako punktu wyjścia, a następnie wykorzystać koncepcje bardziej złożonych rozwiązań w miarę rozwoju wymagań.

## <a name="what-is-a-management-solution"></a>Co to jest rozwiązanie do zarządzania?

Rozwiązania do zarządzania zawierają zasoby platformy Azure, które współpracują ze sobą w celu osiągnięcia określonego scenariusza zarządzania.  Są one implementowane jako [szablony zarządzania zasobami,](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) które zawierają szczegółowe informacje na temat instalowania i konfigurowania zawartych zasobów po zainstalowaniu rozwiązania.

Podstawową strategią jest uruchomienie rozwiązania do zarządzania przez tworzenie poszczególnych składników w środowisku platformy Azure.  Gdy funkcja działa poprawnie, możesz rozpocząć pakowanie ich do [pliku rozwiązania do zarządzania]( solutions-solution-file.md). 


## <a name="design-your-solution"></a>Zaprojektuj swoje rozwiązanie
Najbardziej typowy wzorzec rozwiązania do zarządzania jest pokazany na poniższym diagramie.  Różne składniki w tym wzorze są omówione w poniższej.

![Omówienie rozwiązań do zarządzania](media/solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Źródła danych
Pierwszym krokiem w projektowaniu rozwiązania jest określenie danych, które są wymagane z repozytorium usługi Log Analytics.  Dane te mogą być gromadzone przez [źródło danych](../../azure-monitor/platform/agent-data-sources.md) lub [inne rozwiązanie,]( solutions.md)lub rozwiązanie może być konieczne, aby zapewnić proces ich zbierania.

Istnieje wiele sposobów, w jakie źródła danych mogą być zbierane w repozytorium usługi Log Analytics zgodnie z opisem w [źródło danych w usłudze Log Analytics.](../../azure-monitor/platform/agent-data-sources.md)  Obejmuje to zdarzenia w dzienniku zdarzeń systemu Windows lub generowane przez Syslog oprócz liczników wydajności dla klientów systemu Windows i Linux.  Można również zbierać dane z zasobów platformy Azure zebranych przez usługę Azure Monitor.  

Jeśli potrzebujesz danych, które nie są dostępne za pośrednictwem dowolnego z dostępnych źródeł danych, można użyć [interfejsu API modułu zbierającego dane HTTP,](../../azure-monitor/platform/data-collector-api.md) który umożliwia zapisywanie danych w repozytorium usługi Log Analytics od dowolnego klienta, który może wywołać interfejs API REST.  Najczęstszym sposobem niestandardowego zbierania danych w rozwiązaniu do zarządzania jest utworzenie [elementów runbook w usłudze Azure Automation,](../../automation/automation-runbook-types.md) która zbiera wymagane dane z platformy Azure lub zasobów zewnętrznych i używa interfejsu API modułu zbierającego dane do zapisu w repozytorium.  

### <a name="log-searches"></a>Wyszukiwanie w dzienniku
[Wyszukiwanie dzienników](../../azure-monitor/log-query/log-query-overview.md) służy do wyodrębniania i analizowania danych w repozytorium usługi Log Analytics.  Są one używane przez widoki i alerty oprócz umożliwienia użytkownikowi wykonywania analizy ad hoc danych w repozytorium.  

Należy zdefiniować wszelkie zapytania, które uważasz, że będą pomocne dla użytkownika, nawet jeśli nie są one używane przez żadnych widoków lub alertów.  Będą one dostępne jako zapisane wyszukiwania w portalu, a także można je uwzględnić w [części wizualizacji listy zapytań](../../azure-monitor/platform/view-designer-parts.md#list-of-queries-part) w widoku niestandardowym.

### <a name="alerts"></a>Alerty
[Alerty w usłudze Log Analytics](../../azure-monitor/platform/alerts-overview.md) identyfikują problemy za pomocą [wyszukiwania w dzienniku](#log-searches) danych w repozytorium.  Powiadamiają użytkownika lub automatycznie uruchamiają akcję w odpowiedzi. Należy zidentyfikować różne warunki alertu dla aplikacji i dołączyć odpowiednie reguły alertów w pliku rozwiązania.

Jeśli problem można potencjalnie rozwiązać za pomocą zautomatyzowanego procesu, zazwyczaj utworzysz element runbook w usłudze Azure Automation, aby wykonać to rozwiązanie korygowane.  Większość usług platformy Azure można zarządzać za pomocą [poleceń cmdlet,](/powershell/azure/overview) które runbook będzie korzystać do wykonywania takich funkcji.

Jeśli rozwiązanie wymaga funkcji zewnętrznych w odpowiedzi na alert, można użyć [odpowiedzi elementu webhook](../../azure-monitor/platform/alerts-metric.md).  Dzięki temu można wywołać zewnętrzną usługę sieci web wysyłającą informacje z alertu.

### <a name="views"></a>Widoki
Widoki w usłudze Log Analytics służą do wizualizacji danych z repozytorium usługi Log Analytics.  Każde rozwiązanie zazwyczaj zawiera pojedynczy widok z [kafelkiem,](../../azure-monitor/platform/view-designer-tiles.md) który jest wyświetlany na głównym pulpicie nawigacyjnym użytkownika.  Widok może zawierać dowolną liczbę [części wizualizacji,](../../azure-monitor/platform/view-designer-parts.md) aby zapewnić użytkownikowi różne wizualizacje zebranych danych.

Widoki niestandardowe można [utworzyć przy użyciu Projektanta widoku,](../../azure-monitor/platform/view-designer.md) które można później wyeksportować do włączenia do pliku rozwiązania.  


## <a name="create-solution-file"></a>Tworzenie pliku rozwiązania
Po skonfigurowaniu i przetestowaniu składników, które będą częścią rozwiązania, można [utworzyć plik rozwiązania]( solutions-solution-file.md).  Zaimplementujesz składniki rozwiązania w [szablonie Menedżera zasobów,](../../azure-resource-manager/templates/template-syntax.md) który zawiera [zasób rozwiązania]( solutions-solution-file.md#solution-resource) z relacjami z innymi zasobami w pliku.  


## <a name="test-your-solution"></a>Przetestuj swoje rozwiązanie
Podczas opracowywania rozwiązania należy zainstalować i przetestować je w obszarze roboczym.  Można to zrobić za pomocą dowolnej z dostępnych metod [testowania i instalowania szablonów Menedżera zasobów](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="publish-your-solution"></a>Publikowanie rozwiązania
Po zakończeniu i przetestowaniu rozwiązania można udostępnić je klientom za pośrednictwem następujących źródeł.

- **Szablony szybki start platformy Azure**.  [Szablony szybki start platformy Azure](https://azure.microsoft.com/resources/templates/) to zestaw szablonów Usługi Resource Manager współtworzonych przez społeczność za pośrednictwem usługi GitHub.  Rozwiązanie można udostępnić, postępając zgodnie z informacjami zawartymi w [przewodniku po wkładach](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Portal Azure Marketplace**.  [Portal Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) umożliwia dystrybucję i sprzedaż rozwiązania innym deweloperom, dostawcom oprogramowania i specjalistom IT.  Możesz dowiedzieć się, jak opublikować rozwiązanie w portalu Azure Marketplace w [witrynie Jak opublikować ofertę i zarządzać nią w portalu Azure Marketplace.](../../marketplace/marketplace-publishers-guide.md)



## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [utworzyć plik rozwiązania]( solutions-solution-file.md) dla rozwiązania do zarządzania.
* Poznaj szczegóły tworzenia [szablonów usługi Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
* Przeszukaj [szablony szybki start platformy Azure](https://azure.microsoft.com/documentation/templates) w poszukiwaniu przykładów różnych szablonów Menedżera zasobów.
