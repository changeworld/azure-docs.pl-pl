---
title: Zalecenia dotyczące wydajności — baza danych SQL Azure | Dokumentacja firmy Microsoft
description: Baza danych SQL Azure zawiera zalecenia dotyczące Twojej bazy danych SQL, które może poprawić wydajność kwerend bieżącej.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: b88b8a313a03ef2ad72aeb053b89505201c23819
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="performance-recommendations-for-sql-database"></a>Zalecenia dotyczące wydajności bazy danych SQL

Baza danych SQL Azure uczy się i dostosowuje z aplikacją. Zapewnia on dostosowane zalecenia, które umożliwiają zmaksymalizować wydajność bazy danych SQL. Baza danych SQL stale ocenia i analizuje historię użycia bazy danych SQL. Zaleceń, które są dostarczane są oparte na wzorce obciążeń unikatowy bazy danych i zwiększyć wydajność.

> [!TIP]
> [Automatycznego dostrajania](sql-database-automatic-tuning.md) jest to zalecana metoda dostrajania wydajności. [Inteligentnego Insights](sql-database-intelligent-insights.md) jest to zalecana metoda do monitorowania wydajności. 
>

## <a name="create-index-recommendations"></a>Utwórz zalecenia dotyczące indeksu
Baza danych SQL stale monitoruje zapytań, które są uruchomione i identyfikuje indeksów, które może poprawić wydajność. Po za mało pewność, że niektóre indeks jest Brak, nowy **Utwórz indeks** zalecenie jest tworzony.

 Baza danych SQL Azure tworzy zaufania szacowaną bardziej wydajne, który indeks doprowadzi do czasu. W zależności od szacunkowego wzrostu wydajności zalecenia są sklasyfikowane jako wysoki, średni lub niski. 

Indeksy, które są tworzone za pomocą zalecenia są zawsze oznaczonej jako indeksy utworzone automatycznie. Widać, które indeksy są tworzone automatycznie analizując sys.indexes widoku. Automatycznie utworzone indeksy nie blokować polecenia ALTER/Zmień nazwę. 

Jeśli spróbujesz porzucić tę kolumnę, która ma indeks utworzony automatycznie nad nim przekazuje polecenia. Indeks utworzony automatycznie zostało porzucone z poleceniem również. Zwykłych indeksów bloku polecenia ALTER/Zmień nazwę kolumny, które są indeksowane.

Po zastosowaniu zalecenie dotyczące indeksu tworzenie bazy danych SQL Azure porównuje wydajność kwerend z linię bazową wydajności. Jeśli nowego indeksu wyższą wydajność, zalecane jest oznaczony jako pomyślnie, a raport wpływ jest dostępny. Jeśli indeks nie zwiększyć wydajność, automatycznie został przywrócony. Baza danych SQL używa tego procesu, aby upewnić się, że zalecenia dotyczące poprawy wydajności bazy danych.

Wszelkie **Utwórz indeks** zalecenie ma zasady wycofania, która nie zezwala na stosowanie zalecenie, jeśli jest wysokie obciążenie zasobów bazy danych lub puli. Zasady wycofania uwzględnia konta procesora CPU, dane we/wy dziennika we/wy i dostępnej pamięci. 

Jeśli procesor CPU, we/wy danych lub dziennika we/wy jest wyższa niż 80% w ciągu poprzednich 30 minut, zostanie wstrzymane Utwórz zalecenie dotyczące indeksu. Jeśli dostępny magazyn będzie poniżej 10% po utworzeniu indeksu, zalecenie przechodzi w stan błędu. Jeśli po paru dniach, automatycznego dostrajania nadal uznaje, że indeks jest korzystne, ponownie uruchamiania procesu. 

Ten proces jest powtarzany do momentu jest za mało dostępnego magazynu, aby utworzyć indeks, lub indeks nie jest już widoczne jako odsetek.

## <a name="drop-index-recommendations"></a>Zalecenia dotyczące usuwania indeksów
Oprócz wykrywania brakujące indeksy, baza danych SQL stale analizuje wydajność istniejące indeksy. Jeśli indeks nie jest używany, baza danych SQL Azure zaleca porzuceniem jej. W obu przypadkach zaleca się porzucenie indeksu:
* Indeks jest duplikat nazwy innego indeksu (sama indeksowane i zawiera kolumny, schemat partycji i filtrów).
* Indeks nie został użyty przez dłuższy okres (93 dni).

Zalecenia dotyczące usuwania indeksów także przejść do weryfikacji po implementacji. Jeśli zwiększa wydajność, raport wpływ jest dostępny. Jeśli powoduje spadek wydajności, zalecenie zostanie przywrócony.


## <a name="parameterize-queries-recommendations"></a>Parametryzacja zapytań zalecenia
*Parametryzacja zapytań* zalecenia są wyświetlane, jeśli masz co najmniej jednego zapytania, które są stale jest ponownie kompilowana ale zakończenia się z tego samego planu wykonania zapytania. Ten warunek tworzy możliwość zastosowania parametryzacja wymuszone. Parametryzacja wymuszone z kolei umożliwia plany zapytań w pamięci podręcznej i używana w przyszłości, co zwiększa wydajność i zmniejszyć obciążenie zasobów. 

Każdego zapytania, które jest początkowo wystawiony na podstawie programu SQL Server musi być skompilowany do wygenerowania planu wykonywania. Każdy plan wygenerowany jest dodawana do pamięci podręcznej planu. Podczas kolejnych wykonań kodu tego samego zapytania można ponownie użyć tego planu z pamięci podręcznej, która eliminuje potrzebę stosowania dodatkowych kompilacji. 

