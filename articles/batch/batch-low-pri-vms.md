---
title: Uruchamianie obciążeń na oszczędnych maszynach wirtualnych o niskim priorytecie — usługa Azure Batch | Dokumenty firmy Microsoft
description: Dowiedz się, jak aprowizować maszyny wirtualne o niskim priorytecie, aby zmniejszyć koszty obciążeń usługi Azure Batch.
services: batch
author: mscurrell
manager: evansma
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.topic: article
ms.workload: na
ms.date: 03/19/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 9f4b9ed9254eaf950311dd27d5716c4681707614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053908"
---
# <a name="use-low-priority-vms-with-batch"></a>Używanie maszyn wirtualnych o niskim priorytecie z usługą Batch

Usługa Azure Batch oferuje maszyny wirtualne o niskim priorytecie (VM), aby zmniejszyć koszt obciążeń usługi Batch. Maszyny wirtualne o niskim priorytecie umożliwiają nowe typy obciążeń wsadowych, umożliwiając użycie dużej ilości mocy obliczeniowej przy bardzo niskich kosztach.
 
Maszyny wirtualne o niskim priorytecie korzystają z nadwyżki pojemności na platformie Azure. Po określeniu maszyn wirtualnych o niskim priorytecie w pulach usługa Azure Batch może używać tej nadwyżki, jeśli jest dostępna.
 
Kompromisem przy użyciu maszyn wirtualnych o niskim priorytecie jest to, że te maszyny wirtualne mogą nie być dostępne do przydzielenia lub mogą być wywłaszczone w dowolnym momencie, w zależności od dostępnej pojemności. Z tego powodu maszyny wirtualne o niskim priorytecie są najbardziej odpowiednie dla niektórych typów obciążeń. Użyj maszyn wirtualnych o niskim priorytecie dla obciążeń przetwarzania wsadowego i asynchroniczego, w których czas ukończenia zadania jest elastyczny, a praca jest rozproszona na wielu maszynach wirtualnych.
 
