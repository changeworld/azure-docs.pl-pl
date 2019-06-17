---
title: Uruchamianie obciążeń na ekonomiczne o niskim priorytecie maszyny wirtualne — Azure Batch | Dokumentacja firmy Microsoft
description: Dowiedz się, jak inicjować obsługę maszyn wirtualnych o niskim priorytecie, aby zmniejszyć koszt obciążeń usługi Azure Batch.
services: batch
author: mscurrell
manager: jeconnoc
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 03/19/2018
ms.author: markscu
ms.custom: seodec18
ms.openlocfilehash: 17668470be3e997c215aacc4cc2c32c80de2dd81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60776142"
---
# <a name="use-low-priority-vms-with-batch"></a>Używanie maszyn wirtualnych o niskim priorytecie z usługą Batch

Usługa Azure Batch oferuje maszyny wirtualne o niskim priorytecie (VM), aby zmniejszyć koszt obciążeń usługi Batch. Maszyny wirtualne o niskim priorytecie należy nowych typów obciążeń, możliwe, należy włączyć dużą ilość obliczeniowa służący do bardzo niskich kosztów usługi Batch.
 
Maszyny wirtualne o niskim priorytecie zalet nadwyżki zdolności produkcyjnych na platformie Azure. Po określeniu maszyny wirtualne o niskim priorytecie w pulach usługi Azure Batch można używać tego nadwyżka, jeśli jest dostępna.
 
Kompromisem za ułatwienie przy użyciu maszyn wirtualnych o niskim priorytecie jest, że te maszyny wirtualne mogą być niedostępne do alokacji lub mogą być przerywane, w dowolnym momencie, w zależności od dostępnej pojemności. Z tego powodu maszyny wirtualne o niskim priorytecie są najbardziej odpowiednie dla niektórych rodzajów obciążeń. Dla usługi batch i obciążeń przetwarzania asynchronicznego, których czas ukończenia zadania jest elastyczny i praca jest rozłożona na wiele maszyn wirtualnych, należy użyć maszyn wirtualnych o niskim priorytecie.
 
