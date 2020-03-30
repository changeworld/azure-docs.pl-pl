---
title: Zakres kwerendy dziennika w usłudze Azure Monitor Log Analytics | Dokumenty firmy Microsoft
description: W tym artykule opisano zakres zakresu i zakresu czasu kwerendy dziennika w usłudze Azure Monitor Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2019
ms.openlocfilehash: 897eff62fcbab5996b6b9493bd825ae412aa4c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249596"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Zakres i zakres czasu kwerendy dziennika w usłudze Azure Monitor Log Analytics
Po uruchomieniu [kwerendy dziennika](log-query-overview.md) w [usłudze Log Analytics w witrynie Azure portal,](get-started-portal.md)zestaw danych ocenianych przez kwerendę zależy od zakresu i zakresu czasu, który można wybrać. W tym artykule opisano zakres i zakres czasu oraz sposób, w jaki można ustawić każdy w zależności od wymagań. Opisano w nim również zachowanie różnych typów zakresów.


## <a name="query-scope"></a>Zakres zapytania
Zakres kwerendy definiuje rekordy, które są oceniane przez kwerendę. Zazwyczaj obejmuje to wszystkie rekordy w jednym obszarze roboczym usługi Log Analytics lub aplikacji usługi Application Insights. Usługa Log Analytics umożliwia również ustawienie zakresu dla określonego monitorowanego zasobu platformy Azure. Dzięki temu właściciel zasobu skupić się tylko na swoich danych, nawet jeśli ten zasób zapisuje w wielu obszarach roboczych.

Zakres jest zawsze wyświetlany w lewym górnym rogu okna usługi Log Analytics. Ikona wskazuje, czy zakres jest obszarem roboczym usługi Log Analytics, czy aplikacją usługi Application Insights. Żadna ikona nie wskazuje innego zasobu platformy Azure.

![Zakres](media/scope/scope.png)

Zakres jest określany przez metodę używasz do uruchamiania usługi Log Analytics, a w niektórych przypadkach można zmienić zakres, klikając na niego. W poniższej tabeli wymieniono różne typy używanego zakresu i różne szczegóły dla każdego z nich.

| Zakres zapytania | Rekordy w zakresie | Jak wybrać | Zmiana zakresu |
|:---|:---|:---|:---|
| Obszar roboczy usługi Log Analytics | Wszystkie rekordy w obszarze roboczym usługi Log Analytics. | Wybierz **dzienniki** z menu **Usługi Azure Monitor** lub menu **obszarów roboczych usługi Log Analytics.**  | Można zmienić zakres na dowolny inny typ zasobu. |
| Aplikacja Usługi Application Insights | Wszystkie rekordy w aplikacji Usługi Application Insights. | Wybierz **pozycję Analytics** ze strony **Przegląd** usługi Application Insights. | Można tylko zmienić zakres do innej aplikacji usługi Application Insights. |
| Grupa zasobów | Rekordy utworzone przez wszystkie zasoby w grupie zasobów. Może zawierać dane z wielu obszarów roboczych usługi Log Analytics. | Z menu grupy zasobów wybierz **pozycję Dzienniki.** | Nie można zmienić zakresu.|
| Subskrypcja | Rekordy utworzone przez wszystkie zasoby w ramach subskrypcji. Może zawierać dane z wielu obszarów roboczych usługi Log Analytics. | Wybierz **dzienniki** z menu subskrypcji.   | Nie można zmienić zakresu. |
| Inne zasoby platformy Azure | Rekordy utworzone przez zasób. Może zawierać dane z wielu obszarów roboczych usługi Log Analytics.  | Z menu zasobów **wybierz pozycję Dzienniki.**<br>LUB<br>Wybierz **dzienniki** z menu **Usługi Azure Monitor,** a następnie wybierz nowy zakres. | Można tylko zmienić zakres na ten sam typ zasobu. |

### <a name="limitations-when-scoped-to-a-resource"></a>Ograniczenia w zakresie do zasobu

Gdy zakres kwerendy jest obszarem roboczym usługi Log Analytics lub aplikacją usługi Application Insights, dostępne są wszystkie opcje w portalu i wszystkie polecenia kwerendy. Gdy zakres do zasobu jednak następujące opcje w portalu nie są dostępne, ponieważ są one skojarzone z jednego obszaru roboczego lub aplikacji:

- Zapisz
- Eksplorator zapytań
- Nowa reguła alertu

Nie można użyć następujących poleceń w kwerendzie, gdy jest ograniczony do zasobu, ponieważ zakres kwerendy będzie już zawierać wszystkie obszary robocze z danymi dla tego zasobu lub zestawu zasobów:

- [App](app-expression.md)
- [Obszaru roboczego](workspace-expression.md)
 

