---
title: Rozwiązywanie problemów z automatyczną skalą platformy Azure
description: Śledzenie problemów ze skalowaniem automatycznym platformy Azure używanym w sieci szkieletowej usług, maszynach wirtualnych, aplikacjach sieci Web i usługach w chmurze.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: 9780cf88070110c4efc13c477d65307aa3985fe5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751334"
---
# <a name="troubleshooting-azure-autoscale"></a>Rozwiązywanie problemów z automatyczną skalą platformy Azure
 
Skalowanie automatyczne usługi Azure Monitor pomaga mieć odpowiednią ilość zasobów uruchomionych do obsługi obciążenia aplikacji. Umożliwia dodawanie zasobów do obsługi wzrost obciążenia, a także zaoszczędzić pieniądze, usuwając zasoby, które siedzą bezczynnie. Można skalować na podstawie wybranego harmonogramu, ustalonej daty i godziny lub metryki zasobu. Aby uzyskać więcej informacji, zobacz [Omówienie skalowania automatycznego](autoscale-overview.md).

Usługa skalowania automatycznego zapewnia metryki i dzienniki, aby zrozumieć, jakie akcje skalowania wystąpiły i oceny warunków, które doprowadziły do tych akcji. Odpowiedzi na pytania, takie jak:

- Dlaczego moja usługa była skalowana w poziomie lub w?
- Dlaczego moja usługa nie została skalowana?
- Dlaczego akcja skalowania automatycznego nie powiodła się?
- Dlaczego skalowanie automatyczne zajmuje czas na skalowanie?
  
## <a name="autoscale-metrics"></a>Dane skalowania automatycznego

