---
title: Szybsze odzyskiwanie bazy danych — Azure SQL Database
description: Azure SQL Database ma nową funkcję, która zapewnia szybkie i spójne odzyskiwanie bazy danych, natychmiastowe wycofywanie transakcji oraz agresywne obcinanie dzienników dla pojedynczych baz danych i baz danych w puli w Azure SQL Database i bazach danych w usłudze Azure SQL Data Hurtowni.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: e66b3e6563d796cc7b59e82233bd1b22bc906c6e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691356"
---
# <a name="accelerated-database-recovery"></a>Szybsze odzyskiwanie bazy danych

**Szybsze odzyskiwanie bazy danych (ADR)** to nowa funkcja aparatu bazy danych SQL, która znacznie zwiększa dostępność bazy danych, szczególnie w przypadku długotrwałych transakcji, przez przeprojektowanie procesu odzyskiwania aparatu bazy danych SQL. Reguły ADR są obecnie dostępne dla pojedynczych baz danych i baz danych w puli w Azure SQL Database, a bazy danych w Azure SQL Data Warehouse (obecnie w publicznej wersji zapoznawczej). Podstawowe korzyści wynikające z ADR są następujące:

- **Szybkie i spójne odzyskiwanie bazy danych**

  W przypadku opcji ADR długotrwałe transakcje nie wpływają na ogólny czas odzyskiwania, umożliwiając szybkie i spójne odzyskiwanie bazy danych niezależnie od liczby aktywnych transakcji w systemie lub ich rozmiarów.

- **Natychmiastowe wycofywanie transakcji**

  Za pomocą reguły ADR wycofywanie transakcji jest chwilowo, niezależnie od czasu aktywności transakcji lub liczby wykonanych aktualizacji.

- **Agresywne obcinanie dziennika**

  W przypadku korzystania z reguły ADR dziennik transakcji jest agresywnie obcinany, nawet w obecności aktywnych transakcji długotrwałych, które uniemożliwiają przeprowadzenie kontroli.

## <a name="the-current-database-recovery-process"></a>Bieżący proces odzyskiwania bazy danych

Odzyskiwanie bazy danych w SQL Server jest zgodne z modelem odzyskiwania [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) i składa się z trzech faz, które przedstawiono na poniższym diagramie i wyjaśniono bardziej szczegółowo po diagramie.

