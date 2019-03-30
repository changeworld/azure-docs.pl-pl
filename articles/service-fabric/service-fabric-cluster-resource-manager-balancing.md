---
title: Równoważenie klastra usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Wprowadzenie do równoważenie klastra za pomocą usługi Service Fabric Menedżer zasobów klastra.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 74fe4f7c4c231f80c7555f39f840a85baae310e9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662027"
---
# <a name="balancing-your-service-fabric-cluster"></a>Równoważenie klastra usługi service fabric
Menedżer zasobów klastra usługi Service Fabric obsługuje zmiany dynamicznego obciążenia reagowanie na dodawania lub usuwania węzłów lub usługi. Automatycznie koryguje naruszenia ograniczeń i aktywnie rebalances klastra. Ale jak często te akcje są wykonywane i ich wyzwalacze?

Istnieją trzy różne kategorie w pracy, który wykonuje Menedżer zasobów klastra. Oto one:

1. Umieszczanie — ten etap dotyczy dowolnego stanowych replik lub wystąpień bezstanowych, których brakuje. Umieszczanie obejmuje nowych usług i obsługa stanowych replik lub wystąpień bezstanowych, które nie powiodło się. Usuwanie i upuszczając replik lub wystąpień są obsługiwane w tym miejscu.
2. Ograniczenie sprawdza — ten etap sprawdza i naprawia naruszenia ograniczeń umieszczania różnych (reguły) w systemie. Przykłady reguł elementów, takich jak zagwarantowanie, że węzły nie są przeciążone i że zostały spełnione ograniczeniami dotyczącymi umieszczania usługi.
3. Równoważenie — ten etap sprawdza, czy ponowne równoważenie jest wymagane na podstawie skonfigurowanego żądanego poziomu salda dla różnych metryk. Jeśli tak, próbuje odnaleźć rozmieszczenia w klastrze, który jest bardziej zrównoważone.

## <a name="configuring-cluster-resource-manager-timers"></a>Konfigurując czasomierze Menedżer zasobów klastra
Pierwszy zestaw kontrolek wokół równoważenia to zbiór czasomierzy. Czasomierze te określają, jak często sprawdza, czy klaster Menedżer zasobów klastra i wykonuje akcje naprawcze.

Każdy z tych różnych typów poprawek, które ułatwia Menedżer zasobów klastra jest kontrolowana przez inny czasomierz, które regulują jej częstotliwość. Gdy zostanie wyzwolony każdego czasomierza, zadanie jest zaplanowane. Domyślnie usługi Resource Manager:

* skanuje stanu i stosuje aktualizacje (na przykład nagrywania, który węzeł jest wyłączony) co 1/10 sekundy
* Ustawia znacznik wyboru umieszczenia 
* Ustawia znacznik wyboru ograniczenia co sekundę
* Ustawia flagę równoważenia co pięć sekund.

Przykłady konfiguracji dotyczące tych czasomierze są poniżej:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

za pomocą ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json na platformie Azure hostowane klastrów:

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

Już dziś Menedżer zasobów klastra wykonuje tylko jedno z tych działań w czasie, po kolei. To dlatego nazywamy te czasomierzy akcje, które Pobierz wykonywane, gdy czasomierze jako "ustawienie flagi" oraz "minimalna odstępach czasu". Na przykład Menedżer zasobów klastra dba o oczekujących żądań w celu utworzenia usługi przed równoważenie klastra. Jak widać, według przedziałów czasowych domyślne, które są określone, Menedżer zasobów klastra skanuje w poszukiwaniu coś, co trzeba zrobić często. Zwykle oznacza to, że zestaw zmian wprowadzonych na każdym etapie jest mała. Zmiany wprowadzone w małych często umożliwia Menedżer zasobów klastra jest dynamiczny, gdy rzeczy odbywa się w klastrze. Czasomierze domyślne zawierają niektóre przetwarzanie wsadowe, ponieważ wiele z tych samych typów zdarzeń, które mają na celu wystąpiły równocześnie. 

Na przykład podczas awarii węzłów robią tak całej domeny błędów w danym momencie. Wszystkie te błędy są przechwytywane podczas następnego stanu aktualizacji po *PLBRefreshGap*. Poprawki są określane podczas umieszczania umożliwić wybranie następujących — Sprawdzanie ograniczenia i równoważenia działa. Domyślnie Menedżer zasobów klastra nie jest skanowanie za pomocą godzin zmian w klastrze i podjęcie próby jednocześnie rozwiązać wszystkie zmiany. Ten sposób doprowadziłoby do błędów wzrosty współczynnika zmian.

