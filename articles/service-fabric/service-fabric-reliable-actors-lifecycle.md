---
title: Przegląd cyklu życia na podstawie aktora Azure mikrousług | Dokumentacja firmy Microsoft
description: Wyjaśniono niezawodnego aktora sieci szkieletowej usługi cyklu życia, wyrzucanie elementów bezużytecznych i ręcznego usuwania złośliwych użytkowników i ich stan
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 4e919c565574e0765227abda5832c858c36a77c0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208441"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Cykl życia aktora, automatyczne wyrzucanie elementów bezużytecznych i ręcznego usuwania
Aktor jest uaktywniany przy pierwszym uruchomieniu połączenie jest nawiązywane w przypadku dowolnej metody. Aktor jest dezaktywowany (odzyskiwanie zebranych przez środowisko uruchomieniowe złośliwych użytkowników), jeśli nie jest on używany przez można skonfigurować czas. Aktor i stanu można również zostaną usunięte ręcznie w dowolnym momencie.

## <a name="actor-activation"></a>Aktywacja aktora
Po uaktywnieniu aktora są następujące operacje:

* Wywołanie jest dostępna dla aktora i jeszcze nie została active, nowe aktora zostanie utworzona.
* Stanu aktora jest ładowana, jeśli jest zachowanie stanu.
* `OnActivateAsync` (C#) lub `onActivateAsync` wywołanie metody (Java) (która może zostać zastąpiona w celu wykonania aktora).
* Aktor teraz jest traktowane jako aktywne.

## <a name="actor-deactivation"></a>Dezaktywacja aktora
Gdy aktora jest dezaktywowany, są następujące operacje:

* Gdy aktora nie jest używany przez niektóre czas, zostanie ono usunięte z tabeli aktywnych złośliwych użytkowników.
* `OnDeactivateAsync` (C#) lub `onDeactivateAsync` wywołanie metody (Java) (która może zostać zastąpiona w celu wykonania aktora). Czyści wszystkie czasomierze aktora. Aktora operacji, takich jak stan, jaki zmiany nie powinna być wywoływana z tej metody.

> [!TIP]
> Środowisko uruchomieniowe sieci szkieletowej podmiotów emituje niektóre [zdarzenia związane z aktorem Aktywacja i dezaktywacja](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Są one przydatne w Diagnostyka i monitorowanie wydajności.
>
>

### <a name="actor-garbage-collection"></a>Aktora wyrzucanie elementów bezużytecznych
Gdy aktora jest dezaktywowana, odwołania do obiektu aktora są wydawane i może być bezużytecznych zwykle przez środowisko uruchomieniowe języka wspólnego (CLR) lub moduł zbierający elementy bezużyteczne programu java (JVM). maszyny wirtualnej. Wyrzucanie elementów bezużytecznych tylko czyści obiektu aktora. robi **nie** usunąć stan przechowywane w Menedżerze stanu aktora. Przy następnym aktora jest aktywna, tworzony jest nowy obiekt aktora i jej przywróceniu.

Co to jest traktowana jako "jest używane" na potrzeby dezaktywacji i wyrzucanie elementów bezużytecznych?

* Odbieranie wywołania
* `IRemindable.ReceiveReminderAsync` Metoda wywoływana (ma zastosowanie tylko wtedy, gdy aktor używa przypomnienia)

> [!NOTE]
> Jeśli aktora używa czasomierze i jej wywołanie zwrotne czasomierza jest wywoływana, nie **nie** liczby jako "jest używane".
>
>

Przed możemy przejść do szczegółów dezaktywację, należy zdefiniować następujące warunki:

* *Interwał skanowania*. Jest to interwał, jaką środowiska uruchomieniowego podmiotów skanowania tabeli aktywnych uczestników osób, które można dezaktywować i w ramach odzyskiwania pamięci. Wartość domyślna to to 1 minuta.
* *Limit czasu bezczynności*. Jest to czas, który musi pozostać nieużywane aktora (bezczynny), zanim można dezaktywować i w ramach odzyskiwania pamięci. Wartość domyślna to wynosi 60 min.

Zazwyczaj nie trzeba zmieniać tych wartości domyślnych. Jednak w razie potrzeby kolejnymi interwałami można zmienić za pomocą `ActorServiceSettings` podczas rejestrowania programu [usługi aktora](service-fabric-reliable-actors-platform.md):

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
    }
}
```

```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
    }
}
```
Dla każdego aktywnego aktora środowiska uruchomieniowego aktora przechowuje informacje o czas, który był bezczynny (tj. nie jest używany). Środowisko uruchomieniowe aktora sprawdza każdego podmiotów co `ScanIntervalInSeconds` aby zobaczyć, czy może być odzyskiwanie zbierane i pobiera ją, jeśli był bezczynny `IdleTimeoutInSeconds`.

W dowolnym momencie aktora jest używany, jego czas bezczynności jest resetowany do 0. Następnie aktora może być bezużytecznych tylko wtedy, gdy ponownie pozostanie bezczynny `IdleTimeoutInSeconds`. Odwołaj, że zostały użyte, jeśli wykonywane jest metody interfejsu aktora lub wywołania zwrotnego monitu aktora jest uznawany za aktora. Aktor jest **nie** uznaje użyte, jeśli jej wywołanie zwrotne czasomierza jest wykonywane.

Na poniższym diagramie przedstawiono cyklem życia pojedynczego aktora w celu zilustrowania koncepcji te.

![Przykład czas bezczynności][1]

W przykładzie wpływ wywołania metody aktora, przypomnienia i czasomierze na okres istnienia tego aktora. Są następujące kwestie dotyczące przykładzie warto zauważyć:

* ScanInterval i IdleTimeout są ustawiane odpowiednio 5 i 10. (Jednostek niezależnie od tego, ponieważ naszym celem jest tylko w celu zilustrowania koncepcji.)
* Skanowanie pod kątem uczestników jako bezużytecznych odbywa się na T = 0, 5, 10, 15, 20, 25, zgodnie z definicją interwał skanowania 5.
* Okresowe czasomierza wyzwala T = 4, 8, 12, 16, 20, 24, i wykonuje jej wywołanie zwrotne. Nie wpływa na czas bezczynności aktora.
* Wywołanie metody aktora w T = 7 resetuje czas bezczynności, po 0 i wybrano opcję opóźnienia wyrzucanie elementów bezużytecznych aktora.
* Wywołanie zwrotne monitu aktora jest wykonywana co T = 14 i dalsze opóźnienia wyrzucanie elementów bezużytecznych aktora.
* Podczas skanowania kolekcji pamięci na T = 25 czas bezczynności aktora finally przekracza limit czasu bezczynności 10 i aktora jest bezużytecznych.

Aktor nigdy nie będą bezużytecznych podczas wykonywania jednej z metod, niezależnie od tego, ile czas podczas wykonywania tej metody. Jak wspomniano wcześniej, wykonywanie metod interfejsu aktora i wywołania zwrotne monitu zapobiega wyrzucanie elementów bezużytecznych resetując czas bezczynności aktora na 0. Wykonywanie wywołań zwrotnych czasomierza nie powoduje resetowania czas bezczynności na 0. Wyrzucanie elementów bezużytecznych aktora jest jednak opóźnione aż do zakończenia wykonywania wywołanie zwrotne czasomierza.

## <a name="manually-deleting-actors-and-their-state"></a>Ręczne usuwanie złośliwych użytkowników i ich stan
Wyrzucanie elementów bezużytecznych dezaktywowane podmiotów tylko czyści obiektu aktora, ale nie powoduje usunięcia danych przechowywanych w Menedżerze stanu aktora. Aktor jest ponowna aktywacja, jego dane ponownie się dostępne za pośrednictwem Menedżera stanu. W przypadku gdy podmiotów przechowywania danych w Menedżerze stanu i są dezaktywowane ale nigdy aktywowany ponownie może być konieczne wyczyścić swoje dane.  Przykłady sposobu usuwania uczestników, przeczytaj [usunąć złośliwych użytkowników i ich stan](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Kolejne kroki
* [Czasomierze aktora i przypomnieniami](service-fabric-reliable-actors-timers-reminders.md)
* [Zdarzenia aktora](service-fabric-reliable-actors-events.md)
* [Wielobieżność aktora](service-fabric-reliable-actors-reentrancy.md)
* [Aktora Diagnostyka i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentację referencyjną aktora interfejsu API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java przykładowy kod](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
