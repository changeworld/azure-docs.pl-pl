---
title: Menedżer zasobów klastra sieci szkieletowej usług — koligacja
description: Omówienie koligacji usług dla usług sieci szkieletowej usług Azure Service Fabric i wskazówki dotyczące konfiguracji koligacji usług.
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7bfd261802fbf891b8f45079255783cb1e8ac7d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551747"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Konfigurowanie i używanie koligacji usług w sieci szkieletowej usług
Koligacja jest formantem, który jest dostarczany głównie w celu ułatwienia przejścia większych aplikacji monolitycznych do świata chmury i mikrousług. Jest również używany jako optymalizacja dla poprawy wydajności usług, chociaż może to mieć skutki uboczne.

Załóżmy, że przenosisz większą aplikację lub aplikację, która po prostu nie została zaprojektowana z myślą o mikrousługach, do sieci szkieletowej usług (lub dowolnego środowiska rozproszonego). Ten typ przejścia jest powszechny. Zacznij od podnoszenia całej aplikacji do środowiska, pakowania jej i upewnienia się, że działa płynnie. Następnie zaczynasz rozbijać go na różne mniejsze usługi, które wszystkie ze sobą rozmawiają.

Po pewnym czasie może się okazać, że aplikacja występuje pewne problemy. Problemy zazwyczaj należą do jednej z następujących kategorii:

1. Niektóre składniki X w aplikacji monolityczne miał nieudokumentowane zależności od składnika Y i po prostu okazało tych składników w oddzielnych usług. Ponieważ te usługi są teraz uruchomione w różnych węzłach w klastrze, są one uszkodzone.
2. Składniki te komunikują się za pośrednictwem (lokalne nazwane potoki | pliki pamięci współdzielonej | pliki na dysku) i naprawdę muszą być w stanie zapisać do udostępnionego zasobu lokalnego ze względu na wydajność już teraz. Ta twarda zależność zostanie usunięta później, być może.
3. Wszystko jest w porządku, ale okazuje się, że te dwa składniki są rzeczywiście chatty / wydajność wrażliwe. Po przeniesieniu ich do oddzielnych usług ogólna wydajność aplikacji zatankowane lub opóźnienie zwiększone. W rezultacie ogólna aplikacja nie spełnia oczekiwań.

W takich przypadkach nie chcemy stracić naszej refaktoryzacji i nie chcemy wracać do monolitu. Ostatni warunek może być nawet pożądane jako zwykły optymalizacji. Jednak dopóki nie będziemy mogli przeprojektować komponentów, aby działały naturalnie jako usługi (lub dopóki nie rozwiążemy oczekiwań dotyczących wydajności w inny sposób), będziemy potrzebować pewnego poczucia lokalizacji.

Postępowanie Cóż, możesz spróbować włączyć powinowactwo.

## <a name="how-to-configure-affinity"></a>Jak skonfigurować koligacji
Aby skonfigurować koligacji, należy zdefiniować relację koligacji między dwoma różnymi usługami. Koligacja można potraktować jako "wskaż" jedną usługę na inną i powiedzenie "Ta usługa może działać tylko wtedy, gdy ta usługa jest uruchomiona". Czasami odnosimy się do koligacji jako relacji nadrzędny/podrzędny (gdzie punkt podrzędny na rodzica). Koligacja zapewnia, że repliki lub wystąpienia jednej usługi są umieszczane w tych samych węzłach, co w innych usługach.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Usługa podrzędna może uczestniczyć tylko w relacji jednego koligacji. Jeśli chcesz, aby dziecko zostało powinięte do dwóch usług nadrzędnych naraz, masz kilka opcji:
> - Odwróć relacje (mają nadrzędnyService1 i parentService2 punkt w bieżącej usłudze podrzędnej) lub
> - Wyznaczyć jednego z rodziców jako centrum zgodnie z konwencją i wszystkie usługi punkt w tej usłudze. 
>
> Wynikowe zachowanie umieszczania w klastrze powinno być takie samo.
>

## <a name="different-affinity-options"></a>Różne opcje koligacji
Koligacja jest reprezentowana za pośrednictwem jednego z kilku schematów korelacji i ma dwa różne tryby. Najczęstszym trybem koligacji jest to, co nazywamy NonAlignedAffinity. W NonAlignedAffinity repliki lub wystąpienia różnych usług są umieszczane w tych samych węzłach. Inny tryb jest AlignedAffinity. Wyrównane koligacji jest przydatne tylko w przypadku usług stanowych. Konfigurowanie dwóch usług stanowych, aby mieć wyrównane koligacji zapewnia, że prawybory tych usług są umieszczane w tych samych węzłach, jak siebie nawzajem. Powoduje to również, że każda para pomocniczych dla tych usług, które mają być umieszczone w tych samych węzłach. Jest również możliwe (choć mniej powszechne) skonfigurować NonAlignedAffinity dla usług stanowych. W przypadku nonalignedAffinity różne repliki dwóch usług stanowych będą uruchamiane w tych samych węzłach, ale ich prawybory mogą kończyć się na różnych węzłach.

