---
title: Usuwanie typu węzła w sieci szkieletowej usług Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak usunąć typ węzła z klastra sieci szkieletowej usług uruchomionego na platformie Azure.
author: inputoutputcode
manager: sridmad
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: chrpap
ms.openlocfilehash: 330b455a61c45ccdb59e5aef8162fd1b04859a00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969403"
---
# <a name="how-to-remove-a-service-fabric-node-type"></a>Jak usunąć typ węzła sieci szkieletowej usług
W tym artykule opisano sposób skalowania klastra sieci szkieletowej usług Azure przez usunięcie istniejącego typu węzła z klastra. Klaster sieci szkieletowej usług to połączony z siecią zestaw maszyn wirtualnych lub fizycznych, na których mikrousługi są wdrażane i zarządzane. Maszyna lub maszyna wirtualna, która jest częścią klastra jest nazywany węzłem. Zestawy skalowania maszyny wirtualnej to zasób obliczeniowy platformy Azure używany do wdrażania i zarządzania kolekcją maszyn wirtualnych jako zestaw. Każdy typ węzła zdefiniowany w klastrze platformy Azure jest [łączona jako oddzielny zestaw skalowania.](service-fabric-cluster-nodetypes.md) Każdy typ węzła można następnie zarządzać oddzielnie. Po utworzeniu klastra sieci szkieletowej usług można skalować klaster w poziomie, usuwając typ węzła (zestaw skalowania maszyny wirtualnej) i wszystkie jego węzły.  Klaster można skalować w dowolnym momencie, nawet gdy obciążenia są uruchomione w klastrze.  W miarę skalowania klastra aplikacje są również skalowane automatycznie.

> [!WARNING]
> Za pomocą tej metody, aby usunąć typ węzła z klastra produkcyjnego nie jest zalecane do częstego używania. Jest to niebezpieczne polecenie, ponieważ usuwa zasób zestawu skalowania maszyny wirtualnej za typem węzła. 

## <a name="durability-characteristics"></a>Charakterystyka trwałości
Bezpieczeństwo jest priorytetem nad prędkością podczas korzystania z Remove-AzServiceFabricNodeType. Typ węzła musi być [poziomem trwałości](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster)Silver lub Gold, ponieważ:
- Brąz nie daje żadnych gwarancji dotyczących zapisywania informacji o stanie.
- Wytrzymałość na srebro i złoto zalewkuje wszelkie zmiany w zestawie skalowania.
- Gold zapewnia również kontrolę nad aktualizacjami platformy Azure pod zestawem skalowania.

Sieci szkieletowej usług "organizuje" podstawowe zmiany i aktualizacje, dzięki czemu dane nie zostaną utracone. Jednak po usunięciu typu węzła z brązową trwałością, może utracić informacje o stanie. Jeśli usuwasz typ węzła podstawowego, a aplikacja jest bezstanowa, Brązowy jest dopuszczalne. Po uruchomieniu obciążeń stanowych w produkcji, minimalna konfiguracja powinna być Silver. Podobnie w scenariuszach produkcyjnych typ węzła podstawowego powinien być zawsze srebrny lub złoty.

### <a name="more-about-bronze-durability"></a>Więcej o wytrzymałości brązu

Podczas usuwania typu węzła, który jest Brązowy, wszystkie węzły w typie węzła przejść w dół natychmiast. Sieci szkieletowej usług nie pułapki żadnych brązowy węzłów skalowania zestaw aktualizacji, w związku z tym wszystkie maszyny wirtualne przejść w dół natychmiast. Jeśli miałeś coś stanowego w tych węzłach, dane są tracone. Teraz, nawet jeśli jesteś bezstanowy, wszystkie węzły w sieci szkieletowej usług uczestniczyć w pierścieniu, więc całe sąsiedztwo może zostać utracone, co może zdestabilizować samego klastra.

## <a name="remove-a-node-type"></a>Usuwanie typu węzła

1. Przed rozpoczęciem procesu należy zadbać o te wymagania wstępne.

    - Klaster jest w dobrej kondycji.
    - Po usunięciu typu węzła nadal będzie wystarczająca pojemność, np. liczba węzłów do umieszczenia wymaganej liczby replik.

2. Przenieś wszystkie usługi, które mają ograniczenia umieszczania, aby użyć typu węzła poza typem węzła.

    - Zmodyfikuj manifest aplikacji/ usługi, aby nie odwoływać się już do typu węzła.
    - Wdrożenie zmiany.

    Następnie sprawdź, czy:
    - Wszystkie usługi zmodyfikowane powyżej nie są już uruchomione w węźle należącym do typu węzła.
    - Wszystkie usługi są zdrowe.

3. Odznacz typ węzła jako niepodstawowego (Pomiń dla typów węzłów innych niż podstawowe)

    - Znajdź szablon usługi Azure Resource Manager używany do wdrożenia.
    - Znajdź sekcję związaną z typem węzła w sekcji Sieci szkieletowej usług.
    - Zmień isPrimary właściwość false. ** Nie usuwaj sekcji związanej z typem węzła w tym zadaniu.
    - Wdrażanie zmodyfikowanego szablonu usługi Azure Resource Manager. ** W zależności od konfiguracji klastra ten krok może trochę potrwać.
    
    Następnie sprawdź, czy:
    - Sekcja sieci szkieletowej usług w portalu wskazuje, że klaster jest gotowy.
    - Klaster jest w dobrej kondycji.
    - Żaden z węzłów należących do typu węzła nie są oznaczone jako węzeł źródłowy.

