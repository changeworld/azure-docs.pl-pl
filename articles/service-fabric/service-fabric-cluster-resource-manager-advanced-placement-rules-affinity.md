---
title: Menedżer zasobów klastra usługi Service Fabric — koligacja | Dokumentacja firmy Microsoft
description: Omówienie konfigurowania koligacji dla usługi Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 29377492b90f366227ca7bedf85890b7734ea25f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663421"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurowanie i używanie koligacji usługi w usłudze Service Fabric
Koligacja jest formant, który znajduje się przede wszystkim ułatwiają przejście większej aplikacji monolitycznych w świecie chmury i mikrousług. Jest również używany jako optymalizacji dla poprawy wydajności usługi, mimo że ten sposób można mieć skutki uboczne.

Załóżmy, że korzystamy większej aplikacji lub taką, która po prostu nie została zaprojektowana pod kątem mikrousług należy pamiętać, aby Usługa Service Fabric (lub dowolnym środowisku rozproszonym). Ten typ przejścia jest wspólne. Uruchom, przenosząc całej aplikacji do środowiska, pakowanie i czy działa bez problemów. Następnie możesz uruchomić podzielenie go na różnych usługach mniejszych wszystkie komunikować się ze sobą.

Po pewnym czasie może się okazać, że aplikacji występują pewne problemy. Problemy zazwyczaj dzielą się na jednej z tych kategorii:

1. Niektóre składnik X monolityczną aplikację miał nieudokumentowane zależność na składnik Y, a tylko włączone tych składników na osobne usługi. Ponieważ te usługi są uruchomione na różnych węzłach w klastrze, są one uszkodzone.
2. Te składniki komunikują się za pośrednictwem (lokalnej nazwane potoki | pamięć współużytkowaną | pliki na dysku) i naprawdę potrzebne można było zapisać do udostępnionego zasobu lokalnego ze względu na wydajność teraz. Tej zależności twardych usuwana później, być może.
3. Wszystko, co jest dobrym rozwiązaniem, ale okazuje się, że te dwa składniki są faktycznie duża liczba/wydajność poufnych. Kiedy je przenieść na osobne usługi ogólną wydajność aplikacji tanked lub opóźnienia zwiększyć. W rezultacie cała aplikacja nie spełniają oczekiwań.

W takich przypadkach firma Microsoft nie chcesz stracić współpracując refaktoryzacji, a nie chcesz wrócić do monolitu. Ostatni warunek może być pożądane jako zwykły optymalizacji. Jednak do czasu firma Microsoft możesz ponownie zaprojektować składników do pracy w naturalny sposób jako usługi (lub do czasu firma Microsoft może rozwiązać oczekiwania dotyczące wydajności w inny sposób) zamierzamy muszą pewnym sensie miejscowości.

Co można zrobić w takiej sytuacji? Możesz również spróbować Włączenie koligacji.

## <a name="how-to-configure-affinity"></a>Jak skonfigurować koligację
Aby skonfigurować koligację, należy zdefiniować relacja koligacji między dwoma różnymi usługami. Można potraktować koligacji "wskazującego" w jednej usłudze innej oraz informujący o tym, "tej usługi można uruchamiać tylko gdy czy usługa jest uruchomiona." Czasami nazywamy koligacji relacji nadrzędny/podrzędny (gdzie wskazaniu elementu podrzędnego w element nadrzędny). Koligacji gwarantuje, że replik lub wystąpień jednej usługi są umieszczane w tej samej węzłów jako udostępnianych przez inną usługę.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Usługa podrzędnych tylko mogą uczestniczyć w relacji koligacji pojedyncze. Jeżeli chcesz, aby dziecko skoligaconym się dwie usługi nadrzędnej jednocześnie masz kilka opcji:
> - Odwróć relacje (ma parentService1 i parentService2 punkt u bieżącej usługi podrzędne), lub
> - Wyznaczanie jednego z elementów nadrzędnych koncentrator zgodnie z Konwencją i mieć wszystkie usługi, które wskazują na tę usługę. 
>
> Wynikowe zachowania umieszczenia w klastrze powinna być taka sama.
>

## <a name="different-affinity-options"></a>Koligacja różnych opcji
Koligacja jest przedstawiana za pomocą jednego z kilku systemów korelacji i ma dwa różne tryby. Najbardziej typowe rodzaj koligacji to tak zwany NonAlignedAffinity. W NonAlignedAffinity replik lub wystąpień różnych usług są umieszczane w tej samej węzłów. Inny tryb jest AlignedAffinity. Koligacja wyrównany przydaje się tylko w przypadku usług stanowych. Konfigurowanie dwóch usług stanowych do wyrównany w koligacji gwarantuje, że kolory podstawowe te usługi są umieszczane w tej samej węzłów poszczególnych. On również powoduje, że każda para pomocnicze bazy danych dla tych usług, które mają być umieszczane w tej samej węzłów. Użytkownik może również (chociaż są mniej typowe) można skonfigurować NonAlignedAffinity dla usług stanowych. NonAlignedAffinity różnych replik z dwóch usług stanowych są uruchamiane na tym samym węzłów, ale ich kolory podstawowe można znajdą się w różnych węzłach.

