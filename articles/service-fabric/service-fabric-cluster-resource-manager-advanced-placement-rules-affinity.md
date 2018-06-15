---
title: Menedżer zasobów klastra usługi sieć szkieletowa — koligacji | Dokumentacja firmy Microsoft
description: Omówienie konfigurowania koligacji dla usługi sieci szkieletowej usług
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 57abea79a620aa83e16ad4cc2fd78a4294f2b278
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204859"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurowanie i używanie koligację usługi w sieci szkieletowej usług
Koligacja jest formant, który znajduje się głównie do pomóc w usprawnieniu przejścia większych wbudowanymi aplikacji w chmurze i mikrousług świecie. Jest również używany jako optymalizacji dla poprawy wydajności usług, mimo że ten sposób może mieć efekty uboczne.

Załóżmy, że w przypadku wprowadzenia większych aplikacji lub taki, który właśnie nie został zaprojektowany z mikrousług pamiętać sieci szkieletowej usług (lub dowolnym środowisku rozproszonym). Ten typ przejścia jest wspólnej. Rozpoczyna się od podnoszenia całą aplikację do środowiska, pakowanie i czy działa bez problemów. Następnie możesz uruchomić podzielenie go na różnych usług mniejszych, że wszystkie komunikować się ze sobą.

Po pewnym czasie może się okazać czy aplikacji występują problemy. Problemy zwykle należą do jednej z tych kategorii:

1. Inny składnik X wbudowanymi aplikacji ma zależność nieudokumentowanej składnika Y, i tylko włączone tych składników do poszczególnych usług. Ponieważ te usługi są uruchomione w różnych węzłach w klastrze, są one uszkodzone.
2. Te składniki komunikują się za pośrednictwem (lokalnej nazwane potoki | współużytkowana pamięć | pliki na dysku) i naprawdę muszą mieć możliwość zapisywania w tej chwili do udostępnionego zasobu lokalnego ze względu na wydajność. Zależność twardych ta zostanie usunięta później, może być.
3. Wszystko działa poprawnie, ale włącza się, że te dwa składniki są faktycznie chatty wydajności poufnych. Gdy są przenoszone do poszczególnych usług ogólne tanked wydajność aplikacji lub opóźnienia zwiększyć. W związku z tym ogólną aplikacji nie spełniających oczekiwań.

W takich przypadkach firma Microsoft nie chcesz stracić refaktoryzacji pracę i nie chcesz przejść z powrotem do monolityczna. Ostatni warunek nawet może być pożądane zwykły optymalizacji. Jednak do momentu możemy zmodyfikowanie składników do pracy w naturalny sposób jako usługi (lub firma Microsoft może rozwiązać oczekiwania dotyczące wydajności w inny sposób) zamierzamy muszą pewnym sensie lokalizacji.

Co można zrobić w takiej sytuacji? Można również spróbuj Włączenie koligacji.

## <a name="how-to-configure-affinity"></a>Konfigurowanie koligacji
Aby skonfigurować koligację, należy zdefiniować relację koligację między dwóch różnych usług. Można potraktować koligacji jako "wskazuje" jedną usługę w innej i informacją "tej usługi można uruchamiać tylko gdy czy usługa jest uruchomiona." Czasami nazywamy koligacji relacji nadrzędny/podrzędny (gdzie wskażesz dziecka na element nadrzędny). Koligacja zapewnia repliki lub wystąpień jednej usługi są umieszczane na tych samych węzłów, które innej usługi.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Usługa podrzędne tylko mogą uczestniczyć w relacji jeden koligacji. Jeśli potrzebujesz dziecka, które ma być koligowane z dwóch usług nadrzędnego jednocześnie masz kilka opcji:
> - Wycofaj relacje (mają parentService1 i parentService2 punkt w aktualnie usługi podrzędnego), lub
> - Wyznaczanie jeden z elementów nadrzędnych koncentratora przez Konwencję i mieć wszystkich usług punkt w tej usłudze. 
>
> Efekty umieszczenia w klastrze powinna być taka sama.
>

## <a name="different-affinity-options"></a>Koligacja różnych opcji
Koligacja jest reprezentowany przez jeden z kilku systemów korelacji i ma dwa różne tryby. Najbardziej typowe tryb koligacji jest określane mianem NonAlignedAffinity. W NonAlignedAffinity repliki lub wystąpienia różnych usług są umieszczane na tych samych węzłów. Inne tryb jest AlignedAffinity. Wyrównane koligacji przydaje się tylko z usług stanowych. Konfigurowanie dwóch usług stanowych ma wyrównane koligacji zapewnia umieszczanie kolory podstawowe tych usług na tych samych węzłów poszczególnych. Powoduje także każda para pomocnicze bazy danych dla tych usług na tych samych węzłów. Istnieje również możliwość (chociaż są mniej typowe) do konfigurowania NonAlignedAffinity dla stanowych usług. Dla NonAlignedAffinity innej repliki dwie usługi stanowej są uruchamiane na tych samych węzłów, ale może to spowodować ich kolory podstawowe w różnych węzłach.

