---
title: Wprowadzenie do kolekcji niezawodnej w stanowe usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Usługi sieć szkieletowa usług stanowych zapewniają niezawodne kolekcje, które pozwalają na zapis aplikacji w chmurze wysoko dostępnych, skalowalnych i małe opóźnienia.
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/6/2017
ms.author: mcoskun
ms.openlocfilehash: 2876d90c02995394104009d1b2d62d5b3ed6a8d9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Wprowadzenie do kolekcji niezawodnej w stanowe usługi sieć szkieletowa usług Azure
Niezawodne kolekcje umożliwiają pisanie aplikacji w chmurze wysoko dostępnych, skalowalnych i małych opóźnieniach tak, jakby podczas pisania aplikacji pojedynczego komputera. Klasy w **Microsoft.ServiceFabric.Data.Collections** przestrzeni nazw udostępniają zestaw kolekcji, które automatycznie swój stan wysokiej dostępności. Deweloperzy muszą program tylko dla niezawodnego interfejsów API kolekcji i pozwól niezawodnej kolekcje Zarządzanie stanem replikowane i lokalnej.

Klucza różnica między kolekcje niezawodnych i innymi technologiami wysokiej dostępności (np. pamięci podręcznej Redis, usługa tabeli platformy Azure i usługi kolejek platformy Azure) jest, że stan jest przechowywany lokalnie w wystąpieniu usługi podczas również wysyłanych wysokiej dostępności. Oznacza to, że:

* Wszystkie operacje odczytu są lokalne, które powoduje małe opóźnienia i odczytuje wysokiej przepustowości.
* Zapisuje wszystkie pociągnąć za sobą minimalną liczbę IOs sieci, co powoduje małe opóźnienia i zapisuje wysokiej przepustowości.

![Obraz zmiany kolekcji.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Niezawodne kolekcji można traktować jak fizyczną rozwoju **System.Collections** klasy: nowy zestaw kolekcji, które są przeznaczone dla aplikacji chmury i złożonym z wielu komputerów bez zwiększania złożoności dla deweloperów. W efekcie niezawodnej kolekcje są:

* Replikacja: Zmiany stanu są replikowane wysokiej dostępności.
* Utrwalony: Danych jest utrwalony na dysku do trwałości względem awarii na dużą skalę (na przykład awaria zasilania centrum danych).
* Asynchroniczne: Interfejsy API są asynchroniczne, aby upewnić się, że wątków nie są blokowane, gdy są naliczane we/wy.
* Transakcyjne: Abstrakcji transakcji używają interfejsów API, a więc można łatwo zarządzać wielu kolekcji niezawodnej w ramach usługi.

Silna spójność gwarantuje fabrycznej, aby ułatwić rozsądkiem o stanie aplikacji zapewniają niezawodne kolekcji.
Wysoki poziom spójności uzyskuje się poprzez zapewnienie transakcji, które zatwierdzeń Zakończ tylko wtedy, gdy cała transakcja został zalogowany większość kworum replik, łącznie z serwera podstawowego.
W celu osiągnięcia słabszych spójności, aplikacje można potwierdzić powrót do klienta/zleceniodawcy przed zwraca zatwierdzania asynchronicznego.

Niezawodne interfejsów API kolekcje są zmiany kolekcji współbieżnych interfejsów API (w **System.Collections.Concurrent** przestrzeni nazw):

* Asynchroniczne: Zwraca klasę task, ponieważ w przeciwieństwie do kolekcji współbieżnych operacji są replikowane i utrwalone.
* Parametry wyjściowe nie: używa `ConditionalValue<T>` aby zwrócić wartość logiczną i wartość zamiast parametrów wyjściowych. `ConditionalValue<T>` przypomina `Nullable<T>` , ale nie wymaga T, aby być strukturą.
* Transakcje: Używa obiekt transakcji, aby umożliwić użytkownikowi grupy działań w wielu kolekcjach niezawodnej w transakcji.

Obecnie **Microsoft.ServiceFabric.Data.Collections** zawiera trzy kolekcje:

* [Słownik niezawodnej](https://msdn.microsoft.com/library/azure/dn971511.aspx): reprezentuje replikowane, transakcyjne i asynchroniczne kolekcję par klucz/wartość. Podobnie jak **obiekt ConcurrentDictionary**, zarówno klucz i wartość mogą być dowolnego typu.
* [Kolejka niezawodnych](https://msdn.microsoft.com/library/azure/dn971527.aspx): reprezentuje replikowane, transakcyjne i asynchroniczne strict pierwszy w, FIFO (FIFO) kolejki. Podobnie jak **ConcurrentQueue**, wartość może być dowolnego typu.
* [Niezawodne kolejki równoczesnych](service-fabric-reliable-services-reliable-concurrent-queue.md): reprezentuje replikowane, transakcyjne i asynchroniczne optymalnego porządkowanie kolejki wysokiej przepływności. Podobnie jak **ConcurrentQueue**, wartość może być dowolnego typu.

## <a name="next-steps"></a>Kolejne kroki
* [Niezawodnej kolekcji wskazówki i zalecenia](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakcje i blokad](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Zarządzanie danymi
  * [Tworzenie kopii zapasowej i przywracanie](service-fabric-reliable-services-backup-restore.md)
  * [Powiadomienia](service-fabric-reliable-services-notifications.md)
  * [Serializacja elementu Reliable Collection](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Serializacja i uaktualniania](service-fabric-application-upgrade-data-serialization.md)
  * [Stan niezawodny Menedżera konfiguracji](service-fabric-reliable-services-configuration.md)
* Inne
  * [Niezawodne usługi szybki start](service-fabric-reliable-services-quick-start.md)
  * [Dokumentacja dla deweloperów niezawodnej kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
