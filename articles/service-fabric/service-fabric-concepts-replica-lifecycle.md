---
title: Repliki i wystąpienia w sieci szkieletowej usług Azure
description: Dowiedz się więcej o replikach i wystąpieniach w sieci szkieletowej usług, w tym omówienie ich cyklu życia i funkcji.
author: appi101
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: cf21af43de553a2802289e44eaece12952d077d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258566"
---
# <a name="replicas-and-instances"></a>Repliki i wystąpienia 
W tym artykule przedstawiono omówienie cyklu życia replik usług stanowych i wystąpień usług bezstanowych.

## <a name="instances-of-stateless-services"></a>Wystąpienia usług bezpaństwowych
Wystąpienie usługi bezstanowej jest kopią logiki usługi, która działa na jednym z węzłów klastra. Wystąpienie w obrębie partycji jest jednoznacznie identyfikowane przez jego **Identyfikator Wystąpienia**. Cykl życia wystąpienia jest modelowany na poniższym diagramie:

![Cykl życia wystąpienia](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Po tym, jak Menedżer zasobów klastra określi miejsce docelowe dla wystąpienia, wejdzie w ten stan cyklu życia. Wystąpienie jest uruchamiane w węźle. Host aplikacji jest uruchamiany, wystąpienie jest tworzone, a następnie otwierane. Po zakończeniu uruchamiania wystąpienie przechodzi do stanu gotowości. 

Jeśli host aplikacji lub węzeł dla tego wystąpienia ulega awarii, przechodzi do stanu porzuconego.

### <a name="ready-rd"></a>Gotowy (RD)
W stanie gotowości wystąpienie jest uruchomione w węźle. Jeśli to wystąpienie jest niezawodną usługą, **RunAsync** został wywołany. 

Jeśli host aplikacji lub węzeł dla tego wystąpienia ulega awarii, przechodzi do stanu porzuconego.

### <a name="closing-cl"></a>Zamykanie (CL)
W stanie zamknięcia usługi Azure Service Fabric jest w trakcie zamykania wystąpienia w tym węźle. To zamknięcie może być spowodowane wieloma przyczynami — na przykład uaktualnienie aplikacji, równoważenie obciążenia lub usuwana usługa. Po zakończeniu zamykania przechodzi do stanu porzuconego.

### <a name="dropped-dd"></a>Upuszczony (DD)
W stanie upuszczonego wystąpienie nie jest już uruchomiony w węźle. W tym momencie sieci szkieletowej usług przechowuje metadane dotyczące tego wystąpienia, który ostatecznie zostanie usunięty, jak również.

> [!NOTE]
> Możliwe jest przejście z dowolnego stanu do stanu porzuconego za `Remove-ServiceFabricReplica`pomocą opcji **ForceRemove** on .
>

## <a name="replicas-of-stateful-services"></a>Repliki usług stanowych
Replika usługi stanowej jest kopią logiki usługi uruchomionej w jednym z węzłów klastra. Ponadto replika przechowuje kopię stanu tej usługi. Dwie powiązane pojęcia opisują cykl życia i zachowanie replik stanowych:
- Cykl życia repliki
- Rola repliki

W poniższej dyskusji opisano utrwalone usługi stanowe. W przypadku usług stanowych volatile (lub w pamięci) stany w dół i porzucone są równoważne.

![Cykl życia repliki](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Replika InBuild jest repliką, która jest tworzona lub przygotowywana do łączenia zestawu replik. W zależności od roli repliki IB ma różne semantyki. 

Jeśli host aplikacji lub węzeł repliki InBuild ulegnie awarii, przechodzi do stanu w dół.

   - **Podstawowe repliki InBuild:** Podstawowe InBuild są pierwszymi replikami dla partycji. Ta replika zwykle dzieje się podczas tworzenia partycji. Podstawowe repliki InBuild również powstają, gdy wszystkie repliki partycji ponownie uruchomić lub są usuwane.

   - **Repliki InBuild w systemie IdleSecondary:** Są to nowe repliki utworzone przez Menedżera zasobów klastra lub istniejące repliki, które spadły i muszą zostać dodane z powrotem do zestawu. Repliki te są rozstawione lub komponowane przez podstawowy, zanim będą mogły dołączyć do zestawu replik jako ActiveSecondary i uczestniczyć w potwierdzeniu kworum operacji.

   - **ActiveSecondary InBuild repliki:** Ten stan jest obserwowany w niektórych kwerend. Jest to optymalizacja, w której zestaw replik nie zmienia się, ale replika musi zostać zbudowana. Sama replika jest zgodna z przejściami maszyny stanu normalnego (zgodnie z opisem w sekcji ról repliki).

### <a name="ready-rd"></a>Gotowy (RD)
Replika Ready to replika, która uczestniczy w replikacji i potwierdzaniu kworum operacji. Stan gotowości ma zastosowanie do replik pomocniczych podstawowych i aktywnych.

Jeśli host aplikacji lub węzeł dla gotowej repliki ulega awarii, przechodzi do stanu w dół.

### <a name="closing-cl"></a>Zamykanie (CL)
Replika wchodzi w stan zamknięcia w następujących scenariuszach:

- **Zamykanie kodu repliki:** może być konieczne zamknięcie uruchomionego kodu repliki. To zamknięcie może być z wielu powodów. Na przykład może się zdarzyć z powodu uaktualnienia aplikacji, sieci szkieletowej lub infrastruktury lub z powodu błędu zgłoszonego przez replikę. Po zakończeniu zamykania repliki repliki przechodzi do stanu w dół. Utrwalony stan skojarzony z tą repliką przechowywaną na dysku nie jest czyszczony.

- **Usuwanie repliki z klastra:** Sieci szkieletowej usług może być konieczne usunięcie stanu utrwalone i zamknięcie uruchomionego kodu repliki. To zamknięcie może być z wielu powodów, na przykład równoważenia obciążenia.

### <a name="dropped-dd"></a>Upuszczony (DD)
W stanie upuszczonego wystąpienie nie jest już uruchomiony w węźle. Nie ma również stan w lewo na węźle. W tym momencie sieci szkieletowej usług przechowuje metadane dotyczące tego wystąpienia, który ostatecznie zostanie usunięty, jak również.

### <a name="down-d"></a>W dół (D)
W stanie w dół kod repliki nie jest uruchomiony, ale utrwalony stan dla tej repliki istnieje w tym węźle. Replika może być wyłączona z wielu powodów — na przykład węzeł jest w dół, awaria w kodzie repliki, uaktualnienie aplikacji lub błędy repliki.

Replika w dół jest otwierana przez sieci szkieletowej usług zgodnie z wymaganiami, na przykład po zakończeniu uaktualniania w węźle.

Rola repliki nie jest istotne w stanie w dół.

### <a name="opening-op"></a>Otwarcie (PO)
Replika w dół wchodzi w stan otwarcia, gdy sieci szkieletowej usług musi przywrócić replikę. Na przykład ten stan może być po uaktualnieniu kodu dla aplikacji kończy się w węźle. 

Jeśli host aplikacji lub węzeł repliki otwierającej ulega awarii, przechodzi do stanu w dół.

Rola repliki nie jest istotna w stanie otwarcia.

### <a name="standby-sb"></a>Tryb gotowości (SB)
Replika wstrzymania jest repliką utrwalone usługi, która została wyłączona, a następnie otwarty. Ta replika może być używana przez sieci szkieletowej usług, jeśli musi dodać inną replikę do zestawu replik (ponieważ replika ma już pewną część stanu, a proces kompilacji jest szybszy). Po wygaśnięciu standbyreplicaKeepDuration repliki wstrzymania jest odrzucany.

Jeśli host aplikacji lub węzeł repliki rezerwowej ulega awarii, przechodzi do stanu w dół.

Rola repliki nie jest istotna w stanie wstrzymania.

> [!NOTE]
> Każda replika, która nie jest w dół lub upuszczona jest uważana za *górę*.
>

> [!NOTE]
> Możliwe jest przejście z dowolnego stanu do stanu porzuconego za `Remove-ServiceFabricReplica`pomocą opcji **ForceRemove** na .
>

## <a name="replica-role"></a>Rola repliki 
Rola repliki określa jej funkcję w zestawie replik:

- **Podstawowy (P)**: Istnieje jeden podstawowy w zestawie replik, który jest odpowiedzialny za wykonywanie operacji odczytu i zapisu. 
- **ActiveSecondary (S)**: Są to repliki, które otrzymują aktualizacje stanu z podstawowego, zastosować je, a następnie odesłać potwierdzenia. Istnieje wiele aktywnych pomocniczych w zestawie replik. Liczba tych aktywnych pomocniczych określa liczbę błędów, które usługa może obsłużyć.
- **IdleSecondary (I)**: Te repliki są budowane przez podstawowy. Są one odbieranie stanu od podstawowego, zanim mogą być promowane do aktywnego pomocniczego. 
- **Brak (N)**: Te repliki nie mają odpowiedzialności w zestawie replik.
- **Nieznany (U)**: Jest to początkowa rola repliki, zanim otrzyma wywołanie interfejsu API **ChangeRole** z sieci szkieletowej usług.

Na poniższym diagramie przedstawiono przejścia roli repliki i kilka przykładowych scenariuszy, w których mogą one wystąpić:

![Rola repliki](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P: Tworzenie nowej repliki podstawowej.
- U -> I: Tworzenie nowej repliki bezczynności.
- U -> N: Usunięcie repliki rezerwowej.
- I -> S: Promocja bezczynności wtórnej do aktywnego wtórnego, tak aby jego uznanie przyczyniło się do kworum.
- I -> P: Promocja bezczynności wtórnej do podstawowej. Może się to zdarzyć w ramach specjalnych rekonfiguracji, gdy bezczynny pomocniczy jest właściwym kandydatem do podstawowej.
- I -> N: Usuwanie bezczynnej repliki pomocniczej.
- S -> P: Promocja aktywnego wtórnego do podstawowego. Może to być spowodowane przewijanym w stan failover ruchu podstawowego lub podstawowego zainicjowanym przez Menedżera zasobów klastra. Na przykład może to być odpowiedź na uaktualnienie aplikacji lub równoważenie obciążenia.
- S -> N: Usunięcie aktywnej repliki pomocniczej.
- P -> S: Degradacja repliki podstawowej. Może to być spowodowane ruchem podstawowym zainicjowanym przez Menedżera zasobów klastra. Na przykład może to być odpowiedź na uaktualnienie aplikacji lub równoważenie obciążenia.
- P -> N: Usunięcie repliki podstawowej.

> [!NOTE]
> Modele programowania wyższego poziomu, takie jak [Reliable Actors](service-fabric-reliable-actors-introduction.md) i [Reliable Services](service-fabric-reliable-services-introduction.md), ukrywają koncepcję ról repliki przed deweloperem. W Actors pojęcie roli jest niepotrzebne. W usługach jest w dużej mierze uproszczone dla większości scenariuszy.
>

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat koncepcji sieci szkieletowej usług, zobacz następujący artykuł:

[Cykl życia usług Reliable Services — C#](service-fabric-reliable-services-lifecycle.md)

