---
title: Repliki i wystąpienia w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Omówienie repliki i wystąpienia — funkcje i cykle
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: 7f8638365b40395a5dd82457c40e5c15209ba1a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882418"
---
# <a name="replicas-and-instances"></a>Repliki i wystąpienia 
Ten artykuł zawiera omówienie cyklu życia repliki usług stanowych oraz wystąpień usług bezstanowych.

## <a name="instances-of-stateless-services"></a>Wystąpienia usługi bezstanowej
Wystąpienie usługi bezstanowej stanowi kopię logiki usługi, które jest uruchamiane na jednym z węzłów klastra. Wystąpienie w ramach partycji jest unikatowo identyfikowana przez jego **InstanceId**. Cykl życia wystąpienia w modelu na poniższym diagramie:

![Cykl życia wystąpienia](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Po Menedżer zasobów klastra określa umieszczania dla tego wystąpienia, wprowadza ten stan cyklu życia. Wystąpienie jest uruchomiona w węźle. Host aplikacji została uruchomiona, wystąpienie jest tworzone i następnie otworzyć. Po zakończeniu uruchamiania, wystąpienie przechodzi do stanu gotowości. 

Jeśli wystąpiła awaria aplikacji hosta lub węzła dla tego wystąpienia, przechodzi do stanu porzucone.

### <a name="ready-rd"></a>Gotowe (RD)
W stanie gotowe wystąpienie jest uruchomiona w węźle. Jeśli to wystąpienie usługi reliable service **RunAsync** zostało wywołane. 

Jeśli wystąpiła awaria aplikacji hosta lub węzła dla tego wystąpienia, przechodzi do stanu porzucone.

### <a name="closing-cl"></a>Zamknięcia (CL)
W stanie zamknięcia usługi Azure Service Fabric Trwa zamykanie wystąpienia w tym węźle. Zamknięcie może być z powodu wielu przyczyn — na przykład uaktualnienie aplikacji, równoważenia obciążenia lub usługi, usuwany. Po ukończeniu zamknij przechodzi do stanu porzucone.

### <a name="dropped-dd"></a>Porzucone (DD)
W stanie porzuconych wystąpienie jest już uruchomione w węźle. W tym momencie usługi Service Fabric przechowuje metadane dotyczące tego wystąpienia, które ostatecznie zostanie także usunięta.

> [!NOTE]
> Istnieje możliwość, do którego nastąpi przejście z dowolny stan porzuconych stanu przy użyciu **ForceRemove** opcja `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Repliki usług stanowych
Repliki usługi stanowej stanowi kopię logiki usługi uruchomione na jednym z węzłów klastra. Ponadto replika utrzymuje kopię stanu tej usługi. Dwoma pojęciami powiązane opisują cykl życia i zachowanie stanowych replik:
- Cykl życia repliki
- Rola repliki

Następujące dyskusji w tym artykule opisano trwałych usług stanowych. W przypadku usług stanowych volatile (lub w pamięci) szczegółów oraz porzuconych stany są równoważne.

![Cykl życia repliki](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Replika w stanie InBuild jest repliki, który został utworzony lub przygotowane na potrzeby dołączania zestawu replik. W zależności od roli repliki IB ma semantykę różną. 

Jeśli host aplikacji lub węzeł do replik w stanie InBuild ulegnie awarii, go przechodzi w stan naciśnięcia.

   - **Podstawowy replik w stanie InBuild**: Podstawowy stanie InBuild to pierwszy replik partycji. Ta replika zwykle odbywa się po utworzeniu partycji. Podstawowy stanie InBuild repliki powstają również podczas ponownego uruchomienia wszystkie repliki partycji lub są usunięte.

   - **Replik w stanie IdleSecondary InBuild**: Te nowe repliki, które są tworzone przez Menedżer zasobów klastra lub istniejących replik, które pojawiły się w dół i muszą zostać ponownie dodane do zestawu. Tych replik zasilany lub utworzonych przez podstawowe, zanim będą oni Dołącz do zestawu replik jako ActiveSecondary i uczestniczyć w kworum potwierdzenie operacji.

   - **Replik w stanie ActiveSecondary InBuild**: Ten stan jest obserwowane w przypadku niektórych zapytań. Jest optymalizacji, gdzie zestawu replik nie ulega zmianie, ale replika musi zostać utworzony. Repliki, sama następuje przejścia do normalnego stanu komputera (zgodnie z opisem w sekcji, w ramach ról repliki).

### <a name="ready-rd"></a>Gotowe (RD)
Gotowe replika jest repliki, która uczestniczy w replikacji i kworum potwierdzenie operacji. Stanu gotowości stosuje się do podstawowych i aktywne replik pomocniczych.

Jeśli wystąpiła awaria hosta aplikacji lub węzeł gotowe repliki, go przechodzi w stan naciśnięcia.

### <a name="closing-cl"></a>Zamknięcia (CL)
Replika przejdzie w stan zamknięcie w następujących scenariuszach:

- **Zamykanie kodu dla repliki**: Usługa Service Fabric może być konieczne Zamknij uruchomiony kod dla repliki. Musi być to zamknięcie wielu powodów. Na przykład może się zdarzyć z powodu aplikacji, sieci szkieletowej lub rozbudowy infrastruktury lub z powodu błędów zgłoszonych przez replikę. Gdy replika zamknąć zostanie zakończone, replika przechodzi w stan naciśnięcia. Stan utrwalony skojarzone z tej repliki, który jest przechowywany na dysku nie są czyszczone.

- **Usuwanie repliki z klastra**: Usługa Service Fabric może być konieczne usunięcie utrwalonego stanu, a następnie zamknij uruchomiony kod dla repliki. Musi być to zamknięcie wielu powodów, na przykład Równoważenie obciążenia.

### <a name="dropped-dd"></a>Porzucone (DD)
W stanie porzuconych wystąpienie jest już uruchomione w węźle. Dostępna jest również bez stanu pozostanie w węźle. W tym momencie usługi Service Fabric przechowuje metadane dotyczące tego wystąpienia, które ostatecznie zostanie także usunięta.

### <a name="down-d"></a>Dół (D)
W stanie down kod repliki nie jest uruchomiony, ale utrwalonego stanu dla tej repliki istnieje w tym węźle. Repliki mogą być wyłączone wielu powodów — na przykład węzeł jest w dół, awarii kodu repliki, uaktualnienie aplikacji lub błędy repliki.

Dół repliki jest otwarty przez usługę Service Fabric zgodnie z wymaganiami, na przykład, po zakończeniu uaktualniania w węźle.

Rola repliki nie jest w stanie down odpowiednie.

### <a name="opening-op"></a>Otwieranie (OP)
Dół repliki wchodzi w stan otwierania w przypadku usługi Service Fabric musi ponownie przełączyć repliki kopii zapasowej. Na przykład ten stan może być po zakończeniu uaktualniania przy użyciu kodu dla aplikacji, w węźle. 

Jeśli wystąpiła awaria hosta aplikacji lub węzeł dla repliki otwarcia go przechodzi w stan naciśnięcia.

Rola repliki nie jest odpowiednie w stanie otwarcia.

### <a name="standby-sb"></a>Stan wstrzymania (SB)
Replik w stanie wstrzymania jest repliką utrwalonych usługi zakończył działanie i następnie został otwarty. Ta replika może być używany przez usługi Service Fabric, jeśli należy go dodać innej repliki dla repliki (ponieważ replika ma już część stanu, a proces kompilacji jest szybsze). Po wygaśnięciu StandByReplicaKeepDuration wstrzymania repliki jest odrzucany.

Jeśli wystąpiła awaria hosta aplikacji lub węzeł wstrzymania repliki, go przechodzi w stan naciśnięcia.

Rola repliki nie jest odpowiednie w stan wstrzymania.

> [!NOTE]
> Wszystkie repliki, która nie działa lub usunięta, jest uznawana za *się*.
>

> [!NOTE]
> Istnieje możliwość, do którego nastąpi przejście z dowolny stan porzuconych stanu przy użyciu **ForceRemove** opcja `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Rola repliki 
Rola repliki określa jego funkcję zestawu replik:

- **Podstawowy (P)**: Istnieje podstawowego zestawu replik, który jest odpowiedzialny za wykonanie operacji odczytu i zapisu. 
- **ActiveSecondary (S)**: Są to replik, które otrzymują aktualizacje stanu z serwera podstawowego, stosować je, a następnie wyślij potwierdzeń Wstecz. Istnieje wiele aktywne pomocnicze bazy danych w zestawie. Liczba tych aktywne pomocnicze bazy danych określa liczbę błędów, których usługa może obsługiwać.
- **IdleSecondary (I)**: Te repliki są kompilowane przez podstawową. Są one odbiera stanu z serwera podstawowego, zanim może być podwyższony do aktywnej pomocniczej. 
- **Brak (N)**: Te repliki nie mają za zadanie w zestawie.
- **Nieznany (U)**: Jest to początkowej Rola repliki przed jej odbiera wszelkie **ChangeRole** wywołania interfejsu API z usługi Service Fabric.

Na poniższym diagramie przedstawiono przejścia do roli repliki i niektóre przykładowe scenariusze, w których może wystąpić:

![Rola repliki](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: Tworzenie nowej repliki podstawowej.
- U -> I: Tworzenie nowej repliki bezczynności.
- U -> N: Usuwanie repliki wstrzymania.
- I -> S: Promocja bezczynności dodatkowej aktywne pomocnicze tak, aby jego potwierdzeń ma wpływ na kworum.
- I -> P: Promocja bezczynności pomocniczej do podstawowej. Może to nastąpić w ramach reconfigurations specjalne po bezczynności pomocniczy poprawne Release candidate jako główną.
- I -> N: Usuwanie repliki pomocniczej bezczynności.
- S -> P: Promocja aktywnej pomocniczej do podstawowej. Może to być spowodowane pracy w trybie failover podstawowej lub podstawowego przenoszenia zainicjowane przez Menedżer zasobów klastra. Na przykład może być w odpowiedzi na uaktualnianie aplikacji lub usługi równoważenia obciążenia.
- S -> N: Usuwania aktywnej repliki pomocniczej.
- P -> S: Zwężanie repliki podstawowej. Może to być spowodowane głównej przenoszenia zainicjowane przez Menedżer zasobów klastra. Na przykład może być w odpowiedzi na uaktualnianie aplikacji lub usługi równoważenia obciążenia.
- P -> N: Usuwanie repliki podstawowej.

> [!NOTE]
> Modele programowania wyższego poziomu, takie jak [elementów Reliable Actors](service-fabric-reliable-actors-introduction.md) i [usług Reliable Services](service-fabric-reliable-services-introduction.md), Ukryj koncepcji ról repliki od dewelopera. W przypadku podmiotów pojęcie roli nie jest konieczne. W usługach go stopniu upraszcza w przypadku większości scenariuszy.
>

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pojęć usługi Service Fabric zobacz następujący artykuł:

[Cykl życia usług Reliable Services — C#](service-fabric-reliable-services-lifecycle.md)

