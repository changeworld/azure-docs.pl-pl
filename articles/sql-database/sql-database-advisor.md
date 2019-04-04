---
title: Zalecenia dotyczące wydajności — usługi Azure SQL Database | Dokumentacja firmy Microsoft
description: Usługa Azure SQL Database zawiera zalecenia dotyczące baz danych SQL, które może poprawić wydajność zapytań bieżącego.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: d09adbfa7cb2782d710ef3116cbd7bc68ee247b7
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903323"
---
# <a name="performance-recommendations-for-sql-database"></a>Zalecenia dotyczące wydajności usługi SQL Database

Usługa Azure SQL Database uczy się i dostosowuje się ze swoją aplikacją. Zapewnia dostosowane zalecenia, które pozwalają zmaksymalizować wydajność bazy danych SQL. SQL Database stale ocenia i analizuje historię użycia bazy danych SQL. Zalecenia, które są dostarczane są oparte na wzorcach obciążenia bazy danych jest unikatowa i zwiększyć wydajność.

> [!TIP]
> [Automatyczne dostrajanie](sql-database-automatic-tuning.md) jest zalecaną metodą automatyczne dostosowywanie niektóre typowe problemy z wydajnością bazy danych. [Szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) jest zalecaną metodą podstawową wydajność usługi Azure SQL Database, na potrzeby w zakresie monitorowania. [Usługa Azure SQL Analytics](../azure-monitor/insights/azure-sql.md) jest zalecaną metodą zaawansowane monitorowanie wydajności bazy danych na dużą skalę, dzięki wbudowanym funkcjom analizy dla Rozwiązywanie problemów z wydajnością automatycznych.
>

## <a name="create-index-recommendations"></a>Utwórz zalecenia dotyczące indeksu
SQL Database stale monitoruje zapytań, które są uruchomione i identyfikuje indeksy, które może zwiększyć wydajność. Po za mało pewność, że niektóre indeksu brakuje, nowy **Utwórz indeks** zalecenie jest tworzone.

 Usługa Azure SQL Database tworzy ufności poprzez oszacowanie przyrost wydajności, które niosą indeksu w czasie. W zależności od szacowane są bardziej wydajne zalecenia są podzielone na wysoki, średni lub niski. 

Indeksy, które są tworzone za pomocą zalecenia zawsze są oznaczane jako indeksy utworzony automatycznie. Możesz zobaczyć, której indeksy są tworzone automatycznie, analizując widoku sys.indexes. Utworzony automatycznie indeksów nie blokują polecenia ALTER/zmiany nazwy. 

Jeśli zostanie podjęta próba porzucić tę kolumnę, która ma indeks utworzony automatycznie nad nim, przekazuje polecenia. Indeks utworzony automatycznie zostało porzucone za pomocą polecenia, a także. Zwykłych indeksów blokować polecenia ALTER/zmiany nazwy kolumn, które są indeksowane.

Po zastosowaniu zalecenie dotyczące indeksu Tworzenie usługi Azure SQL Database porównuje wydajność zapytań o punkt odniesienia wydajności. Jeśli nowy indeks zwiększona wydajność, zalecenie jest oznaczony jako pomyślne i jest dostępny raport wpływ. Jeśli indeks nie poprawić wydajność, automatycznie przywrócono. Aby upewnić się, że zalecenia dotyczące poprawy wydajności bazy danych SQL Database przy użyciu tego procesu.

Wszelkie **Utwórz indeks** zalecenie obejmuje zasadę wycofywania, która nie zezwala na stosowanie zalecenia, jeśli użycie zasobów bazy danych lub puli jest wysokie. Zasady wycofywania bierze pod uwagę procesora CPU, we/wy danych, we/wy dziennika i dostępna pamięć. 

Jeśli procesor CPU, we/wy danych lub we/wy dziennika jest wyższa niż 80% w poprzednim 30 minut, zalecenie dotyczące indeksu Utwórz zostanie wstrzymane. Jeśli dostępna pamięć będzie poniżej 10% po utworzeniu indeksu, zalecenie przechodzi w stan błędu. Jeśli po paru dniach, dostrajania automatycznego nadal uważa, że indeksu będzie korzystne, proces jest uruchamiany ponownie. 

