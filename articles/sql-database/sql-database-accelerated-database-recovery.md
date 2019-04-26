---
title: Przyspieszone odzyskiwanie bazy danych — usługa Azure SQL Database | Dokumentacja firmy Microsoft
description: Usługi Azure SQL Database zawiera nową funkcję, która zapewnia szybki i spójny bazy danych odzyskiwania, cofnięcie transakcji natychmiastowe i obcinanie dziennika agresywne dla pojedynczych baz danych, bazy danych w puli usługi Azure SQL Database i baz danych w usłudze Azure SQL Data Magazyn.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: bb88da48f8961969176fd67bf6e5fa346655aeac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60388747"
---
# <a name="accelerated-database-recovery-preview"></a>Przyspieszone odzyskiwanie bazy danych (wersja zapoznawcza)

**Przyspieszone odzyskiwanie bazy danych (ADR)** nową funkcję do aparatu bazy danych SQL, która znacznie zwiększa dostępność bazy danych, szczególnie obecności długo działa transakcji, przeprojektowanie proces odzyskiwania aparatu bazy danych SQL. Reguły ADR jest obecnie dostępna dla pojedynczych baz danych i baz danych w puli usługi Azure SQL Database i baz danych w usłudze Azure SQL Data Warehouse. Najważniejsze korzyści reguły ADR są:

- **Odzyskiwanie bazy danych szybkie i spójne**

  Przy użyciu reguły ADR długotrwałej transakcji nie wpływa na ogólny czas odzyskiwania, włączenie odzyskiwania szybkie i spójne bazy danych niezależnie od tego, liczba aktywnych transakcji w systemie lub ich rozmiary.

- **Cofnięcie transakcji natychmiastowe**

  Przy użyciu reguły ADR natychmiastowe, niezależnie od tego, czy czas transakcji była aktywna lub liczbę aktualizacji, który wykonał jest cofnięcie transakcji.

- **Obcinanie dziennika agresywne**

  Przy użyciu reguły ADR dziennik transakcji jest agresywnie przycięty, nawet w obecności aktywne transakcje długo działające, co uniemożliwia nieograniczonemu z kontroli.

## <a name="the-current-database-recovery-process"></a>Bieżący proces odzyskiwania bazy danych

Odzyskiwanie bazy danych w programie SQL Server jest zgodna [ARIES](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) model odzyskiwania składa się z trzech etapów, które są przedstawione na poniższym diagramie i omówione bardziej szczegółowo, zgodnie z diagramem.

![bieżący proces odzyskiwania](./media/sql-database-accelerated-database-recovery/current-recovery-process.png)

- **Faza analizy**

  Skanowanie w poszukiwaniu dziennika transakcji w przód od początku ostatniego pomyślnego punktu kontrolnego (lub najstarsze strony zanieczyszczeniu numer LSN) aż do zakończenia, aby określić stan każdej transakcji w chwili zatrzymania serwera SQL.

- **Wykonaj ponownie fazy**

  Skanowanie w poszukiwaniu dziennika transakcji w przód od najstarszych transakcji niezatwierdzonych aż do zakończenia, aby przełączyć tę bazę danych do stanu, w jakim był w momencie pojawienia się awarii przez ponawianie wszystkie zatwierdzone operacje.

- **Cofnij fazy**

  W przypadku każdej transakcji, który był aktywny od chwili pojawienia się awarii przechodzi przez dziennik Wstecz, cofanie operacji wykonanych w tej transakcji.

Na podstawie tego projektu, czas, jaki zajmuje aparatu bazy danych SQL, aby odzyskać sprawność nieoczekiwane ponowne uruchomienie jest (w przybliżeniu) proporcjonalny do rozmiaru najdłuższy aktywnej transakcji w systemie w momencie pojawienia się awarii. Odzyskiwanie wymaga wycofanie wszystkich transakcji niekompletne. Czas wymagany jest proporcjonalna do pracy, który wykonał transakcji, a czas był aktywny. W związku z tym proces odzyskiwania programu SQL Server może długo trwać w obecności długotrwałej transakcji (takich jak duże zbiorczo wstawić operacji lub operacji kompilacji indeksu dla tabeli dużych).

Ponadto anulowanie/wycofywania dużych transakcji na podstawie tego projektu mogą także zająć dużo czasu, ponieważ korzysta ona z tego samego fazy cofania odzyskiwania zgodnie z powyższym opisem.

Ponadto, aparatu bazy danych SQL nie można obciąć dziennika transakcji, w przypadku długich uruchomionych transakcji, ponieważ ich odpowiednich rekordów dziennika są wymagane dla procesów odzyskiwania i wycofywania. W wyniku ten projekt aparatu bazy danych SQL niektórzy klienci stoją w obliczu problemów, że rozmiaru dziennika transakcji rośnie bardzo duży i zużywa ogromne ilości miejsca na dysku.

## <a name="the-accelerated-database-recovery-process"></a>Proces przyspieszone odzyskiwanie bazy danych

Reguły ADR rozwiązuje powyższe problemy, całkowicie przeprojektowanie procesu odzyskiwania aparatu bazy danych SQL do:

- Ułatwiają stałą czasu/natychmiastowe przez uniknięcie konieczności skanowania dziennika z/do stanu sprzed najstarsza aktywna transakcja. Przy użyciu reguły ADR dziennik transakcji jest przetwarzany tylko z ostatniego pomyślnego punktu kontrolnego (lub najstarsze strony zanieczyszczeniu numer sekwencyjny dziennika (LSN)). W rezultacie czas odzyskiwania nie ma wpływu długich uruchomione transakcje.
- Minimalizować ilość miejsca w dzienniku transakcji wymagane, ponieważ nie będzie już trzeba przetworzyć dziennika dla całego transakcji. W rezultacie dziennik transakcji może być obcinana agresywnie jako punkty kontrolne i kopie zapasowe są wykonywane.