## <a name="query-limits"></a>Limity zapytań
Możesz mieć wymagania biznesowe dla zasobu platformy Azure do zapisywania danych w wielu obszarach roboczych usługi Log Analytics. Obszar roboczy nie musi znajdować się w tym samym regionie co zasób, a pojedynczy obszar roboczy może zbierać dane z zasobów w różnych regionach.  

Ustawienie zakresu na zasób lub zestaw zasobów jest szczególnie zaawansowana cechą usługi Log Analytics, ponieważ umożliwia automatyczną konsolidację danych rozproszonych w jednej kwerendzie. Może to znacznie wpłynąć na wydajność, jeśli dane muszą być pobierane z obszarów roboczych w wielu regionach platformy Azure.

Usługa Log Analytics pomaga chronić przed nadmiernym obciążeniem przed zapytaniami obejmującymi obszary robocze w wielu regionach, wydając ostrzeżenie lub błąd, gdy używana jest określona liczba regionów. Kwerenda otrzyma ostrzeżenie, jeśli zakres zawiera obszary robocze w 5 lub więcej regionach. nadal będzie działać, ale może to zająć zbyt dużo czasu.

![Ostrzeżenie o kwerendzie](media/scope/query-warning.png)

Kwerenda zostanie zablokowana przed uruchomieniem, jeśli zakres zawiera obszary robocze w 20 lub więcej regionach. W takim przypadku zostanie wyświetlony monit o zmniejszenie liczby regionów obszaru roboczego i ponowną próbę uruchomienia kwerendy. W rozwijanej będzie wyświetlany wszystkie regiony w zakresie kwerendy i należy zmniejszyć liczbę regionów przed podjęciem próby ponownego uruchomienia kwerendy.

![Kwerenda nie powiodła się](media/scope/query-failed.png)


## <a name="time-range"></a>Przedział czasu
Zakres czasu określa zestaw rekordów, które są oceniane dla kwerendy na podstawie czasu utworzenia rekordu. Jest to zdefiniowane przez standardową właściwość dla każdego rekordu w obszarze roboczym lub aplikacji, jak określono w poniższej tabeli.

| Lokalizacja | Właściwość |
|:---|:---|
| Obszar roboczy usługi Log Analytics          | TimeGenerated |
| Aplikacja Usługi Application Insights | sygnatura czasowa     |

Ustaw zakres czasu, wybierając go z selektora czasu w górnej części okna usługi Log Analytics.  Można wybrać wstępnie zdefiniowany okres lub wybrać **opcję Niestandardowy,** aby określić określony zakres czasu.

![Selektor czasu](media/scope/time-picker.png)

Jeśli w kwerendzie zostanie ustawiony filtr, który używa właściwości czasu standardowego, jak pokazano w powyższej tabeli, selektor czasu zmieni się **na Ustaw w kwerendzie,** a selektor czasu zostanie wyłączony. W takim przypadku jest najbardziej efektywne, aby umieścić filtr w górnej części kwerendy, tak aby wszelkie kolejne przetwarzanie musi pracować tylko z filtrowanych rekordów.

![Kwerenda filtrowana](media/scope/query-filtered.png)

Jeśli używasz polecenia [obszaru roboczego](workspace-expression.md) lub [aplikacji](app-expression.md) do pobierania danych z innego obszaru roboczego lub aplikacji, selektor czasu może zachowywać się inaczej. Jeśli zakres jest obszarem roboczym usługi Log Analytics i używasz **aplikacji**, lub jeśli zakres jest aplikacją usługi Application Insights i używasz **obszaru roboczego,** usługa Log Analytics może nie zrozumieć, że właściwość używana w filtrze powinna określać filtr czasu.

W poniższym przykładzie zakres jest ustawiony na obszar roboczy usługi Log Analytics.  Kwerenda używa **obszaru roboczego** do pobierania danych z innego obszaru roboczego usługi Log Analytics. Selektor czasu zmienia się **na Ustaw w kwerendzie,** ponieważ widzi filtr, który używa oczekiwanej **timegenerated** właściwości.

![Kwerenda z obszarem roboczym](media/scope/query-workspace.png)

Jeśli kwerenda używa **aplikacji** do pobierania danych z aplikacji usługi Application Insights jednak, Usługa Log Analytics nie rozpoznaje właściwości **sygnatury czasowej** w filtrze, a selektor czasu pozostaje niezmieniona. W takim przypadku oba filtry są stosowane. W przykładzie tylko rekordy utworzone w ciągu ostatnich 24 godzin są uwzględniane w kwerendzie, nawet jeśli określa 7 dni w **where** klauzuli.

![Zapytanie z aplikacją](media/scope/query-app.png)

## <a name="next-steps"></a>Następne kroki

- Przejmij [samouczek dotyczący korzystania z usługi Log Analytics w witrynie Azure portal](get-started-portal.md).
- Przejdź przez [samouczek na temat pisania zapytań](get-started-queries.md).
