---
title: Omówienie cyklu życia aktora sieci szkieletowej usługi Azure
description: Objaśnienie cyklu życia niezawodnego aktora sieci usług, wyrzucania elementów bezużytecznych i ręcznego usuwania aktorów i ich stanu
author: amanbha
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: b05da78091260297d94062c06cba100d01ce7e2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258319"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Cykl życia aktora, automatyczne wyrzucanie elementów bezużytecznych i ręczne usuwanie
Aktor jest aktywowany po raz pierwszy wywołanie do któregokolwiek z jego metod. Aktor jest dezaktywowany (moduł śmietnikowy zebrany przez środowisko uruchomieniowe Actors), jeśli nie jest używany przez konfigurowalny okres czasu. Aktor i jego stan można również usunąć ręcznie w dowolnym momencie.

## <a name="actor-activation"></a>Aktywacja aktora
Po aktywowaniu aktora występują następujące zdarzenia:

* Gdy nadejdzie połączenie dla aktora, a jeden nie jest jeszcze aktywny, tworzony jest nowy aktor.
* Stan aktora jest ładowany, jeśli utrzymuje stan.
* Wywołana `OnActivateAsync` jest metoda `onActivateAsync` (C#) lub (Java) (która może zostać zastąpiona w implementacji aktora).
* Aktor jest obecnie uważany za aktywnego.

## <a name="actor-deactivation"></a>Dezaktywacja aktora
Gdy aktor jest dezaktywowany, występują następujące zdarzenia:

* Gdy aktor nie jest używany przez pewien okres czasu, jest usuwany z tabeli Aktywne aktorzy.
* Wywołana `OnDeactivateAsync` jest metoda `onDeactivateAsync` (C#) lub (Java) (która może zostać zastąpiona w implementacji aktora). To czyści wszystkie czasomierze dla aktora. Operacje aktora, takie jak zmiany stanu, nie powinny być wywoływane z tej metody.

> [!TIP]
> Środowisko wykonawcze Fabric Actors emituje niektóre [zdarzenia związane z aktywacją i dezaktywacją aktora.](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) Są one przydatne w diagnostyce i monitorowaniu wydajności.
>
>

### <a name="actor-garbage-collection"></a>Pobieranie elementów bezużytecznych aktora
Gdy aktor jest dezaktywowany, odwołania do obiektu aktora są zwalniane i mogą być ścierne zbierane normalnie przez środowisko wykonawcze języka wspólnego (CLR) lub moduł odśmiecania maszyn wirtualnych java (JVM). Wyrzucanie elementów bezużytecznych tylko czyści obiekt aktora; **nie** usuwa stanu przechowywanego w menedżerze stanu aktora. Przy następnym aktywowanie aktora zostanie utworzony nowy obiekt aktora i jego stan zostanie przywrócony.

Co liczy się jako "używane" w celu dezaktywacji i wyrzucania elementów bezużytecznych?

* Odbieranie połączenia
* `IRemindable.ReceiveReminderAsync`wywoływana metoda (ma zastosowanie tylko wtedy, gdy aktor używa przypomnień)

> [!NOTE]
> jeśli aktor używa czasomierzy i wywoływane jest jego wywołanie zwrotne czasomierza, **nie** jest onliczany jako "używany".
>
>

Zanim przejdziemy do szczegółów dezaktywacji, ważne jest, aby zdefiniować następujące terminy:

* *Interwał skanowania*. Jest to interwał, w którym środowisko wykonawcze Actors skanuje swoją tabelę Aktywnych aktorów dla aktorów, które można dezaktywować i zbierać śmieci. Wartość domyślna dla tego jest 1 minuta.
* *Limit czasu bezczynnego*. Jest to czas, który aktor musi pozostać nieużywane (bezczynne), zanim będzie można dezaktywować i śmieci zebrane. Wartość domyślna dla tego jest 60 minut.

Zazwyczaj nie trzeba zmieniać tych wartości domyślnych. Jednak w razie potrzeby te interwały można zmienić podczas `ActorServiceSettings` rejestrowania usługi [aktora:](service-fabric-reliable-actors-platform.md)

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
Dla każdego aktywnego aktora środowisko wykonawcze aktora śledzi czas, przez jaki był bezczynny (tj. nie używany). Środowisko wykonawcze aktora sprawdza każdego `ScanIntervalInSeconds` z aktorów, aby sprawdzić, czy można go zebrać, i zaznacza je, jeśli jest bezczynny dla `IdleTimeoutInSeconds`.

Za każdym razem, gdy aktor jest używany, jego czas bezczynny jest resetowany do 0. Po tym, aktor może być śmieci zbierane tylko wtedy, `IdleTimeoutInSeconds`gdy ponownie pozostaje bezczynny dla . Przypomnijmy, że aktor jest uważany za używany, jeśli wykonywana jest metoda interfejsu aktora lub wywołanie zwrotne przypomnienia aktora. Aktor **nie** jest uważany za używany, jeśli jego wywołania zwrotnego czasomierza jest wykonywana.

Na poniższym diagramie przedstawiono cykl życia pojedynczego aktora, aby zilustrować te pojęcia.

![Przykład czasu bezczynności][1]

W przykładzie pokazano wpływ wywołań metody aktora, przypomnień i czasomierzy na okres istnienia tego aktora. Warto wspomnieć o następujących punktach dotyczących przykładu:

* ScanInterval i IdleTimeout są ustawione odpowiednio na 5 i 10. (Jednostki nie mają tu znaczenia, ponieważ naszym celem jest tylko zilustrowanie tej koncepcji).
* Skanowanie dla podmiotów, które mają być zbierane śmieci dzieje się w T = 0,5,10,15,20,25, zgodnie z definicją w interwale skanowania 5.
* Okresowy zegar uruchamia się na T=4,8,12,16,20,24, a jego wywołanie zwrotne jest wykonywane. Nie ma to wpływu na czas bezczynności aktora.
* Wywołanie metody aktora w T =7 resetuje czas bezczynności do 0 i opóźnia wyrzucanie elementów bezużytecznych aktora.
* Wywołanie zwrotne przypomnienia aktora wykonuje w T = 14 i dalsze opóźnienia wyrzucania elementów bezużytecznych aktora.
* Podczas skanowania wyrzucania elementów bezużytecznych w T =25 czasu bezczynności aktora ostatecznie przekracza limit czasu bezczynności 10, a aktor jest zbierane śmieci.

Aktor nigdy nie będzie śmieci zebrane podczas wykonywania jednej z jego metod, bez względu na to, ile czasu spędza się na wykonywaniu tej metody. Jak wspomniano wcześniej, wykonanie metod interfejsu aktora i wywołania zwrotne przypomnienia zapobiega wyrzucania elementów bezużytecznych przez zresetowanie aktora czasu bezczynności do 0. Wykonywanie wywołań zwrotnych czasomierza nie resetuje czasu bezczynności do 0. Jednak wyrzucanie elementów bezużytecznych aktora jest odroczone, dopóki wywołanie zwrotne czasomierza nie zostanie zakończone wykonanie.

## <a name="manually-deleting-actors-and-their-state"></a>Ręczne usuwanie aktorów i ich stanu
Wyrzucanie elementów bezużytecznych dezaktywowanych aktorów tylko czyści obiekt aktora, ale nie usuwa danych, które są przechowywane w menedżerze stanu aktora. Gdy aktor jest ponownie aktywowany, jego dane są ponownie udostępniane za pośrednictwem menedżera stanu. W przypadkach, gdy podmioty przechowują dane w Menedżerze stanu i są dezaktywowane, ale nigdy nie ponownie aktywowane, może być konieczne oczyszczenie ich danych.  Przykłady usuwania aktorów, odczytywania [usuwania aktorów i ich stanu](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Następne kroki
* [Czasomierze aktorów i przypomnienia](service-fabric-reliable-actors-timers-reminders.md)
* [Wydarzenia aktorskie](service-fabric-reliable-actors-events.md)
* [Ponowne entrancy aktora](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostyka aktora i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentacja referencyjna interfejsu API aktora](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# Przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowy kod Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