<center>
![Tryby koligacji i ich wpływ][Image1]
</center>

### <a name="best-effort-desired-state"></a>Najlepszy stan potrzeby nakładu pracy
Relacja koligacji to optymalne rozwiązanie. Nie zapewnia te same gwarancje kolokacji lub niezawodności, która działa w taki sam proces wykonywalny nie. Usługi w relacja koligacji są zasadniczo różne jednostki, które może zakończyć się niepowodzeniem i można przenosić niezależnie. Relacja koligacji może również spowodować przerwanie, chociaż te podziały są tymczasowe. Na przykład ograniczenia dotyczące pojemności może oznaczać tylko niektóre obiekty usługi relacja koligacji można zmieścić w danym węźle. W takich przypadkach, mimo że istnieje relacja koligacji w miejscu, go nie można wymusić ze względu na inne ograniczenia. Jeśli jest to możliwe to zrobić, naruszenie jest automatycznie rozwiązany później.

### <a name="chains-vs-stars"></a>Łańcuchy a gwiazdek
Dzisiaj Menedżera zasobów klastra nie jest możliwość łańcuchów modelu relacji koligacji. Co to oznacza, że jest to, że usługa, która jest elementem podrzędnym w jednej relacji koligacji nie może być elementem nadrzędnym w innej relacji koligacji. Jeśli chcesz modelu tego typu relacji należy skutecznie go model gwiazdy, jako łańcuch. Aby przenieść z łańcucha gwiazdy, znajdujących się najniżej podrzędnych może być elementem nadrzędnym do pierwszy element podrzędny elementu nadrzędnego zamiast tego. W zależności od rozmieszczenie usługi może być konieczne są wielokrotnie. Jeśli nie ma rodzica usługi, należy utworzyć jedną, która służy jako symbol zastępczy. W zależności od wymagań, możesz zapoznać się [grup aplikacji](service-fabric-cluster-resource-manager-application-groups.md).

<center>
![Łańcuchy vs. Gwiazdki w kontekście relacje koligacji][Image2]
</center>

Innym czynnikiem, który należy pamiętać o relacjach koligacji dzisiaj te są dwukierunkowego. Oznacza to, że reguła koligacji wymusza tylko umieszczanie z nadrzędnego elementu podrzędnego. Zapewnia to, że element nadrzędny znajduje się z elementem podrzędnym. Jest również należy pamiętać, że relacja koligacji nie jest doskonała lub natychmiast wymuszana od różnych usług z różnych cykle i może zakończyć się niepowodzeniem, a przenosić niezależnie. Załóżmy na przykład, że element nadrzędny nagła awaria za pośrednictwem do innego węzła, ponieważ wystąpiła awaria. Menedżer zasobów klastra i menedżera trybu Failover obsługują tryb failover najpierw od zatrzymując usług, spójne, a dostępna jest priorytet. Po zakończeniu trybu failover, relacja koligacji zostało przerwane, ale Menedżera zasobów klastra sądzi, że wszystko jest poprawnie, dopóki nie wykryje, że obiekt podrzędny nie znajduje się z nadrzędnym. Tego rodzaju testy są wykonywane okresowo. Więcej informacji na temat jak Menedżer zasobów klastra ocenia ograniczenia jest dostępna w [w tym artykule](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), i [tego](service-fabric-cluster-resource-manager-balancing.md) zawiera więcej informacji na temat sposobu konfigurowania okresach, na którym są tych warunków ograniczających obliczone.   


### <a name="partitioning-support"></a>Partycjonowanie pomocy technicznej
Końcowy jest, aby uwagi dotyczące koligacji jest koligacji, że relacje nie są obsługiwane, gdy element nadrzędny jest podzielona na partycje. Usługi nadrzędnej podzielonym na partycje, które mogą być obsługiwane po pewnym czasie, ale obecnie nie jest dozwolone.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usługi [Dowiedz się więcej o konfigurowaniu usługi](service-fabric-cluster-resource-manager-configure-services.md)
- Do ograniczenia usług niewielki zestaw komputerów lub agregowanie obciążenie usługi, użyj [grup aplikacji](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png