---
title: Wskazówki i zalecenia dotyczące niezawodnej kolekcje w programie Azure Usługa sieci szkieletowej | Dokumentacja firmy Microsoft
description: Wskazówki i zalecenia dotyczące używania kolekcji niezawodnej sieci szkieletowej usług
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
ms.date: 12/10/2017
ms.author: mcoskun
ms.openlocfilehash: 7a61fa9df5b5232c11f4a546ec5f050461c88e88
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Wskazówki i zalecenia dotyczące niezawodnej kolekcji w sieci szkieletowej usług Azure
Ta sekcja zawiera wskazówki dotyczące korzystania niezawodnej Menedżer stanu i niezawodne kolekcji. Celem jest ułatwienie użytkownikom uniknąć typowych problemów.

Wytyczne dzielą się na prosty zalecenia prefiksem warunki *czy*, *rozważ*, *należy unikać* i *nie*.

* Nie należy modyfikować niestandardowego typu zwrócone przez operacje odczytu obiektu (na przykład `TryPeekAsync` lub `TryGetValueAsync`). Niezawodne kolekcje, podobnie jak kolekcji współbieżnych zwraca odwołanie do obiektów, a nie kopię.
* Przed zmodyfikowaniem go, należy wykonać bezpośrednich kopii zwrócony obiekt typu niestandardowego. Ponieważ wbudowane typy i struktury są przebiegu przez wartość, nie trzeba zrobić bezpośrednich kopii na nich, chyba że zawierają one wpisane odwołanie do pola lub właściwości, które chcesz zmodyfikować.
* Nie używaj `TimeSpan.MaxValue` dla limitu czasu. Limity czasu powinien służyć do wykrywania zakleszczenia.
* Nie używaj transakcji po została zatwierdzona, zostało przerwane lub usunięty.
* Nie należy używać wyliczania poza zasięg transakcji, który został utworzony.
* Nie twórz transakcji w innej transakcji `using` instrukcji, ponieważ może to spowodować zakleszczenie.
* Upewnij się, że Twoje `IComparable<TKey>` implementacja jest prawidłowa. System ma zależności `IComparable<TKey>` do scalenia punkty kontrolne i wierszy.
* Użyj blokady aktualizacji podczas odczytywania elementu zamiaru Aby zaktualizować go w celu uniemożliwienia klasy zakleszczenia.
* Należy wziąć pod uwagę zachowanie liczby kolekcji niezawodnej przypadających na partycję za mniej niż 1000. Preferuj niezawodnej kolekcji z elementów przed więcej niezawodnej kolekcje z mniej elementów.
* Pomyśl o pozostawieniu elementów (na przykład TKey + TValue niezawodnej słownika) poniżej 80 KB: mniejszych, tym lepiej. Zmniejsza to ilość wymagania we/wy sterty dużych obiektów użycia jak również dysków i sieci. Często zmniejsza replikowanie danych zduplikowanych, gdy tylko jednej części mała wartość jest aktualizowana. Typowy sposób można to osiągnąć w słowniku niezawodnej jest włamanie swoich wierszy się na wiele wierszy.
* Należy wziąć pod uwagę przy użyciu kopii zapasowej i przywrócenia jej funkcjonalności mają odzyskiwania po awarii.
* Unikaj mieszanie operacje pojedynczej jednostki i operacje obejmujące wiele urządzeń (np. `GetCountAsync`, `CreateEnumerableAsync`) w ramach jednej transakcji z powodu poziomu izolacji inny.
* Obsługa InvalidOperationException. Transakcje użytkowników może być przerwane przez system wielu powodów. Na przykład gdy niezawodnej Menedżer stanów jest zmiana jego rolą poza podstawowej lub transakcji długotrwałe blokuje obcinania dziennika transakcyjnych. W takich przypadkach użytkownik może odbierać InvalidOperationException wskazujący, że ich transakcji został już zakończony. Przy założeniu, przerwanie transakcji nie zażądano przez użytkownika, najlepszym sposobem obsługi tego wyjątku jest można zlikwidować transakcji, sprawdź, czy token anulowania zostały sygnalizowane (lub Rola repliki została zmieniona), a jeśli nie utworzyć nową transakcję i spróbuj ponownie.  

Poniżej przedstawiono niektóre czynności, które należy wziąć pod uwagę:

* Domyślny limit czasu wynosi cztery sekund dla wszystkich niezawodnej kolekcji interfejsów API. Większość użytkowników należy używać domyślny limit czasu.
* Domyślny token anulowania jest `CancellationToken.None` w wszystkich interfejsów API niezawodnej kolekcji.
* Parametr typu klucza (*TKey*) dla słownika niezawodnej poprawnie musi implementować `GetHashCode()` i `Equals()`. Klucze muszą być niezmienialne.
* Uzyskanie wysokiej dostępności dla kolekcji niezawodne, każda usługa powinna mieć co najmniej docelowej i repliki minimalny rozmiar 3.
* Operacje odczytu na serwerze pomocniczym może odczytać wersje, które nie są zatwierdzone kworum.
  Oznacza to, że wersji danych, które są odczytywane z jednej dodatkowej może FAŁSZ postępem.
  Odczyty z podstawowego zawsze są stabilne: może nie być false biegiem czasu.

### <a name="next-steps"></a>Kolejne kroki
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakcje i blokad](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Zarządzanie danymi
  * [Tworzenie kopii zapasowej i przywracanie](service-fabric-reliable-services-backup-restore.md)
  * [Powiadomienia](service-fabric-reliable-services-notifications.md)
  * [Serializacja i uaktualniania](service-fabric-application-upgrade-data-serialization.md)
  * [Stan niezawodny Menedżera konfiguracji](service-fabric-reliable-services-configuration.md)
* Inne
  * [Niezawodne usługi szybki start](service-fabric-reliable-services-quick-start.md)
  * [Dokumentacja dla deweloperów niezawodnej kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
