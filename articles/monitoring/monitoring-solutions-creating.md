---
title: Utworzenie rozwiązania do zarządzania na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania obejmują scenariusze pakietów zarządzania na platformie Azure, która klientów można dodać do swojego obszaru roboczego analizy dzienników.  Ten artykuł zawiera szczegółowe informacje dotyczące tworzenia rozwiązań do zarządzania do użycia w środowisku lub udostępniane klientom.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 92089904941ae913f1992a4407083bfcae010f2d
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887860"
---
# <a name="design-and-build-a-management-solution-in-azure-preview"></a>Projektowanie i kompilowanie rozwiązania do zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> To jest wstępna dokumentacji do tworzenia rozwiązań do zarządzania na platformie Azure, które są obecnie w wersji zapoznawczej. Żadnego schematu opisanych poniżej może ulec zmianie.

[Rozwiązania do zarządzania]( monitoring-solutions.md) Podaj scenariusze zarządzania spakowanych klientów można dodać do swojego obszaru roboczego analizy dzienników.  Ten artykuł przedstawia podstawowy proces projektowania i utworzenie rozwiązania do zarządzania, które jest odpowiednie dla najbardziej typowych wymagań.  Jeśli dopiero zaczynasz do tworzenia rozwiązań do zarządzania można użyć tego procesu jako punkt początkowy i korzystać z założenia bardziej złożonych rozwiązań zgodnie z wymaganiami rozwijać.

## <a name="what-is-a-management-solution"></a>Co to jest rozwiązaniem do zarządzania?

Rozwiązania do zarządzania zawiera zasobów platformy Azure, które współpracują, aby osiągnąć zarządzania konkretnego scenariusza.  Są one zaimplementowane jako [szablony zarządzania zasobami](../azure-resource-manager/resource-manager-template-walkthrough.md) zawierających szczegółowe informacje dotyczące sposobu instalowania i konfigurowania ich zawartych w niej zasobów, po zainstalowaniu rozwiązania.

Podstawowa strategia jest zacząć od rozwiązania do zarządzania przez tworzenie pojedynczych składników w środowisku platformy Azure.  Po utworzeniu funkcji działa prawidłowo, następnie można uruchomić je do pakowania [plik rozwiązania zarządzania]( monitoring-solutions-solution-file.md). 


## <a name="design-your-solution"></a>Projektowanie własnego rozwiązania
Na poniższym diagramie przedstawiono najbardziej typowe wzorzec rozwiązania do zarządzania.  Różne składniki w tym wzorcu zostały omówione w poniżej.

![Rozwiązanie do zarządzania — omówienie](media/monitoring-solutions-creating/solution-overview.png)


### <a name="data-sources"></a>Źródła danych
Pierwszy krok w projektowaniu rozwiązania jest określenie danych, które wymagają z repozytorium analizy dzienników.  Te dane mogą być zbierane przez [źródła danych](../log-analytics/log-analytics-data-sources.md) lub [inne rozwiązanie]( monitoring-solutions.md), lub rozwiązanie może być konieczne podanie proces zbierania go.

Istnieją różne sposoby źródeł danych, które mogą być zbierane w repozytorium analizy dzienników, zgodnie z opisem w [źródeł danych w analizy dzienników](../log-analytics/log-analytics-data-sources.md).  Obejmuje zdarzenia w dzienniku zdarzeń systemu Windows, lub wygenerowane przez dziennik systemowy oprócz liczniki wydajności dla klientów z systemami Windows i Linux.  Można także gromadzić dane z zasobów platformy Azure, zebrane przez Azure Monitor.  

Jeśli potrzebujesz danych, który nie jest dostępny za pomocą dowolnej z dostępnych źródeł danych, a następnie można użyć [interfejsu API modułów zbierających dane HTTP](../log-analytics/log-analytics-data-collector-api.md) umożliwia zapisu danych do repozytorium analizy dzienników za pomocą dowolnego klienta, który można wywołać interfejsu API REST.  Najbardziej typowe metody zbierania danych niestandardowych w rozwiązaniu do zarządzania jest tworzenie [runbook automatyzacji Azure](../automation/automation-runbook-types.md) zbiera wymagane dane z zasobów platformy Azure lub zewnętrznego i używa interfejsu API modułów zbierających dane do zapisu do repozytorium.  

