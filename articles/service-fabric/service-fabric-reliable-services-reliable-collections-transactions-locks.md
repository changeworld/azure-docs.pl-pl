---
title: Transakcje i tryby blokady w niezawodnych kolekcjach
description: Usługa Azure Service Fabric Reliable State Manager i niezawodne transakcje kolekcji i blokowanie.
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938909"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Tryby transakcji i blokad w niezawodnych kolekcjach sieci szkieletowej usług Azure

## <a name="transaction"></a>Transakcja

Transakcja jest sekwencją operacji wykonywanych jako pojedyncza logiczna jednostka pracy. Wykazuje wspólne [właściwości ACID](https://en.wikipedia.org/wiki/ACID) *(niepodzielność,* *spójność,* *izolacja,* *trwałość)* transakcji bazy danych:

* **Niepodzielność:** Transakcja musi być jednostką pracy atomowej. Innymi słowy, wszystkie jego modyfikacje danych są wykonywane lub żadna z nich nie jest wykonywana.
* **Spójność**: Po zakończeniu transakcji musi pozostawić wszystkie dane w spójnym stanie. Wszystkie wewnętrzne struktury danych muszą być poprawne na końcu transakcji.
* **Izolacja:** Modyfikacje dokonane przez równoczesnych transakcji muszą być odizolowane od modyfikacji dokonanych przez inne transakcje równoczesnych. Poziom izolacji używany dla operacji w ramach [ITransaction](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet) jest określana przez [IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet) wykonywania operacji.
* **Trwałość**: Po zakończeniu transakcji, jej skutki są trwale w systemie. Modyfikacje utrzymują się nawet w przypadku awarii systemu.

### <a name="isolation-levels"></a>Poziomy izolacji

Poziom izolacji określa stopień, w jakim transakcja musi być odizolowana od modyfikacji dokonanych przez inne transakcje.
Istnieją dwa poziomy izolacji, które są obsługiwane w niezawodne kolekcje:

* **Powtarzalny odczyt**: Określa, że instrukcje nie mogą odczytywać danych, które zostały zmodyfikowane, ale nie zostały jeszcze zatwierdzone przez inne transakcje i że żadne inne transakcje nie mogą modyfikować danych odczytanych przez bieżącą transakcję do czasu zakończenia bieżącej transakcji.
* **Migawka**: Określa, że dane odczytywane przez dowolną instrukcję w transakcji jest transakcyjnie spójną wersją danych, które istniały na początku transakcji.
  Transakcja może rozpoznać tylko modyfikacje danych, które zostały zatwierdzone przed rozpoczęciem transakcji.
  Modyfikacje danych wprowadzone przez inne transakcje po rozpoczęciu bieżącej transakcji nie są widoczne dla instrukcji wykonywanych w bieżącej transakcji.
  Efekt jest tak, jakby instrukcje w transakcji uzyskać migawkę danych zatwierdzonych, jak istniał na początku transakcji.
  Migawki są spójne w niezawodnych kolekcjach.

Niezawodne kolekcje automatycznie wybrać poziom izolacji do użycia dla danej operacji odczytu w zależności od operacji i roli repliki w momencie tworzenia transakcji.
Poniżej znajduje się tabela, która przedstawia domyślne poziomy izolacji dla operacji niezawodnego słownika i kolejki.

| Operacja \ Rola | Podstawowy | Pomocniczy |
| --- |:--- |:--- |
| Odczyt pojedynczego encji |Powtarzalny odczyt |Snapshot |
| Wyliczenie, Liczba |Snapshot |Snapshot |

> [!NOTE]
> Typowymi przykładami operacji `IReliableDictionary.TryGetValueAsync`pojedynczych jednostek są . `IReliableQueue.TryPeekAsync`
> 

Zarówno niezawodny słownik, jak i niezawodna kolejka obsługują *odczyt zapisów*.
Innymi słowy każdy zapis w ramach transakcji będą widoczne dla następującej odczytu, który należy do tej samej transakcji.

## <a name="locks"></a>Blokady

W niezawodnych kolekcjach wszystkie transakcje implementują rygorystyczne blokowanie dwufazowe: transakcja nie zwalnia blokad, które nabyła, dopóki transakcja nie zostanie zakończona z przerwaniem lub zatwierdzeniem.

Reliable Dictionary używa blokowania na poziomie wiersza dla wszystkich operacji pojedynczej jednostki.
Niezawodna kolejka handluje współbieżnością dla ścisłej transakcyjnej właściwości FIFO.
Niezawodna kolejka używa blokad na `TryPeekAsync` poziomie operacji, umożliwiając jedną transakcję z i/lub `TryDequeueAsync` i jedną transakcję `EnqueueAsync` naraz.
Należy zauważyć, że aby `TryPeekAsync` zachować FIFO, jeśli lub `TryDequeueAsync` kiedykolwiek zauważy, że niezawodna kolejka jest pusta, będą również blokować `EnqueueAsync`.

Operacje zapisu zawsze zajmują blokady exclusive.
W przypadku operacji odczytu blokada zależy od kilku czynników:

- Każda operacja odczytu wykonana przy użyciu izolacji migawki jest bez blokady.
- Każda operacja powtarzalny odczyt domyślnie wykonuje blokady udostępnione.
- Jednak dla każdej operacji odczytu, który obsługuje powtarzalne odczytu, użytkownik może poprosić o blokadę aktualizacji zamiast blokady udostępnionej.
Blokada aktualizacji jest blokada asymetryczna używana do zapobiegania typowej formie zakleszczenia, który występuje, gdy wiele transakcji zablokować zasoby dla potencjalnych aktualizacji w późniejszym czasie.

Macierz zgodności blokad można znaleźć w poniższej tabeli:

| Wniosek \ Przyznane | Brak | Udostępnione | Aktualizacja | Wyłączne |
| --- |:--- |:--- |:--- |:--- |
| Udostępnione |Brak konfliktu |Brak konfliktu |Konflikt |Konflikt |
| Aktualizacja |Brak konfliktu |Brak konfliktu |Konflikt |Konflikt |
| Wyłączne |Brak konfliktu |Konflikt |Konflikt |Konflikt |

Argument limitu czasu w interfejsach API niezawodnych kolekcji jest używany do wykrywania zakleszczenia.
Na przykład dwie transakcje (T1 i T2) próbują odczytać i zaktualizować K1.
Jest możliwe dla nich zakleszczenie, ponieważ oba kończą się o Shared lock.
W takim przypadku jeden lub oba operacje przesądzą limit czasu. I w tym scenariuszu blokada aktualizacji może zapobiec takiemu zakleszczenie.

## <a name="next-steps"></a>Następne kroki

* [Praca z elementami Reliable Collections](service-fabric-work-with-reliable-collections.md)
* [Powiadomienia o niezawodnych usługach](service-fabric-reliable-services-notifications.md)
* [Tworzenie kopii zapasowych i przywracanie niezawodnych usług (odzyskiwanie po awarii)](service-fabric-reliable-services-backup-restore.md)
* [Niezawodna konfiguracja menedżera stanu](service-fabric-reliable-services-configuration.md)
* [Informacje dla dewelopera dotyczące niezawodnych kolekcji](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
