---
title: Opis ustawień skalowania automatycznego w usłudze Azure Monitor
description: Szczegółowy podział ustawień automatycznego skalowania i sposobie ich działania. Ma zastosowanie do maszyn wirtualnych, usług Cloud Services, Web Apps
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 02840b8a909f46c37130bdb7162674c694a0ff96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60787499"
---
# <a name="understand-autoscale-settings"></a>Omówienie ustawień automatycznego skalowania
Ustawienia automatycznego skalowania pomóc, upewnij się, że masz odpowiednią ilość zasobów do obsługi zmian obciążenia aplikacji. Można skonfigurować ustawienia automatycznego skalowania wyzwolenie oparciu metryki, które wskazują obciążenia lub wydajności lub wyzwalane w zaplanowanym czasie. Ten artykuł przedstawia szczegółowy widok anatomia ustawienia automatycznego skalowania. Artykuł zaczyna się od schematu i właściwości ustawienia, a następnie przeprowadzi typy innego profilu, które można skonfigurować. Na koniec, w tym artykule omówiono sposób funkcja automatycznego skalowania na platformie Azure ocenia profilu, który można wykonać w dowolnym momencie.

## <a name="autoscale-setting-schema"></a>Schemat ustawień automatycznego skalowania
Aby zilustrować schematu ustawienie skalowania automatycznego, służy następujące ustawienie automatycznego skalowania. Należy pamiętać, że to ustawienie automatycznego skalowania ma jest:
- Jeden profil. 
- Dwie reguły metryki, w tym profilu: jeden dla skalowania w poziomie i jeden skalowanie w pionie.
  - Reguła skalowania w poziomie jest wyzwalany, gdy zestaw skalowania maszyn wirtualnych średnią wartość procentową procesora CPU metryka jest większy niż 85 procent dla ostatnich 10 minut.
  - Reguły skalowania jest wyzwalany, gdy średnia zestawu skalowania maszyn wirtualnych jest mniej niż 60% w ciągu ostatnich minuty.

