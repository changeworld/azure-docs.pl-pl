---
title: Uruchom partii zadań Azure obciążeń na maszynach wirtualnych, ekonomiczne niskiego priorytetu | Dokumentacja firmy Microsoft
description: Informacje o sposobie obsługi administracyjnej maszyn wirtualnych niskiego priorytetu będzie zmniejszenie kosztów obciążeń partii zadań Azure.
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
ms.openlocfilehash: 954616e8fbf9e3c3be35fc219d15e3fb36260e1f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34608920"
---
# <a name="use-low-priority-vms-with-batch"></a>Niskiego priorytetu maszyn wirtualnych za pomocą usługi partia zadań

Partia zadań Azure oferuje niskiego priorytetu maszyn wirtualnych (VM) będzie zmniejszenie kosztów obciążeń partii. Maszyny wirtualne o niskim priorytecie należy nowych typów partii obciążeń możliwe przez włączenie dużej ilości mocy do zastosowania w przypadku bardzo niskich kosztów obliczeniowej.
 
Maszyny wirtualne niskiego priorytetu korzystać z nadwyżki zdolności produkcyjnych na platformie Azure. Po określeniu niskiego priorytetu maszyny wirtualne w Twojej puli partii zadań Azure można użyć tego nadwyżka, jeśli jest dostępna.
 
Rozwiązanie dla przy użyciu niskiego priorytetu maszyny wirtualne nie mogą nie być dostępne do alokacji tych maszyn wirtualnych lub może być wywłaszczony w dowolnym momencie, w zależności od dostępnej pojemności. Z tego powodu niskiego priorytetu maszyny wirtualne są najbardziej odpowiednie dla pewnych typów obciążeń. Użyj maszyn wirtualnych niskiego priorytetu partii i przetwarzania asynchronicznego obciążeń, których czas ukończenia zadania są elastyczne i praca będzie rozmieszczona na wiele maszyn wirtualnych.
 
