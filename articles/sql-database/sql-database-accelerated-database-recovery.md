---
title: Przyspieszone odzyskiwanie bazy danych
description: Usługa Azure SQL Database ma nową funkcję, która zapewnia szybkie i spójne odzyskiwanie bazy danych, natychmiastowe wycofywanie transakcji i agresywne obcinanie dzienników dla pojedynczych baz danych i puli baz danych w bazie danych SQL Azure i baz danych w usłudze Azure SQL Data Magazynu.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 03/24/2020
ms.openlocfilehash: 57ca594dd067d15009de5e3abf7276fae48720d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238661"
---
# <a name="accelerated-database-recovery"></a>Przyspieszone odzyskiwanie bazy danych

**Przyspieszone odzyskiwanie bazy danych (ADR)** to funkcja aparatu bazy danych SQL, która znacznie poprawia dostępność bazy danych, szczególnie w obecności długotrwałych transakcji, przez przeprojektowanie procesu odzyskiwania aparatu bazy danych SQL. ADR jest obecnie dostępny dla usługi Azure SQL Database pojedynczej, puli elastycznej i wystąpienia zarządzanego oraz baz danych w usłudze Azure SQL Data Warehouse (obecnie w wersji zapoznawczej). Podstawowe zalety ADR to:

- **Szybkie i spójne odzyskiwanie bazy danych**

  W adr, długotrwałe transakcje nie mają wpływu na ogólny czas odzyskiwania, umożliwiając szybkie i spójne odzyskiwanie bazy danych, niezależnie od liczby aktywnych transakcji w systemie lub ich rozmiarów.

- **Natychmiastowe wycofywanie transakcji**

  W adr wycofywanie transakcji jest natychmiastowe, niezależnie od czasu, w której transakcja była aktywna lub liczby aktualizacji, które zostały wykonane.

- **Agresywne obcinanie dziennika**

  W przypadku ADR dziennik transakcji jest agresywnie obcinany, nawet w obecności aktywnych długotrwałych transakcji, co uniemożliwia wyrastanie spod kontroli.

## <a name="the-current-database-recovery-process"></a>Bieżący proces odzyskiwania bazy danych

Odzyskiwanie bazy danych w programie SQL Server jest zgodne z modelem odzyskiwania [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) i składa się z trzech faz, które są zilustrowane na poniższym diagramie i wyjaśnione bardziej szczegółowo po diagramie.

