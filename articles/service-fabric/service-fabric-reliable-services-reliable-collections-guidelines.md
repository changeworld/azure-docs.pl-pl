---
title: Wytyczne i zalecenia dotyczące elementów Reliable Collections na platformie Azure Service Fabric | Dokumentacja firmy Microsoft
description: Wskazówki i zalecenia dotyczące używania usługi Service Fabric elementy Reliable Collections
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
ms.date: 12/10/2017
ms.author: aljo
ms.openlocfilehash: 810427c394c3912142e0a21cf1b5c29b81620afb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60774101"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Wskazówki i zalecenia dotyczące elementów Reliable Collections w usłudze Azure Service Fabric
Ta sekcja zawiera wskazówki dotyczące korzystania z Reliable State Manager i elementów Reliable Collections. Celem jest pomaganie użytkownikom uniknąć typowych pułapek.

Wytyczne są uporządkowane jako prosty zalecenia prefiksem warunki *czy*, *rozważ*, *należy unikać* i *nie*.

* Nie należy modyfikować obiektu typu niestandardowego zwracany przez operacje odczytu (na przykład `TryPeekAsync` lub `TryGetValueAsync`). Niezawodne kolekcje, podobnie jak kolekcje współbieżne zwraca odwołanie do obiektów, a nie kopia.
* Przed zmodyfikowaniem, należy wykonać głęboka kopia zwracany obiekt typu niestandardowego. Ponieważ struktur i typy wbudowane przekazać przez wartość, nie musisz zrobić kopię głęboką na nich, chyba że zawierają one wpisane odwołania pola lub właściwości, które chcesz zmodyfikować.
* Nie używaj `TimeSpan.MaxValue` dla limitów czasu. Błędy przekroczenia limitu czasu powinna służyć do wykrywania zakleszczenia.
* Nie używaj transakcji po została zatwierdzona, zostało przerwane lub usunięty.
* Nie należy używać wyliczenia poza zakresem transakcji, który został utworzony.
* Nie należy tworzyć transakcji w ramach innej transakcji `using` instrukcji, ponieważ może to spowodować zakleszczenia.
* Nie należy tworzyć niezawodne stanu z `IReliableStateManager.GetOrAddAsync` i niezawodne stanu w ramach jednej transakcji. Skutkuje to InvalidOperationException.
* Upewnij się, że Twoje `IComparable<TKey>` implementacja jest prawidłowa. System zdobywa zależności `IComparable<TKey>` scalania punkty kontrolne i wierszy.
* Użyj blokady aktualizacji podczas odczytywania elementu z zamiarem Aby zaktualizować go w celu uniemożliwienia klasy zakleszczenia.
* Należy wziąć pod uwagę zachowanie liczbę elementów Reliable Collections na partycję za mniej niż 1000. Preferuj elementów Reliable Collections z większej liczby elementów przed więcej elementów Reliable Collections przy użyciu mniejszej liczby elementów.
* Pomyśl o pozostawieniu elementów (na przykład TKey + TValue dla niezawodnego słownika) poniżej 80 w kilobajtach: mniejsze, tym lepiej. Zmniejsza to ilość wymagania sterty obiektów wielkich użycia, a także dysku i sieci We/Wy. Często zmniejsza replikowanie zduplikowanych danych, po zaktualizowaniu tylko jeden niewielką część wartości. Typowym sposobem osiągnięcia tego celu w niezawodnym słowniku, jest włamanie swoich wierszy się na wiele wierszy.
* Należy wziąć pod uwagę przy użyciu kopii zapasowej i przywracania funkcjonalności mieć odzyskiwania po awarii.
* Unikaj łączenia operacji w pojedynczą jednostkę i operacje obejmujące wiele urządzeń (np. `GetCountAsync`, `CreateEnumerableAsync`) w ramach jednej transakcji z powodu izolacji różne poziomy.
* Obsługuj InvalidOperationException. Transakcje użytkownika można przerwane przez system do różnych powodów. Na przykład gdy Reliable State Manager ulegnie zmianie jego rolę, poza podstawowe lub długotrwałej transakcji blokuje obcinania dziennika transakcji. W takiej sytuacji użytkownik może zostać wyświetlony InvalidOperationException wskazujący, że ich transakcji już została zakończona. Zakładając, że, zakończenia transakcji nie zażądano przez użytkownika, najlepszym sposobem obsługi tego wyjątku jest dispose transakcji, sprawdź, czy zasygnalizują token anulowania (lub zmieniono Rola repliki) i tworzy nowy, jeśli Transakcja i spróbuj ponownie.  

Oto kilka rzeczy, które należy uwzględnić:

* Domyślny limit czasu wynosi czterech sekund dla wszystkich niezawodnych kolekcji interfejsów API. Większość użytkowników należy używać domyślny limit czasu.
* Domyślny token anulowania jest `CancellationToken.None` w wszystkie interfejsy API niezawodnych kolekcji.
* Parametr typu klucza (*TKey*) dla niezawodnego słownika należy poprawnie zaimplementować `GetHashCode()` i `Equals()`. Klucze muszą być niezmienialne.
* Uzyskanie wysokiej dostępności dla elementów Reliable Collections, każda usługa powinny mieć co najmniej docelowej i minimalnej repliki Ustaw rozmiar 3.
* Operacje odczytu na pomocniczym może odczytać wersje, które nie są zatwierdzane kworum.
  Oznacza to, że wersję danych, które są odczytywane z jednej pomocniczej może być false wykazała postępów.
  Odczyty z podstawowego są zawsze stabilne: może nigdy nie być false wykazała postępów.
* Zabezpieczenia/prywatność danych utrwalone przez aplikację w niezawodnej kolekcji jest decyzja, z zastrzeżeniem zabezpieczenia udostępniane przez usługi zarządzania magazynem; CZYLI Szyfrowanie dysków systemu operacyjnego może służyć do ochrony danych magazynowanych.  

### <a name="next-steps"></a>Kolejne kroki
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Transakcje i blokady](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Zarządzanie danymi
  * [Tworzenie kopii zapasowej i przywracanie](service-fabric-reliable-services-backup-restore.md)
  * [Powiadomienia](service-fabric-reliable-services-notifications.md)
  * [Serializacja i uaktualnienia](service-fabric-application-upgrade-data-serialization.md)
  * [Elementy Reliable State Manager konfiguracji](service-fabric-reliable-services-configuration.md)
* Inne
  * [Reliable Services — szybki start](service-fabric-reliable-services-quick-start.md)
  * [Dokumentacja dla deweloperów dla elementów Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