<center>

![Tryby koligacji i ich skutki][Image1]
</center>

### <a name="best-effort-desired-state"></a>Najlepszy stan pożądany
Relacja koligacji jest najlepszym wysiłkiem. Nie zapewnia te same gwarancje kolokacji lub niezawodności, które działa w tym samym procesie wykonywalnym nie. Usługi w relacji koligacji są zasadniczo różne jednostki, które mogą zakończyć się niepowodzeniem i być przenoszone niezależnie. Relacja koligacji może również przerwać, chociaż te przerwy są tymczasowe. Na przykład ograniczenia pojemności może oznaczać, że tylko niektóre obiekty usługi w relacji koligacji może zmieścić się w danym węźle. W takich przypadkach, mimo że istnieje relacja koligacji w miejscu, nie można wymuszać ze względu na inne ograniczenia. Jeśli jest to możliwe, naruszenie jest automatycznie korygowane później.

### <a name="chains-vs-stars"></a>Łańcuchy kontra gwiazdy
Obecnie Menedżer zasobów klastra nie może modelować łańcuchów relacji koligacji. Oznacza to, że usługa, która jest elementem podrzędnym w jednej relacji koligacji nie może być elementem nadrzędnym w innej relacji koligacji. Jeśli chcesz modelować ten typ relacji, musisz ją modelować jako gwiazdę, a nie łańcuch. Aby przejść z łańcucha do gwiazdy, najniżej podrzędny będzie nadrzędny do pierwszego dziecka nadrzędnego zamiast. W zależności od układu usług, może być to zrobić wiele razy. Jeśli nie ma naturalnej usługi nadrzędnej, może być trzeba utworzyć taki, który służy jako symbol zastępczy. W zależności od wymagań można również przyjrzeć się [grupom aplikacji.](service-fabric-cluster-resource-manager-application-groups.md)

<center>

![Łańcuchy kontra gwiazdy w kontekście relacji powinowactwa][Image2]
</center>

Inną rzeczą, na którą należy zwrócić uwagę na relacje koligacji dzisiaj jest to, że są one kierunkowe domyślnie. Oznacza to, że reguła koligacji wymusza tylko, że element podrzędny umieszczony z elementem nadrzędnym. Nie zapewnia, że rodzic znajduje się z elementem podrzędnym. W związku z tym, jeśli istnieje naruszenie powinowactwa i poprawić naruszenie z jakiegoś powodu nie jest możliwe przeniesienie dziecka do węzła rodzica, a następnie -- nawet jeśli przeniesienie rodzica do węzła dziecka poprawiłoby naruszenie -- rodzic nie zostanie przeniesiony do węzła rodzica. węzła podrzędnego. Ustawienie konfiguracji [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) na true spowoduje usunięcie kierunkowości. Ważne jest również, aby pamiętać, że relacja koligacji nie może być doskonała lub natychmiast wymuszana, ponieważ różne usługi mają różne cykle życia i mogą zakończyć się niepowodzeniem i poruszać się niezależnie. Na przykład załóżmy, że nadrzędny nagle działa w sieci fail, do innego węzła, ponieważ uległ awarii. Menedżer zasobów klastra i Menedżer trybu failover najpierw obsługują pracę awaryjną, ponieważ priorytetem jest utrzymanie usług, spójność i dostępna. Po zakończeniu pracy awaryjnej relacja koligacji jest przerywana, ale Menedżer zasobów klastra uważa, że wszystko jest w porządku, dopóki nie zauważy, że element podrzędny nie znajduje się z elementem nadrzędnym. Tego rodzaju kontrole są wykonywane okresowo. Więcej informacji na temat sposobu oceny ograniczeń przez Menedżera zasobów klastra jest dostępna w [tym artykule,](service-fabric-cluster-resource-manager-management-integration.md#constraint-types)a [ten](service-fabric-cluster-resource-manager-balancing.md) opowiada więcej o tym, jak skonfigurować rytm, na którym te ograniczenia są oceniane.   


### <a name="partitioning-support"></a>Obsługa partycjonowania
Ostatnią rzeczą, aby zauważyć o koligacji jest, że relacje koligacji nie są obsługiwane, gdy element nadrzędny jest podzielony na partycje. Usługi nadrzędne podzielone na partycje mogą być obsługiwane po pewnym czasie, ale dziś nie jest dozwolone.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usług, [dowiedz się więcej o konfigurowaniu usług](service-fabric-cluster-resource-manager-configure-services.md)
- Aby ograniczyć usługi do małego zestawu maszyn lub agregować obciążenie usług, należy użyć [grup aplikacji](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png