Maszyny wirtualne o niskim priorytecie są oferowane w znacznie obniżonej cenie w porównaniu z dedykowanych maszyn wirtualnych. Aby uzyskać szczegółowe informacje o cenach, zobacz [ceny usługi Batch](https://azure.microsoft.com/pricing/details/batch/).

## <a name="use-cases-for-low-priority-vms"></a>Przypadki użycia dla maszyn wirtualnych o niskim priorytecie

Biorąc pod uwagę charakterystyki maszyny wirtualne o niskim priorytecie, jakie obciążeń i nie można ich używać? Ogólnie rzecz biorąc obciążeń przetwarzania wsadowego są dobrym rozwiązaniem, ponieważ zadania są dzielone na wiele zadań równoległych lub istnieje wiele zadań, które są skalowane do wewnątrz i rozproszone między wiele maszyn wirtualnych.

-   Aby zmaksymalizować wykorzystanie nadwyżki zdolności produkcyjnych na platformie Azure, odpowiedniego zadania można skalować w poziomie.

-   Czasami maszyny wirtualne mogą być niedostępne lub są przerywane, które powoduje zmniejszenie wydajności dla zadań i może prowadzić do przerwania zadań i powtórkami. W związku z tym należy elastyczne w czasie, które można wykonać, aby uruchomić zadania.

-   Zadania z zadaniami dłużej może wpływać na bardziej przerwania. Jeśli długotrwałych zadań implementować punkty kontrolne, aby zapisać postęp, jak są wykonywane, zostanie zmniejszona wpływu na przerwania. Zadania związane z krótsze czasy wykonania zwykle by najlepiej pracować z maszyn wirtualnych o niskim priorytecie, ponieważ wpływu na przerwania jest znacznie mniej.

-   Długotrwałych zadań MPI, które korzystają z wielu maszyn wirtualnych nie są dobrze nadaje się do użycia w przypadku maszyn wirtualnych o niskim priorytecie, ponieważ jedną maszynę Wirtualną, które są przerywane może prowadzić do całego zadania o ponowne uruchomienie.

Przykładowe przypadki użycia przetwarzania wsadowego dobrze nadaje się do użycia maszyn wirtualnych o niskim priorytecie są:

-   **Projektowanie i testowanie**: W szczególności jeśli opracowywane są na dużą skalę rozwiązania można uzyskać znaczne oszczędności. Wszystkie rodzaje testów mogą korzystać, ale testowania obciążenia na dużą skalę i testowaniu metodą regresji są doskonałe używa.

-   **Uzupełniające pojemności na żądanie**: Maszyny wirtualne o niskim priorytecie może służyć jako uzupełnienie zwykłe dedykowanych maszyn wirtualnych — jeśli są dostępne, skalowanie i w związku z tym należy wykonać dzięki szybszemu niższy koszt; zadania gdy nie jest dostępny, pozostanie linii bazowej dedykowanych maszyn wirtualnych.

-   **Czas wykonywania zadania elastyczne**: Jeśli pewien stopień elastyczności w czasie zadań musi zostać zakończony, a następnie potencjalne przerwy w pojemności może być tolerowana; Jednak w przypadku dodawania zadania maszyn wirtualnych o niskim priorytecie są często uruchamiane szybciej i niższym kosztem.

Można skonfigurować pule usługi Batch w celu użycia maszyn wirtualnych o niskim priorytecie na kilka sposobów, w zależności od elastyczność w momencie wykonywania zadania:

-   Maszyny wirtualne o niskim priorytecie może służyć wyłącznie w puli. W tym przypadku usługi Batch odzyskuje wszystkie preempted pojemności, jeśli jest dostępna. Ta konfiguracja jest najtańszy sposób wykonania zadania, maszyn wirtualnych tylko o niskim priorytecie są używane.

-   Maszyny wirtualne o niskim priorytecie może służyć w połączeniu z stały punktu odniesienia dedykowanych maszyn wirtualnych. Stała liczba dedykowanych maszyn wirtualnych zapewnia, że zawsze jest niektóre pojemność, aby zachować postępu zadania.

-   Może być dynamiczny kombinacji maszyny wirtualne dedykowane i o niskim priorytecie, tak, aby tańsze o niskim priorytecie maszyny wirtualne są używane wyłącznie, gdy jest to dostępne, ale ceny pełnej dedykowanych maszyn wirtualnych są skalowane w górę gdy jest to wymagane. Ta konfiguracja zapewnia minimalną ilość miejsca, do prowadzenia zadań postępu.

## <a name="batch-support-for-low-priority-vms"></a>Obsługa usługi Batch dla maszyn wirtualnych o niskim priorytecie

Usługa Azure Batch oferuje kilka możliwości, które ułatwiają używanie i korzystać z maszyn wirtualnych o niskim priorytecie:

-   Pule usługi Batch może zawierać zarówno dedykowanych maszyn wirtualnych, jak i maszyny wirtualne o niskim priorytecie. Po utworzeniu puli lub zmienić w dowolnym momencie do istniejącej puli, za pomocą operacji jawne zmiany rozmiaru lub skalowanie automatyczne można określić liczbę każdego typu maszyny Wirtualnej. Przesyłanie zadań i zadań może pozostać bez zmian, niezależnie od tego, typy maszyn wirtualnych w puli. Można również skonfigurować pulę, aby całkowicie używać do uruchamiania zadań jako niedrogo, jak to możliwe, ale uruchamiania dedykowanych maszyn wirtualnych, gdy wydajność spada poniżej minimalny próg, aby zachować zadania uruchomione maszyny wirtualne o niskim priorytecie.

-   Pule usługi Batch automatycznie wyszukać docelowa liczba maszyn wirtualnych o niskim priorytecie. Jeśli maszyny wirtualne są przerywane, Batch podejmuje próbę zastąpić utraconą wydajność i powrócić do obiektu docelowego.

-   Przerwanie zadania usługi Batch wykrywa i automatycznie requeues zadania, aby ponownie uruchomić.

-   Maszyny wirtualne o niskim priorytecie mają przydział oddzielne procesora wirtualnego vCPU, który różni się od tego dla dedykowanych maszyn wirtualnych. 
    Limit przydziału dla maszyn wirtualnych o niskim priorytecie jest wyższa niż limit przydziału dla dedykowanych maszyn wirtualnych, ponieważ maszyny wirtualne o niskim priorytecie koszt mniej. Aby uzyskać więcej informacji, zobacz [Batch przydziałach i limitach usługi](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Maszyny wirtualne o niskim priorytecie nie są obecnie obsługiwane dla kont usługi Batch utworzonych w [trybu subskrypcji użytkownika](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Tworzenie i aktualizowanie pul

Puli usługi Batch mogą zawierać zarówno w przypadku dedykowanego, jak i o niskim priorytecie maszyn wirtualnych (nazywane także jako węzły obliczeniowe). Docelowa liczba węzłów obliczeniowych można ustawić dla maszyn wirtualnych zarówno w przypadku dedykowanego, jak i o niskim priorytecie. Docelowa liczba węzłów określa liczbę maszyn wirtualnych, które mają w puli.

Na przykład aby utworzyć pulę przy użyciu maszyn wirtualnych usługi w chmurze platformy Azure z obiektem docelowym 5 dedykowanych maszyn wirtualnych i 20 maszyn wirtualnych o niskim priorytecie:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Aby utworzyć pulę przy użyciu maszyn wirtualnych platformy Azure (w tym przypadku maszyn wirtualnych systemu Linux) z obiektem docelowym 5 w wersji dedykowanej maszyn wirtualnych i 20 maszyn wirtualnych o niskim priorytecie:

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

Bieżąca liczba węzłów można uzyskać dla dedykowanych i o niskim priorytecie maszyn wirtualnych:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Węzły puli mają właściwości, aby wskazać, czy węzeł dedykowanych lub o niskim priorytecie maszyny Wirtualnej:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Gdy jeden lub więcej węzłów w puli są przerywane, operacja wygenerowania listy węzłów w puli nadal zwraca tych węzłów. Bieżąca liczba węzłów o niskim priorytecie nie jest zmieniany, ale te węzły mają ich stan ustawiony na **przeniesione** stanu. Próbuje znaleźć zastępczy maszyny wirtualne partii, a w przypadku powodzenia węzły przechodzą przez **tworzenie** i następnie **od** państw, zanim staje się dostępna do wykonania zadania podrzędnego, podobnie jak w przypadku nowych węzłów.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Skalowanie puli zawierających maszyny wirtualne o niskim priorytecie

Dzięki pulom wyłącznie składający się z dedykowanych maszyn wirtualnych, jest to możliwe przeskalować pulę zawierającą maszyny wirtualne o niskim priorytecie, przez wywołanie metody zmiany rozmiaru lub przy użyciu skalowania automatycznego.

Operacji zmiany rozmiaru puli zajmuje drugi opcjonalnym parametrem, który aktualizuje wartość **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Formuła automatycznego skalowania puli obsługuje maszyny wirtualne o niskim priorytecie w następujący sposób:

-   Można pobrać lub ustawić wartość zmiennej zdefiniowane przez usługę **$TargetLowPriorityNodes**.

-   Można uzyskać wartości zmiennej zdefiniowane przez usługę **$CurrentLowPriorityNodes**.

-   Można uzyskać wartości zmiennej zdefiniowane przez usługę **$PreemptedNodeCount**. 
    Ta zmienna zwraca liczbę węzłów w stanie preempted i pozwala na skalowanie w górę lub w dół liczbę dedykowanych węzłów, w zależności od liczby węzłów przerywane, które są niedostępne.

## <a name="jobs-and-tasks"></a>Zadania i podzadania

Zadania i podzadania wymaga nieco dodatkowej konfiguracji węzłów o niskim priorytecie; obsługują tylko jest następująca:

-   Właściwość JobManagerTask zadania ma nową właściwość **AllowLowPriorityNode**. 
    Gdy ta właściwość ma wartość true, można zaplanować zadanie podrzędne Menedżera zadań w węźle dedykowanych lub o niskim priorytecie. Jeśli ta właściwość ma wartość false, zadanie podrzędne Menedżera zadań jest zaplanowane tylko dedykowanych węzłów.

-   [Zmiennej środowiskowej](batch-compute-node-environment-variables.md) jest dostępna dla aplikacji zadania podrzędnego, dzięki czemu można określić, czy jest uruchomiona w węźle o niskim priorytecie i dedykowanych. Zmienna środowiskowa jest AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Obsługa wywłaszczania

Maszyny wirtualne mogą być wypierane; w przypadku wywłaszczania Batch wykonuje następujące czynności:

-   Preempted maszyny wirtualne mają stanu, który został zaktualizowany do **przeniesione**.
-   Jeśli zadania zostały uruchomione na maszynach wirtualnych przeniesiona węzeł, te zadania są ponownie umieszczone w kolejce i uruchom ponownie.
-   Maszyna wirtualna jest skutecznie usunięta, co prowadzi do utraty wszystkich danych przechowywanych lokalnie na maszynie Wirtualnej.
-   Pula stale próbuje nawiązać połączenie docelowej liczby dostępnych węzłów o niskim priorytecie. W przypadku odnalezienia pojemności zastąpienie węzły Zachowaj ich identyfikatorów, ale są ponownie inicjowane, przeprowadzając **tworzenie** i **od** państw, zanim staną się dostępne w przypadku planowania zadań.
-   Wywłaszczanie liczniki są dostępne jako metryki w witrynie Azure portal.

## <a name="metrics"></a>Metryki

Nowe metryki są dostępne w [witryny Azure portal](https://portal.azure.com) dla węzłów o niskim priorytecie. Te metryki są:

- Liczba węzłów o niskim priorytecie
- Liczba rdzeni o niskim priorytecie 
- Przerywane liczba węzłów

Aby wyświetlić metryki w witrynie Azure portal:

1. Przejdź do swojego konta usługi Batch w portalu i wyświetlanie ustawień dla konta usługi Batch.
2. Wybierz **metryki** z **monitorowanie** sekcji.
3. Wybierz metryki użytkowników z **dostępne metryki** listy.

![Metryki dla węzłów o niskim priorytecie](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj artykuł [Batch feature overview for developers](batch-api-basics.md) (Omówienie funkcji usługi Batch dla deweloperów) zawierający informacje kluczowe dla wszystkich osób przygotowujących się do korzystania z usługi Batch. Ten artykuł zawiera bardziej szczegółowe informacje o zasobach usługi Batch, takich jak pule, węzły i zadania oraz wielu funkcjach API, których można używać podczas kompilowania aplikacji usługi Batch.
* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
