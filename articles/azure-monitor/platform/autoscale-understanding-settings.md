---
title: Opis ustawień skalowania automatycznego w usłudze Azure Monitor
description: Szczegółowy podział ustawień skalowania automatycznego i ich działania. Dotyczy maszyn wirtualnych, usług w chmurze, aplikacji sieci Web
ms.topic: conceptual
ms.date: 12/18/2017
ms.subservice: autoscale
ms.openlocfilehash: 9a2b94208de7ce490a0e7acfbb71175b4a7c846e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364309"
---
# <a name="understand-autoscale-settings"></a>Omówienie ustawień automatycznego skalowania
Ustawienia skalowania automatycznego pomagają zapewnić odpowiednią ilość zasobów uruchomionych do obsługi zmiennego obciążenia aplikacji. Można skonfigurować ustawienia skalowania automatycznego, które mają być wyzwalane na podstawie metryk wskazujących obciążenie lub wydajność lub wyzwalane w zaplanowanej dacie i godzinie. W tym artykule przedstawiono szczegółowe spojrzenie na anatomię ustawienia skalowania automatycznego. Artykuł rozpoczyna się od schematu i właściwości ustawienia, a następnie przechodzi przez różne typy profilów, które można skonfigurować. Na koniec w artykule omówiono, jak funkcja skalowania automatycznego na platformie Azure ocenia, który profil do wykonania w danym momencie.

## <a name="autoscale-setting-schema"></a>Schemat ustawień skalowania automatycznego
Aby zilustrować schemat ustawienia skalowania automatycznego, używane jest następujące ustawienie Skalowanie automatyczne. Należy pamiętać, że to ustawienie skalowania automatycznego ma:
- Jeden profil. 
- Dwie reguły metryki w tym profilu: jedna dla skalowania w poziomie i jedna dla skalowania w.
  - Reguła skalowania w poziomie jest wyzwalana, gdy średnia procentowa metryka procesora CPU zestawu maszyn wirtualnych jest większa niż 85 procent w ciągu ostatnich 10 minut.
  - Reguła skalowania w jest wyzwalana, gdy średnia zestawu skalowania maszyny wirtualnej jest mniejsza niż 60 procent dla ostatniej minuty.