> [!NOTE]
> To ustawienie może mieć wiele profilów. Aby dowiedzieć się więcej, zobacz [profile](#autoscale-profiles) sekcji. Profil może być również wiele reguł skalowania w poziomie i zdefiniowanych reguł skalowania na zewnątrz. Aby dowiedzieć się, jak są obliczane, zobacz [oceny](#autoscale-evaluation) sekcji.

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| `Section` | Nazwa elementu | Opis |
| --- | --- | --- |
| Ustawienie | id | Identyfikator zasobu Ustawienia skalowania automatycznego Ustawienia automatycznego skalowania są zasobami usługi Azure Resource Manager. |
| Ustawienie | name | Nazwa ustawienia skalowania automatycznego. |
| Ustawienie | location | Lokalizacja ustawienie skalowania automatycznego. Ta lokalizacja może być inna niż lokalizacja zasobu jest skalowany. |
| properties | targetResourceUri | Identyfikator zasobu zasobu jest skalowany. Może mieć tylko jedno ustawienie automatycznego skalowania dla każdego zasobu. |
| properties | profiles | Ustawienia automatycznego skalowania składa się z co najmniej jeden profil. Przy każdym uruchomieniu aparat skalowania automatycznego, wykonywany jest jeden profil. |
| profile | name | Nazwa profilu. Możesz wybrać dowolną nazwę, ułatwiające identyfikację profilu. |
| profile | Capacity.Maximum | Maksymalna pojemność dozwolone. Zapewnia automatyczne skalowanie podczas wykonywania tego profilu, Skaluj zasób powyżej tej liczby. |
| profile | Capacity.minimum | Pojemność minimalne dozwolone. Zapewnia automatyczne skalowanie podczas wykonywania tego profilu skalowania zasobu pod ten numer. |
| profile | Capacity.default | Jeśli występuje problem z odczytem metryki zasobu (w tym przypadku Procesora "vmss1"), a bieżąca pojemność jest niższa wartość domyślna, skalowania automatycznego skalowania do domyślnego. To jest zapewnienie dostępności zasobów. Jeśli bieżąca pojemność jest już wyższa niż pojemność domyślna, automatycznego skalowania nie jest skalowany w. |
| profile | rules | Automatyczne skalowanie jest skalowana automatycznie w miarę między maksymalne i minimalne możliwości, za pomocą reguł w profilu. Możesz mieć wiele reguł w profilu. Zwykle istnieją dwie reguły: jedną do określenia, kiedy do skalowania w poziomie, a druga do określenia, kiedy umożliwiającą skalowanie w pionie. |
| rule | metricTrigger | Definiuje metryki warunku reguły. |
| metricTrigger | metricName | Nazwa metryki. |
| metricTrigger |  metricResourceUri | Identyfikator zasobu dla zasobu, który emituje metryki. W większości przypadków jest taka sama jak zasób jest skalowany. W niektórych przypadkach może być inny. Na przykład można skalować zestaw skalowania maszyn wirtualnych na podstawie liczby komunikatów w kolejce magazynu. |
| metricTrigger | timeGrain | Czas trwania próbkowania metryk. Na przykład **TimeGrain = "PT1M"** oznacza, że metryki powinna zostać zagregowana co 1 minutę przy użyciu metody agregacji określonej w elemencie statystyk. |
| metricTrigger | statistic | Metoda agregacji w okresie ziarna czasu. Na przykład **Statystyka = "Average"** i **timeGrain = "PT1M"** oznacza, że metryki powinna zostać zagregowana co 1 minutę przy średniej. Ta właściwość decyduje o tym, jak są próbkowane tak metrykę. |
| metricTrigger | timeWindow | Ilość czasu, aby wyszukać metryki. Na przykład **timeWindow = "PT10M"** oznacza, że za każdym razem, gdy działa automatyczne skalowanie, wysyła zapytanie metryki dla ostatnich 10 minut. Przedział czasu umożliwia metryki będą normalizowane i zapobiega reagowaniu na przejściowe skoki. |
| metricTrigger | timeAggregation | Metoda agregacji używana do agregowania próbkowanych metryk. Na przykład **TimeAggregation = "Average"** powinien zagregowanie próbkowanych metryk przy średniej. W przypadku poprzedniego pobierania dziesięć próbek 1 minutę, a średni je. |
| rule | scaleAction | Działanie podejmowane w momencie wyzwolenia metricTrigger reguły. |
| scaleAction | direction | "Zwiększ" do skalowania w poziomie lub "Zmniejsz" do skalowania do wewnątrz.|
| scaleAction | value | Ile, aby zwiększyć lub zmniejszyć wydajność zasobu. |
| scaleAction | ochładzania | Ilość czasu oczekiwania po zakończeniu operacji skalowania przed ponownym skalowaniem. Na przykład jeśli **ochładzania = "PT10M"** , automatycznego skalowania nie jest podejmowana próba skalowania ponownie dla innego 10 minut. Ochładzania jest umożliwienie metryk w celu stabilizacji po dodawania lub usuwania wystąpień. |

## <a name="autoscale-profiles"></a>Profile automatycznego skalowania

Istnieją trzy typy profilów automatycznego skalowania:

- **Regularne profilu:** Najbardziej typowe profil. Jeśli nie ma potrzeby skalowania zasobu na podstawie dnia, tygodnia lub w określonym dniu, można użyć regularnych profilu. Następnie można skonfigurować ten profil przy użyciu reguł metryk, które określają, kiedy należy skalować w poziomie i kiedy umożliwiającą skalowanie w pionie. Powinien mieć tylko jeden profil regularnych zdefiniowane.

    Profil przykład używany we wcześniejszej części tego artykułu znajduje się przykład regularne profilu. Należy zauważyć, że możliwe jest też możliwość ustawienia profilu skalowanie do liczby statycznych wystąpień zasobu bazy danych.

- **Ustalona data profilu:** Ten profil jest przeznaczony dla przypadków specjalnych. Załóżmy na przykład, że masz ważne zdarzenie pojawi się na 26 grudnia 2017 r. (PST). Chcesz, aby minimalnej i maksymalnej pojemności zasobu różniły się na ten dzień, ale nadal skali na te same metryki. W takim przypadku należy dodać profil ustalona data do tego ustawienia listy profilów. Profil, który jest skonfigurowany do uruchamiania tylko dnia wydarzenia. Dla każdego dnia Skalowanie automatyczne używa regularnego profilu.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
- **Profil cyklu:** Ten typ profilu umożliwia upewnij się, że ten profil jest zawsze używana w określonym dniu tygodnia. Profile cyklu mają tylko czas rozpoczęcia. Działają aż do następnej profilu cyklu lub profil ustalona data ma ustawione uruchamianie. Ustawienia automatycznego skalowania za pomocą tylko jednego cyklu profilu uruchamia tego profilu, nawet jeśli istnieje profil regularnych zdefiniowane w tym samym kontekście. Dwa poniższe przykłady ilustrują, jak ten profil jest używany:

    **Przykład 1: Dni tygodnia, a weekendy**
    
    Załóżmy, że w weekendy, chcesz maksymalnej pojemności to 4. W dni robocze ponieważ oczekują większe obciążenie ma maksymalnej pojemności do 10. W takim przypadku ustawienie zawiera dwa profile cyklu jego uruchomienie w weekendy, a druga w dni robocze.
    Ustawienie wygląda następująco:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    Poprzedni ustawienie pokazuje, że każdy profil cyklu ma zgodnie z harmonogramem. Ten harmonogram Określa, kiedy rozpoczyna się profil uruchamiania. Profil, który zatrzymuje, gdy nadszedł czas, aby uruchomić inny profil.

    Na przykład w poprzednim ustawieniu "weekdayProfile" ustawiono można uruchomić w poniedziałek o 12:00. Oznacza to tego profilu uruchamiania w poniedziałek o 12:00. Jest kontynuowany aż do Sobota o 12:00, kiedy "weekendProfile" zaplanowano uruchomienia.

    **Przykład 2: godziny pracy**
    
    Załóżmy, że chcesz mieć jeden próg metryki w godzinach pracy (9:00 AM do 5:00 PM), a druga dla wszystkich pozostałych godzinach. To ustawienie będzie wyglądać następująco:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Poprzedni ustawienie pokazuje "businessHoursProfile" rozpoczyna wykonywanie w poniedziałek o 9:00 i 17:00:00 w dalszym ciągu. Gdy "nonBusinessHoursProfile" zacznie działać. "nonBusinessHoursProfile" działa aż do 9:00:00 wtorek, a następnie "businessHoursProfile" przejmuje ponownie. To jest powtarzany do momentu piątek o godzinie 17:00. W tym momencie "nonBusinessHoursProfile" działa aż do poniedziałek o 9:00.
    
> [!Note]
> Automatyczne skalowanie interfejsu użytkownika w witrynie Azure portal wymusza zakończenia dla cyklu profilów i rozpoczyna wykonywanie profilu domyślnego ustawienia skalowania automatycznego Between profile cyklu.
    
## <a name="autoscale-evaluation"></a>Automatyczne skalowanie w wersji ewaluacyjnej
Biorąc pod uwagę, że ustawienia automatycznego skalowania może mieć wiele profilów, a każdy profil może mieć wiele reguł metryk, ważne jest zrozumienie, jak są obliczane ustawienia automatycznego skalowania. Przy każdym uruchomieniu zadania automatycznego skalowania rozpoczyna, wybierając pozycję profil, który ma zastosowanie. Następnie funkcja automatycznego skalowania ocenia minimalne i maksymalne wartości i reguł metryk w profilu i decyduje, jeśli nie są wymagane akcje skalowania.

### <a name="which-profile-will-autoscale-pick"></a>Profil, który wybierze automatycznego skalowania?

Funkcja automatycznego skalowania stosują następującą sekwencję, aby wybrać profil, który:
1. Najpierw szuka żadnego profilu stałej dacie, który jest skonfigurowany do uruchamiania teraz. Jeśli, automatycznego skalowania, uruchomi go. Jeśli istnieje wiele profili stałej dacie, które powinien uruchamiać, automatycznego skalowania wybiera pierwszy z nich.
2. W przypadku profile nie stałej dacie, automatycznego skalowania patrzy na profile cyklu. Jeśli zostanie znaleziony profilu cyklu, uruchamia go.
3. Jeśli nie ma żadnych ustalona data lub profile cyklu, automatycznego skalowania uruchamia regularne profilu.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Jak Autoscale evaluate wiele reguł

Po skalowania automatycznego okaże się, aby uruchomić profil, który, ocenia wszystkie reguły skalowania w poziomie w profilu (są to reguły z **kierunek = "Zwiększ"** ).

Jeśli co najmniej jedna reguła skalowania w poziomie są wyzwalane, automatycznego skalowania oblicza nową pojemność ustalany na podstawie **scaleAction** każdego z tych zasad. Następnie go skaluje się do maksymalnej wartości tych możliwości, aby zapewnić dostępność usługi.

Na przykład załóżmy, że jest zestawu skalowania maszyn wirtualnych za pomocą bieżące możliwości oferowane przez 10. Istnieją dwie reguły skalowania w poziomie: jeden, który zwiększa pojemność 10 procent i taki, który zwiększa pojemność liczby 3. Pierwszą regułę mogłoby spowodować nową pojemność 11, a druga reguła mogłoby spowodować pojemność 13. Aby zapewnić dostępność usługi, automatycznego skalowania wybiera akcję, która powoduje maksymalną pojemność, więc drugiej reguły jest wybierany.

Jeśli żadne reguły skalowania w poziomie są wyzwalane, automatycznego skalowania ocenia wszystkich skalowania na zewnątrz reguł (reguł z **kierunek = "Zmniejsz"** ). Automatyczne skalowanie wymaga tylko akcji skalowania na zewnątrz, jeśli wszystkie reguły skalowania na zewnątrz są wyzwalane.

Automatyczne skalowanie oblicza nową pojemność ustalany na podstawie **scaleAction** każdego z tych zasad. Następnie wybiera akcję skalowania, która powoduje maksymalną liczbę tych możliwości, aby zapewnić dostępność usługi.

Na przykład załóżmy, że jest zestawu skalowania maszyn wirtualnych za pomocą bieżące możliwości oferowane przez 10. Istnieją dwie reguły skalowania na zewnątrz: jedną, która zmniejsza pojemność o 50%, a drugie zmniejszona pojemność liczby 3. Pierwszą regułę mogłoby spowodować nową pojemność 5, a druga reguła mogłoby spowodować pojemności 7. Aby zapewnić dostępność usługi, automatycznego skalowania wybiera akcję, która powoduje maksymalną pojemność, więc drugiej reguły jest wybierany.

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji na temat automatycznego skalowania, odwołując się do następujących:

* [Omówienie automatycznego skalowania](../../azure-monitor/platform/autoscale-overview.md)
* [Usługa Azure Monitor skalowania automatycznego często używane metryki](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Najlepsze rozwiązania dotyczące skalowania automatycznego usługi Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Użyj akcji skalowania automatycznego, aby wysyłać wiadomości e-mail i elementy webhook powiadomienia o alertach](../../azure-monitor/platform/autoscale-webhook-email.md)
* [Interfejs API REST automatycznego skalowania](https://msdn.microsoft.com/library/dn931953.aspx)

