---
title: Równoważenie klastra usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Wprowadzenie do równoważenia klastra z usługi sieć szkieletowa klastra Menedżera zasobów.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5d2f195c50750a5c7685f62c909f77b2960613e6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="balancing-your-service-fabric-cluster"></a>Równoważenie klastra sieci szkieletowej usług
Menedżer zasobów klastra sieci szkieletowej usług obsługuje zmiany dynamicznego obciążenia reagowanie na dodawania i usuwania węzłów lub usług. Automatycznie koryguje naruszenia ograniczeń i aktywne rebalances klastra. Jak często są te akcje podjęte — a wywołujących je?

Istnieją trzy różne kategorie pracy, który wykonuje Menedżera zasobów klastra. Oto one:

1. Rozmieszczenia — ten etap dotyczy umieszczenie replik stanowe ani bezstanowych wystąpień, które nie są spełnione. Umieszczanie zawiera nowe usługi i obsługa repliki stanowego lub bezstanowego wystąpień, których nie powiodła. Usuwanie i usunięcie repliki lub wystąpienia są obsługiwane w tym miejscu.
2. Ograniczenie sprawdza — ten etap sprawdza i koryguje naruszenia ograniczeń umieszczania różnych (reguły) w systemie. Przykłady reguł jest rzeczy, takich jak zapewnienie, że węzły nie są przeciążone i że spełnione są ograniczenia umieszczania usług.
3. Równoważenie — ten etap sprawdza, czy ponowne równoważenie jest konieczne na podstawie skonfigurowanego poziomu żądaną salda dla różnych metryki. Jeśli tak próbuje znaleźć rozmieszczenia w klastrze, który jest bardziej zrównoważone.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurowanie czasomierze Menedżera zasobów klastra
Pierwszy zestaw kontrolek wokół równoważenia to zbiór czasomierze. Czasomierze te określają częstotliwość sprawdza klastra Menedżera zasobów klastra i wykonuje akcje naprawcze.

Każdego z tych różnych typów poprawek, które ułatwia Menedżera zasobów klastra jest kontrolowana przez różnych kontroluje częstotliwość czasomierza. Po każdym czasomierza, jest zaplanowane zadanie. Domyślnie Menedżer zasobów:

* skanuje stanu i stosuje aktualizacje (na przykład rejestrowania, który jest węzłem w dół) co 1/10 sekundy
* Ustawia flagę wyboru umieszczenia 
* Ustawia flagę wyboru ograniczenia co sekundę
* Ustawia flagę równoważenia co pięć sekund.

Przykłady dotyczące tych czasomierze konfiguracji jest poniżej:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

za pomocą pliku ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json dla platformy Azure hostowanej klastrów:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Dzisiaj Menedżera zasobów klastra tylko wykonuje jedno z następujących działań w czasie, po kolei. Jest to, dlaczego się te czasomierze jako "minimalna interwałów" i akcji, które uzyskać wykonania, kiedy czasomierze go jako "ustawienie flagi". Na przykład Menedżera zasobów klastra odpowiada on za oczekujące żądania utworzenia usługi przed równoważenia klastra. Jak widać przez określone przedziały czasu domyślnego menedżera zasobów klastra skanowania pod kątem coś, co należy zrobić często. Zwykle oznacza to, że zestaw zmian wprowadzonych na każdym etapie ma mała. Zmiany wprowadzone w małych często umożliwia Menedżera zasobów klastra, aby odpowiadać, gdy wystąpi rzeczy w klastrze. Czasomierze domyślne Podaj niektórych przetwarzanie wsadowe, ponieważ wiele takich samych typach zdarzeń często występowały jednocześnie. 

Na przykład po awarii węzłów robią to całego domen błędów w czasie. Wszystkie te błędy są przechwytywane podczas następnego stanu aktualizacji po *PLBRefreshGap*. Poprawki są określane podczas umieszczania następujące ograniczenia check, i Równoważenie działa. Domyślnie Menedżer zasobów klastra nie jest skanowanie za pomocą godziny zmian w klastrze i próby adresów wszystkie zmiany na raz. W ten sposób może spowodować Seria zmian.