Ten proces jest powtarzany, dopóki jest wystarczająco dużo dostępnego magazynu, aby utworzyć indeks lub dopóki indeks nie jest już widoczna jako korzystne.

## <a name="drop-index-recommendations"></a>Upuść zalecenia dotyczące indeksu
Oprócz wykrywania brakujące indeksy, bazy danych SQL w sposób ciągły analizuje wydajność istniejące indeksy. Jeśli indeks nie jest używany, usługi Azure SQL Database zaleca porzuceniem jej. Zaleca się usunięcie indeksu, w dwóch przypadkach:
* Indeks jest duplikatem innego indeksu (takie same indeksowane i uwzględnione kolumny, schemat partycji i filtrów).
* Indeks nie został użyty przez dłuższy okres (w dniach 93).

Zalecenia dotyczące indeksu listy także przejść za pośrednictwem weryfikacji po implementacji. Jeśli zwiększa wydajność, raport wpływ jest dostępny. W przypadku spadku wydajności, zalecenie zostało wycofane.


## <a name="parameterize-queries-recommendations"></a>Parametryzacja zapytań zalecenia
*Sparametryzuj zapytania* zalecenia są wyświetlane, gdy masz jeden lub więcej zapytań, które są stale są ponownie kompilowane, ale zakończenia się przy użyciu tego samego planu wykonania zapytania. Ten warunek tworzy możliwość zastosowanie wymuszona parametryzacji. Wymuszone parametryzacji z kolei umożliwia planów zapytań do pamięci podręcznej i ponowne użycie w przyszłości, który zapewnia lepszą wydajność i zmniejsza obciążenie zasobów. 

Każdego zapytania, które jest początkowo wystawiony na podstawie programu SQL Server musi zostać skompilowana do wygenerowania planu wykonywania. Każdy plan wygenerowany jest dodawana do pamięci podręcznej planu. Kolejne wykonania tego samego zapytania można ponownie użyć tego planu z pamięci podręcznej, która eliminuje potrzebę stosowania dodatkowych kompilacji. 

Zapytania z wartości zdefiniowanych może prowadzić do zmniejszenie wydajności, ponieważ plan wykonania są ponownie kompilowane, każdym razem, gdy zdefiniowanych wartości są różne. W wielu przypadkach tego samego zapytania z wartościami różnych parametrów wygenerować tej samej plany wykonywania. Te plany jednak nadal oddzielnie zostaną dodane do pamięci podręcznej planu. 

Proces ponownej kompilacji plany wykonywania korzysta z zasobów bazy danych, zwiększa czasu trwania zapytania i przepełnienie pamięci podręcznej planu. Te zdarzenia z kolei powodować plany można wykluczyć z pamięci podręcznej. To zachowanie programu SQL Server może być zmienione przez ustawienie opcji wymuszonego parametryzacji w bazie danych. 

Aby ułatwić szacunkowa ocena wpływu tego zalecenia, możesz są dostarczane z porównanie rzeczywiste użycie procesora CPU i przewidywane użycie procesora CPU (tak, jakby zalecenia zostały zastosowane). To zalecenie ułatwia uzyskanie oszczędności użycia Procesora. Może również pomóc skrócić czas trwania zapytania i obciążenie dla pamięci podręcznej planu, co oznacza więcej planów mogą pozostawać w pamięci podręcznej i można użyć ponownie. Można zastosować zalecenie to szybkie, wybierając **Zastosuj** polecenia. 

Po zastosowaniu tej rekomendacji umożliwia wymuszenie parametryzacji w ciągu kilku minut w bazie danych. Uruchamia proces monitorowania trwa około 24 godziny. Po upływie tego czasu możesz zobaczyć raport weryfikacji. Ten raport przedstawia użycie procesora CPU bazy danych, 24 godziny przed i po zastosowaniu rekomendacji. SQL Database Advisor zawiera mechanizm bezpieczeństwa, który zostanie automatycznie przywrócona zastosowane zalecenie Jeśli wykryto regresji wydajności.