<center>

![Tryby koligacji i ich skutków][Image1]
</center>

### <a name="best-effort-desired-state"></a>Najlepszy nakład pracy żądany stan
Relacja koligacji jest najlepszy nakład pracy. Nie zapewnia te same gwarancje kolokacji lub niezawodność wykonującej uruchomione w tym samym pliku wykonywalnego procesu. Usługi w relacji koligacji są całkowicie różnych obiektów, może zakończyć się niepowodzeniem, które można przenosić niezależnie. Relacja koligacji może również spowodować przerwanie, chociaż te podziały są tymczasowe. Na przykład ograniczenia wydajności może oznaczać, że niektóre obiekty usługi relacja koligacji może zaspokajać w danym węźle. W takich przypadkach, mimo że istnieje relacja koligacji w miejscu, go nie można wymusić ze względu na inne ograniczenia. Jeśli jest to możliwe to zrobić, naruszenia jest automatycznie rozwiązany później.

### <a name="chains-vs-stars"></a>Łańcuchy a gwiazdek
Już dziś Menedżer zasobów klastra nie jest w stanie modelu łańcucha koligacji relacji. Oznacza to, że to usługa, która jest elementem podrzędnym w jednej relacji koligacji nie może być elementem nadrzędnym w innej relacji koligacji. Jeśli chcesz modelu tego typu relacji masz skutecznie modelować gwiazdkę zamiast łańcuch. Aby przenieść z łańcucha gwiazdkę, znajdujących się najniżej podrzędnych może być elementem nadrzędnym do pierwszy element podrzędny elementu nadrzędnego zamiast tego. W zależności od rozmieszczenie usług może trzeba wielokrotnie. Nie ma żadnej usługi rodzica, należy utworzyć jedną, która służy jako symbol zastępczy. W zależności od wymagań możesz zbadać [grup aplikacji](service-fabric-cluster-resource-manager-application-groups.md).

<center>

![Łańcuchy programu vs. Gwiazdek w kontekście relacje koligacji][Image2]
</center>

Innym czynnikiem, który należy zwrócić uwagę na relacje koligacji już dziś jest, czy kierunkowe domyślnie. Oznacza to, że reguła koligacji wymusza tylko że podrzędne są umieszczane z elementem nadrzędnym. Nie zapewnia to, że element nadrzędny znajduje się za pomocą elementu podrzędnego. W związku z tym jeśli istnieje naruszenie koligacji, a aby naprawić naruszenie jakiegoś powodu nie jest możliwe, następnie przenieść element podrzędny węzła nadrzędnego, — nawet w razie przenoszenia obiektu nadrzędnego do węzła podrzędnego będzie mieć naruszenia — element nadrzędny nie zostaną przeniesione do kwietnia węzeł podrzędny e. Ustawienie konfiguracji [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) do wartości true spowoduje usunięcie kierunkowość. Jest również pamiętać, że relacja koligacji nie jest doskonała lub natychmiast wymuszana ponieważ różne usługi z różnych cykle życia może zakończyć się niepowodzeniem i przenosić niezależnie. Załóżmy na przykład, że element nadrzędny nagle awaryjnie do innego węzła, ponieważ wystąpiła awaria. Menedżer zasobów klastra i Menedżer trybu Failover obsługi trybu failover najpierw od Nadążanie usług, spójne i dostępna jest priorytetem. Po zakończeniu trybu failover relacja koligacji jest uszkodzona. Jednak Menedżer zasobów klastra sądzą, że wszystko jest poprawnie, dopóki zauważa, że podrzędne nie znajduje się z obiektem nadrzędnym. Tego rodzaju testy są wykonywane okresowo. Więcej informacji na temat sposobu Menedżer zasobów klastra ocenia ograniczenia jest dostępna w [w tym artykule](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), i [tego jednego](service-fabric-cluster-resource-manager-balancing.md) rozmawia więcej informacji na temat sposobu konfigurowania tempo, w którym te ograniczenia są ocenione.   


### <a name="partitioning-support"></a>Partycjonowanie pomocy technicznej
Końcowe rzeczą, którą należy zwrócić uwagę o koligacji jest koligacji, że relacje nie są obsługiwane, gdy element nadrzędny jest podzielona na partycje. Usługi nadrzędnej podzielonym na partycje, które mogą być obsługiwane po pewnym czasie, ale obecnie nie jest dozwolone.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usług [Dowiedz się więcej na temat konfigurowania usługi](service-fabric-cluster-resource-manager-configure-services.md)
- Sposób ograniczania usługi wśród małej grupy maszyn i agregowanie obciążenia usługi, użyj [grupy aplikacji](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png