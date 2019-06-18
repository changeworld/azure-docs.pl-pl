---
title: Przegląd cyklu życia aktora usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Wyjaśnia, Usługa Service Fabric Reliable Actor cyklu życia, wyrzucanie elementów bezużytecznych i ręczne usuwanie aktorów i ich stanu
services: service-fabric
documentationcenter: .net
author: amanbha
manager: chackdan
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: f81fde441a2f0dc2504601f82e5b890eb6e216de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105296"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Cykl życia aktora, automatyczne wyrzucanie elementów bezużytecznych i ręczne delete
Aktor jest aktywowana po raz pierwszy następuje wywołanie do dowolnego z jego metod. Aktor jest dezaktywowany (zebranych przez środowisko uruchomieniowe aktorów pamięci), jeśli nie jest używany przez można skonfigurować czas. Aktor i jej stan można również zostaną usunięte ręcznie w dowolnym momencie.

## <a name="actor-activation"></a>Aktywacja aktora
Po aktywowaniu aktora są następujące operacje:

* Podczas wywołania jest dostępna dla aktora i nie jest już aktywna, jest tworzony nowy aktora.
* Stanu aktora jest ładowany, jeśli jest jego stan.
* `OnActivateAsync` (C#) lub `onActivateAsync` wywoływana jest metoda (Java), (które mogą zostać zastąpione w implementacji aktora).
* Aktor teraz jest uważany za aktywny.

## <a name="actor-deactivation"></a>Dezaktywacja aktora
Po dezaktywacji aktora są następujące operacje:

* Aktor nie jest używany przez pewien czas, spowoduje jego usunięcie z tabeli aktywnych aktorów.
* `OnDeactivateAsync` (C#) lub `onDeactivateAsync` wywoływana jest metoda (Java), (które mogą zostać zastąpione w implementacji aktora). Czyści wszystkie czasomierze aktora. Aktor operacje, takie jak stan, w których zmiany nie powinna być wywoływana z tej metody.

> [!TIP]
> Środowisko uruchomieniowe aktorów sieci szkieletowej emituje niektóre [zdarzenia dotyczące aktora Aktywacja i dezaktywacja](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Są one przydatne w Diagnostyka i monitorowanie wydajności.
>
>

### <a name="actor-garbage-collection"></a>Aktor wyrzucania elementów bezużytecznych
Po dezaktywacji aktora są zwalniane odwołania do obiektu aktora i może być bezużyteczne zwykle przez środowisko uruchomieniowe języka wspólnego (CLR) lub moduł odśmiecania pamięci maszyny wirtualnej (JVM) języka java. Wyrzucanie elementów bezużytecznych tylko czyści obiektu aktora. robi **nie** usunięcia stanu przechowywanego na Menedżera stanu aktora. Przy następnym aktora została aktywowana, tworzony jest nowy obiekt aktora i jej przywróceniu.

Co jest liczone jako "używane" na potrzeby dezaktywacji i wyrzucania elementów bezużytecznych?

* Odbieranie połączenia
* `IRemindable.ReceiveReminderAsync` Metoda wywoływana (ma zastosowanie tylko wtedy, gdy aktor używa przypomnienia)

> [!NOTE]
> Jeśli aktora używa czasomierze i jej wywołanie zwrotne czasomierza jest wywoływana, nie **nie** są liczone jako "używane".
>
>

Zanim przejdziemy do szczegółów dezaktywacji, jest ważne zdefiniować następujące warunki:

* *Interwał skanowania*. Jest to interwał, jaką aktorów środowisko uruchomieniowe szuka aktorów, które będzie możliwa ich dezaktywacja swojej tabeli aktywnych aktorów i wyrzucania. Wartość domyślna to jest 1 minutę.
* *Limit czasu bezczynności*. Jest to ilość czasu, który Aktor musi pozostać nieużywane (bezczynny), zanim możliwa ich Dezaktywacja i wyrzucania. Wartość domyślna to jest 60 minut.

Zazwyczaj nie trzeba zmienić te ustawienia domyślne. Jednakże, jeśli to konieczne, interwałami można zmienić, modyfikując `ActorServiceSettings` podczas rejestrowania usługi [usługa aktora](service-fabric-reliable-actors-platform.md):

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
Dla każdego aktywnego aktora w środowisku uruchomieniowym aktora przechowuje informacje o ilość czasu, która była ona bezczynna (tj. nie jest używany). W środowisku uruchomieniowym aktora sprawdza każdą z uczestnikami co `ScanIntervalInSeconds` aby zobaczyć, może być pamięci zbierane i pobiera ją, jeśli był bezczynny `IdleTimeoutInSeconds`.

W dowolnym momencie aktora jest używany, jego czas bezczynności zostaje zresetowana do 0. Po tym aktora może zostać usunięte tylko wtedy, gdy ponownie węzeł pozostanie bezczynny dla `IdleTimeoutInSeconds`. Odwołaj aktora jest uważany zostały użyte, jeśli jest wykonywane metody interfejsu aktora lub wywołanie zwrotne przypomnienie aktora. Jest Aktor **nie** uważane za użyte, jeśli jej wywołanie zwrotne czasomierza jest wykonywane.

Na poniższym diagramie przedstawiono cykl życia pojedynczego aktora w celu zilustrowania koncepcji te.

![Przykład czasu bezczynności][1]

W przykładzie przedstawiono wpływ wywołania metody aktora, przypomnienia i zegary na okres istnienia tego aktora. Warto wspomnieć o następujących kwestiach dotyczących przykładu:

* ScanInterval i IdleTimeout są ustawione na 5 i 10 odpowiednio. (Jednostki nie mają znaczenia, ponieważ naszym celem jest tylko w celu zilustrowania koncepcji.)
* Skanowanie w poszukiwaniu Aktorzy stanowią bezużytecznych odbywa się na T = 0, 5, 10, 15, 20, 25, zgodnie z definicją interwał skanowania 5.
* Okresowe czasomierz wyzwala T = 4, 8, 12, 16, 20, 24, i wykonuje jej wywołanie zwrotne. Nie ma wpływu na czas bezczynności, po aktora.
* Wywołanie metody aktora w T = 7 resetuje czas bezczynności, po 0 i opóźnia wyrzucanie elementów bezużytecznych aktora.
* Wywołanie zwrotne przypomnienie aktor wykonuje na T = 14 i dalsze opóźnienia wyrzucanie elementów bezużytecznych aktora.
* Podczas skanowania kolekcji wyrzucania elementów na T = 25 aktora czas bezczynności, po wreszcie przekracza limit czasu bezczynności 10, a aktor jest bezużyteczne.

Aktor nigdy nie będą bezużyteczne podczas wykonywania jednej z jego metod, niezależnie od tego, ile jest zużywany czas podczas wykonywania tej metody. Jak wspomniano wcześniej, wykonywanie metod interfejsu aktora i wywołania zwrotne przypomnienie zapobiega wyrzucania elementów bezużytecznych poprzez zresetowanie aktora czas bezczynności, po na 0. Wykonywanie wywołań zwrotnych Czasomierz nie powoduje resetowania czas bezczynności, po na 0. Jednak wyrzucanie elementów bezużytecznych aktora jest odroczone do czasu, zakończyła wykonywanie zadania czasomierza wywołania zwrotnego.

## <a name="manually-deleting-actors-and-their-state"></a>Ręczne usuwanie aktorów i ich stanu
Wyrzucanie elementów bezużytecznych dezaktywowane aktorów tylko Czyści obiekt aktora, ale nie powoduje usunięcia danych, która jest przechowywana w Menedżer stanu aktora. Jeśli ponowna aktywacja jest Aktor, jego dane ponownie stają się dostępne za pośrednictwem Menedżera stanu. W przypadku gdy aktorów przechowywanie danych w State Manager i są dezaktywowane, ale nigdy aktywowany ponownie może być konieczne do wyczyszczenia ich danych.  Przykłady sposobu usuwania aktorów, przeczytaj [usuwanie aktorów i ich stan](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Kolejne kroki
* [Aktor czasomierze i przypomnienia](service-fabric-reliable-actors-timers-reminders.md)
* [Zdarzenia aktora](service-fabric-reliable-actors-events.md)
* [Wielobieżność aktora](service-fabric-reliable-actors-reentrancy.md)
* [Aktor Diagnostyka i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentacja interfejsu API aktora](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Kod przykładowy w języku C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Kod przykładowy w języku Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
