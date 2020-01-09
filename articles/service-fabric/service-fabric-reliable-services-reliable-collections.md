---
title: Wprowadzenie do niezawodnych kolekcji
description: Service Fabric usługi stanowe udostępniają niezawodne kolekcje, które umożliwiają pisanie aplikacji w chmurze o wysokiej dostępności i skalowalności.
ms.topic: conceptual
ms.date: 1/3/2019
ms.openlocfilehash: 48fa682f4c017f66911729e1f581f3aa91cdc28d
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609727"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Wprowadzenie do niezawodnych kolekcji w usłudze Azure Service Fabric stanowe usługi

Niezawodne Kolekcje umożliwiają pisanie aplikacji w chmurze o wysokiej dostępności, skalowalności i małych opóźnieniach, tak jakby były napisane aplikacje pojedynczej aplikacji. Klasy w przestrzeni nazw **Microsoft. servicefabric. Data. Collections** zapewniają zestaw kolekcji, które automatycznie sprawiają, że stan ma wysoką dostępność. Deweloperzy muszą programować tylko do niezawodnych interfejsów API kolekcji i umożliwiają niezawodne kolekcje zarządzać stanem replikowanym i lokalnym.

Kluczowa różnica między niezawodnymi kolekcjami i innymi technologiami wysokiej dostępności (takimi jak Redis, Azure Table service i Azure usługa kolejki) polega na tym, że stan jest przechowywany lokalnie w wystąpieniu usługi, a także zapewnia wysoką dostępność. Oznacza to, że:

* Wszystkie odczyty są lokalne, co skutkuje małymi opóźnieniami i odczytami o wysokiej przepływności.
* Wszystkie zapisy mają minimalną liczbę urządzeń z systemem IOs, co skutkuje małymi opóźnieniami i zapisami o wysokiej przepływności.

![Obraz ewolucji kolekcji.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Niezawodne Kolekcje można traktować jako naturalną ewolucję klas **System. Collections** : nowy zestaw kolekcji przeznaczony do obsługi aplikacji w chmurze i wielokomputerowych bez zwiększania złożoności dla deweloperów. W związku z tym niezawodne kolekcje są następujące:

* Zreplikowane: zmiany stanu są replikowane w celu zapewnienia wysokiej dostępności.
* Utrwalone: dane są utrwalane na dysku w celu zapewnienia trwałości przed awariami na dużą skalę (na przykład w przypadku awarii napięcia w centrum danych).
* Ponieważ zapisy są utrwalane i replikowane, nie można utworzyć nietrwałej ReliableDictionary, ReliableQueue lub innej niezawodnej kolekcji, która zachowuje tylko dane w pamięci.
* Asynchronicznie: interfejsy API są asynchroniczne, aby upewnić się, że wątki nie są blokowane podczas nanoszenia we/wy.
* Transakcyjne: interfejsy API wykorzystują abstrakcję transakcji, dzięki czemu można łatwo zarządzać wieloma niezawodnymi kolekcjami w ramach usługi.

Niezawodne kolekcje oferują Gwarancje silnej spójności, aby zapewnić łatwiejsze tworzenie informacji o stanie aplikacji.
Silna spójność jest zapewniana przez zagwarantowanie, że zatwierdzenia transakcji kończą się tylko po zarejestrowaniu całej transakcji na większości kworum replik, łącznie z podstawowym.
Aby zapewnić słabą spójność, aplikacje mogą potwierdzić z powrotem do klienta/osoby żądającej przed zwróceniem asynchronicznej deklaracji.

Niezawodne interfejsy API kolekcji to ewolucja współbieżnych kolekcji interfejsów API (znajdujących się w przestrzeni nazw **System. Collections. współbieżne** ):

* Asynchroniczne: zwraca zadanie od, w przeciwieństwie do współbieżnych kolekcji, operacje są replikowane i utrwalane.
* Brak parametrów out: używa `ConditionalValue<T>`, aby zwrócić `bool` i wartość zamiast parametrów out. `ConditionalValue<T>` przypomina `Nullable<T>`, ale nie musi być strukturą.
* Transakcje: używa obiektu transakcji, aby umożliwić użytkownikowi grupowanie akcji na wielu niezawodnych kolekcjach w transakcji.

Dzisiaj **firma Microsoft. servicefabric. Data. Collections** zawiera trzy Kolekcje:

* [Niezawodny słownik](https://msdn.microsoft.com/library/azure/dn971511.aspx): reprezentuje replikowany, transakcyjny i asynchroniczny zbiór par klucz/wartość. Podobnie jak w przypadku **ConcurrentDictionary**, zarówno klucz, jak i wartość mogą być dowolnego typu.
* [Niezawodna Kolejka](https://msdn.microsoft.com/library/azure/dn971527.aspx): reprezentuje kolejkę z wbudowaną, transakcyjną i asynchroniczną, pierwszą kolejką (FIFO). Podobnie jak w przypadku **ConcurrentQueue**, wartość może być dowolnego typu.
* [Niezawodna Kolejka współbieżna](service-fabric-reliable-services-reliable-concurrent-queue.md): reprezentuje zreplikowaną, transakcyjną i asynchroniczną kolejkę kolejności, w której ma być duża przepływność. Podobnie jak w przypadku **ConcurrentQueue**, wartość może być dowolnego typu.

## <a name="next-steps"></a>Następne kroki

* [Wskazówki dotyczące niezawodnej kolekcji & zalecenia](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakcje i blokady](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Zarządzanie danymi
  * [Tworzenie kopii zapasowej i przywracanie](service-fabric-reliable-services-backup-restore.md)
  * [Powiadomienia](service-fabric-reliable-services-notifications.md)
  * [Serializacja elementu Reliable Collection](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serializacja i uaktualnienie](service-fabric-application-upgrade-data-serialization.md)
  * [Konfiguracja niezawodnego menedżera stanu](service-fabric-reliable-services-configuration.md)
* Inne
  * [Reliable Services Szybki Start](service-fabric-reliable-services-quick-start.md)
  * [Dokumentacja dla deweloperów dla niezawodnych kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
