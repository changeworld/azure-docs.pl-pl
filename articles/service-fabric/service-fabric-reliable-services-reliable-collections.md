---
title: Wprowadzenie do niezawodnych kolekcji w usłudze Azure Service Fabric stanowe usługi | Microsoft Docs
description: Service Fabric usługi stanowe udostępniają niezawodne kolekcje, które umożliwiają pisanie aplikacji w chmurze o wysokiej dostępności i skalowalności.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 1/3/2019
ms.author: atsenthi
ms.openlocfilehash: a7b30003fd02f8ab2e367311cdb3f56c80dbb4b2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599267"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Wprowadzenie do niezawodnych kolekcji w usłudze Azure Service Fabric stanowe usługi

Niezawodne Kolekcje umożliwiają pisanie aplikacji w chmurze o wysokiej dostępności, skalowalności i małych opóźnieniach, tak jakby były napisane aplikacje pojedynczej aplikacji. Klasy w przestrzeni nazw **Microsoft. servicefabric. Data. Collections** zapewniają zestaw kolekcji, które automatycznie sprawiają, że stan ma wysoką dostępność. Deweloperzy muszą programować tylko do niezawodnych interfejsów API kolekcji i umożliwiają niezawodne kolekcje zarządzać stanem replikowanym i lokalnym.

Kluczowa różnica między niezawodnymi kolekcjami i innymi technologiami wysokiej dostępności (takimi jak Redis, Azure Table service i Azure usługa kolejki) polega na tym, że stan jest przechowywany lokalnie w wystąpieniu usługi, a także zapewnia wysoką dostępność. Oznacza to, że:

* Wszystkie odczyty są lokalne, co skutkuje małymi opóźnieniami i odczytami o wysokiej przepływności.
* Wszystkie zapisy mają minimalną liczbę urządzeń z systemem IOs, co skutkuje małymi opóźnieniami i zapisami o wysokiej przepływności.

![Obraz ewolucji kolekcji.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Niezawodne Kolekcje można traktować jako naturalną ewolucję klas **System. Collections** : nowy zestaw kolekcji przeznaczony do obsługi aplikacji w chmurze i wielokomputerowych bez zwiększania złożoności dla deweloperów. W związku z tym niezawodne kolekcje są następujące:

* Powtórzon Zmiany stanu są replikowane w celu zapewnienia wysokiej dostępności.
* Trwale Dane są utrwalane na dysku w celu zapewnienia trwałości przed awariami na dużą skalę (na przykład w przypadku awarii w centrum danych).
* Ponieważ zapisy są utrwalane i replikowane, nie można utworzyć nietrwałej ReliableDictionary, ReliableQueue lub innej niezawodnej kolekcji, która zachowuje tylko dane w pamięci.
* Komunikacji Interfejsy API są asynchroniczne, aby zapewnić, że wątki nie są blokowane podczas nanoszenia we/wy.
* Transakcyjna Interfejsy API wykorzystują abstrakcję transakcji, dzięki czemu można łatwo zarządzać wieloma niezawodnymi kolekcjami w ramach usługi.

Niezawodne kolekcje oferują Gwarancje silnej spójności, aby zapewnić łatwiejsze tworzenie informacji o stanie aplikacji.
Silna spójność jest zapewniana przez zagwarantowanie, że zatwierdzenia transakcji kończą się tylko po zarejestrowaniu całej transakcji na większości kworum replik, łącznie z podstawowym.
Aby zapewnić słabą spójność, aplikacje mogą potwierdzić z powrotem do klienta/osoby żądającej przed zwróceniem asynchronicznej deklaracji.

Niezawodne interfejsy API kolekcji to ewolucja współbieżnych kolekcji interfejsów API (znajdujących się w przestrzeni nazw **System. Collections. współbieżne** ):

* Komunikacji Zwraca zadanie od, w przeciwieństwie do współbieżnych kolekcji, operacje są replikowane i utrwalane.
* Brak parametrów out: Używa `ConditionalValue<T>` do`bool` zwracania wartości i zamiast parametrów out. `ConditionalValue<T>`przypomina, `Nullable<T>` ale nie musi być strukturą.
* Akcja Używa obiektu transakcji, aby umożliwić użytkownikowi grupowanie akcji na wielu niezawodnych kolekcjach w transakcji.

Dzisiaj **firma Microsoft. servicefabric. Data. Collections** zawiera trzy Kolekcje:

* [Niezawodny słownik](https://msdn.microsoft.com/library/azure/dn971511.aspx): Reprezentuje replikowany, transakcyjny i asynchroniczny zbiór par klucz/wartość. Podobnie jak w przypadku **ConcurrentDictionary**, zarówno klucz, jak i wartość mogą być dowolnego typu.
* [](https://msdn.microsoft.com/library/azure/dn971527.aspx)Niezawodna Kolejka: Przedstawia zreplikowaną, transakcyjną i asynchronicznie rygorystyczną kolejkę First-In, First-Out (FIFO). Podobnie jak w przypadku **ConcurrentQueue**, wartość może być dowolnego typu.
* [](service-fabric-reliable-services-reliable-concurrent-queue.md)Niezawodna Kolejka współbieżna: Reprezentuje zreplikowaną, transakcyjną i asynchroniczną kolejkę kolejności, w której można uzyskać wysoką przepływność. Podobnie jak w przypadku **ConcurrentQueue**, wartość może być dowolnego typu.

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
