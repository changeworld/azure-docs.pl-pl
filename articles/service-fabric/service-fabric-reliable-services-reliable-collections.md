---
title: Wprowadzenie do elementów Reliable Collections w usług stanowych w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Usługa Service Fabric usługi stanowe udostępniają elementy reliable collections, które umożliwiają pisanie aplikacji w chmurze wysoko dostępne, skalowalne i małym opóźnieniem.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 1/3/2019
ms.author: aljo
ms.openlocfilehash: 4ed76b207db4712058b5524cd1b31fd65b0e19a4
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664414"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Wprowadzenie do elementów Reliable Collections w usług stanowych w usłudze Azure Service Fabric

Elementy Reliable Collections umożliwiają pisanie aplikacji w chmurze wysoko dostępne, skalowalne i małego opóźnienia, tak, jakby podczas pisania aplikacji z jednego komputera. Klasy w **Microsoft.ServiceFabric.Data.Collections** przestrzeni nazw udostępniają zestaw kolekcji, które automatycznie swój stan wysokiej dostępności. Deweloperzy muszą program tylko do niezawodne interfejsy API kolekcji i umożliwiają zarządzanie stanem replikowanych i lokalne z elementów Reliable Collections.

Główną różnicą między elementami Reliable Collections i innych technologii wysokiej dostępności (np. pamięci podręcznej Redis Azure Table service i usługi kolejek platformy Azure) to, że stan są przechowywane lokalnie w wystąpieniu usługi podczas również wykonywane o wysokiej dostępności. Oznacza to, że:

* Wszystkie operacje odczytu są lokalne, które powoduje małe opóźnienia i wysoką przepływność odczytuje.
* Wszystkie operacje zapisu pociągnąć za sobą minimalną liczbę sieci dla systemu IOs, co skutkuje małe opóźnienia i zapisuje o wysokiej przepływności.

![Obraz przedstawiający ewolucji kolekcji.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Elementy Reliable Collections mogą być uważane za naturalną kontynuacją **System.Collections** klasy: nowy zestaw kolekcji, które są przeznaczone dla aplikacji w chmurze i wielu komputerów bez zwiększania stopnia złożoności dla dla deweloperów. W efekcie elementów Reliable Collections to:

* Replikowane: Zmiany stanu są replikowane w celu zapewnienia wysokiej dostępności.
* Zachowywane: Dane są utrwalane na dysku w celu zapewnienia trwałości przed awariami na dużą skalę (na przykład awaria zasilania centrum danych).
* Ponieważ operacje zapisu są utrwalenia i replikacji, nie można utworzyć volatile ReliableDictionary, ReliableQueue lub inna kolekcja niezawodne, która tylko utrwala dane w pamięci.
* Asynchroniczne: Interfejsy API są asynchroniczne, aby upewnić się, że wątki nie są blokowane podczas generowania operacji We/Wy.
* Transakcyjne: Interfejsy API wykorzystywać pozyskiwania transakcji, tak łatwe zarządzanie wielu elementów Reliable Collections w ramach usługi.

Elementy Reliable Collections zapewniają gwarancje silnej spójności poza pole, aby ułatwić logikę o stanie aplikacji.
Wysoki poziom spójności uzyskuje się poprzez zapewnienie transakcji, które zatwierdzenia Zakończ tylko wtedy, gdy cała transakcja zalogował kworum z większością replik, w tym podstawowy.
W celu osiągnięcia spójności słabszy, aplikacji można potwierdzić powrót do klienta/osoby żądającej przed zwraca zatwierdzania asynchronicznego.

Niezawodne interfejsy API kolekcje są unowocześnienia kolekcji współbieżnych interfejsy API (w **System.Collections.Concurrent** przestrzeni nazw):

* Asynchroniczne: Zwraca klasę task, ponieważ, w przeciwieństwie do kolekcji współbieżnych operacji są replikowane i utrwalone.
* Brak parametrów out: Używa `ConditionalValue<T>` do zwrócenia `bool` i wartości zamiast parametrów out. `ConditionalValue<T>` przypomina `Nullable<T>` , ale nie wymaga T, aby być strukturą.
* Transakcje: Używa obiektu transakcji, aby umożliwić użytkownikowi grupy akcji na wiele elementów Reliable Collections w transakcji.

Obecnie **Microsoft.ServiceFabric.Data.Collections** zawiera trzy kolekcje:

* [Niezawodnego słownika](https://msdn.microsoft.com/library/azure/dn971511.aspx): Reprezentuje kolekcję replikowane, transakcji i asynchroniczne par klucz/wartość. Podobnie jak **ConcurrentDictionary**, zarówno klucz i wartość mogą być dowolnego typu.
* [Niezawodna kolejka](https://msdn.microsoft.com/library/azure/dn971527.aspx): Reprezentuje replikowane, transakcji i asynchroniczne strict pierwszy w FIFO (FIFO) kolejkę. Podobnie jak **ConcurrentQueue**, wartość może być dowolnego typu.
* [Niezawodna kolejka współbieżna](service-fabric-reliable-services-reliable-concurrent-queue.md): Reprezentuje replikowane, transakcji i asynchroniczne optymalnego porządkowanie kolejki wysokiej przepływności. Podobnie jak **ConcurrentQueue**, wartość może być dowolnego typu.

## <a name="next-steps"></a>Kolejne kroki

* [Reliable Collection wytyczne i zalecenia](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakcje i blokady](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Zarządzanie danymi
  * [Tworzenie kopii zapasowej i przywracanie](service-fabric-reliable-services-backup-restore.md)
  * [Powiadomienia](service-fabric-reliable-services-notifications.md)
  * [Serializacja elementu Reliable Collection](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serializacja i uaktualnienia](service-fabric-application-upgrade-data-serialization.md)
  * [Elementy Reliable State Manager konfiguracji](service-fabric-reliable-services-configuration.md)
* Inne
  * [Reliable Services — szybki start](service-fabric-reliable-services-quick-start.md)
  * [Dokumentacja dla deweloperów dla elementów Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
