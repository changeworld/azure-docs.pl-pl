---
title: Opis ustawień automatycznego skalowania w monitorze Azure
description: Podział szczegółowe ustawienia skalowania automatycznego i jak działają. Ma zastosowanie do maszyn wirtualnych i usług w chmurze, aplikacji sieci Web
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 982bc43fd86a808da07833d77bde17e17789b2d6
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265000"
---
# <a name="understand-autoscale-settings"></a>Omówienie ustawień automatycznego skalowania
Ustawienia skalowania automatycznego zagwarantować, że masz jednostkom zasoby z uruchomionym obsłużyć wahania obciążenia aplikacji. Można skonfigurować ustawienia skalowania automatycznego wyzwolenie oparciu metryki, które wskazują obciążenia lub wydajności lub wyzwalanych w zaplanowanym czasie. Ten artykuł przedstawia szczegółowe anatomia ustawieniu skalowania automatycznego. Artykuł rozpoczyna się od schematu i właściwości ustawienia, a następnie przeprowadzi Cię przez typy innego profilu, które można skonfigurować. Ponadto w tym artykule omówiono sposób funkcję skalowania automatycznego na platformie Azure ocenia profil, który można wykonać w dowolnym momencie.

## <a name="autoscale-setting-schema"></a>Schemat Ustawienia skalowania automatycznego
Aby zilustrować schematu Ustawienia skalowania automatycznego, następujące ustawienia skalowania automatycznego jest używany. Należy pamiętać, że to ustawienie skalowania automatycznego zawiera:
- Jeden profil. 
- Dwie reguły metryki w tym profilu: jeden do skalowania w poziomie, a drugi do skalowania w.
  - Reguła skalowalnego w poziomie jest wyzwalane, gdy metryki Procesora średnią wartość procentową zestaw skali maszyny wirtualnej jest większa niż 85% dla ostatnich 10 minut.
  - Reguła w skali jest wyzwalane, gdy średni zestaw skali maszyny wirtualnej jest mniejsza niż 60 procent dla ostatniej minucie.