4. Wyłącz dane dla typu węzła.

    Połącz się z klastrem za pomocą programu PowerShell, a następnie uruchom następujący krok.
    
    ```powershell
    $nodeType = "" # specify the name of node type
    $nodes = Get-ServiceFabricNode
    
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
      }
    }
    ```

    - Aby uzyskać trwałość brązu, poczekaj, aż wszystkie węzły dostaną się do stanu wyłączonego
    - Dla wytrzymałości srebra i złota, niektóre węzły pójdą do niepełnosprawnych, a reszta będzie w stanie wyłączenia. Sprawdź szczegóły kartę węzłów w stanie wyłączenia, jeśli wszystkie one są zablokowane na zapewnienie kworum dla partycji usługi infrastruktury, a następnie jest bezpieczne, aby kontynuować.

5. Zatrzymaj dane dla typu węzła.

    Połącz się z klastrem za pomocą programu PowerShell, a następnie uruchom następujący krok.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
      }
    }
    ```
    
    Poczekaj, aż wszystkie węzły dla typu węzła są oznaczone w dół.
    
6. Usuń dane dla typu węzła.

    Połącz się z klastrem za pomocą programu PowerShell, a następnie uruchom następujący krok.
    
    ```powershell
    foreach($node in $nodes)
    {
      if ($node.NodeType -eq $nodeType)
      {
        $node.NodeName
     
        Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
      }
    }
    ```

    Poczekaj, aż wszystkie węzły zostaną usunięte z klastra. Węzły nie powinny być wyświetlane w SFX.

7. Usuń typ węzła z sekcji Sieci szkieletowej usług.

    - Znajdź szablon usługi Azure Resource Manager używany do wdrożenia.
    - Znajdź sekcję związaną z typem węzła w sekcji Sieci szkieletowej usług.
    - Usuń sekcję odpowiadającą typowi węzła.
    - Tylko dla klastrów Silver i wyższej trwałości zaktualizuj zasób klastra w `applicationDeltaHealthPolicies` szablonie `properties` i skonfiguruj zasady kondycji, aby ignorować kondycję aplikacji szkieletowej:/System, dodając w obszarze zasób klastra, jak podano poniżej. Poniższe zasady powinny ignorować istniejące błędy, ale nie zezwalają na nowe błędy kondycji. 
 
 
     ```json
    "upgradeDescription":  
    { 
      "forceRestart": false, 
      "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
      "healthCheckWaitDuration": "00:05:00", 
      "healthCheckStableDuration": "00:05:00", 
      "healthCheckRetryTimeout": "00:45:00", 
      "upgradeTimeout": "12:00:00", 
      "upgradeDomainTimeout": "02:00:00", 
      "healthPolicy": { 
        "maxPercentUnhealthyNodes": 100, 
        "maxPercentUnhealthyApplications": 100 
      }, 
      "deltaHealthPolicy":  
      { 
        "maxPercentDeltaUnhealthyNodes": 0, 
        "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
        "maxPercentDeltaUnhealthyApplications": 0, 
        "applicationDeltaHealthPolicies":  
        { 
            "fabric:/System":  
            { 
                "defaultServiceTypeDeltaHealthPolicy":  
                { 
                        "maxPercentDeltaUnhealthyServices": 0 
                } 
            } 
        } 
      } 
    },
    ```

    - Wdrażanie zmodyfikowanego szablonu usługi Azure Resource Manager. ** Ten krok zajmie trochę czasu, zwykle do dwóch godzin. To uaktualnienie spowoduje zmianę ustawień na InfrastructureService, w związku z czym konieczne jest ponowne uruchomienie węzła. W tym `forceRestart` przypadku jest ignorowana. 
    Parametr `upgradeReplicaSetCheckTimeout` określa maksymalny czas oczekiwania sieci szkieletowej usług na partycję w stanie bezpiecznym, jeśli nie jest już w stanie bezpiecznym. Gdy kontrole bezpieczeństwa przechodzą dla wszystkich partycji w węźle, sieci szkieletowej usług kontynuuje uaktualnienie w tym węźle.
    Wartość parametru `upgradeTimeout` można zmniejszyć do 6 godzin, ale dla maksymalnego bezpieczeństwa należy użyć 12 godzin.

    Następnie sprawdź, czy:
    - Zasób sieci szkieletowej usług w portalu jest wyświetlany jako gotowy.

8. Usuń wszystkie odwołania do zasobów związanych z typem węzła.

    - Znajdź szablon usługi Azure Resource Manager używany do wdrożenia.
    - Usuń zestaw skalowania maszyny wirtualnej i inne zasoby związane z typem węzła z szablonu.
    - Wdrażanie zmian.

    Następnie:
    - Poczekaj na zakończenie wdrożenia.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [charakterystykach trwałości klastra](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).
- Dowiedz się więcej o [typach węzłów i zestawach skalowania maszyn wirtualnych](service-fabric-cluster-nodetypes.md).
- Dowiedz się więcej o [skalowaniu klastra sieci szkieletowej usług](service-fabric-cluster-scaling.md).