Maszyny wirtualne o niskim priorytecie są oferowane po znacznie obniżonej cenie w porównaniu z dedykowanymi maszynami wirtualnymi. Aby uzyskać szczegółowe informacje o cenach, zobacz [Cennik wsadów](https://azure.microsoft.com/pricing/details/batch/).

> [!NOTE]
> [Maszyny wirtualne punktowe](https://azure.microsoft.com/pricing/spot/) są teraz dostępne dla [maszyn wirtualnych z pojedynczym wystąpieniem](https://docs.microsoft.com/azure/virtual-machines/linux/spot-vms) i [zestawów skalowania maszyn wirtualnych.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/use-spot) Maszyny wirtualne spot są ewolucją maszyn wirtualnych o niskim priorytecie, ale różnią się tym, że ceny mogą się różnić i można ustawić opcjonalną cenę maksymalną podczas przydzielania maszyn wirtualnych spot.
>
> Pule usługi Azure Batch zaczną obsługiwać maszyny wirtualne spot w ciągu kilku miesięcy od ich ogólnie dostępnych, z nowymi wersjami [interfejsów API i narzędzi wsadowych.](https://docs.microsoft.com/azure/batch/batch-apis-tools) Po udostępnieniu obsługi maszyn wirtualnych punktowych maszyny wirtualne o niskim priorytecie będą przestarzałe — będą nadal obsługiwane przy użyciu bieżących interfejsów API i wersji narzędzi przez co najmniej 12 miesięcy, aby zapewnić wystarczającą ilość czasu na migrację do maszyn wirtualnych punktowych. 
>
> Maszyny wirtualne punktowe nie będą obsługiwane w pulach [konfiguracji usługi w chmurze.](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) Aby korzystać z maszyn wirtualnych punktowych, pule usług w chmurze będą musiały zostać zmigrowane do pul [konfiguracji maszyny wirtualnej.](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)


## <a name="use-cases-for-low-priority-vms"></a>Przypadki użycia maszyn wirtualnych o niskim priorytecie

Biorąc pod uwagę cechy maszyn wirtualnych o niskim priorytecie, jakie obciążenia mogą i nie mogą ich używać? Ogólnie rzecz biorąc obciążeń przetwarzania wsadowego są dobre dopasowanie, jak zadania są podzielone na wiele zadań równoległych lub istnieje wiele zadań, które są skalowane w poziomie i dystrybuowane na wielu maszynach wirtualnych.

-   Aby zmaksymalizować wykorzystanie nadwyżki pojemności na platformie Azure, odpowiednie zadania można skalować w poziomie.

-   Od czasu do czasu maszyny wirtualne mogą nie być dostępne lub wywłaszczone, co powoduje zmniejszenie pojemności zadań i może prowadzić do przerwania zadania i ponownego uruchomienia. Zadania muszą zatem być elastyczne w czasie, jaki mogą zająć do uruchomienia.

-   Zadania z dłuższymi zadaniami mogą mieć większy wpływ, jeśli zostaną przerwane. Jeśli długotrwałe zadania implementują punkty kontrolne, aby zapisać postęp podczas wykonywania, zmniejsza się wpływ przerwania. Zadania z krótszym czasem wykonywania mają tendencję do pracy najlepiej z maszynami wirtualnymi o niskim priorytecie, ponieważ wpływ przerwania jest znacznie mniejszy.

-   Długotrwałe zadania MPI, które wykorzystują wiele maszyn wirtualnych, nie są dobrze przystosowane do używania maszyn wirtualnych o niskim priorytecie, ponieważ jedna wywłaszczona maszyna wirtualna może prowadzić do ponownego uruchomienia całego zadania.

Oto kilka przykładów przypadków użycia przetwarzania wsadowego, które są odpowiednie do używania maszyn wirtualnych o niskim priorytecie:

-   **Rozwój i testowanie**: W szczególności, jeśli opracowywane są rozwiązania na dużą skalę, można do osiągnięcia znacznych oszczędności. Wszystkie rodzaje testów mogą korzystać, ale testowanie obciążenia na dużą skalę i testowanie regresji są doskonałymi zastosowaniami.

-   **Uzupełnienie pojemności na żądanie**: Maszyny wirtualne o niskim priorytecie mogą być używane do uzupełniania regularnych dedykowanych maszyn wirtualnych - jeśli są dostępne, zadania mogą być skalowane, a tym samym szybsze w celu obniżenia kosztów; gdy nie są dostępne, linia bazowa dedykowanych maszyn wirtualnych pozostaje dostępna.

-   **Elastyczny czas wykonywania zadań**: Jeśli istnieje elastyczność w czasie, w jaki zadania muszą zostać ukończone, potencjalne spadki zdolności produkcyjnych mogą być tolerowane; jednak z dodatkiem zadań maszyn wirtualnych o niskim priorytecie często działają szybciej i przy niższych kosztach.

Pule wsadowe można skonfigurować do używania maszyn wirtualnych o niskim priorytecie na kilka sposobów, w zależności od elastyczności czasu wykonywania zadania:

-   Maszyny wirtualne o niskim priorytecie mogą być używane wyłącznie w puli. W takim przypadku batch odzyskuje wszelkie wywłaszczone pojemności, jeśli są dostępne. Ta konfiguracja jest najtańszym sposobem wykonywania zadań, ponieważ używane są tylko maszyny wirtualne o niskim priorytecie.

-   Maszyny wirtualne o niskim priorytecie mogą być używane w połączeniu ze stałą linią bazową dedykowanych maszyn wirtualnych. Stała liczba dedykowanych maszyn wirtualnych zapewnia, że zawsze istnieje pewna zdolność do utrzymania postępu zadania.

-   Może istnieć dynamiczna kombinacja dedykowanych i niskiego priorytetu maszyn wirtualnych, dzięki czemu tańsze maszyny wirtualne o niskim priorytecie są używane wyłącznie wtedy, gdy są dostępne, ale pełne ceny dedykowanych maszyn wirtualnych są skalowane w górę, gdy jest to wymagane. Ta konfiguracja utrzymuje minimalną ilość dostępnych zdolności produkcyjnych, aby utrzymać postęp zadań.

## <a name="batch-support-for-low-priority-vms"></a>Obsługa partii dla maszyn wirtualnych o niskim priorytecie

Usługa Azure Batch udostępnia kilka funkcji, które ułatwiają korzystanie z maszyn wirtualnych o niskim priorytecie i korzystanie z nich:

-   Pule wsadowe mogą zawierać zarówno dedykowane maszyny wirtualne, jak i maszyny wirtualne o niskim priorytecie. Numer każdego typu maszyny Wirtualnej można określić podczas tworzenia puli lub zmiany w dowolnym momencie dla istniejącej puli, przy użyciu jawnej operacji zmiany rozmiaru lub przy użyciu automatycznego skalowania. Przesyłanie zadań i zadań może pozostać niezmienione, niezależnie od typów maszyn wirtualnych w puli. Można również skonfigurować pulę, aby całkowicie używać maszyn wirtualnych o niskim priorytecie do uruchamiania zadań tak tanio, jak to możliwe, ale uruchamiać dedykowane maszyny wirtualne, jeśli pojemność spadnie poniżej minimalnego progu, aby utrzymać zadania uruchomione.

-   Pule wsadowe automatycznie szukają docelowej liczby maszyn wirtualnych o niskim priorytecie. Jeśli maszyny wirtualne są wywłaszczone, a następnie Batch próbuje zastąpić utraconej pojemności i powrócić do obiektu docelowego.

-   Gdy zadania zostaną przerwane, usługa Batch wykrywa i automatycznie ponownie ustawia zadania w kolejce do ponownego uruchomienia.

-   Maszyny wirtualne o niskim priorytecie mają oddzielny przydział procesora wirtualnego, który różni się od przydziału dla dedykowanych maszyn wirtualnych. 
    Przydział dla maszyn wirtualnych o niskim priorytecie jest wyższy niż przydział dla dedykowanych maszyn wirtualnych, ponieważ maszyny wirtualne o niskim priorytecie kosztują mniej. Aby uzyskać więcej informacji, zobacz [Przydziały i limity usługi wsadowej](batch-quota-limit.md#resource-quotas).    

> [!NOTE]
> Maszyny wirtualne o niskim priorytecie nie są obecnie obsługiwane dla kont usługi Batch utworzonych w [trybie subskrypcji użytkownika](batch-api-basics.md#account).
>

## <a name="create-and-update-pools"></a>Tworzenie i aktualizowanie pul

Pula usługi Batch może zawierać zarówno dedykowane, jak i niskiego priorytetu maszyn wirtualnych (nazywane również węzłami obliczeniowymi). Można ustawić docelową liczbę węzłów obliczeniowych dla dedykowanych i niskiego priorytetu maszyn wirtualnych. Docelowa liczba węzłów określa liczbę maszyn wirtualnych, które mają mieć w puli.

Na przykład, aby utworzyć pulę przy użyciu maszyn wirtualnych usługi w chmurze platformy Azure z docelowym 5 dedykowanych maszyn wirtualnych i 20 maszyn wirtualnych o niskim priorytecie:

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5") // WS 2016
);
```

Aby utworzyć pulę przy użyciu maszyn wirtualnych platformy Azure (w tym przypadku maszyn wirtualnych z systemem Linux) z docelowym 5 dedykowanymi maszynami wirtualnymi i 20 maszynami wirtualnymi o niskim priorytecie:

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

Można uzyskać bieżącą liczbę węzłów dla dedykowanych i niskiego priorytetu maszyn wirtualnych:

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

Węzły puli mają właściwość wskazującą, czy węzeł jest dedykowaną maszyną wirtualną o niskim priorytecie:

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

Gdy jeden lub więcej węzłów w puli są wywłaszczone, operacja węzłów listy w puli nadal zwraca te węzły. Bieżąca liczba węzłów o niskim priorytecie pozostaje niezmieniona, ale te węzły mają swój stan ustawiony na stan **wywłaszczone.** Partia próbuje znaleźć zastępcze maszyny wirtualne i, jeśli zakończy się pomyślnie, węzły przejść przez **Tworzenie,** a następnie **Uruchamianie** stanów przed staje się dostępny do wykonania zadania, podobnie jak nowe węzły.

## <a name="scale-a-pool-containing-low-priority-vms"></a>Skalowanie puli zawierającej maszyny wirtualne o niskim priorytecie

Podobnie jak w przypadku pul składających się wyłącznie z dedykowanych maszyn wirtualnych, można skalować pulę zawierającą maszyny wirtualne o niskim priorytecie, wywołując metodę Resize lub korzystając ze skalowania automatycznego.

Operacja zmiany rozmiaru puli przyjmuje drugi opcjonalny parametr, który aktualizuje wartość **targetLowPriorityNodes:**

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

Formuła skalowania automatycznego puli obsługuje maszyny wirtualne o niskim priorytecie w następujący sposób:

-   Można uzyskać lub ustawić wartość zmiennej zdefiniowanej przez usługę **$TargetLowPriorityNodes**.

-   Można uzyskać wartość zmiennej zdefiniowanej przez usługę **$CurrentLowPriorityNodes**.

-   Można uzyskać wartość zmiennej zdefiniowanej przez usługę **$PreemptedNodeCount**. 
    Ta zmienna zwraca liczbę węzłów w stanie wywłaszczonych i umożliwia skalowanie w górę lub w dół liczby dedykowanych węzłów, w zależności od liczby wywłaszczonych węzłów, które są niedostępne.

## <a name="jobs-and-tasks"></a>Zadania i zadania

Zadania i zadania wymagają niewielkiej dodatkowej konfiguracji dla węzłów o niskim priorytecie; jedynym wsparciem jest w następujący sposób:

-   Właściwość JobManagerTask zadania ma nową właściwość **AllowLowPriorityNode**. 
    Jeśli ta właściwość jest true, zadanie menedżera zadań można zaplanować w węźle dedykowanym lub o niskim priorytecie. Jeśli ta właściwość jest false, zadanie menedżera zadań jest zaplanowane tylko do dedykowanego węzła.

-   [Zmienna środowiskowa](batch-compute-node-environment-variables.md) jest dostępna dla aplikacji zadania, dzięki czemu może określić, czy jest uruchomiona w węźle o niskim priorytecie lub dedykowanym. Zmienna środowiskowa jest AZ_BATCH_NODE_IS_DEDICATED.

## <a name="handling-preemption"></a>Obsługa wywłaszczenia

Maszyny wirtualne mogą być od czasu do czasu wywłaszczone; gdy nastąpi wywłaszczenie, partia wykonuje następujące czynności:

-   Wywłaszczone maszyny wirtualne mają ich stan zaktualizowany do **wywłaszczone**.
-   Jeśli zadania były uruchomione na wywłaszczonych maszynach wirtualnych węzła, te zadania są ponownie wyświetlane w kolejce i uruchamiane ponownie.
-   Maszyna wirtualna jest skutecznie usuwana, co prowadzi do utraty wszelkich danych przechowywanych lokalnie na maszynie Wirtualnej.
-   Pula stale próbuje osiągnąć docelową liczbę węzłów o niskim priorytecie dostępnych. Po znalezieniu zdolności wymienne węzły zachowują swoje identyfikatory, ale są ponownie inicjowane, **przechodząc** przez tworzenie i **uruchamianie** stanów, zanim będą dostępne do planowania zadań.
-   Liczby wyprzedaży są dostępne jako metryki w witrynie Azure portal.

## <a name="metrics"></a>Metryki

Nowe metryki są dostępne w [witrynie Azure portal](https://portal.azure.com) dla węzłów o niskim priorytecie. Te dane są następujące:

- Liczba węzłów o niskim priorytecie
- Liczba rdzeni o niskim priorytecie 
- Wywłaszczona liczba węzłów

Aby wyświetlić metryki w witrynie Azure portal:

1. Przejdź do konta usługi Batch w portalu i wyświetl ustawienia konta usługi Batch.
2. Wybierz **metryki** z sekcji **Monitorowanie.**
3. Wybierz metryki, których potrzebujesz, z listy **Dostępne metryki.**

![Metryki dla węzłów o niskim priorytecie](media/batch-low-pri-vms/low-pri-metrics.png)

## <a name="next-steps"></a>Następne kroki

* Przeczytaj artykuł [Batch feature overview for developers](batch-api-basics.md) (Omówienie funkcji usługi Batch dla deweloperów) zawierający informacje kluczowe dla wszystkich osób przygotowujących się do korzystania z usługi Batch. Ten artykuł zawiera bardziej szczegółowe informacje o zasobach usługi Batch, takich jak pule, węzły i zadania oraz wielu funkcjach API, których można używać podczas kompilowania aplikacji usługi Batch.
* Dowiedz się więcej o [interfejsach API i narzędziach usługi Batch](batch-apis-tools.md) umożliwiających tworzenie rozwiązań usługi Batch.
* Zacznij planować przejście z maszyn wirtualnych o niskim priorytecie na maszyny wirtualne punktowe. Jeśli używasz maszyn wirtualnych o niskim priorytecie z pulami **konfiguracji usługi w chmurze,** planujesz przenieść się do pul **konfiguracji maszyny wirtualnej.**
