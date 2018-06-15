---
title: Repliki i wystąpień w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Zrozumienie repliki i wystąpień — funkcje i cykle
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
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211392"
---
# <a name="replicas-and-instances"></a>Repliki i wystąpień 
Ten artykuł zawiera omówienie życia repliki usługi stanowej i wystąpień usług bezstanowych.

## <a name="instances-of-stateless-services"></a>Wystąpień usług bezstanowych
Wystąpienie usługi bezstanowej jest kopią logiki usługi, która działa na jednym z węzłów klastra. Wystąpienie w partycji jest unikatowo identyfikowana przez jego **InstanceId**. Cykl życia wystąpienia jest modelowana na poniższym diagramie:

![Wystąpienie cyklu życia](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>Stanie InBuild (IB)
Po Menedżera zasobów klastra określa umieszczania dla tego wystąpienia, wejdzie ona ten stan cyklu życia. Wystąpienie jest uruchomiona w węźle. Host aplikacji została uruchomiona, wystąpienie jest utworzony i następnie otwarty. Po zakończeniu uruchamiania, wystąpienie przechodzi w stan gotowości. 

Jeśli wystąpiła awaria hosta aplikacji lub węzła dla tego wystąpienia go przechodzi w stan porzucone.

### <a name="ready-rd"></a>Gotowe (RD)
W stanie gotowe wystąpienie jest uruchomiona w węźle. Jeśli to wystąpienie jest usługą niezawodnej **RunAsync** została wywołana. 

Jeśli wystąpiła awaria hosta aplikacji lub węzła dla tego wystąpienia go przechodzi w stan porzucone.

### <a name="closing-cl"></a>Zamknięcia (CL)
Stan zamknięcia sieć szkieletowa usług Azure jest właśnie zamykana wystąpienia w tym węźle. Ten shutdown może być wiele przyczyn — na przykład uaktualnienie aplikacji, równoważenia obciążenia lub usługi, usuwany. Po zakończeniu zamknij go przechodzi w stan porzucone.

### <a name="dropped-dd"></a>Porzucone (DD)
W trakcie porzuconych wystąpienie nie jest już uruchomiony na węźle. W tym momencie usługi sieć szkieletowa zachowuje metadane dotyczące tego wystąpienia, który ostatecznie zostanie również usunięta.

> [!NOTE]
> Możliwe jest przejście ze stanu wszelkich porzuconych stan za pomocą **ForceRemove** opcja `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Repliki usługi stanowej
Repliki usługi stanowej jest kopią logiki usługi uruchomione na jednym z węzłów klastra. Ponadto replica przechowuje kopię stan tej usługi. Dwa pojęcia pokrewne opisano cykl życia i zachowanie stanowe repliki:
- Cykl życia repliki
- Rola repliki

Następujące dyskusji opisuje utrwalonego usług stanowych. Volatile (lub w pamięci) stanowych usług dół i porzuconych stanów są równoważne.

![Cykl życia repliki](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>Stanie InBuild (IB)
Replika stanie InBuild jest repliki został utworzony lub przygotowane za dołączenie do tego zestawu replik. W zależności od roli repliki IB ma semantykę różną. 

Jeśli wystąpiła awaria hosta aplikacji lub węzeł replik w stanie InBuild go przechodzi w stan naciśnięcia.

   - **Podstawowy replik w stanie InBuild**: podstawowy stanie InBuild są pierwszy replik partycji. Ta replika zwykle odbywa się po utworzeniu partycji. Podstawowy stanie InBuild repliki również wystąpić, gdy ponowne uruchomienie wszystkich replik partycji lub są porzucony.

   - **Replik w stanie IdleSecondary InBuild**: są to albo nowych replik, które są tworzone przez Menedżera zasobów klastra lub istniejących replik, które poszło w dół i należy dodać z powrotem do zestawu. Te repliki są rozpoczęta lub utworzony przez podstawową, zanim można dołączyć zestawu replik jako ActiveSecondary i uczestniczyć w kworum potwierdzenia operacji.

   - **Replik w stanie ActiveSecondary InBuild**: ten stan jest przestrzegana niektórych kwerend. Jest optymalizacji, gdzie zestawu replik nie jest zmieniany, ale replika musi zostać utworzony. Replika sam następuje przejścia do normalnego stanu maszyny (zgodnie z opisem w sekcji ról repliki).

### <a name="ready-rd"></a>Gotowe (RD)
Gotowe repliki jest repliki, który uczestniczy w replikacji i kworum potwierdzenia operacji. Stan gotowości ma zastosowanie do głównej i aktywne replikach pomocniczych.

Jeśli wystąpiła awaria hosta aplikacji lub węzeł gotowy repliki go przechodzi w stan naciśnięcia.

### <a name="closing-cl"></a>Zamknięcia (CL)
Replika przejdzie w stan zamknięcia w następujących scenariuszach:

- **Zamykanie kodu dla repliki**: sieć szkieletowa usług może być konieczne wyłączenie uruchomiony kod dla repliki. Ten shutdown może być wielu powodów. Na przykład może się zdarzyć z powodu aplikacji, sieci szkieletowej lub uaktualnienie infrastruktury lub ze względu na błąd zgłoszony przez repliki. Gdy replika zamknąć zakończenie, replika przechodzi w stan naciśnięcia. Stan utrwalony skojarzone z tej repliki, który jest przechowywany na dysku nie są czyszczone.

- **Usuwanie repliki z klastra**: sieć szkieletowa usług może być konieczne usunięcie stanu utrwalonego i zamknąć uruchomiony kod dla repliki. Ten shutdown może być wiele przyczyn, na przykład równoważenia obciążenia.

### <a name="dropped-dd"></a>Porzucone (DD)
W trakcie porzuconych wystąpienie nie jest już uruchomiony na węźle. Ponadto stan jest niedostępny w lewo na węźle. W tym momencie usługi sieć szkieletowa zachowuje metadane dotyczące tego wystąpienia, który ostatecznie zostanie również usunięta.

### <a name="down-d"></a>Dół (D)
W stanie down kodu repliki nie jest uruchomiona, ale stan utrwalony dla tej repliki istnieje w tym węźle. Replikę można dół wielu powodów — na przykład węzeł jest zakończone, awarii w kodzie repliki, uaktualnienie aplikacji lub błędy repliki.

Dół repliki jest otwarty przez sieć szkieletowa usług zgodnie z potrzebami, na przykład po zakończeniu uaktualniania w węźle.

Rola repliki nie jest ważna w stanie down.

### <a name="opening-op"></a>Otwierający (OP)
Gdy usługa sieć szkieletowa usług musi ponownie przełączyć repliki wykonaj kopię zapasową dół repliki wchodzi w stan otwarcia. Na przykład ten stan może być po zakończeniu uaktualniania kodu dla aplikacji w węźle. 

Jeśli wystąpiła awaria hosta aplikacji lub węzeł repliki otwierania go przechodzi w stan naciśnięcia.

Rola repliki nie jest ważna w stanie otwarcia.

### <a name="standby-sb"></a>Stan wstrzymania (SB)
Replika wstrzymania jest repliką utrwalonego usługi, która zakończył działanie i następnie został otwarty. Ta replika mogą używać usługi sieć szkieletowa, jeśli trzeba dodać innej repliki dla repliki (ponieważ replika ma już pewną część stanu i procesu kompilacji jest szybsze). Po wygaśnięciu StandByReplicaKeepDuration rezerwy repliki zostaną odrzucone.

Jeśli wystąpiła awaria hosta aplikacji lub węzeł rezerwy repliki go przechodzi w stan naciśnięcia.

Rola repliki nie jest ważna w stan wstrzymania.

> [!NOTE]
> Repliki nie jest w dół lub porzucony jest traktowany jako *się*.
>

> [!NOTE]
> Możliwe jest przejście ze stanu wszelkich porzuconych stan za pomocą **ForceRemove** opcja `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Rola repliki 
Rola repliki określa jej funkcji w zestawie replik:

- **Podstawowy (P)**: istnieje podstawowy w zestawie replik, który wykonuje operacje odczytu i zapisu. 
- **ActiveSecondary (S)**: są to replik, które otrzymują aktualizacje stanu z serwera podstawowego, ich zastosowania, a następnie wyślij wstecz potwierdzeń. Istnieje wiele aktywne pomocnicze bazy danych w zestawie replik. Liczba tych aktywne pomocnicze bazy danych określa liczbę błędów, które może obsłużyć usługę.
- **IdleSecondary (I)**: te repliki są tworzone przez podstawową. Są one odbieranie stanu z serwera podstawowego, przed może funkcjonować jako aktywne pomocnicze. 
- **Brak (N)**: tych replik nie mają za zadanie w zestawie replik.
- **Nieznany (U)**: jest to rola początkowej repliki przed odbierze żadnego **ChangeRole** wywołanie interfejsu API z sieci szkieletowej usług.

Na poniższym diagramie przedstawiono przejścia do roli repliki i niektóre przykładowe scenariusze, w których mogą występować:

![Rola repliki](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: Tworzenie nowej repliki podstawowej.
- U -> i. Tworzenie nowej repliki bezczynności.
- U -> N: usuwania wstrzymania repliki.
- -S: promocji bezczynności pomocniczej do aktywne pomocnicze I > tak, aby jego potwierdzeń przyczyniają się do kworum.
- I -> promocji P: bezczynności pomocniczej podstawowym. Może to nastąpić w obszarze reconfigurations specjalne podczas bezczynności pomocniczy jest poprawna candidate jako podstawowy.
- I -> N: usuwanie repliki pomocniczej bezczynności.
- S -> P: promocji aktywne pomocnicze podstawowym. Może to być spowodowane pracy awaryjnej podstawową lub głównej ruch inicjowane przez Menedżera zasobów klastra. Na przykład może być w odpowiedzi na uaktualnienie aplikacji lub równoważenia obciążenia.
- S -> N: usuwania aktywnej repliki pomocniczej.
- P -> obniżania poziomu S: repliki podstawowej. Może to być spowodowane głównej ruch inicjowane przez Menedżera zasobów klastra. Na przykład może być w odpowiedzi na uaktualnienie aplikacji lub równoważenia obciążenia.
- P -> N: usuwanie repliki podstawowej.

> [!NOTE]
> Modele programowania wyższego poziomu, takich jak [Reliable Actors](service-fabric-reliable-actors-introduction.md) i [niezawodne usługi](service-fabric-reliable-services-introduction.md), Ukryj koncepcji ról repliki od dewelopera. W uczestników pojęcie roli jest niepotrzebna. W usługach jest on przede wszystkim uproszczone dla większości scenariuszy.
>

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji dotyczących pojęć sieci szkieletowej usług zobacz następujący artykuł:

[Cykl życia usług Reliable Services — C#](service-fabric-reliable-services-lifecycle.md)