![bieżący proces odzyskiwania](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Faza analizy**

  Przesyłaj dalej skanowanie dziennika transakcji od początku ostatniego pomyślnego punktu kontrolnego (lub najstarszej brudnej strony LSN) do końca, aby określić stan każdej transakcji w momencie zatrzymania programu SQL Server.

- **Faza ponownego ponawiania**

  Prześlij dalej skanowanie dziennika transakcji z najstarszej niezakończonej transakcji do końca, aby doprowadzić bazę danych do stanu, w jaki był w momencie awarii, ponawiając wszystkie zatwierdzone operacje.

- **Faza cofania**

  Dla każdej transakcji, która była aktywna w momencie awarii, przechodzi do tyłu dziennika, cofając operacje, które tej transakcji wykonywane.

Na podstawie tego projektu czas potrzebny aparat bazy danych SQL do odzyskania po nieoczekiwanym ponownym uruchomieniu jest (w przybliżeniu) proporcjonalna do rozmiaru najdłuższej aktywnej transakcji w systemie w momencie awarii. Odzyskiwanie wymaga wycofania wszystkich niekompletnych transakcji. Wymagany czas jest proporcjonalny do pracy wykonanej przez transakcję i czasu, w której była aktywna. W związku z tym proces odzyskiwania programu SQL Server może zająć dużo czasu w obecności długotrwałych transakcji (takich jak duże operacje wstawiania zbiorczego lub operacje kompilacji indeksu względem dużej tabeli).

Ponadto anulowanie/wycofywanie dużej transakcji na podstawie tego projektu może również zająć dużo czasu, ponieważ używa tej samej fazy odzyskiwania Cofania, jak opisano powyżej.

Ponadto aparat bazy danych SQL nie można obciąć dziennika transakcji, gdy istnieją długotrwałe transakcje, ponieważ ich odpowiednie rekordy dziennika są potrzebne do odzyskiwania i wycofywania procesów. W wyniku tego projektu aparatu bazy danych SQL, niektórzy klienci używane do stawienia czoła problemowi, że rozmiar dziennika transakcji rośnie bardzo duży i zużywa ogromne ilości miejsca na dysku.

## <a name="the-accelerated-database-recovery-process"></a>Przyspieszony proces odzyskiwania bazy danych

ADR rozwiązuje powyższe problemy, całkowicie przeprojektując proces odzyskiwania aparatu bazy danych SQL, aby:

- Zrób to stały czas / natychmiastowy, unikając konieczności skanowania dziennika z /do początku najstarszej aktywnej transakcji. W przypadku adr dziennik transakcji jest przetwarzany tylko z ostatniego pomyślnego punktu kontrolnego (lub najstarszego numeru sekwencji dziennika dziennika (LSN)). W rezultacie czas odzyskiwania nie ma wpływu na długotrwałe transakcje.
- Zminimalizuj miejsce w dzienniku transakcji, ponieważ nie ma już potrzeby przetwarzania dziennika dla całej transakcji. W rezultacie dziennik transakcji może być obcinane agresywnie jako punkty kontrolne i kopie zapasowe występują.

Na wysokim poziomie ADR osiąga szybkie odzyskiwanie bazy danych przez przechowywanie wersji wszystkich modyfikacji fizycznej bazy danych i tylko cofanie operacji logicznych, które są ograniczone i mogą zostać cofnięte niemal natychmiast. Każda transakcja, która była aktywna w momencie awarii są oznaczone jako przerwane i w związku z tym wszystkie wersje generowane przez te transakcje mogą być ignorowane przez równoczesnych zapytań użytkowników.

Proces odzyskiwania ADR ma te same trzy fazy co bieżący proces odzyskiwania. Sposób działania tych faz z ADR przedstawiono na poniższym diagramie i wyjaśniono bardziej szczegółowo po diagramie.

![Proces odzyskiwania ADR](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Faza analizy**

  Proces pozostaje taki sam jak poprzednio z dodatkiem rekonstrukcji sLog i kopiowania rekordów dziennika dla operacji nie wersjonatowanych.
  
- **Faza ponownego ponawiania**

  Podzielone na dwie fazy (P)
  - Faza 1

      Wykonaj ponownie z sLog (najstarsza niezaangażowana transakcja do ostatniego punktu kontrolnego). Wykonaj ponownie jest szybka operacja, ponieważ musi tylko przetworzyć kilka rekordów z sLog.
      
  - Faza 2

     Wykonaj ponownie z dziennika transakcji rozpoczyna się od ostatniego punktu kontrolnego (zamiast najstarszej niezakończonej transakcji)
     
- **Faza cofania**

   Faza Cofania z ADR kończy się niemal natychmiast przy użyciu sLog do cofania operacji niewątrwalonych i magazynu utrwalonych wersji (PVS) z logicznym przywróceniem do wykonywania cofania na poziomie wiersza.

## <a name="adr-recovery-components"></a>Składniki odzyskiwania ADR

Cztery kluczowe składniki ADR to:

- **Magazyn wersji utrwalonych (PVS)**

  Magazyn wersji utrwalone jest nowy mechanizm aparatu bazy danych SQL do utrwalania `tempdb` wersji wiersza generowane w samej bazie danych zamiast magazynu wersji tradycyjnej. PVS umożliwia izolację zasobów, a także zwiększa dostępność czytelnych pomocniczych.

- **Logiczne przywrócenie**

  Logiczne przywrócenie jest procesem asynchronialnym odpowiedzialnym za wykonywanie operacji opartych na wersji na poziomie wiersza — zapewniając natychmiastowe wycofywanie transakcji i cofanie dla wszystkich operacji wersjonowanych. Logiczne przywrócenie odbywa się przez:

  - Śledzenie wszystkich przerwanych transakcji i oznaczanie ich niewidocznych dla innych transakcji. 
  - Wykonywanie wycofywania przy użyciu PVS dla wszystkich transakcji użytkownika, a nie fizycznie skanowanie dziennika transakcji i cofanie zmian po jednym na raz.
  - Zwalnianie wszystkich blokad natychmiast po przerwaniu transakcji. Ponieważ przerwa polega na po prostu oznaczanie zmian w pamięci, proces jest bardzo wydajny i dlatego blokady nie muszą być przechowywane przez długi czas.

- **Walić**

  sLog jest pomocniczy strumień dziennika w pamięci, który przechowuje rekordy dziennika dla operacji nie wersjonatowanych (takich jak unieważnienie pamięci podręcznej metadanych, nabycia blokady i tak dalej). SLog jest:

  - Niska głośność i pamięć
  - Utrwalone na dysku przez serializacji podczas procesu punktu kontrolnego
  - Okresowo skrakane jako zatwierdzanie transakcji
  - Przyspiesza ponawianie i cofanie, przetwarzając tylko operacje nieocenione  
  - Umożliwia obcięcie dziennika transakcji agresywnej przez zachowanie tylko wymaganych rekordów dziennika

- **Cleaner**

  Środek czyszczący jest procesem asynchronizacyjnym, który budzi się okresowo i czyści wersje stron, które nie są potrzebne.

## <a name="accelerated-database-recovery-patterns"></a>Przyspieszone wzorce odzyskiwania bazy danych

Z ADR najbardziej korzystają następujące typy obciążeń:

- Obciążenia z długotrwałymi transakcjami.
- Obciążenia, które widziały przypadki, w których aktywne transakcje powodują znaczny wzrost dziennika transakcji.  
- Obciążenia, które doświadczyły długich okresów niedostępności bazy danych z powodu długotrwałego odzyskiwania programu SQL Server (na przykład nieoczekiwanego ponownego uruchomienia programu SQL Server lub ręcznego wycofywania transakcji).

