---
title: Transakcje i tryby blokowania na platformie Azure usługi Service Fabric elementy Reliable Collections | Dokumentacja firmy Microsoft
description: Usługi Azure Service Fabric Reliable Menedżer stanu i elementów Reliable Collections transakcji i blokowania.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: aljo
ms.openlocfilehash: 9785a09a3ac3e119507b4ac28075d887c7edc619
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774067"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transakcje i blokady tryby w elementach Reliable Collections usługi Azure Service Fabric

## <a name="transaction"></a>Transakcja
Transakcja jest sekwencją operacje wykonywane jako pojedyncza jednostka logiczna pracy.
Transakcji musi mieć następujące właściwości ACID. (zobacz: https://technet.microsoft.com/library/ms190612)
* **Niepodzielność**: Transakcji musi być pojedynczej Atomowej jednostki pracy. Innymi słowy wszystkie zmiany danych są wykonywane albo żadna z nich jest wykonywane.
* **Spójność**: Po zakończeniu transakcji, należy pozostawić wszystkie dane w spójnym stanie. Wszystkich wewnętrznych struktur danych musi być poprawna, na końcu transakcji.
* **Izolacja**: Zmiany dokonane przez jednoczesnych transakcji musi być odizolowany od zmiany dokonane przez innych równoczesnych transakcji. Poziom izolacji, używana na potrzeby operacji w ramach ITransaction jest określany przez IReliableState wykonującego operację.
* **Trwałość**: Po zakończeniu transakcji jej efekty są trwałe w miejscu w systemie. Modyfikacje są zachowywane nawet w przypadku awarii systemu.

### <a name="isolation-levels"></a>Poziomy izolacji
Poziom izolacji definiuje żądany stopień, w którym transakcji muszą zostać odizolowane od zmian wprowadzonych przez inne transakcje.
Istnieją dwa poziomy izolacji, które są obsługiwane w elementach Reliable Collections:

* **Odczyt powtarzalny**: Określa, że instrukcji nie można odczytać danych, które zostały zmodyfikowane, ale nie zostały jeszcze zatwierdzone przez inne transakcje oraz że żadne inne transakcje może zmodyfikować dane odczytane przez bieżącą transakcję, aż do zakończenia bieżącej transakcji. Aby uzyskać więcej informacji, zobacz [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).
* **Migawka**: Określa, że dane odczytywane w programie żadnej instrukcji w transakcji jest transakcyjnie spójne wersję danych, które istniały na początku transakcji.
  Transakcja może rozpoznawać tylko modyfikacji danych, które zostały zatwierdzone przed rozpoczęciem transakcji.
  Modyfikacje danych wprowadzonych przez inne transakcje po rozpoczęciu bieżącej transakcji nie są widoczne dla instrukcje wykonywania w bieżącej transakcji.
  Efekt jest tak, jakby instrukcji w ramach transakcji migawki danych zatwierdzone znajdowały się na początku transakcji.
  Migawki są spójne we wszystkich kolekcjach niezawodne.
  Aby uzyskać więcej informacji, zobacz [ https://msdn.microsoft.com/library/ms173763.aspx ](https://msdn.microsoft.com/library/ms173763.aspx).

Elementy Reliable Collections automatycznie wybiera poziom izolacji dla danej operacji odczytu, w zależności od operacji i Rola repliki w czasie tworzenia transakcji.
Poniżej przedstawiono tabeli, który przedstawia wartości domyślnych poziomu izolacji dla operacji niezawodnego słownika i kolejki.

| Operacja \ roli | Podstawowy | Pomocniczy |
| --- |:--- |:--- |
| Jeden podmiot Odczyt |Odczyt powtarzalny |Snapshot |
| Wyliczenie, liczba |Snapshot |Snapshot |

> [!NOTE]
> Typowe przykłady dla pojedynczej operacji jednostki to `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.
> 

Zarówno w niezawodnym słowniku, jak i niezawodna kolejka obsługuje Your zapisuje odczytu.
Innymi słowy dowolnej zapisu w obrębie transakcji będą widoczne dla następujących odczytu, który należy do tej samej transakcji.

## <a name="locks"></a>Blokady
W elementach Reliable Collections wszystkie transakcje Implementowanie rygorystyczne dwie fazy blokowania: transakcji nie spowoduje zwolnienia blokady uzyskała aż do zakończenia transakcji przy użyciu przerwania lub zatwierdzenia.

Niezawodnego słownika używa blokowania dla wszystkich operacji w pojedynczą jednostkę na poziomie wiersza.
Niezawodna kolejka zamienia wyłączanie współbieżności dla strict właściwości FIFO transakcyjnych.
Niezawodna kolejka używa operacji blokadami poziomu umożliwiające utworzenie jednej transakcji `TryPeekAsync` i/lub `TryDequeueAsync` i transakcja o `EnqueueAsync` w danym momencie.
Pamiętaj, że aby zachować FIFO, jeśli `TryPeekAsync` lub `TryDequeueAsync` nigdy nie przestrzega, że niezawodna kolejka jest pusta, również spowoduje to zablokowanie `EnqueueAsync`.

Zapis operacji zawsze pobierają blokady na wyłączność.
Dla operacji odczytu blokowania zależy od kilku czynników.
Wszystkie operacje odczytu wykonywane przy użyciu izolacji migawki jest wolne od blokady.
Każdej operacji Odczyt powtarzalny domyślnie przyjmuje współdzielona blokad.
Jednak dla każdej operacji odczytu, która obsługuje Odczyt powtarzalny, użytkownika można uzyskać blokady aktualizacji zamiast blokady udostępnione.
Blokada aktualizacji to asymetryczny blokady używany w celu zapobiegania wspólnej formy zakleszczenia, który występuje, gdy wiele transakcji blokowania zasobów potencjalnych aktualizacji w późniejszym czasie.

Macierz zgodności blokady na ten temat można znaleźć w poniższej tabeli:

| Żądanie \ przyznane | Brak | Udostępniona | Aktualizacja | Wyłączne |
| --- |:--- |:--- |:--- |:--- |
| Udostępniona |Nie konfliktów |Nie konfliktów |Konflikt |Konflikt |
| Aktualizacja |Nie konfliktów |Nie konfliktów |Konflikt |Konflikt |
| Wyłączne |Nie konfliktów |Konflikt |Konflikt |Konflikt |

Argument limitu czasu w niezawodnej kolekcji interfejsów API jest używane do wykrywania zakleszczeń.
Na przykład dwie transakcje (T1 i T2) próbuje odczytywać i aktualizować K1.
Jest możliwość ich zakleszczenie, ponieważ oba znajdą się o blokady udostępnione.
W tym przypadku co najmniej jeden z operacji przekroczy limit czasu.

W tym scenariuszu zakleszczeń jest doskonałym przykładem jak zapobiec zakleszczenia blokady aktualizacji.

## <a name="next-steps"></a>Kolejne kroki
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Niezawodne usługi powiadomień](service-fabric-reliable-services-notifications.md)
* [Niezawodne usługi Kopia zapasowa i przywracanie (odzyskiwanie po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Elementy Reliable State Manager konfiguracji](service-fabric-reliable-services-configuration.md)
* [Dokumentacja dla deweloperów dla elementów Reliable Collections](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