![bieżący proces odzyskiwania](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Faza analizy**

  Przeprowadź skanowanie dziennika transakcji od początku ostatniego pomyślnego punktu kontrolnego (lub najstarszej wersji LSN strony) aż do zakończenia, aby określić stan każdej transakcji w chwili SQL Server zatrzymania.

- **Wykonaj ponownie fazę**

  Przeprowadź skanowanie dziennika transakcji od najstarszej niezatwierdzonej transakcji do końca, aby przenieść bazę danych do stanu, który był w chwili awarii, wykonując ponownie wszystkie operacje zatwierdzone.

- **Faza cofania**

  W przypadku każdej transakcji, która była aktywna jako czas awarii, przejdzie do tyłu dziennika, cofając operacje wykonywane przez tę transakcję.

W oparciu o ten projekt czas odzyskiwania aparatu bazy danych SQL po nieoczekiwanym ponownym uruchomieniu jest (w przybliżeniu) proporcjonalny do rozmiaru najdłuższej aktywnej transakcji w systemie w chwili awarii. Odzyskiwanie wymaga wycofania wszystkich nieukończonych transakcji. Czas wymagany jest proporcjonalny do pracy wykonanej przez transakcję i czasu, gdy był aktywny. W związku z tym proces odzyskiwania SQL Server może zająć dużo czasu w obecności długotrwałych transakcji (takich jak duże operacje wstawiania zbiorczego lub operacje kompilowania indeksów względem dużej tabeli).

Ponadto anulowanie i wycofywanie dużej transakcji na podstawie tego projektu może być również czasochłonne, ponieważ używa tej samej fazy odzyskiwania cofania, jak opisano powyżej.

Ponadto aparat bazy danych SQL nie może obciąć dziennika transakcji, jeśli istnieją długotrwałe transakcje, ponieważ odpowiednie rekordy dzienników są zbędne dla procesów odzyskiwania i wycofywania. W wyniku tego projektu aparatu bazy danych SQL niektórzy klienci napotykają problem polegający na tym, że rozmiar dziennika transakcji jest zbyt duży i zużywa ogromną ilość miejsca na dysku.

## <a name="the-accelerated-database-recovery-process"></a>Proces odzyskiwania przyspieszonej bazy danych

ADR eliminuje powyższe problemy poprzez całkowite przeprojektowanie procesu odzyskiwania aparatu bazy danych SQL do:

- Zwiększ czas/natychmiast, unikając konieczności skanowania dziennika z/do początku najstarszej aktywnej transakcji. Za pomocą reguły ADR dziennik transakcji jest przetwarzany tylko z ostatniego pomyślnego punktu kontrolnego (lub najstarszego nieprawidłowego numeru sekwencyjnego dziennika strony (LSN)). W związku z tym czas odzyskiwania nie ma wpływu na długotrwałe transakcje.
- Zminimalizuj wymaganą ilość miejsca w dzienniku transakcji, ponieważ nie ma już potrzeby przetwarzania dziennika dla całej transakcji. W związku z tym dziennik transakcji można skrócić w sposób agresywny jako punkty kontrolne i kopie zapasowe.

Na wysokim poziomie funkcja ADR umożliwia szybkie odzyskiwanie bazy danych przez przechowywanie wersji wszystkich modyfikacji fizycznych baz danych i wykonywanie tylko operacji logicznych, które są ograniczone i mogą być cofnięte niemal natychmiastowo. Wszystkie transakcje, które były aktywne jako czas awarii, są oznaczane jako przerwane i w związku z tym wszystkie wersje wygenerowane przez te transakcje mogą być ignorowane przez współbieżne zapytania użytkownika.

Proces odzyskiwania ADR ma te same trzy fazy jak bieżący proces odzyskiwania. Sposób działania tych faz z użyciem reguły ADR przedstawiono na poniższym diagramie i wyjaśniono bardziej szczegółowo po diagramie.

![Proces odzyskiwania ADR](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Faza analizy**

  Proces pozostaje taki sam, jak dzisiaj z dodaniem rekonstrukcji sLog i skopiowaniem rekordów dziennika dla operacji niezwiązanych z wersjami.
  
- **Wykonaj ponownie** fazę

  Podzielone na dwie fazy (P)
  - Faza 1

      Wykonaj ponownie z sLog (najstarszej niezatwierdzonej transakcji do ostatniego punktu kontrolnego). Wykonaj ponownie szybką operację, ponieważ wymaga tylko przetworzenia kilku rekordów z sLog.
      
  - Faza 2

     Ponowne wykonywanie z dziennika transakcji zaczyna się od ostatniego punktu kontrolnego (zamiast najstarszej niezatwierdzonej transakcji)
     
- **Faza cofania**

   Faza cofania z ADR kończy niemal natychmiastowo za pomocą sLog do cofania operacji niezwiązanych z wersjami i trwałego magazynu wersji (PVS) z przywróceniem logicznym do wykonywania operacji cofania opartej na poziomie wiersza.

## <a name="adr-recovery-components"></a>Składniki odzyskiwania ADR

Cztery kluczowe składniki reguły ADR są następujące:

- **Magazyn trwałych wersji (PVS)**

  Utrwalony magazyn wersji to nowy mechanizm aparatu usługi SQL Database służący do utrwalania wersji wierszy generowanych w bazie danych, a nie w tradycyjnych `tempdb` wersji. PVS umożliwia izolację zasobów, a także zwiększa dostępność czytelnych serwerów pomocniczych.

- **Przywrócenie logiczne**

  Przywrócenie logiczne to proces asynchroniczny odpowiedzialny za wykonywanie operacji cofania opartego na poziomie wiersza, która umożliwia wycofywanie transakcji i cofanie dla wszystkich operacji związanych z wersjami.

  - Śledzi wszystkie przerwane transakcje
  - Wykonuje wycofywanie przy użyciu PVS dla wszystkich transakcji użytkownika
  - Zwalnia wszystkie blokady natychmiast po przerwaniu transakcji

- **sLog**

  sLog to pomocniczy strumień dziennika w pamięci, który przechowuje rekordy dziennika dla operacji niezwiązanych z wersjami (takich jak unieważnienie pamięci podręcznej metadanych, blokady pozyskiwania itd.). SLog:

  - Niski wolumin i w pamięci
  - Utrwalone na dysku przez jego serializację podczas procesu Checkpoint
  - Okresowo obcinaj jako zatwierdzenie transakcji
  - Przyspiesza ponowne wykonywanie i cofanie przez przetwarzanie tylko operacji niezwiązanych z wersjami  
  - Umożliwia agresywne obcinanie dziennika transakcji przez zachowanie tylko wymaganych rekordów dziennika

- **Kontur**

  Oczyszczarka to proces asynchroniczny, który okresowo wznawia i czyści niewymagane wersje stron.

## <a name="who-should-consider-accelerated-database-recovery"></a>Kto powinien rozważyć przyspieszone odzyskiwanie bazy danych

Następujące typy klientów powinny rozważyć włączenie reguły ADR:

- Klienci, którzy mają obciążenia z długotrwałymi transakcjami.
- Klienci, którzy widziją przypadki, w których aktywne transakcje powodują znaczne zwiększenie rozmiaru dziennika transakcji.  
- Klienci, którzy mieli długotrwałe okresy niedostępności bazy danych z powodu SQL Server długotrwałego odzyskiwania (na przykład nieoczekiwanego SQL Server ponownego uruchomienia lub ręcznego wycofania transakcji).