Zapytania z wartości zdefiniowanych parametrów może spowodować zmniejszenie wydajności, ponieważ plan wykonania jest ponownie kompilowana każdym razem, gdy wartości bez parametrów są różne. W wielu przypadkach tego samego zapytania z innym parametrem wartości Generowanie tego samego planu wykonywania. Te plany jednak nadal oddzielnie są dodawane do pamięci podręcznej planu. 

Proces ponownego kompilowania planów wykonywania używa bazy danych zasobów, zwiększa zapytanie czas trwania i przepełnienie pamięci podręcznej planu. Te zdarzenia z kolei powodować planuje można wykluczyć z pamięci podręcznej. Może się zmienić to zachowanie programu SQL Server przez ustawienie opcji parametryzacja wymuszone w bazie danych. 

Ułatwić oszacowanie wpływu tego zalecenia, można uzyskać z porównanie rzeczywistego użycia procesora CPU i projektowane użycie Procesora (tak, jakby były stosowane zalecenie). To zalecenie ułatwia uzyskanie użycie Procesora CPU. Może również pomóc skrócić czas trwania kwerendy i obciążenie dla pamięci podręcznej planu, co oznacza więcej planów może pozostać w pamięci podręcznej i można użyć ponownie. Zastosowanie tego zalecenia szybko wybierając **Zastosuj** polecenia. 

Po zastosowaniu tego zalecenia umożliwia parametryzacja wymuszone w ciągu minut w Twojej bazie danych. Rozpoczyna się proces monitorowania trwa około 24 godzin. Po tym okresie można wyświetlić raport weryfikacji. Ten raport przedstawia użycie procesora CPU bazy danych, 24 godzin przed i po zastosowaniu zalecenia. Doradca bazy danych programu SQL ma mechanizm bezpieczeństwa, który automatycznie przywrócona zalecenie zastosowane, jeśli wykryto regresji wydajności.

## <a name="fix-schema-issues-recommendations-preview"></a>Usuń zalecenia dotyczące problemów schematu (wersja zapoznawcza)

> [!IMPORTANT]
> Microsoft obecnie wycofano "Napraw problem schematu" zalecenia. Firma Microsoft zaleca użycie [Insights inteligentnego](sql-database-intelligent-insights.md) do monitorowania z bazy danych problemy z wydajnością, m.in. problemów schematu, które poprzednio objęte zalecenia "Napraw problem schematu".
> 

**Rozwiązywanie problemów schematu** zalecenia są wyświetlane po usługi SQL Database powiadomienia anomalii w liczbie błędów związanych z schematu SQL, w których są wykonywane na bazie danych SQL. To zalecenie jest zwykle wyświetlany, gdy bazy danych wystąpi wiele błędów związanych z schematu (Nieprawidłowa nazwa kolumny, nieprawidłowa nazwa obiektu itd.) w ciągu godziny.

"Problemy z schematu" jest klasą błędy składniowe w programie SQL Server. Występują, gdy nie są wyrównane definicji zapytania SQL, jak i definicja schematu bazy danych. Na przykład jedna z kolumn, które jest oczekiwane przez zapytanie może brakować w tabeli docelowej lub na odwrót. 

"Napraw problem schematu" zalecenie jest wyświetlany, gdy usługi Azure SQL Database powiadomienia anomalii w liczbie błędów związanych z schematu SQL, w których są wykonywane na bazie danych SQL. W poniższej tabeli przedstawiono błędów, które dotyczą problemów schematu:

| Kod błędu SQL | Komunikat |
| --- | --- |
| 201 |Procedura lub funkcja "*"oczekuje parametru"*", który nie został dostarczony. |
| 207 |Nieprawidłowa nazwa kolumny ' *'. |
| 208 |Nieprawidłowa nazwa obiektu "*". |
| 213 |Nazwa kolumny lub liczba podanych wartości nie odpowiada definicji tabeli. |
| 2812 |Nie można odnaleźć procedury składowanej "*". |
| 8144 |Procedura lub funkcja * ma określono zbyt wiele argumentów. |

## <a name="next-steps"></a>Kolejne kroki
Monitorowanie zalecenia i zastosować je do ograniczania wydajności w dalszym ciągu. Obciążeń bazy danych są dynamiczne i zmienianie w sposób ciągły. Doradca bazy danych programu SQL kontynuuje monitorowanie i podano zalecenia, które może potencjalnie podnieść wydajność bazy danych. 

* Aby uzyskać więcej informacji na temat automatycznego dostrajania indeksy bazy danych i planów wykonywania zapytania, zobacz [automatycznego dostrajania bazy danych SQL Azure](sql-database-automatic-tuning.md).
* Aby uzyskać więcej informacji na temat automatycznie monitorowanie wydajności bazy danych z automatycznych Diagnostyka i analiza głównych przyczyn problemów z wydajnością, zobacz [Insights inteligentnego SQL Azure](sql-database-intelligent-insights.md).
*  Aby uzyskać więcej informacji o sposobie używania zalecenia dotyczące wydajności w portalu Azure, zobacz [zalecenia dotyczące wydajności w portalu Azure](sql-database-advisor-portal.md).
* Zobacz [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) poznać i wyświetlić jego wpływ na wydajność kwerend top.