> [!NOTE]
> To ustawienie może mieć wiele profilów. Aby dowiedzieć się więcej, zobacz [profile](#autoscale-profiles) sekcji. Profil może istnieć wiele reguł skalowania w poziomie i w skali reguł zdefiniowanych. Aby dowiedzieć się, jak są analizowane, zobacz [oceny](#autoscale-evaluation) sekcji.

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
| Ustawienie | ID | Identyfikator zasobu w ustawieniu skalowania automatycznego. Ustawienia skalowania automatycznego są zasobów usługi Azure Resource Manager. |
| Ustawienie | name | Nazwa ustawienia skalowania automatycznego. |
| Ustawienie | location | Lokalizacja w ustawieniu skalowania automatycznego. Ta lokalizacja może być inna niż lokalizacja zasobów są skalowane. |
| properties | targetResourceUri | Identyfikator zasobu jest skalowana zasobu. Może mieć tylko jedno ustawienie automatycznego skalowania dla zasobów. |
| properties | Profile | Ustawienia skalowania automatycznego składa się z co najmniej jeden profil. Każdym uruchomieniu aparatu skalowania automatycznego wykonuje jeden profil. |
| profil | name | Nazwa profilu. Można wybrać dowolną nazwę, która pomaga zidentyfikować ten profil. |
| profil | Capacity.Maximum | Moc maksymalna dozwolona. Zapewnia automatycznego skalowania, podczas wykonywania tego profilu, nie obsługuje zasobu powyżej ten numer. |
| profil | Capacity.minimum | Pojemność minimalne dozwolone. Zapewnia automatycznego skalowania, podczas wykonywania tego profilu, nie obsługuje zasobu pod ten numer. |
| profil | Capacity.default | Jeśli występuje problem z odczytem pomiar zasobów (w tym przypadku Procesora "vmss1"), a obecna pojemność jest mniejsza wartość domyślna, automatycznego skalowania skaluje się domyślną. To jest zapewnienie dostępności zasobów. Jeśli pojemność bieżąca już jest wyższa niż wydajność domyślna, skalowania automatycznego nie obsługuje w. |
| profil | rules | Skalowania automatycznego jest automatycznie skalowany między możliwości maksymalne i minimalne, za pomocą reguł w profilu. Istnieje wiele reguł w profilu. Zazwyczaj istnieją dwie reguły: jeden do określenia, kiedy do skalowania w poziomie, a drugą do określenia, kiedy skalować w. |
| zasada | metricTrigger | Definiuje metryki warunek reguły. |
| metricTrigger | metricName | Nazwa metryki. |
| metricTrigger |  metricResourceUri | Identyfikator zasobu zasób, który emituje metryki. W większości przypadków jest taka sama jak zasobów są skalowane. W niektórych przypadkach może być różne. Na przykład można skalować zestaw skali maszyny wirtualnej na podstawie liczby wiadomości w kolejce magazynu. |
| metricTrigger | Ziarnem czasu | Czas trwania metryki próbkowania. Na przykład **ziarnem czasu = "PT1M"** oznacza, że metryki agregowania co 1 minutę, przy użyciu określonej w elemencie Statystyka metody agregacji. |
| metricTrigger | Statystyka | Metoda agregacji w okresie ziarnem czasu. Na przykład **Statystyka = "Średnia"** i **ziarnem czasu = "PT1M"** oznacza, że metryki agregowania co 1 minutę, wykonując średniej. Ta właściwość decyduje o tym, jak jest próbkowany metryki. |
| metricTrigger | timeWindow | Ilość czasu, aby wyszukać metryki. Na przykład **timeWindow = "PT10M"** oznacza, że każdym uruchomieniu automatycznego skalowania, wysyła zapytanie metryki dla ostatnich 10 minut. Przedział czasu umożliwia Twoje metryki będą normalizowane i pozwala uniknąć reagowanie na nagłego przejściowej. |
| metricTrigger | timeAggregation | Metoda agregacji używane do agregowania miar próbki. Na przykład **TimeAggregation = "Średnia"** powinien agregacji próbkowanych metryki wykonując średniej. W przypadku poprzedniego potrwać 10 próbek 1 minutę, a średnie je. |
| zasada | scaleAction | Akcja wykonywana po wyzwoleniu metricTrigger reguły. |
| scaleAction | kierunek | "Zwiększyć" do skalowania w poziomie lub "Zmniejsz" skali w.|
| scaleAction | wartość | Ile, aby zwiększyć lub zmniejszyć pojemność zasobu. |
| scaleAction | cooldown | Ilość czasu oczekiwania po zakończeniu operacji skalowania przed skalowanie ponownie. Na przykład jeśli **cooldown = "PT10M"**, skalowania automatycznego nie jest podejmowana próba skalowania ponownie dla innego 10 minut. Cooldown jest umożliwienie metryk do utrwalania po Dodawanie lub usuwanie wystąpienia. |

## <a name="autoscale-profiles"></a>Profile skalowania automatycznego

Istnieją trzy typy profilów skalowania automatycznego:

- **Regularne profilu:** najbardziej typowych profilu. Jeśli nie ma potrzeby skalowania zasobu na podstawie dzień tygodnia, lub w określonym dniu, mogą używać profilu regularne. Następnie można skonfigurować przy użyciu reguł metryki, które wymuszają do skalowania w poziomie gdzie i kiedy skalować w tego profilu. Powinien mieć tylko jeden profil regularne zdefiniowany.

    Profil przykład używane w tym artykule jest przykładem regularne profilu. Należy pamiętać, że istnieje również możliwość ustawienia profilu skalowanie liczby wystąpień statycznych dla zasobu.

- **Stała daty profilu:** ten profil jest przeznaczony dla przypadków specjalnych. Załóżmy na przykład, że masz ważne zdarzenie powtarzający się na 26 grudnia 2017 (PST). Ma minimalną i maksymalną pojemność zasobu ma być inna, w tym dnia, ale nadal skali na te same metryki. W takim przypadku należy dodać profil daty stałej do listy z ustawieniem profilów. Profil jest skonfigurowany do uruchamiania tylko w dniu zdarzenia. Dla innych dnia skalowania automatycznego używa regularne profil.

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
    
- **Profil cyklu:** takiego profilu umożliwia upewnij się, że ten profil jest zawsze używana na określony dzień tygodnia. Cykl profile zawierają tylko czas rozpoczęcia. Działają aż do następnego cyklu profilu lub profilu daty stałej ma ustawione uruchamianie. Ustawienia skalowania automatycznego z profilem tylko jednego cyklu uruchamia tego profilu, nawet jeśli dostępny jest profil regularne zdefiniowane w tym samym kontekście. Dwa poniższe przykłady przedstawiają sposób ten profil jest używany:

    **Przykład 1: Dni tygodnia, w porównaniu z weekendów**
    
    Załóżmy, że w weekendy, określ, czy maksymalna pojemność za 4. W dni robocze ponieważ oczekuje większe obciążenie ma czy maksymalną pojemność się 10. W takim przypadku ustawienia zawiera dwa profile cyklu jego uruchomienie w weekendy, a drugą w dni robocze.
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

    Poprzednie ustawienia pokazuje, że każdy profil cyklu ma zgodnie z harmonogramem. Ten harmonogram Określa podczas uruchamiania profilu uruchamiania. Profil zatrzymywana, gdy jest czasu na wykonanie innego profilu.

    Na przykład w poprzednim ustawienie "weekdayProfile" jest ustawione uruchamianie w poniedziałek o 12:00. Oznacza to, że tego profilu uruchamiania w poniedziałek o 12:00. Jest kontynuowany do momentu sobota o 12:00, gdy "weekendProfile" jest zaplanowane do uruchomienia.

    **Przykład 2: godzinami pracy**
    
    Załóżmy, że mają jeden metryki próg w godzinach pracy (9:00 AM do 5:00 PM), a druga dla wszystkich pozostałych godzinach. Ustawienie będzie wyglądać następująco:
    
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
    
    Poprzednie ustawienia pokazuje rozpoczyna w poniedziałek o 9:00 "businessHoursProfile", a w dalszym ciągu 5:00 PM. Gdy jest uruchamiana "nonBusinessHoursProfile". "nonBusinessHoursProfile" działa do momentu 9:00 AM wtorek, a następnie "businessHoursProfile" przejmuje ponownie. To jest powtarzany aż do piątku w 5:00 PM. W tym momencie "nonBusinessHoursProfile" uruchamia aż do poniedziałek o 9:00.
    
> [!Note]
> Skalowania automatycznego interfejsu użytkownika w portalu Azure wymusza zakończenia cyklu profilów i uruchamia w ustawieniu skalowania automatycznego profil domyślny Between profile cyklu.
    
## <a name="autoscale-evaluation"></a>Ocena skalowania automatycznego
Biorąc pod uwagę, że ustawienia skalowania automatycznego może mieć wiele profilów, a każdy profil może mieć wiele reguł metryki, ważne jest zrozumienie, jak są analizowane ustawieniu skalowania automatycznego. Każdym uruchomieniu zadania automatycznego skalowania rozpoczyna, wybierając profil, który ma zastosowanie. Następnie skalowania automatycznego ocenia wartości minimalnej i maksymalnej wartości i wszystkie metryki reguł w profilu i decyduje o tym, czy konieczne jest akcji skalowania.

### <a name="which-profile-will-autoscale-pick"></a>Profil, który będzie pobranie skalowania automatycznego

Funkcja automatycznego skalowania stosują następującą sekwencję i wybierz profil:
1. Najpierw wyszukiwana dla dowolnego profilu daty stałej, który jest skonfigurowany do uruchomienia. Jeśli, skalowania automatycznego uruchamia go. W przypadku wielu profilów dacie, które mogą uruchomić skalowania automatycznego wybiera pierwszy.
2. Jeśli nie Brak profilów daty stałej, skalowania automatycznego analizuje profile cyklu. Jeśli zostanie znaleziony profilu cyklu, uruchamia go.
3. Jeśli nie ma żadnych daty stałej lub cyklu profile, skalowania automatycznego jest uruchamiany regularne profilu.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Sposób automatycznego skalowania ocenić wiele reguł?

Po skalowania automatycznego określa profil, który można uruchomić, oceniając wszystkich reguł skalowania w poziomie w profilu (są to reguły z **kierunek = "Zwiększyć"**).

Jeśli jeden lub więcej reguł skalowania w poziomie są wyzwalane, skalowania automatycznego oblicza nowego miejsca ustaleniami **scaleAction** każdego z tych zasad. Następnie go skaluje się do maksymalnej wartości tych możliwości, aby zapewnić dostępność usługi.

Na przykład załóżmy, że ustawiono skalowania maszyn wirtualnych o pojemności bieżącego 10. Istnieją dwie reguły skalowania w poziomie: jedną, która zwiększa pojemność o 10 procent, a drugie zwiększa pojemność liczby 3. Pierwsza reguła spowoduje powstanie nowego miejsca 11, a drugą regułę spowodowałoby pojemności 13. Aby zapewnić dostępność usługi, skalowania automatycznego wybiera się, że wybrano akcję, która powoduje maksymalną pojemność, więc drugą regułę.

Jeśli żadne reguły skalowania w poziomie są wyzwalane, skalowania automatycznego ocenia wszystkich w skali reguł (reguły z **kierunek = "Zmniejsz"**). Skalowania automatycznego ma akcję skalowania tylko, jeśli wszystkie reguły w skali są wyzwalane.

Skalowania automatycznego oblicza nowego miejsca ustaleniami **scaleAction** każdego z tych zasad. Następnie wybiera akcję skalowania, która powoduje maksymalną tych możliwości w celu zapewnienia dostępności usługi.

Na przykład załóżmy, że ustawiono skalowania maszyn wirtualnych o pojemności bieżącego 10. Istnieją dwie reguły skali: jedną, która zmniejsza pojemność 50 procent, a drugie zmniejsza pojemność przez liczenie 3. Pierwsza reguła spowoduje powstanie nowego pojemność wynoszącą 5, a drugą regułę spowodowałoby pojemności 7. Aby zapewnić dostępność usługi, skalowania automatycznego wybiera się, że wybrano akcję, która powoduje maksymalną pojemność, więc drugą regułę.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o skalowania automatycznego, odwołując się do następujących:

* [Omówienie automatycznego skalowania](monitoring-overview-autoscale.md)
* [Azure metryki wspólnej skalowania automatycznego monitora](insights-autoscale-common-metrics.md)
* [Najlepsze praktyki dotyczące skalowania automatycznego Azure Monitor](insights-autoscale-best-practices.md)
* [Użyj akcji skalowania automatycznego do wysyłania wiadomości e-mail i elementu webhook powiadomień o alertach](insights-autoscale-to-webhook-email.md)
* [Interfejs API REST skalowania automatycznego](https://msdn.microsoft.com/library/dn931953.aspx)