> [!NOTE]
> Ustawienie może mieć wiele profili. Aby dowiedzieć się więcej, zobacz sekcję [Profile.](#autoscale-profiles) Profil może również mieć wiele reguł skalowania w poziomie i reguły skalowania. Aby zobaczyć, jak są one oceniane, zobacz sekcję [oceny.](#autoscale-evaluation)

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

| Sekcja | Nazwa elementu | Opis |
| --- | --- | --- |
| Ustawienie | ID | Identyfikator zasobu ustawienia Skalowanie automatyczne. Ustawienia skalowania automatycznego są zasobem usługi Azure Resource Manager. |
| Ustawienie | name | Nazwa ustawienia Skalowanie automatyczne. |
| Ustawienie | location | Lokalizacja ustawienia Skalowanie automatyczne. Ta lokalizacja może się różnić od lokalizacji skalowany zasób. |
| properties | celResourceUri | Identyfikator zasobu skalowany. Na zasób można mieć tylko jedno ustawienie skalowania automatycznego. |
| properties | Profile | Ustawienie Skalowanie automatyczne składa się z jednego lub większej liczby profili. Za każdym razem, gdy aparat skalowania automatycznego działa, wykonuje jeden profil. |
| profil | name | Nazwa profilu. Można wybrać dowolną nazwę, która pomoże Ci zidentyfikować profil. |
| profil | Pojemność.maksymalnie | Maksymalna dozwolona pojemność. Zapewnia, że skalowanie automatyczne podczas wykonywania tego profilu nie skaluje zasobu powyżej tego numeru. |
| profil | Pojemność.minimum | Minimalna dozwolona pojemność. Zapewnia, że skalowanie automatyczne podczas wykonywania tego profilu nie skaluje zasobu poniżej tego numeru. |
| profil | Capacity.default | Jeśli występuje problem z odczytaniem metryki zasobu (w tym przypadku procesora CPU "vmss1"), a bieżąca pojemność jest niższa niż domyślna, skalowanie automatyczne jest skalowane w poziomie domyślnym. Ma to na celu zapewnienie dostępności zasobu. Jeśli bieżąca pojemność jest już wyższa niż domyślna pojemność, skalowanie automatyczne nie jest skalowane. |
| profil | rules | Skalowanie automatyczne jest skalowane automatycznie między maksymalną i minimalną pojemnością, używając reguł w profilu. W profilu może być dostępnych wiele reguł. Zazwyczaj istnieją dwie reguły: jedna do określenia, kiedy skalować w poziomie, a druga, aby określić, kiedy skalować w. |
| Reguły | metricTrigger | Definiuje warunek metryki reguły. |
| metricTrigger | metricName | Nazwa metryki. |
| metricTrigger |  metricResourceUri | Identyfikator zasobu, który emituje metrykę. W większości przypadków jest taka sama jak zasób skalowany. W niektórych przypadkach może być inaczej. Na przykład można skalować zestaw skalowania maszyny wirtualnej na podstawie liczby komunikatów w kolejce magazynu. |
| metricTrigger | timeGrain | Czas próbkowania metryki. Na przykład **TimeGrain = "PT1M"** oznacza, że metryki powinny być agregowane co 1 minutę, przy użyciu metody agregacji określonej w elemencie statystycznym. |
| metricTrigger | Statystyki | Metoda agregacji w okresie timeGrain. Na przykład **statystyka = "Średnia"** i **timeGrain = "PT1M"** oznacza, że metryki powinny być agregowane co 1 minutę, biorąc średnią. Ta właściwość decyduje, jak metryka jest próbkowana. |
| metricTrigger | czasWindow | Czas, aby spojrzeć wstecz na metryki. Na przykład **timeWindow = "PT10M"** oznacza, że za każdym razem, gdy program Autoscale jest uruchamiany, wysyła kwerendy do metryk z ostatnich 10 minut. Przedział czasu umożliwia normalizację metryk i pozwala uniknąć reakcji na przejściowe skoki. |
| metricTrigger | timeAggregation | Metoda agregacji używana do agregowania metryk z próbkami. Na przykład **TimeAggregation = "Średnia"** należy agregować próbkowane metryki, biorąc średnią. W poprzednim przypadku weź dziesięć próbek 1-minutowych i uśrednij je. |
| Reguły | scaleAction (scaleAction) | Akcja do podjęcia po wyzwoleniu metricTrigger reguły. |
| scaleAction (scaleAction) | kierunek | "Zwiększ", aby skalować w poziomie, lub "Zmniejsz", aby skalować.|
| scaleAction (scaleAction) | value | Ile zwiększyć lub zmniejszyć pojemność zasobu. |
| scaleAction (scaleAction) | cooldown | Czas oczekiwania po operacji skalowania przed ponownym skalowaniem. Na przykład, jeśli **czas odnowienia = "PT10M"**, Skalowanie automatyczne nie próbuje skalować ponownie przez kolejne 10 minut. Czas odnowienia ma umożliwić stabilizację metryk po dodaniu lub usunięciu instancji. |

## <a name="autoscale-profiles"></a>Profile skalowania automatycznego

Istnieją trzy typy profili skalowania automatycznego:

- **Zwykły profil:** Najczęstszy profil. Jeśli nie musisz skalować zasobu na podstawie dnia tygodnia lub określonego dnia, możesz użyć zwykłego profilu. Ten profil można następnie skonfigurować za pomocą reguł metryki, które określają, kiedy skalować w poziomie i kiedy skalować w. Powinieneś mieć zdefiniowany tylko jeden zwykły profil.

    Przykładowy profil używany wcześniej w tym artykule jest przykładem zwykłego profilu. Należy zauważyć, że istnieje również możliwość ustawienia profilu do skalowania do statycznej liczby wystąpień dla zasobu.

- **Profil stałej daty:** Ten profil jest przeznaczony dla szczególnych przypadków. Załóżmy na przykład, że 26 grudnia 2017 r. (PST) odbędzie się ważne wydarzenie. Chcesz, aby minimalne i maksymalne możliwości zasobu były różne w tym dniu, ale nadal skalować na tych samych metryki. W takim przypadku należy dodać stały profil daty do listy profili ustawień. Profil jest skonfigurowany do uruchamiania tylko w dniu zdarzenia. W przypadku każdego innego dnia skalowanie automatyczne używa zwykłego profilu.

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
    
- **Profil cyklu:** Ten typ profilu umożliwia zapewnienie, że ten profil jest zawsze używany w danym dniu tygodnia. Profile cyklu mają tylko czas rozpoczęcia. Są one uruchamiane do momentu rozpoczęcia następnego profilu cyklu lub stałego profilu daty. Ustawienie Skalowanie automatyczne z tylko jednym profilem cyklu uruchamia ten profil, nawet jeśli w tym samym ustawieniu jest zdefiniowany zwykły profil. Poniższe dwa przykłady ilustrują sposób użycia tego profilu:

    **Przykład 1: Dni powszednie a weekendy**
    
    Załóżmy, że w weekendy chcesz, aby twoja maksymalna pojemność wynosić 4. W dni powszednie, ponieważ oczekujesz większego obciążenia, chcesz, aby maksymalna pojemność wynosi 10. W takim przypadku ustawienie będzie zawierać dwa profile cyklu, jeden do uruchomienia w weekendy, a drugi w dni powszednie.
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

    Poprzednie ustawienie pokazuje, że każdy profil cyklu ma harmonogram. Ten harmonogram określa, kiedy profil zaczyna działać. Profil zatrzymuje się, gdy nadszedł czas, aby uruchomić inny profil.

    Na przykład w poprzednim ustawieniu "weekdayProfile" ma się rozpocząć w poniedziałek o godzinie 12:00. Oznacza to, że ten profil zaczyna działać w poniedziałek o godzinie 12:00. Trwa do soboty do 12:00, kiedy to zaplanowano "weekendProfile".

    **Przykład 2: Godziny pracy**
    
    Załóżmy, że chcesz mieć jeden próg metryki w godzinach pracy (od 9:00 do 17:00) i inny dla wszystkich innych czasów. Ustawienie będzie wyglądać następująco:
    
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
    
    Poprzednie ustawienie pokazuje, że "businessHoursProfile" rozpoczyna się w poniedziałek o godzinie 9:00 i trwa do 17:00. Wtedy zaczyna działać "nonBusinessHoursProfile". "NonBusinessHoursProfile" trwa do 9:00 we wtorek, a następnie "businessHoursProfile" przejmuje ponownie. To powtarza się do piątku do 17:00. W tym momencie "nonBusinessHoursProfile" działa aż do poniedziałku o 9:00 AM.
    
> [!Note]
> Interfejs użytkownika skalowania automatycznego w witrynie Azure portal wymusza czasy zakończenia dla profilów cyklu i rozpoczyna uruchamianie domyślnego profilu ustawienia skalowania automatycznego między profilami cyklu.
    
## <a name="autoscale-evaluation"></a>Ocena skalowania automatycznego
Biorąc pod uwagę, że ustawienia skalowania automatycznego mogą mieć wiele profilów, a każdy profil może mieć wiele reguł metryki, ważne jest, aby zrozumieć, jak obliczane jest ustawienie skalowanie automatyczne. Za każdym razem, gdy zadanie skalowania automatycznego jest uruchamiane, rozpoczyna się od wybrania profilu, który ma zastosowanie. Następnie skalowanie automatyczne ocenia wartości minimalne i maksymalne oraz wszystkie reguły metryki w profilu i decyduje, czy akcja skalowania jest konieczna.

### <a name="which-profile-will-autoscale-pick"></a>Który profil zostanie wybrany w trybie automatycznym?

Skalowanie automatyczne używa następującej sekwencji, aby wybrać profil:
1. Najpierw wyszukuje dowolny profil daty stałej, który jest skonfigurowany do uruchamiania teraz. Jeśli tak, uruchomi go skalowanie automatyczne. Jeśli istnieje wiele stałych profilów dat, które mają być uruchamiane, skalowanie automatyczne wybiera pierwszy.
2. Jeśli nie ma stałych profilów dat, skala automatyczna analizuje profile cyklu. Jeśli zostanie znaleziony profil cyklu, uruchomi go.
3. Jeśli nie ma stałych profilów daty lub cyklu, skalowanie automatyczne uruchamia profil regularny.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Jak skalowanie automatyczne ocenia wiele reguł?

Po skalowaniu automatycznym określa, który profil do uruchomienia, ocenia wszystkie reguły skalowania w poziomie w profilu (są to reguły z **kierunkiem = "Wzrost").**

Jeśli zostanie wyzwolona co najmniej jedna reguła skalowania w poziomie, skalowanie automatyczne oblicza nową pojemność określoną przez **skalęDziałanie** każdej z tych reguł. Następnie skaluje się do maksimum tych pojemności, aby zapewnić dostępność usług.

Załóżmy na przykład, że istnieje zestaw skalowania maszyny wirtualnej o bieżącej pojemności 10. Istnieją dwie reguły skalowania w poziomie: jedna, która zwiększa pojemność o 10 procent, i jedna, która zwiększa wydajność o 3 liczby. Pierwsza reguła spowoduje nową pojemność 11, a druga zasada spowoduje pojemność 13. Aby zapewnić dostępność usługi, skalowanie automatyczne wybiera akcję, która powoduje maksymalną pojemność, więc wybrano drugą regułę.

Jeśli nie są wyzwalane żadne reguły skalowania w poziomie, skalowanie automatyczne ocenia wszystkie reguły skalowania (reguły z **kierunkiem = "Zmniejsz").** Skalowanie automatyczne przyjmuje akcję skalowania w przypadku wyzwolenia wszystkich reguł skalowania.

Skalowanie automatyczne oblicza nową pojemność określoną przez **skalęDziałanie** każdej z tych reguł. Następnie wybiera akcję skalowania, która powoduje maksymalną te zdolności, aby zapewnić dostępność usług.

Załóżmy na przykład, że istnieje zestaw skalowania maszyny wirtualnej o bieżącej pojemności 10. Istnieją dwie reguły skalowania: jedna, która zmniejsza pojemność o 50 procent, a druga zmniejsza pojemność o 3 liczby. Pierwsza reguła skutkowałaby nową zdolnością produkcyjną 5, a druga – zdolnością produkcyjną 7. Aby zapewnić dostępność usługi, skalowanie automatyczne wybiera akcję, która powoduje maksymalną pojemność, więc wybrano drugą regułę.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o skalowaniu automatycznym, odwołując się do następujących kwestii:

* [Omówienie skalowania automatycznego](../../azure-monitor/platform/autoscale-overview.md)
* [Wspólne metryki usługi Azure Monitor](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Najlepsze rozwiązania dotyczące skalowania automatycznego w usłudze Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Wysyłanie powiadomień o alertach e-mail i webhook za pomocą automatycznego skalowania](../../azure-monitor/platform/autoscale-webhook-email.md)
* [Interfejs API REST w automatycznym skalowaniu](https://msdn.microsoft.com/library/dn931953.aspx)