Maszyny wirtualne niskiego priorytetu oferowanych w znacznie obniżonej cenie w porównaniu z dedykowanych maszyn wirtualnych. Aby uzyskać szczegółowe informacje o cenach, zobacz [cennik partii](https://azure.microsoft.com/pricing/details/batch/).

## <a name="use-cases-for-low-priority-vms"></a>Przypadki użycia dla maszyn wirtualnych o niskim priorytecie

Podane właściwości niskiego priorytetu maszyny wirtualne, jakie obciążenia może i nie można ich używać? Ogólnie rzecz biorąc obciążeń przetwarzania wsadowego są świetnie sprawdza się, ponieważ zadania są podzielone na wiele zadań równoległych lub ma wiele zadań, które skalowana w poziomie i rozproszone na wielu maszyn wirtualnych.

-   Aby zapewnić maksymalne użycie nadwyżki pojemności na platformie Azure, odpowiedniego zadania można skalować w poziomie.

-   Czasami maszyny wirtualne mogą być niedostępne lub są zastępowane, które powoduje zmniejszenie wydajności dla zadań i może prowadzić do przerwania zadań i powtórki. W związku z tym należy elastycznych w czasie, które można wykonać, aby uruchomić zadania.

-   Zadania z zadaniami dłużej może to mieć wpływ na więcej przerwania. Jeśli długotrwałych zadań implementować tworzenie punktów kontrolnych do zapisania postępu, ponieważ są one wykonywane, zostanie zmniejszona wpływ przerwania. Zadania związane z krótszy czas wykonywania zazwyczaj najlepiej o niskim priorytecie maszyn wirtualnych, ponieważ wpływ przerwania jest znacznie mniej.

-   Długotrwałych zadań MPI, wykorzystujące wiele maszyn wirtualnych nie są dobrze nadają się do użycia VMs niskiego priorytetu, ponieważ jedna przeniesiona maszyna wirtualna może prowadzić do całego zadania o ponowne uruchomienie.

Przykładowe przypadki użycia przetwarzania wsadowego dobrze nadaje się do użycia niskiego priorytetu maszyny wirtualne to:

-   **Projektowanie i testowanie**: W szczególności, jeśli są tworzone na dużą skalę rozwiązania, znaczne oszczędności mogą być realizowana. Wszystkie typy testowania można korzystać, ale testów obciążenia na dużą skalę i testowania regresji są doskonałe używa.

-   **Uzupełniające pojemności na żądanie**: niskiego priorytetu maszyn wirtualnych może służyć do uzupełnienia regular dedykowanych maszyn wirtualnych — Jeśli jest dostępny, zadania można skalować i z tego powodu ukończyć przyspieszają obniżenia kosztów; Jeśli nie jest dostępny, linia bazowa dedykowanych maszyn wirtualnych jest nadal dostępny .

-   **Czas wykonania zadania elastyczne**: w przypadku elastyczność w zadaniach czasu trzeba wykonać, a następnie potencjalnych porzucania pojemności może następować; z uwzględnieniem maszyn wirtualnych niskiego priorytetu zadania często uruchamiać szybsze i tańsze.

Pule partii może być skonfigurowana do używania niskiego priorytetu maszyny wirtualne na kilka sposobów, w zależności od elastyczność w czasie wykonywania zadania:

-   Można jedynie niskiego priorytetu maszyny wirtualne w puli. W takim przypadku partii odzyskuje wszelkie preempted pojemności, jeśli jest dostępna. Ta konfiguracja jest najtańszej sposobem wykonania zadania, które są używane tylko niskiego priorytetu maszyny wirtualne.

-   Maszyny wirtualne niskiego priorytetu może służyć w połączeniu z linii bazowej stałym dedykowanych maszyn wirtualnych. Stała liczba dedykowanych maszyn wirtualnych zapewnia, że jest zawsze niektórych wydajności, aby zachować postęp zadania.

-   Może istnieć dynamiczne mieszanego dedykowanej i niskiego priorytetu maszyn wirtualnych, tak, aby tańsze niskiego priorytetu maszyny wirtualnej wyłącznie są używane, jeśli jest dostępny, ale dedykowanych maszyn wirtualnych cenach full są skalowane w górę na żądanie. Ta konfiguracja zapewnia minimalna ilość zdolności do prowadzenia postępu zadania.

## <a name="batch-support-for-low-priority-vms"></a>Obsługa partii dla maszyn wirtualnych o niskim priorytecie

Partia zadań Azure oferuje kilka możliwości, które ułatwiają korzystanie i korzystają z maszyn wirtualnych o niskim priorytecie:

-   Pule partii może zawierać zarówno dedykowanych maszyn wirtualnych, jak i niskiego priorytetu maszyn wirtualnych. Podczas tworzenia puli lub zmienić w dowolnym momencie dla istniejącej puli, przy użyciu operacji zmiany rozmiaru jawne lub automatyczne skalowanie można określić liczbę każdego typu maszyny wirtualnej. Przesyłanie zadań i zadań może pozostać bez zmian, niezależnie od tego, typy maszyn wirtualnych w puli. Można również skonfigurować pulę całkowicie maszyn wirtualnych niskiego priorytetu uruchomienie zadania jako tanio, jak to możliwe, ale pokrętła zapasowej dedykowanych maszyn wirtualnych, jeśli pojemność spadnie poniżej minimalnej, aby zachować zadania uruchomione.

-   Pule partii automatycznie wyszukiwać docelowej liczby maszyn wirtualnych o niskim priorytecie. Jeśli maszyny wirtualne są zastępowane, partii podejmuje próbę Zastąp utracone pojemności i zwracany do obiektu docelowego.

-   Przerwanie zadania wsadowego wykrywa i automatycznie requeues zadania, aby uruchomić ponownie.

-   Maszyny wirtualne o niskim priorytecie ma przydział oddzielnych vCPU, która różni się od dla dedykowanych maszyn wirtualnych. 
    Limit przydziału maszyn wirtualnych o niskim priorytecie jest większy niż limit przydziału dla dedykowanych maszyn wirtualnych, ponieważ koszt niskiego priorytetu maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [partii usługi przydziały i limity](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Niskiego priorytetu maszyny wirtualne nie są obsługiwane dla kont usługi partia zadań utworzonych w [tryb subskrypcji użytkownika](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Tworzenie i aktualizowanie pul

Pula usługi partia zadań może zawierać dedykowanych i niskiego priorytetu maszyny wirtualne (zwaną także jako węzły obliczeniowe). Można ustawić docelowy liczba węzłów obliczeniowych dla dedykowanych i niskiego priorytetu maszyn wirtualnych. Docelowy liczba węzłów określa liczbę maszyn wirtualnych mają w puli.

Na przykład aby utworzyć pulę przy użyciu usługi w chmurze Azure maszyn wirtualnych z elementem docelowym 5 dedykowanych maszyn wirtualnych i 20 niskiego priorytetu maszyn wirtualnych:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4") // WS 2012 R2
);
```

Aby utworzyć pulę przy użyciu maszyn wirtualnych platformy Azure (w tym przypadku maszyn wirtualnych systemu Linux) z docelowym 5 dedykowanych maszyn wirtualnych i 20 niskiego priorytetu maszyn wirtualnych:

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

Bieżąca liczba węzłów można uzyskać dedykowane i niskiego priorytetu maszyn wirtualnych:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Węzły puli mają właściwości, aby wskazać, czy węzeł jest dedykowanej lub niskiego priorytetu maszyny Wirtualnej:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Jeśli co najmniej jeden węzeł w puli są zastępowane, operację listy węzłów w puli nadal zwraca tych węzłów. Bieżąca liczba węzłów niskiego priorytetu pozostaje bez zmian, ale te węzły mają ich stan ustawioną **przeniesione** stanu. Partii próbuje odnaleźć zastąpienia maszyn wirtualnych, a w razie powodzenia węzły przejść przez **tworzenie** , a następnie **uruchamianie** stanów, zanim staną się dostępne do wykonania zadania, podobnie jak nowe węzły.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Skalowanie pulę zawierającą maszyn wirtualnych o niskim priorytecie

Przy użyciu pul wyłącznie składające się z dedykowanych maszyn wirtualnych, jest możliwość skalowania zawierających maszyny wirtualne o niskim priorytecie przez wywołanie metody zmiany rozmiaru lub przy użyciu automatycznego skalowania puli.

Drugi parametr opcjonalny, który aktualizuje wartość przyjmuje operacji zmiany rozmiaru puli **targetLowPriorityNodes**:

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Formuła automatycznego skalowania puli obsługuje niskiego priorytetu maszyn wirtualnych w następujący sposób:

-   Można pobrać lub ustawić wartość zmiennej zdefiniowanej przez usługę **$TargetLowPriorityNodes**.

-   Można uzyskać wartości zmiennej zdefiniowanej przez usługę **$CurrentLowPriorityNodes**.

-   Można uzyskać wartości zmiennej zdefiniowanej przez usługę **$PreemptedNodeCount**. 
    Ta zmienna zwraca liczbę węzłów w stanie preempted i umożliwia skalowanie w górę lub w dół Liczba dedykowanych węzłów, w zależności od liczby węzłów wywłaszczone, które są niedostępne.

## <a name="jobs-and-tasks"></a>Zadań i zadań

Zadań i zadań wymaga niewiele dodatkowej konfiguracji dla węzłów niskiego priorytetu; obsługiwane jest tylko wygląda następująco:

-   Właściwość JobManagerTask zadania ma nową właściwość **AllowLowPriorityNode**. 
    Jeśli ta właściwość ma wartość true, można zaplanować zadanie Menedżer zadania w węźle dedykowanej lub niskim priorytecie. Jeśli ta właściwość ma wartość false, zadanie Menedżer zadania jest zaplanowane tylko dedykowane węzła.

-   [Zmiennej środowiskowej](batch-compute-node-environment-variables.md) jest dostępna w aplikacji zadań, dzięki czemu można określić, czy jest uruchomiona w węźle niskiego priorytetu lub dedykowanych. Zmienna środowiskowa jest AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Obsługa wywłaszczania

Maszyny wirtualne od czasu do czasu może być wywłaszczony; w przypadku wywłaszczanie partii wykonuje następujące czynności:

-   Preempted maszyny wirtualne mają ich stan aktualizacji do **przeniesione**.
-   Jeśli zadania zostały uruchomione na maszynach wirtualnych węzła wywłaszczone, te zadania są umieszczony w kolejce i uruchom ponownie.
-   Maszyna wirtualna jest skutecznie usunięta, co prowadziło do utraty wszystkich danych przechowywanych lokalnie na maszynie Wirtualnej.
-   Pula stale próbuje nawiązać połączenie docelowej liczba dostępnych węzłów niskiego priorytetu. Po znalezieniu pojemności zastępczy Zachowaj ich identyfikatory węzłów, ale są ponownie inicjowane, pośrednictwa **tworzenie** i **uruchamianie** stwierdza, zanim staną się dostępne do planowania zadań.
-   Wywłaszczanie liczniki są dostępne jako metrykę w portalu Azure.

## <a name="metrics"></a>Metryki

Dostępne są nowe metryki [portalu Azure](https://portal.azure.com) dla węzłów o niskim priorytecie. Te metryki są:

- Liczba węzłów o niskim priorytecie
- Liczby rdzeni o niskim priorytecie 
- Wywłaszczone liczba węzłów

Aby wyświetlić metryki w portalu Azure:

1. Przejdź do konta partii zadań w portalu i wyświetlenia ustawień konta usługi partia zadań.
2. Wybierz **metryki** z **monitorowanie** sekcji.
3. Wybrać metryki chcesz z **dostępne metryki** listy.

![Metryki dla węzłów o niskim priorytecie](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Kolejne kroki

* Przeczytaj artykuł [Batch feature overview for developers](batch-api-basics.md) (Omówienie funkcji usługi Batch dla deweloperów) zawierający informacje kluczowe dla wszystkich osób przygotowujących się do korzystania z usługi Batch. Ten artykuł zawiera bardziej szczegółowe informacje o zasobach usługi Batch, takich jak pule, węzły i zadania oraz wielu funkcjach API, których można używać podczas kompilowania aplikacji usługi Batch.
* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