Na wysokim poziomie ADR realizuje odzyskiwanie szybkie bazy danych przez przechowywanie wersji, wszystkie modyfikacje fizycznej bazy danych i tylko cofanie operacji logicznych, które są ograniczone i może być cofnięte niemal natychmiast. Każdej transakcji, który był aktywny od czasu awarii są oznaczone jako zostało przerwane, i w związku z tym, można zignorować wszelkie wersje generowanych przez te transakcje w zapytaniach jednoczesnych użytkowników.

Proces odzyskiwania ADR ma ten sam trzy fazy jako bieżący proces odzyskiwania. Sposób działania tych faz przy użyciu reguły ADR zilustrowane na poniższym diagramie i opisano szczegółowo następujące na diagramie.

![Proces odzyskiwania reguły ADR](./media/sql-database-accelerated-database-recovery/adr-recovery-process.png)

- **Faza analizy**

  Proces pozostaje taka sama jak dzisiaj dodając Rekonstruowanie sLog oraz kopiowania rekordów dziennika operacji bez numerów wersji.
  
- **Wykonaj ponownie** fazy

  Podzielony na dwie fazy (P)
  - Faza 1

      Wykonaj ponownie z sLog (najstarsze niezatwierdzonych transakcji do ostatniego punktu kontrolnego). Powtórz to szybka operacja, gdy tylko musi przetworzyć kilka rekordów z sLog.
      
  - Faza 2

     Powtórz z dziennika transakcji, który rozpoczyna się od ostatniego punktu kontrolnego (zamiast najstarsze niezatwierdzonych transakcji)
     
- **Cofnij fazy**

   Fazy cofania przy użyciu reguły ADR niemal natychmiast kończy się za pomocą sLog do cofania operacji niewersjonowanych i utrwalone wersji Store (Odwiedziny) przy użyciu logicznych Przywróć do wykonania wiersza poziomu na podstawie wersji cofania.

## <a name="adr-recovery-components"></a>Składniki recovery reguły ADR

Czterech kluczowych składników ADR są:

- **Utrwalonych wersji Store (Odwiedziny)**

  Magazyn wersji utrwalonych jest nowy mechanizm aparatu bazy danych SQL na potrzeby utrwalania wersji wierszy w bazie danych sam zamiast tradycyjnych generowane `tempdb` magazynu wersji. Odwiedziny umożliwia izolację zasobów oraz zwiększa dostępność do odczytu pomocniczych baz danych.

- **Logiczne przywrócić**

  Przywróć logiczne jest odpowiedzialny za wykonywanie Cofnij wersja oparta na poziomie wiersza — podając wycofywania transakcji błyskawiczne i cofania dla wszystkich operacji wersjonowany procesu asynchronicznego.

  - Przechowuje informacje o wszystkich przerwane transakcje
  - Wykonuje wycofywania przy użyciu Odwiedziny dla wszystkich transakcji użytkownika
  - Przerwij wszystkie blokady natychmiast po transakcji wydań

- **sLog**

  sLog jest strumień dodatkowej dziennika w pamięci, że magazyny rejestrowania rekordów niewersjonowanych operacje (takie jak unieważniania pamięci podręcznej metadanych, pozyskanie blokady i tak dalej). SLog jest:

  - Niskim poziomie i w pamięci
  - Utrzymywana na dysku przez serializowanego w procesie punktu kontrolnego
  - Okresowo są obcinane jako zatwierdzenie transakcji
  - Przyspiesza wykonaj ponownie i cofnąć tylko niewersjonowanych operacji przetwarzania  
  - Włącza obcinanie dziennika transakcji agresywne przy zachowaniu tylko rekordy dziennika wymagane

- **Czyszczenia**

  Oczyszczarkę jest proces asynchroniczny, który budzi okresowo i czyści wersje stron, które nie są wymagane.

## <a name="who-should-consider-accelerated-database-recovery"></a>Kto powinien rozważyć przyspieszone odzyskiwanie bazy danych

Następujące typy klientów, należy rozważyć włączenie ADR:

- Klienci, którzy mają obciążeń przy użyciu długie uruchomione transakcje.
- Klienci, którzy przejrzane przypadki, w którym aktywne transakcje powodują dziennika transakcji i znacznie wzrośnie.  
- Klienci, którzy wystąpił długie okresy niedostępność bazy danych z powodu programu SQL Server, długie wykonywanie odzyskiwania (np. nieoczekiwany programu SQL Server lub ręczne ponowne uruchomienie wycofywania transakcji).

## <a name="to-enable-adr-during-this-preview-period"></a>Aby włączyć reguły ADR w tym okresie (wersja zapoznawcza)

W okresie wersji zapoznawczej dla tej funkcji, Wyślij wiadomość e-mail do [ adr@microsoft.com ](mailto:adr@microsoft.com) Aby dowiedzieć się więcej, a następnie spróbuj się odzyskiwanie bazy danych Accelerated (ADR). W wiadomości e-mail na przykład nazwa serwera usługi SQL Database (dla pojedynczych baz danych i baz danych w puli w bazie danych SQL i baz danych w usłudze Azure Data Warehouse). Ponieważ jest to funkcja w wersji zapoznawczej, to serwer testowy powinien być serwerem nieprodukcyjnych.