Skalowanie automatyczne zapewnia [cztery metryki,](metrics-supported.md#microsoftinsightsautoscalesettings) aby zrozumieć jego działanie. 

- **Obserwowana wartość metryki** — wartość metryki wybranej do podjęcia akcji skalowania, jak widać lub obliczone przez aparat skalowania automatycznego. Ponieważ ustawienie skalowania automatycznego może mieć wiele reguł, a zatem wiele źródeł metryki, można filtrować przy użyciu "źródła metryki" jako wymiaru.
- **Próg metryki** — próg ustawiony do podjęcia akcji skalowania. Ponieważ ustawienie skalowania automatycznego może mieć wiele reguł, a zatem wiele źródeł metryki, można filtrować przy użyciu "reguły metryki" jako wymiaru.
- **Obserwowana pojemność** — aktywna liczba wystąpień zasobu docelowego widziana przez aparat skalowania automatycznego.
- **Zainicjowane akcje skalowania** — liczba akcji zwiększania i zmniejszania skali inicjowanych przez aparat skalowania automatycznego. Można filtrować według skalowania w poziomie a skalowania w akcjach.

[Explorer metryk](metrics-getting-started.md) służy do wykresu powyższych metryk w jednym miejscu. Wykres powinien być pokaźny:

  - rzeczywista metryka
  - metryki widocznej/obliczonej przez silnik skalowania automatycznego
  - próg dla działania skali
  - zmiana zdolności produkcyjnych 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>Przykład 1 - Analizowanie prostej reguły skalowania automatycznego 

Mamy proste ustawienie skalowania automatycznego dla zestawu skalowania maszyny wirtualnej, który:

- skaluje się, gdy średni procent procesora zestawu jest większy niż 70% przez 10 minut 
- skaluje się, gdy procent procesora cpu zestawu jest mniejszy niż 5% przez więcej niż 10 minut. 

Przejrzyjmy metryki z usługi skalowania automatycznego.
 
![Przykład procentowego zestawu procesora CPU w skali maszyny wirtualnej](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Przykład procentowego zestawu procesora CPU w skali maszyny wirtualnej](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***Rysunek 1a — procentowa metryka procesora CPU dla zestawu skalowania maszyny wirtualnej i metryka Obserwowana wartość metryki dla ustawienia skalowania automatycznego***

![Próg metryczny i obserwowana pojemność](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***Rysunek 1b - Próg metryczny i obserwowana pojemność***

Na rysunku 1b **próg metryki** (jasnoniebieska linia) dla reguły skalowania w poziomie wynosi 70.  **Obserwowana pojemność** (ciemnoniebieska linia) pokazuje liczbę aktywnych wystąpień, która jest obecnie 3. 

> [!NOTE]
> Należy filtrować **próg metryki** według reguły wymiaru wyzwalacza metryki w poziomie (zwiększ), aby wyświetlić próg skalowania w poziomie i według skali w regule (spadek). 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>Przykład 2 — Zaawansowane skalowanie automatyczne dla zestawu skalowania maszyny wirtualnej

Mamy ustawienie skalowania automatycznego, które umożliwia zasób zestawu skalowania maszyny wirtualnej na skalowanie w poziomie na podstawie własnej metryki **Przepływy wychodzące.** Należy zauważyć, że opcja **metryka podziału przez liczbę wystąpień** dla progu metryki jest zaznaczona. 

Reguła akcji skali to: 

Jeśli wartość **przepływu wychodzącego na wystąpienie** jest większa niż 10, usługa skalowania automatycznego powinna być skalowana w poziomie przez 1 wystąpienie. 

W takim przypadku obserwowana wartość metryki aparatu automatycznego jest obliczana jako rzeczywista wartość metryki podzielona przez liczbę wystąpień. Jeśli obserwowana wartość metryki jest mniejsza niż próg, nie jest inicjowana żadna akcja skalowania w poziomie. 
 
![Przykład wykresów metryk skalowania automatycznego maszyny wirtualnej](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Przykład wykresów metryk skalowania automatycznego maszyny wirtualnej](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***Rysunek 2 — przykład wykresów skalowania maszyny wirtualnej ustawiania metryk skalowania automatycznego***

Na rysunku 2 można zobaczyć dwa wykresy metryczne. 

Wykres na górze przedstawia rzeczywistą wartość metryki **Przepływy wychodzące.** Rzeczywista wartość wynosi 6. 

Wykres na dole pokazuje kilka wartości. 
 - **Obserwowana wartość metryczna** (jasnoniebieska) wynosi 3, ponieważ istnieją 2 aktywne wystąpienia, a 6 podzielone przez 2 to 3. 
 - **Obserwowana pojemność** (fioletowa) pokazuje liczbę wystąpień widzianą przez aparat skalowania automatycznego. 
 - **Próg metryczny** (jasnozielony) jest ustawiony na 10. 

Jeśli istnieje wiele reguł akcji skalowania, można użyć podziału lub opcji **dodaj filtr** na wykresie Eksploratora metryk, aby przyjrzeć się metryki według określonego źródła lub reguły. Aby uzyskać więcej informacji na temat dzielenia wykresu metrycznego, zobacz [Zaawansowane funkcje wykresów metrycznych — dzielenie](metrics-charts.md#apply-splitting-to-a-chart)

## <a name="example-3---understanding-autoscale-events"></a>Przykład 3 — Opis zdarzeń skalowania automatycznego

Na ekranie ustawień skalowania automatycznego przejdź do karty **Uruchom historię,** aby wyświetlić najnowsze akcje skalowania. Na karcie jest również wyświetlana zmiana **obserwowanej pojemności** w czasie. Aby znaleźć więcej szczegółów na temat wszystkich akcji skalowania automatycznego, w tym operacji, takich jak aktualizowanie/usuwanie ustawień skalowania automatycznego, wyświetl dziennik aktywności i filtruj według operacji skalowania automatycznego.

![Historia uruchamiania ustawień automatycznego skalowania](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Dzienniki zasobów skalowania automatycznego

Tak samo jak w przypadku innych zasobów platformy Azure usługa skalowania automatycznego udostępnia [dzienniki zasobów](platform-logs-overview.md). Istnieją dwie kategorie dzienników.

- **Oceny skalowania** automatycznego — aparat skalowania automatycznego rejestruje wpisy dziennika dla każdej oceny pojedynczego warunku za każdym razem, gdy wykonuje sprawdzanie.  Wpis zawiera szczegółowe informacje na temat obserwowanych wartości metryk, ocenianych reguł i jeśli ocena spowodowała akcję skali, czy nie.

- **Akcje skalowania automatycznego** — aparat rejestruje zdarzenia akcji skalowania zainicjowane przez usługę skalowania automatycznego i wyniki tych akcji skalowania (powodzenie, niepowodzenie i ile skalowania wystąpiły, jak widać przez usługę skalowania automatycznego).

Podobnie jak w przypadku każdej obsługiwanej usługi Usługi Azure Monitor, można użyć [ustawień diagnostycznych](diagnostic-settings.md) do rozsyłania tych dzienników:

- do obszaru roboczego usługi Log Analytics w celu uzyskania szczegółowej analizy
- do centrów zdarzeń, a następnie do narzędzi innych niż Azure
- do konta magazynu platformy Azure w celu archiwizacji  

![Ustawienia diagnostyczne automatycznego skalowania](media/autoscale-troubleshoot/diagnostic-settings.png)

Poprzedni obraz przedstawia ustawienia diagnostyczne skalowania automatycznego witryny azure portal. Tam można wybrać kartę Dzienniki diagnostyczne/zasobu i włączyć zbieranie i routing dzienników. Tę samą akcję można również wykonać przy użyciu interfejsów API REST, interfejsu wiersza polecenia, programu PowerShell, szablonów Menedżera zasobów dla ustawień diagnostycznych, wybierając typ zasobu jako *Microsoft.Insights/AutoscaleSettings*. 

## <a name="troubleshooting-using-autoscale-logs"></a>Rozwiązywanie problemów przy użyciu dzienników skalowania automatycznego 

Aby uzyskać najlepsze środowisko rozwiązywania problemów, zaleca się routing dzienników do dzienników usługi Azure Monitor logs (Log Analytics) za pośrednictwem obszaru roboczego podczas tworzenia ustawienia skalowania automatycznego. Ten proces jest pokazany na rysunku w poprzedniej sekcji. Można sprawdzić poprawność ocen i zwiększyć skalę akcji przy użyciu usługi Log Analytics.

Po skonfigurowaniu dzienników skalowania automatycznego do wysłania do obszaru roboczego usługi Log Analytics można wykonać następujące kwerendy, aby sprawdzić dzienniki. 

Aby rozpocząć, spróbuj tej kwerendy, aby wyświetlić najnowsze dzienniki oceny skalowania automatycznego:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Możesz też wypróbować następującą kwerendę, aby wyświetlić najnowsze dzienniki akcji skali:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Poniższe sekcje dotyczą tych pytań. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Wystąpiła akcja skali, której się nie spodziewałem

Najpierw wykonaj kwerendę dla akcji skalowania, aby znaleźć akcję skalowania, która Cię interesuje. Jeśli jest to najnowsza akcja skalowania, użyj następującej kwerendy:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Wybierz pole CorrelationId z dziennika akcji skalowania. Użyj CorrelationId, aby znaleźć odpowiedni dziennik oceny. Wykonanie poniższej kwerendy spowoduje wyświetlenie wszystkich reguł i warunków ocenionych prowadzących do tej akcji skalowania.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Jaki profil spowodował akcję skalowania?

Wystąpiła akcja skalowana, ale masz nakładające się reguły i profile i trzeba wyśledzić, które spowodowało akcję. 

Znajdź correlationId akcji skali (jak wyjaśniono w przykładzie 1), a następnie wykonać kwerendę w dziennikach oceny, aby dowiedzieć się więcej o profilu.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

Całą ocenę profilu można również lepiej zrozumieć za pomocą następującego zapytania

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Akcja skali nie wystąpiła

Spodziewałem się akcji skali i nie doszło. Może nie być żadnych zdarzeń akcji skali lub dzienników.

Przejrzyj metryki skalowania automatycznego, jeśli używasz reguły skalowania opartej na metrykach. Jest możliwe, że **obserwowana wartość metryczna** lub **obserwowana pojemność** nie są tym, czego się spodziewano i dlatego reguła skali nie została podpalona. Nadal będą widoczne oceny, ale nie reguła skalowania w poziomie. Jest również możliwe, że czas schładzania przechowywane działania skali z występujących. 

 Przejrzyj dzienniki oceny skalowania automatycznego w okresie oczekiwanym działania skalowania. Przejrzyj wszystkie oceny, które przeprowadził i dlaczego postanowiła nie uruchamiać akcji skali.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>Akcja skalowania nie powiodła się

Może się okazać, że usługa skalowania automatycznego podjęła akcję skalowania, ale system postanowił nie skalować lub nie ukończył akcji skalowania. Użyj tej kwerendy, aby znaleźć akcje skalowania nie powiodło się.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Tworzenie reguł alertów w celu uzyskania powiadomienia o akcjach lub błędach skalowania automatycznego. Można również utworzyć reguły alertów, aby otrzymywać powiadomienia o zdarzeniach skalowania automatycznego.

## <a name="schema-of-autoscale-resource-logs"></a>Schemat dzienników zasobów w automatycznym skalowaniu

Aby uzyskać więcej informacji, zobacz [automatyczne skalowanie dzienników zasobów](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>Następne kroki
Przeczytaj informacje o [najlepszych praktykach skalowania automatycznego](autoscale-best-practices.md). 
