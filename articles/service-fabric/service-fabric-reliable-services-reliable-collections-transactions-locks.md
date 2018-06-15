---
title: Transakcje i tryby blokady na platformie Azure usługi sieci szkieletowej niezawodnej kolekcje | Dokumentacja firmy Microsoft
description: Menedżer niezawodnej stanu sieci szkieletowej usługi Azure i transakcji niezawodnej kolekcje i blokowania.
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: 79be861a70abb0331d971b00e753691e77642637
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207370"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transakcje i tryby blokady w kolekcjach niezawodnej sieci szkieletowej usług Azure

## <a name="transaction"></a>Transakcji
Sekwencja operacji wykonywanych jako pojedyncza jednostka logiczna pracy jest transakcja.
Transakcji musi mieć następujące właściwości ACID. (zobacz: https://technet.microsoft.com/library/ms190612)
* **Niepodzielność**: transakcji musi być Atomowej jednostki pracy. Innymi słowy wszystkie zmiany danych są wykonywane albo żadna z nich jest wykonywana.
* **Spójność**: po zakończeniu transakcji musi pozostać wszystkie dane w spójnym stanie. Wszystkie struktur danych wewnętrznych musi być prawidłowy na końcu transakcji.
* **Izolacja**: zmiany dokonane przez równoczesnych transakcji musi być odizolowany od zmiany dokonane przez równoczesnych transakcji. Poziom izolacji używany podczas operacji w ramach ITransaction jest określana przez IReliableState wykonywania operacji.
* **Trwałość**: po zakończeniu transakcji jego skutków obowiązują trwale w systemie. Modyfikacje utrwalić nawet w przypadku awarii systemu.

### <a name="isolation-levels"></a>Poziom izolacji
Poziom izolacji definiuje stopień, do którego transakcja musi być odizolowany od zmiany dokonane przez inne transakcje.
Istnieją dwa poziomy izolacji, które są obsługiwane w kolekcjach niezawodnej:

* **Odczyt powtarzalny**: Określa, czy instrukcje nie można odczytać danych, które zostały zmodyfikowane, ale nie zostały jeszcze zatwierdzone przez inne transakcje i że nie inne transakcje nie mogą zmodyfikować danych został odczytany przez bieżącą transakcję do bieżącej transakcji Zakończenie. Aby uzyskać więcej informacji, zobacz [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).
* **Migawki**: Określa, że dane odczytane przez żadnej instrukcji w transakcji jest spójna transakcyjnie wersji danych, które istniały na początku transakcji.
  Transakcja może rozpoznawać tylko zmiany danych, które zostały zatwierdzone przed rozpoczęciem transakcji.
  Dane zmiany wprowadzone przez inne transakcje po rozpoczęciu bieżącej transakcji nie są widoczne dla instrukcji wykonywania w bieżącej transakcji.
  Efekt jest tak, jakby instrukcje w transakcji migawki danych zatwierdzone znajdowały się na początku transakcji.
  Migawki są spójne w kolekcjach wiarygodne.
  Aby uzyskać więcej informacji, zobacz [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).

Kolekcje niezawodnej automatycznie wybrać poziom izolacji dla danej operacji odczytu, w zależności od operacji i Rola repliki w czasie tworzenia transakcji.
Poniżej znajduje się tabela, która przedstawia wartości domyślnych poziomu izolacji operacje słownika niezawodnych i kolejki.

| Operacja \ roli | Podstawowy | Pomocniczy |
| --- |:--- |:--- |
| Odczyt pojedynczej jednostki |Powtarzalnego odczytu |Snapshot |
| Wyliczenie, liczba |Snapshot |Snapshot |

> [!NOTE]
> Typowe przykłady dla jednej operacji jednostki `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

Słownik niezawodnej oraz niezawodnej kolejki obsługuje zapisuje swój odczytu.
Innymi słowy wszelkie zapisu w obrębie transakcji będą widoczne dla następujących odczytu, który należy do tej samej transakcji.

## <a name="locks"></a>Blokady
W kolekcjach niezawodnej wszystkie transakcje wdrożenie rygorystyczne dwie fazy blokowania: transakcji nie zwalnia blokad ustawił aż do zakończenia transakcji z przerwania lub zatwierdzenia.

Słownik niezawodnej używa blokowania dla wszystkich operacji pojedynczy element na poziomie wiersza.
Kolejka niezawodnych zamienia poza współbieżności dla właściwości FIFO transakcyjnej strict.
Kolejka niezawodnych używa operacji blokad poziomu umożliwiające utworzenie jednej transakcji `TryPeekAsync` i/lub `TryDequeueAsync` i transakcja o `EnqueueAsync` naraz.
Pamiętaj, że aby zachować FIFO, jeśli `TryPeekAsync` lub `TryDequeueAsync` kiedykolwiek przestrzega, że kolejka niezawodnych jest pusta, również spowoduje to zablokowanie `EnqueueAsync`.

Zawsze mieć blokady na wyłączność operacje zapisu.
Dla operacji odczytu blokowania zależy od kilku czynników.
Żadnej operacji odczytu wykonywane przy użyciu izolacji migawki jest nieblokującą.
Wszelkie operacje odczytu powtarzalne domyślnie przyjmuje współużytkowane blokady.
Jednak do żadnej operacji odczytu obsługującego powtarzalnego odczytu użytkownika można uzyskać blokady aktualizacji zamiast blokady współużytkowane.
Blokady aktualizacji jest asymetrycznego blokady zapobiegające wspólnej formy zakleszczenia, gdy wiele transakcji blokowania zasobów potencjalnych aktualizacji w późniejszym czasie.

Macierz zgodności blokad można znaleźć w poniższej tabeli:

| Żądanie \ przyznane | Brak | Udostępniona | Aktualizacja | Wyłączności |
| --- |:--- |:--- |:--- |:--- |
| Udostępniona |Nie konfliktów |Nie konfliktów |Konflikt |Konflikt |
| Aktualizacja |Nie konfliktów |Nie konfliktów |Konflikt |Konflikt |
| Wyłączności |Nie konfliktów |Konflikt |Konflikt |Konflikt |

Argument limitu czasu w niezawodnej interfejsów API kolekcji jest używana do wykrywania zakleszczeń.
Na przykład dwie transakcje (T1 a T2) próbuje odczytywanie i aktualizowanie K1.
Możliwe jest ich do zakleszczenia, ponieważ oba mających blokady udostępnione.
W takim przypadku limit czasu będzie jedną lub obie czynności.

W tym scenariuszu zakleszczenie jest doskonałym przykładem jak blokady aktualizacji można zapobiec zakleszczenia.

## <a name="next-steps"></a>Kolejne kroki
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Niezawodne usługi powiadomień](service-fabric-reliable-services-notifications.md)
* [Niezawodne usługi Kopia zapasowa i przywracanie (odzyskiwania po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Stan niezawodny Menedżera konfiguracji](service-fabric-reliable-services-configuration.md)
* [Dokumentacja dla deweloperów niezawodnej kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

