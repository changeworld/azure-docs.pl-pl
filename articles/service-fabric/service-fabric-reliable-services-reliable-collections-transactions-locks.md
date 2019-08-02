---
title: Transakcje i tryby blokowania w usłudze Azure Service Fabric niezawodne Kolekcje | Microsoft Docs
description: Usługa Azure Service Fabric niezawodny Menedżer stanu i niezawodne transakcje kolekcji oraz blokowanie.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: atsenthi
ms.openlocfilehash: 8e77e488a3c0a40a714a0e8efffba0a2947454bf
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599318"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Transakcje i tryby blokowania w usłudze Azure Service Fabric niezawodne Kolekcje

## <a name="transaction"></a>Transakcji
Transakcja to sekwencja operacji wykonywanych w ramach pojedynczej jednostki logicznej pracy.
Transakcja musi mieć następujące właściwości KWASów. wyświetlania https://technet.microsoft.com/library/ms190612)
* Niepodzielność: Transakcja musi być niepodzielną jednostką pracy. Innymi słowy, wszystkie modyfikacje danych są wykonywane lub żadne z nich nie są wykonywane.
* **Spójność**: Po zakończeniu transakcji należy pozostawić wszystkie dane w spójnym stanie. Wszystkie wewnętrzne struktury danych muszą być poprawne na końcu transakcji.
* **Izolacja**: Modyfikacje dokonane przez współbieżne transakcje muszą być odizolowane od zmian wprowadzonych przez inne współbieżne transakcje. Poziom izolacji użyty dla operacji w ramach metody ITransaction jest określany przez IReliableState wykonujący operację.
* **Trwałość**: Po zakończeniu transakcji jego skutki są stale stosowane w systemie. Modyfikacje są zachowywane nawet w przypadku awarii systemu.

### <a name="isolation-levels"></a>Poziomy izolacji
Poziom izolacji definiuje stopień odizolowania transakcji od modyfikacji dokonanych przez inne transakcje.
Istnieją dwa poziomy izolacji, które są obsługiwane w niezawodnych kolekcjach:

* **Powtórzony odczyt**: Określa, że instrukcje nie mogą odczytywać danych, które zostały zmodyfikowane, ale jeszcze nie zostały zatwierdzone przez inne transakcje i że żadne inne transakcje nie mogą modyfikować danych odczytanych przez bieżącą transakcję do momentu zakończenia bieżącej transakcji. Aby uzyskać więcej informacji, [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx)Zobacz.
* **Migawka**: Określa, że dane odczytane przez żadną instrukcję w transakcji są spójną transakcyjnie wersją danych, które istniały na początku transakcji.
  Transakcja może rozpoznać tylko modyfikacje danych, które zostały zatwierdzone przed rozpoczęciem transakcji.
  Modyfikacje danych wprowadzone przez inne transakcje po rozpoczęciu bieżącej transakcji nie są widoczne dla instrukcji wykonywanych w bieżącej transakcji.
  Jest to efekt, tak jakby instrukcje w transakcji uzyskują migawkę zatwierdzonych danych, które istniały na początku transakcji.
  Migawki są spójne dla niezawodnych kolekcji.
  Aby uzyskać więcej informacji, [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx)Zobacz.

Niezawodne kolekcje automatycznie wybierają poziom izolacji, który ma być używany dla danej operacji odczytu, w zależności od operacji i roli repliki w momencie tworzenia transakcji.
Poniżej znajduje się tabela, która przedstawia ustawienia domyślne na poziomie izolacji dla niezawodnych operacji słownika i kolejki.

| Operacja \ rola | Podstawowy | Pomocnicza |
| --- |:--- |:--- |
| Odczyt pojedynczej jednostki |Odczyt powtarzalny |Snapshot |
| Wyliczenie, liczba |Snapshot |Snapshot |

> [!NOTE]
> Typowe przykłady operacji pojedynczych jednostek to `IReliableDictionary.TryGetValueAsync`,. `IReliableQueue.TryPeekAsync`
> 

Zarówno niezawodny słownik, jak i niezawodna Kolejka obsługują odczytywanie zapisów.
Innymi słowy, każdy zapis w ramach transakcji będzie widoczny dla następującego odczytu, który należy do tej samej transakcji.

## <a name="locks"></a>Blokady
W niezawodnych kolekcjach wszystkie transakcje implementują rygorystyczne dwie fazy blokowania: transakcja nie zwalnia blokad, które uzyskały do momentu zakończenia transakcji z przerwaniem lub zatwierdzeniem.

Niezawodny słownik używa blokowania na poziomie wiersza dla wszystkich operacji pojedynczych jednostek.
Niezawodna wymiana transakcji w ramach współbieżności dla rygorystycznej transakcyjnej właściwości FIFO.
Niezawodna Kolejka używa blokad poziomu operacji, `TryPeekAsync` umożliwiając jedną transakcję z i/ `EnqueueAsync` lub `TryDequeueAsync` i jedną transakcję jednocześnie.
Należy pamiętać, że w celu zachowania FIFO `TryPeekAsync` , `TryDequeueAsync` Jeśli lub kiedykolwiek obserwuje, że niezawodna kolejka jest pusta `EnqueueAsync`, również zostanie zablokowana.

Operacje zapisu zawsze pobierają blokady na wyłączność.
W przypadku operacji odczytu blokowanie zależy od kilku czynników.
Wszystkie operacje odczytu wykonane przy użyciu izolacji migawki są wolne od blokady.
Wszystkie powtarzalne operacje odczytu domyślnie pobierają blokady udostępnione.
Jednak dla każdej operacji odczytu, która obsługuje powtarzanie odczytu, użytkownik może poprosił o blokadę aktualizacji zamiast blokady udostępnionej.
Blokada aktualizacji jest asymetryczną blokadą używaną do zapobiegania wspólnej formie zakleszczenia, która występuje, gdy wiele transakcji blokuje zasoby na potrzeby potencjalnych aktualizacji w późniejszym czasie.

Macierz zgodności blokad można znaleźć w poniższej tabeli:

| Żądanie \ przyznane | Brak | Udostępnione | Aktualizacja | Klucz |
| --- |:--- |:--- |:--- |:--- |
| Udostępnione |Brak konfliktu |Brak konfliktu |Konflikt |Konflikt |
| Aktualizacja |Brak konfliktu |Brak konfliktu |Konflikt |Konflikt |
| Klucz |Brak konfliktu |Konflikt |Konflikt |Konflikt |

Argument limitu czasu w interfejsach API niezawodnych kolekcji służy do wykrywania zakleszczenia.
Na przykład dwie transakcje (T1 i T2) próbują odczytywać i aktualizować K1.
Istnieje możliwość zakleszczenia, ponieważ oba te elementy kończą się z blokadą udostępnioną.
W takim przypadku jedna lub obie operacje przekroczą limit czasu.

Ten scenariusz zakleszczenia to doskonały przykład, jak blokada aktualizacji może uniemożliwić zakleszczenie.

## <a name="next-steps"></a>Następne kroki
* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Powiadomienia Reliable Services](service-fabric-reliable-services-notifications.md)
* [Reliable Services kopii zapasowej i przywracania (odzyskiwanie po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Konfiguracja niezawodnego menedżera stanu](service-fabric-reliable-services-configuration.md)
* [Dokumentacja dla deweloperów dla niezawodnych kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