Menedżer zasobów klastra musi również dodatkowe informacje, aby określić, czy imbalanced klastra. W tym mamy dwóch elementów konfiguracji: *BalancingThresholds* i *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Równoważenie progi
Próg równoważenia jest służącą do wyzwalania ponowne równoważenie formantu. Próg równoważenia metryka jest _współczynnik_. Jeśli obciążenie dla metryki w węźle najbardziej załadować podzielona przez ilość obciążenia w węźle najmniej załadować przekracza tego Metryka *BalancingThreshold*, klaster jest imbalanced. W związku z tym równoważenia jest wyzwalane po następnym sprawdza Menedżera zasobów klastra. *MinLoadBalancingInterval* czasomierza Określa, jak często Menedżera zasobów klastra należy sprawdzić, czy konieczne jest ponowne równoważenie. Sprawdzanie nie oznacza, że awarii. 

Progi równoważenia są zdefiniowane w zasadzie na Metryka jako część definicji klastra. Aby uzyskać więcej informacji dotyczących metryki, zapoznaj się [w tym artykule](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

za pomocą pliku ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json dla platformy Azure hostowanej klastrów:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>
![Równoważenie przykład próg][Image1]
</center>

W tym przykładzie każda usługa zajmuje jedną jednostkę niektóre metryki. W przykładzie top maksymalne obciążenie w węźle osiągnie wartość 5 i minimalna wynosi dwa. Załóżmy, że próg równoważenia ta metryka jest trzy. Ponieważ współczynnik w klastrze jest 5/2 = 2.5 i który jest mniejsza niż określony próg trzech równoważenia klastra jest rozmieszczana. Bez równoważenia jest wyzwalane, gdy sprawdza Menedżera zasobów klastra.

W przykładzie dolnej maksymalne obciążenie w węźle wynosi 10, gdy jest co najmniej dwóch, co w stosunku pięć. Pięć jest większy niż próg równoważenia wyznaczonych trzech dla tej metryki. W związku z tym ponowne równoważenie Uruchom będzie następnym zaplanowanym równoważenia czasomierza wyzwala. W sytuacji, jak to niektóre obciążenia jest zazwyczaj dystrybuowane do Węzeł3. Menedżera zasobów klastra sieci szkieletowej usług korzysta z podejścia intensywnie, niektóre obciążenia można również przekazać Node2. 

<center>
![Równoważenie akcje przykład próg][Image2]
</center>

> [!NOTE]
> "" Równoważenie dwa różne strategie zarządzania obciążenia w klastrze. Strategii domyślny, który używa Menedżera zasobów klastra jest rozłożenie obciążenia między węzłami w klastrze. Strategia ta jest [defragmentacji](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentacja jest wykonywane podczas tego samego równoważenia Uruchom. Strategie równoważenia i defragmentacji może służyć do różnych metryk w tym samym klastrze. Usługa może mieć równoważenia i defragmentacji metryki. Dla metryki defragmentacji stosunek obciążenia w klastrze wyzwala ponowne równoważenie, gdy jest _poniżej_ równoważenia wartość progową. 
>

Pobieranie poniżej progu równoważenia nie jest celem jawnego. Progi równoważenia są po prostu *wyzwalacza*. Podczas równoważenia działa, Menedżera zasobów klastra określa jakie ulepszenia go, jeśli istnieją. Tak, ponieważ zostało rozpoczęte równoważenia wyszukiwania nie oznacza niczego przenosi. Czasami klastra jest imbalanced, ale zbyt ograniczone, aby poprawić. Alternatywnie ulepszenia wymagają przesunięcia, które są zbyt [kosztowne](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Progi działania
Czasami, mimo że węzły są stosunkowo imbalanced *całkowita* obciążenie w klastrze jest niska. Brak obciążenia może być przejściowy dip lub załadować, ponieważ klaster jest nowy i po prostu pobierania. W obu przypadkach nie możesz poświęcić czas równoważenia klastra, ponieważ niewiele możliwy. Jeśli klaster został poddany równoważenia, czy spędzają sieci i zasoby można przenosić elementy bez wprowadzania żadnych dużych obliczeniowe *bezwzględną* różnicy. Aby uniknąć niepotrzebnych przenosi, istnieje inny formant znany jako progi działania. Progi działania można określić niektóre bezwzględnym dolnej granicy dla działania. Jeśli żaden węzeł nie jest powyżej tego progu, równoważenia nie jest wyzwalane, nawet jeśli osiągnięty jest próg równoważenia.

Załóżmy, że firma Microsoft będzie przechowywała naszych próg równoważenia trzech dla tej metryki. Również Załóżmy, że mamy 1536 próg działania. W pierwszym przypadku gdy klaster jest imbalanced na brak próg równoważenia żaden węzeł nie spełnia tego progu działania, nic się nie dzieje. W przykładzie dolnej Node1 jest powyżej wartości progowej działania. Ponieważ przekroczona zarówno próg równoważenia i próg działania dla metryki równoważenia według harmonogramu. Na przykład Przyjrzyjmy się poniższym diagramie: 

<center>
![Przykład progu działania][Image3]
</center>

Podobnie jak Równoważenie progi progi działania są zdefiniowane na — pomiar za pośrednictwem definicji klastra:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

za pomocą pliku ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json dla platformy Azure hostowanej klastrów:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Progi równoważenie i działania są oba związana z określonej metryki - równoważenia zostanie wywołany tylko wtedy, gdy Przekroczono próg równoważenia i próg działania dla tej samej metryki.

> [!NOTE]
> Jeśli nie zostanie określony, próg metryki równoważenia to 1, a działanie próg wynosi 0. Oznacza to, Menedżer zasobów klastra będzie próbował zachować tego Metryka doskonale zrównoważonym żadnych danego obciążenia. Jeśli używasz metryki niestandardowe zalecane jest jawnie definiować własne progi równoważenia i działania dla Twojego metryki. 
>

## <a name="balancing-services-together"></a>Razem równoważenia usług
Klaster jest imbalanced lub nie jest decyzja całego klastra. Jednak sposób rozszerzana o naprawienie go przenoszone replik poszczególnych usług i wystąpień wokół. To sens, PRAWDA? Jeśli pamięci jest skumulowany na jednym węźle, wiele replik lub wystąpień może przyczyniać się do niego. Ustalania nierównowagi może wymagać przenoszenie żadnego z repliki stanowego lub bezstanowego wystąpieniom imbalanced metryki.

Od czasu do czasu, gdy usługi, której sam imbalanced nie pobiera przenieść (Pamiętaj dyskusji lokalnych i globalnych przeprowadzi wcześniej). Dlaczego czy usługi są przenoszone podczas wszystkie opcje, które zostały zrównoważonym metryki usługi? Zobaczmy:

- Załóżmy, że istnieją cztery usług, Service1 roboczego2, Service3 i Service4. 
- Service1 raporty metryk Metric1 i Metric2. 
- Klienta2 raporty metryk Metric2 i Metric3. 
- Service3 raporty metryk Metric3 i Metric4.
- Service4 raporty Metric99 metryki. 

Z pewnością widać, gdy chcemy tutaj: istnieje łańcuch! Nie mamy naprawdę cztery usługi niezależne, będziemy mieć trzy usługi, które są powiązane i taki, który jest wyłączony na jego własnej.

<center>
![Razem równoważenia usług][Image4]
</center>

Ze względu na tym łańcuchu jest możliwe, że nierównowaga w metryki 1-4 może spowodować repliki lub wystąpienia należące do usługi 1-3, aby poruszać się po. Wiemy, również nierównowaga w metryki 1, 2 lub 3 nie może spowodować przeniesień w Service4. Ponieważ przenoszenie replik nie byłoby żadnego punktu lub wystąpienia należące do Service4 wokół można całkowicie nic nie rób wpłynąć na saldo metryki 1-3.

Menedżer zasobów klastra automatycznie danych liczbowych się tym, które usługi są powiązane. Dodawanie, usuwanie lub Zmiana metryki dla usługi może mieć wpływ na ich relacji. Na przykład między dwa przebiegi równoważenia klienta2 mogły zostać zaktualizowane do usunięcia Metric2. To spowoduje przerwanie łańcucha między Service1 i klienta2. Teraz zamiast dwie grupy usług, istnieją trzy:

<center>
![Razem równoważenia usług][Image5]
</center>

## <a name="next-steps"></a>Kolejne kroki
* Metryki są zarządzaniu Menedżer zasobów klastra sieci szkieletowej usług konsumenckich i pojemności w klastrze. Aby dowiedzieć się więcej na temat metryki i sposobach ich konfigurowania, zapoznaj się [w tym artykule](service-fabric-cluster-resource-manager-metrics.md)
* Koszt przeniesienia jest jednym ze sposobów sygnalizowania Menedżera zasobów do klastra, że niektórych usług są droższe do przeniesienia od innych. Więcej informacji o koszt przeniesienia, można znaleźć w temacie [w tym artykule](service-fabric-cluster-resource-manager-movement-cost.md)
* Menedżer zasobów klastra ma kilka limity, które można skonfigurować tak, aby zwolnić postęp dokonany w klastrze. Nie są one zazwyczaj konieczne, ale jeśli są potrzebne informacje na temat ich [tutaj](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
