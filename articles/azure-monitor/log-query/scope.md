---
title: Zaloguj się zakres zapytania w usłudze Azure Monitor Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano zakres zakresu i czasu dla zapytania dzienników w usłudze Azure Monitor Log Analytics.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: a948b80f6524339f0908a2fb19c4a83d70b3b140
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297198"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Zapytanie zakresu i czasu zakresu dziennika w usłudze Azure Monitor Log Analytics
Po uruchomieniu [zapytanie dziennika](log-query-overview.md) w [usługi Log Analytics w witrynie Azure portal](get-started-portal.md), zestaw danych oceniany przez zapytanie zależy od zakresu i przedziału czasu, który wybierzesz. W tym artykule opisano zakres zakresu i czasu i jak można ustawić każdy w zależności od wymagań. Omówiono także zachowanie różnego rodzaju zakresów.


## <a name="query-scope"></a>Zakres kwerendy
Zakres zapytania definiuje rekordów, które są oceniane przez zapytanie. Zwykle będzie to obejmowało wszystkie rekordy w jeden obszar roboczy usługi Log Analytics lub aplikacji usługa Application Insights. Usługi log Analytics umożliwia także ustawić zakresu dla określonego monitorowanych zasobów platformy Azure. Dzięki temu właściciela zasobu skoncentrować się tylko na ich danych nawet wtedy, gdy ten zasób zapisuje do wielu obszarów roboczych.

Zakres jest zawsze wyświetlany u góry po lewej części okna usługi Log Analytics. Ikona wskazuje, czy zakres jest obszarem roboczym usługi Log Analytics lub aplikacji usługa Application Insights. Brak ikony wskazuje inny zasób platformy Azure.

![Scope](media/scope/scope.png)

Zakres jest określany przez metodę używasz można uruchomić usługi Log Analytics, a w niektórych przypadkach można zmienić zakres, klikając ją. W poniższej tabeli wymieniono różne rodzaje zakres używany i różnych szczegółów dla każdego.

| Zakres kwerendy | Rekordy w zakresie | Jak wybrać | Zmiana zakresu |
|:---|:---|:---|:---|
| Obszar roboczy usługi log Analytics | Wszystkie rekordy w obszarze roboczym usługi Log Analytics. | Wybierz **dzienniki** z **usługi Azure Monitor** menu lub **obszarów roboczych usługi Log Analytics** menu.  | Można zmienić zakres do jakichkolwiek innych typów zasobów. |
| Application Insights aplikacji | Wszystkie rekordy w aplikacji usługi Application Insights. | Wybierz **Analytics** z **Przegląd** strony usługi Application Insights. | Można zmienić tylko zakres do innej aplikacji w usłudze Application Insights. |
| Grupa zasobów | Rekordy utworzone przez wszystkie zasoby w grupie zasobów. Może zawierać dane z wielu obszarów roboczych usługi Log Analytics. | Wybierz **dzienniki** z menu grupy zasobów. | Nie można zmienić zakres.|
| Subskrypcja | Rekordy utworzone przez wszystkie zasoby w subskrypcji. Może zawierać dane z wielu obszarów roboczych usługi Log Analytics. | Wybierz **dzienniki** menu subskrypcji.   | Nie można zmienić zakres. |
| Inne zasoby platformy Azure | Rekordy zasobu. Może zawierać dane z wielu obszarów roboczych usługi Log Analytics.  | Wybierz **dzienniki** z menu zasobów.<br>LUB<br>Wybierz **dzienniki** z **usługi Azure Monitor** menu, a następnie wybierz nowy zakres. | Można zmienić tylko zakres na ten sam typ zasobu. |

### <a name="limitations-when-scoped-to-a-resource"></a>Ograniczenia w przypadku zakresu do zasobu

Zakres kwerendy jest obszarem roboczym usługi Log Analytics lub aplikacji usługa Application Insights, wszystkie opcje w portalu i wszystkie polecenia zapytania są dostępne. Gdy jednak ograniczone do zasobu, następujące opcje w portalu nie są dostępne, ponieważ są one skojarzone z jednego obszaru roboczego lub aplikacji:

- Zapisanie
- Eksplorator zapytań
- Nowa reguła alertu

W zapytaniu, gdy ograniczone do zasobu, ponieważ zakres kwerendy już będą obejmować żadnych obszarów roboczych, wraz z danymi dotyczącymi tego zasobu lub zestaw zasobów, nie można użyć następujących poleceń:

- [Aplikacja](app-expression.md)
- [Obszar roboczy](workspace-expression.md)
 


## <a name="time-range"></a>Przedział czasu
Zakres czasu określa zestaw rekordów, które są oceniane pod kątem zapytań, w zależności od momentu utworzenia rekordu. To jest definiowany przez standardowe właściwości każdego rekordu w obszarze roboczym lub aplikacji, jak to określono w poniższej tabeli.

| Lokalizacja | Właściwość |
|:---|:---|
| Obszar roboczy usługi log Analytics          | TimeGenerated |
| Application Insights aplikacji | timestamp     |

Ustaw zakres czasu, wybierając z selektora czasu, w górnej części okna usługi Log Analytics.  Można wybrać okres wstępnie zdefiniowanych lub wybierz **niestandardowe** do określenia z określonego zakresu czasu.

![Selektor godziny](media/scope/time-picker.png)

Jeśli ustawisz filtr w zapytaniu, który używa właściwości (czas standardowy), jak pokazano w powyższej tabeli, selektor czasu zmieni się na **Ustaw w zapytaniu**, a selektor czasu jest wyłączony. W takim przypadku najlepiej jest umieścić filtru w górnej części zapytania, tak aby dalszego przetwarzania tylko potrzebne do pracy z przefiltrowane rekordy.

![Zapytania filtrowanego](media/scope/query-filtered.png)

Jeśli używasz [obszaru roboczego](workspace-expression.md) lub [aplikacji](app-expression.md) polecenia do pobierania danych z innego obszaru roboczego lub aplikacji, selektor czasu może zachowywać się inaczej. Jeśli zakres jest obszarem roboczym usługi Log Analytics i korzystać z **aplikacji**, lub jeśli zakres jest aplikacja usługi Application Insights i używasz **obszaru roboczego**, a następnie usługi Log Analytics mogą nie rozumieć, że właściwość jest używana w Filtr należy określić filtr czasu.

W poniższym przykładzie ustawiono zakres, do obszaru roboczego usługi Log Analytics.  Zapytanie używa **obszaru roboczego** do pobierania danych z innym obszarem roboczym usługi Log Analytics. Selektor godziny zmieni się na **Ustaw w zapytaniu** ponieważ widzi on filtr, który używa oczekiwanej **TimeGenerated** właściwości.

![Wykonywać zapytania za pomocą obszaru roboczego](media/scope/query-workspace.png)

Jeśli zapytanie używa **aplikacji** do pobierania danych z aplikacji usługi Application Insights do usługi Log Analytics nie rozpoznaje **sygnatura czasowa** właściwości w filtrze i selektor czasu pozostaje bez zmian. W takim przypadku zarówno filtry są stosowane. W przykładzie, tylko rekordy utworzone w ciągu ostatnich 24 godzin są uwzględnione w zapytaniu, nawet, jeśli Określa on 7 dni w **gdzie** klauzuli.

![Wykonywać zapytania za pomocą aplikacji](media/scope/query-app.png)

## <a name="next-steps"></a>Kolejne kroki

- Przeprowadzenie [samouczek na temat korzystania z usługi Log Analytics w witrynie Azure portal](get-started-portal.md).
- Przeprowadzenie [samouczek na temat pisania zapytań](get-started-queries.md).