### <a name="log-searches"></a>Dziennik wyszukiwania
[Zaloguj się wyszukiwanie](../log-analytics/log-analytics-log-searches.md) służą do wyodrębniania i analizowanie danych w repozytorium analizy dzienników.  Są one używane przez widoki i alerty Oprócz umożliwienia użytkownikowi przeprowadzenie ad hoc analizy danych w repozytorium.  

Należy zdefiniować żadnych zapytań, które prawdopodobnie będą przydatne dla użytkownika, nawet jeśli nie są one używane przez wszystkie widoki lub alerty.  Te będą dostępne dla nich jako zapisane wyszukiwania w portalu, a można również uwzględnić je w [części wizualizacji listy kwerend](../log-analytics/log-analytics-view-designer-parts.md#list-of-queries-part) w widoku niestandardowym.

### <a name="alerts"></a>Alerty
[Alerty w analizy dzienników](../log-analytics/log-analytics-alerts.md) identyfikowanie problemów za pomocą [dziennika wyszukiwania](#log-searches) z danymi w repozytorium.  One Powiadom użytkownika, albo automatycznie uruchomić akcję w odpowiedzi. Określić różnych warunków alertów dla aplikacji i umieścić odpowiednie reguły alertów w pliku rozwiązania.

Jeśli ten problem można rozwiązać potencjalnie z zautomatyzowanego procesu, następnie zwykle utworzysz element runbook automatyzacji Azure do wykonywania tego korygowania.  Najbardziej Azure usługi można zarządzać za pomocą [poleceń cmdlet](/powershell/azure/overview) której będzie korzystać z elementu runbook do wykonania tych funkcji.

Jeśli rozwiązanie wymaga funkcji zewnętrznych w odpowiedzi na alert, a następnie można użyć [odpowiedzi elementu webhook](../log-analytics/log-analytics-alerts-actions.md).  Dzięki temu można wywołać zewnętrznej usługi sieci web wysyła informacje w alercie.

### <a name="views"></a>Widoki
Widoki analizy dzienników są używane do wizualizacji danych z repozytorium analizy dzienników.  Każde rozwiązanie zazwyczaj będzie zawierać pojedynczy widok o [Kafelek](../log-analytics/log-analytics-view-designer-tiles.md) która jest wyświetlana na głównym pulpicie nawigacyjnym użytkownika.  Widok może zawierać dowolną liczbę [części wizualizacji](../log-analytics/log-analytics-view-designer-parts.md) aby zapewnić różne wizualizacje zebranych danych użytkownika.

Możesz [Tworzenie niestandardowych widoków przy użyciu narzędzia Projektant widoku](../log-analytics/log-analytics-view-designer.md) który później można wyeksportować do uwzględnienia w pliku rozwiązania.  


## <a name="create-solution-file"></a>Utwórz plik rozwiązania
Po skonfigurowaniu i przetestowane składniki, które będą częścią rozwiązania, możesz [utworzyć plik rozwiązania]( monitoring-solutions-solution-file.md).  Wdroży składniki rozwiązania w [szablonu usługi Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) zawierającą [zasobów rozwiązania]( monitoring-solutions-solution-file.md#solution-resource) z relacjami do innych zasobów w pliku.  


## <a name="test-your-solution"></a>Testowanie rozwiązania
Gdy tworzysz rozwiązania, należy zainstalować i przetestować go w obszarze roboczym.  Można to zrobić przy użyciu dowolnej z dostępnych metod do [testowania i zainstalowanie szablonów usługi Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Publikowanie rozwiązania
Po wprowadzeniu i przetestowane rozwiązanie można udostępnić go do klientów za pośrednictwem następujących źródeł.

- **Szablony szybkiego startu usługi Azure**.  [Szablony szybkiego startu usługi Azure](https://azure.microsoft.com/resources/templates/) jest zestaw szablonów usługi Resource Manager zamieszczone przez społeczność za pośrednictwem usługi GitHub.  Można udostępnić rozwiązania przez następujące informacje w [przewodnik wkład](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  [Portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) służy do rozpowszechniania i sprzedawać rozwiązania inni deweloperzy ISV i specjalistów IT.  Znajdują się informacje dotyczące publikowanie rozwiązania do portalu Azure Marketplace w [jak publikowanie i zarządzanie nimi oferty w portalu Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).



## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się, jak [Utwórz plik rozwiązania]( monitoring-solutions-solution-file.md) rozwiązania do zarządzania.
* Poznaj lepiej [szablonów Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Wyszukiwanie [szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates) przykłady różnych szablonów usługi Resource Manager.