Menedżer zasobów klastra musi również pewne dodatkowe informacje, aby określić, czy imbalanced klastra. W tym mamy dwie inne elementy konfiguracji: *BalancingThresholds* i *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Równoważenie progi
Próg równoważenia jest główną kontrolką służącą do wyzwalania ponowne równoważenie. Równoważenie próg dla metryki jest _współczynnik_. Jeśli obciążenia dla metryki w węźle najbardziej załadowany, podzielone według ilości obciążenia w węźle najmniej załadować przekracza tej metryki *BalancingThreshold*, nie będzie imbalanced klastra. W wyniku równoważenia jest wyzwalana po następnym sprawdza, czy Menedżer zasobów klastra. *MinLoadBalancingInterval* czasomierza definiuje, jak często należy sprawdzić Menedżer zasobów klastra, jeśli konieczne jest ponowne równoważenie. Sprawdzanie nie oznacza, że nic się dzieje. 

Progi równoważenia są definiowane na podstawie na metrykę, jako część definicji klastra. Aby uzyskać więcej informacji dotyczących metryk, zapoznaj się z [w tym artykule](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

za pomocą ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json na platformie Azure hostowane klastrów:

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

![Równoważenie przykładowe wartości progowej][Image1]
</center>

W tym przykładzie każda usługa używa jednej jednostki niektóre metryki. W tym przykładzie najważniejsze maksymalnego obciążenia, w węźle jest pięć, a wartość minimalna to dwa. Załóżmy, że równoważenia próg ta metryka to trzy. Ponieważ współczynnik w klastrze jest 5/2 = 2,5 a to znaczy mniejsza niż określona równoważenie próg trzech, klastra jest równoważone. Bez równoważenia jest wyzwalany, gdy sprawdza, czy Menedżer zasobów klastra.

W tym przykładzie dolnej maksymalnego obciążenia, w węźle jest równy 10 podczas co najmniej dwóch, co w stosunku do 5. Pięć jest większa niż wyznaczonym progiem równoważenia trzech dla tej metryki. W rezultacie ponowne równoważenie Uruchom będzie następnym zaplanowanym aktywacji czasomierza równoważenia. W sytuacji, jak to niektóre obciążenia, jest zazwyczaj wysyłana do Węzeł3. Menedżer zasobów klastra usługi Service Fabric korzysta z metody zachłannego, niektóre obciążenia mogą również być dystrybuowane do Węzeł2. 

<center>

![Równoważenie próg przykład akcji][Image2]
</center>

> [!NOTE]
> "" Równoważenie dwa różne strategie zarządzania obciążenia w klastrze. Strategia domyślna, która używa Menedżer zasobów klastra jest rozłożenie obciążenia między węzłami w klastrze. Strategia ta jest [defragmentacji](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentacja jest wykonywane podczas tego samego równoważenia uruchomienia. Strategie równoważenia i defragmentacji może służyć do różnych metryk, w tym samym klastrze. Usługa może mieć równoważenia i defragmentacji metryk. Dla defragmentacji metryk, współczynnik obciążeń w klastrze wyzwala ponowne równoważenie, gdy jest ona _poniżej_ równoważenia wartość progową. 
>

Pobieranie poniżej progu równoważenia nie jest celem jawnego. Progi równoważenia są po prostu *wyzwalacza*. Podczas równoważenia działa, Menedżer zasobów klastra określa ulepszenia, jakie może sprawić, że, jeśli istnieje. Po prostu, ponieważ jest rozpoczęła się równoważenia wyszukiwania nie oznacza niczego przenosi. Czasami klastra jest imbalanced, ale zbyt ograniczone, aby poprawić. Alternatywnie ulepszenia wymagają przesunięcia, które są zbyt [kosztownych](service-fabric-cluster-resource-manager-movement-cost.md)).

## <a name="activity-thresholds"></a>Progi działania
Czasami, mimo że węzły są stosunkowo imbalanced *całkowita* obciążenie w klastrze jest niska. Brak obciążenia może być przejściowy dip lub ponieważ klaster znajduje się nowego i dopiero zaczynać załadować. W obu przypadkach może nie chcieć poświęcać czasu na równoważenie klastra, ponieważ występuje niewiele możliwy. Jeśli klaster został poddany, równoważenia, czy wydatki sieci i zasobów obliczeniowych w celu zrobienia bez wprowadzania żadnych dużych *bezwzględne* różnica. Aby uniknąć niepotrzebnych przenosi, istnieje inny formant nazywany progi działania. Progi działań pozwala określić pewne bezwzględne dolną granicę dla działania. Jeśli żaden węzeł nie jest powyżej tej wartości progowej, równoważenia nie zostanie wyzwolony, nawet wtedy, gdy są spełnione równoważenia próg.

Załóżmy, że firma Microsoft będzie przechowywała naszego progu równoważenia trzech dla tej metryki. Ponadto Załóżmy, że mamy 1536 próg działania. W pierwszym przypadku podczas imbalanced na próg równoważenia istnieje klastra żaden węzeł nie jest zgodna z progu działania, więc nic się nie dzieje. W tym przykładzie dolnej Węzeł1 jest powyżej wartości progowej działania. Ponieważ przekroczeniu progu równoważenia i próg działania dla metryki równoważenia według harmonogramu. Na przykład Spójrzmy na poniższym diagramie: 

<center>

![Przykład progu działania][Image3]
</center>

Podobnie jak Równoważenie progów progów działania są na metrykę za pośrednictwem definicji klastra:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

za pomocą ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json na platformie Azure hostowane klastrów:

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

Progi równoważenia i działania są zarówno związany z określonej metryki - równoważenia jest wyzwalane tylko wtedy, gdy próg równoważenia i działania próg został przekroczony dla tego samego metryki.

> [!NOTE]
> Jeśli nie zostanie określony, równoważenia próg dla metryki to 1, a próg działanie ma wartość 0. Oznacza to, czy Menedżer zasobów klastra podejmie próbę zachowania tej metryki doskonale równoważenia dowolnego danego obciążenia. Jeśli używasz metryki niestandardowe zalecane jest jawne zdefiniowanie własnego progi równoważenia i działanie dla metryk. 
>

## <a name="balancing-services-together"></a>Razem równoważenia usług
Klaster jest imbalanced lub nie jest to decyzja całego klastra. Jednak sposób przejdziemy o naprawienie go przechodzi konkretną usługę repliki i wystąpienia wokół. To sens, PRAWDA? Jeśli pamięć jest skumulowany na jednym węźle, wiele replik lub wystąpień może przyczyniać się do niego. Naprawianie nierównowagi może wymagać przechodzenia stanowych replik lub wystąpień bezstanowych, które Użyj imbalanced metryki.

Od czasu do czasu, chociaż to usługa, która nie była sam imbalanced pobiera przenieść (Pamiętaj dyskusji lokalne i globalne przeprowadzi wcześniej). Dlaczego czy usługi są przenoszone po wszystkim metryki usługi były ze zrównoważonym? Zobaczmy przykład:

- Załóżmy, że istnieją cztery usług, Service1, roboczego2, Service3 i Service4. 
- Service1 raporty metryk Metric1 i Metric2. 
- Klienta2 raporty metryk Metric2 i Metric3. 
- Service3 raporty metryk Metric3 i Metric4.
- Service4 raporty Metric99 metryki. 

Z pewnością zostanie wyświetlony, gdy będziemy tutaj: Istnieje łańcuch! Tak naprawdę nie ma cztery usługi niezależne, mamy trzy usługi, które są ze sobą powiązane i taki, który jest wyłączony na swój własny.

<center>

![Razem równoważenia usług][Image4]
</center>

Ze względu na ten łańcuch jest możliwe, że nierównowagi metryki 1 – 4 może spowodować replik lub wystąpień należące do usługi 1-3, aby poruszać się. Wiemy również nierównowagi metryki 1, 2 lub 3 nie może powodować przepływów w Service4. Od przenoszenie replik nie byłoby żadnego punktu lub wystąpień należących do Service4 wokół można całkowicie nic nie rób mających wpływ na saldo metryki 1-3.

Menedżer zasobów klastra automatycznie określa jakie usługi są ze sobą powiązane. Dodawanie, usuwanie lub Zmiana metryki dotyczące usług mogą mieć wpływ na ich wzajemne relacje. Na przykład między dwoma przebiegami równoważenia klienta2 mogły zostać zaktualizowane do usunięcia Metric2. Spowoduje to podzielenie łańcucha między Service1 i klienta2. Teraz zamiast dwóch grup powiązanych usług, dostępne są trzy:

<center>

![Razem równoważenia usług][Image5]
</center>

## <a name="next-steps"></a>Kolejne kroki
* Metryki są, jak Menedżer zasobów usługi Service Fabric klaster zarządza użycia i pojemności w klastrze. Aby dowiedzieć się więcej na temat metryk i sposobach ich konfigurowania, zapoznaj się z [w tym artykule](service-fabric-cluster-resource-manager-metrics.md)
* Koszt przeniesienia jest jednym ze sposobów sygnalizowanie Menedżer zasobów klastra, że niektóre usługi są bardziej kosztowne przenieść niż inne. Aby uzyskać więcej informacji o koszt przeniesienia, dotyczą [w tym artykule](service-fabric-cluster-resource-manager-movement-cost.md)
* Menedżer zasobów klastra ma kilka ograniczenia, skonfigurowanych spowolnienia postępu dokonanego w klastrze. Nie są zwykle wymagane, ale jeśli potrzebne informacje na temat ich [tutaj](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