## <a name="fix-schema-issues-recommendations-preview"></a>Usuń schemat problemów zaleceń (wersja zapoznawcza)

> [!IMPORTANT]
> Obecnie wycofano "Naprawić problem ze schematem" zalecenia firmy Microsoft. Firma Microsoft zaleca użycie [Intelligent Insights](sql-database-intelligent-insights.md) do monitorowania usługi bazy danych problemy z wydajnością, problemy ze schematem, w których zalecenia "Naprawić problem ze schematem" wcześniej w tym.
> 

**Napraw problemy ze schematem** zalecenia są wyświetlane, gdy usługa SQL Database uwagi dotyczące anomalii w liczbę błędów dotyczące schematu SQL, w które są wykonywane w bazie danych SQL. To zalecenie jest zwykle wyświetlany, gdy bazy danych wystąpi wiele błędów związanych z schematu (Nieprawidłowa nazwa kolumny, nieprawidłowa nazwa obiektu itd.) w ciągu godziny.

"Problemy ze schematem" jest klasą błędy składniowe w programie SQL Server. Mogą wystąpić, gdy definicja zapytania SQL, jak i definicja schematu bazy danych nie są wyrównane. Na przykład jedna z kolumn, których oczekuje się przez zapytanie może brakować w tabeli docelowej lub na odwrót. 

Zalecenie "Naprawić problem ze schematem" jest wyświetlany, gdy usługa Azure SQL Database uwagi dotyczące anomalii w liczbę błędów dotyczące schematu SQL, w które są wykonywane w bazie danych SQL. W poniższej tabeli przedstawiono błędów, które są powiązane z problemy ze schematem:

| Kod błędu SQL | Komunikat |
| --- | --- |
| 201 |Procedura lub funkcja "*"oczekuje parametru"*", który nie został dostarczony. |
| 207 |Nieprawidłowa nazwa kolumny "*". |
| 208 |Nieprawidłowa nazwa obiektu "*". |
| 213 |Nazwa kolumny lub liczba podanych wartości nie jest zgodna definicję tabeli. |
| 2812 |Nie można odnaleźć procedury składowanej "*". |
| 8144 |Procedura lub funkcja * ma określono zbyt wiele argumentów. |

## <a name="custom-applications"></a>Aplikacje niestandardowe

Deweloperzy mogą należy wziąć pod uwagę tworzenia niestandardowych aplikacji przy użyciu zalecenia dotyczące wydajności usługi Azure SQL Database. Wszystkie zalecenia na liście w portalu dla bazy danych jest możliwy za pośrednictwem [Get AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) interfejsu API.

## <a name="next-steps"></a>Kolejne kroki
Monitoruj zalecenia i stosować je do uściślić wydajności w dalszym ciągu. Różnych obciążeń bazy danych są dynamiczne i zmiany w sposób ciągły. SQL Database Advisor w dalszym ciągu monitorować i przedstawić zalecenia w zakresie, które może potencjalnie podnieść wydajność bazy danych. 

* Aby uzyskać więcej informacji na temat dostrajania automatycznego indeksy bazy danych i plany wykonywania zapytań, zobacz [dostrajania automatycznego usługi Azure SQL Database](sql-database-automatic-tuning.md).
* Aby uzyskać więcej informacji na temat Automatyczne monitorowanie wydajności bazy danych za pomocą automatycznych diagnostyki i analizy głównych przyczyn problemów z wydajnością, zobacz [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
*  Aby uzyskać więcej informacji o sposobie używania zalecenia dotyczące wydajności w witrynie Azure portal, zobacz [zalecenia dotyczące wydajności w witrynie Azure portal](sql-database-advisor-portal.md).
* Zobacz [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) poznać i wyświetlić wpływ na wydajność najczęściej używane zapytania.


