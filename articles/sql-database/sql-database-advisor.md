---
title: Zalecenia dotyczące wydajności doradcy bazy danych dla pojedynczych i łączonych baz danych
description: Usługa Azure SQL Database zawiera zalecenia dotyczące pojedynczych i puli baz danych, które mogą zwiększyć wydajność zapytań w bazie danych SQL platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: bd7473813722fd413947535413b98d493058634a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214134"
---
# <a name="database-advisor-performance-recommendations-for-single-and-pooled-databases"></a>Zalecenia dotyczące wydajności klasyfikatora bazy danych dla pojedynczych i pulowych baz danych

Usługa Azure SQL Database uczy się i dostosowuje się do aplikacji. W przypadku pojedynczych i pulchnych baz danych baza danych SQL ma szereg doradców bazy danych, które zawierają niestandardowe zalecenia, które umożliwiają maksymalizację wydajności. Te doradcy bazy danych stale ocenić i analizować historię użycia i dostarczyć zalecenia na podstawie wzorców obciążenia, które pomagają zwiększyć wydajność.

## <a name="performance-overview"></a>Omówienie wydajności

Omówienie wydajności zawiera podsumowanie wydajności bazy danych i pomaga w dostrajaniu wydajności i rozwiązywaniu problemów.

![Omówienie wydajności bazy danych SQL usługi Azure](./media/sql-database-performance/performance-overview-annotated.png)

- **Kafelek Zalecenia** zawiera podział zaleceń dostrajania dla bazy danych (trzy pierwsze zalecenia są wyświetlane, jeśli istnieje więcej). Kliknięcie tego kafelka powoduje przejście do **[opcji rekomendacji wydajności](sql-database-advisor-portal.md#viewing-recommendations)**.
- Kafelek **Działania dostrajania** zawiera podsumowanie trwających i zakończonych akcji dostrajania bazy danych, co umożliwia szybki wgląd w historię aktywności dostrajania. Kliknięcie tego kafelka spowoduje przejście do pełnego widoku historii strojenia bazy danych.
- Kafelek **Automatycznego dostrajania** pokazuje **[konfigurację automatycznego dostrajania](sql-database-automatic-tuning-enable.md)** bazy danych (opcje strojenia, które są automatycznie stosowane do bazy danych). Kliknięcie tego kafelka powoduje otwarcie okna dialogowego konfiguracji automatyzacji.
- Kafelek **kwerend bazy danych** zawiera podsumowanie wydajności kwerendy dla bazy danych (ogólne użycie DTU i najlepsze kwerendy zużywające zasoby). Kliknięcie tego kafelka powoduje przejście do **[query performance insight](sql-database-query-performance.md)**.

## <a name="performance-recommendation-options"></a>Opcje rekomendacji wydajności

Opcje rekomendacji wydajności dostępne dla pojedynczych i puli baz danych w usłudze Azure SQL Database to:

| Zalecenie dotyczące wydajności | Obsługa pojedynczej bazy danych i puli baz danych | Obsługa bazy danych wystąpień |
| :----------------------------- | ----- | ----- |
| **Tworzenie rekomendacji indeksu** — zaleca tworzenie indeksów, które mogą zwiększyć wydajność obciążenia. | Tak | Nie |
| **Zalecenia indeksu upuszczenia** — zaleca usuwanie indeksów nadmiarowych i zduplikowanych codziennie, z wyjątkiem unikatowych indeksów i indeksów, które nie były używane przez długi czas (>90 dni). Należy pamiętać, że ta opcja nie jest zgodna z aplikacjami korzystającymi z przełączania partycji i wskazówek dotyczących indeksu. Upuszczanie nieużywanych indeksów nie jest obsługiwane w warstwach usług Premium i Business Critical. | Tak | Nie |
| **Parametryzacja zapytań zalecenia (wersja zapoznawcza)** — zaleca wymuszone parametryzację w przypadkach, gdy masz jeden lub więcej zapytań, które są stale ponownie kompilowane, ale kończy się z tym samym planem wykonywania kwerendy. | Tak | Nie |
| **Rozwiązywanie problemów schematu zalecenia (wersja zapoznawcza)** — zalecenia dotyczące korekcji schematu pojawiają się, gdy usługa bazy danych SQL powiadamia o anomalii w liczbie błędów SQL związanych ze schematem, które występują w bazie danych SQL. Firma Microsoft obecnie przestarzałe "Fix schema problem" zalecenia. | Tak | Nie |

![Zalecenia dotyczące wydajności dla bazy danych SQL usługi Azure](./media/sql-database-performance/performance-recommendations-annotated.png)

Aby zastosować zalecenia dotyczące wydajności, zobacz [stosowanie zaleceń](sql-database-advisor-portal.md#applying-recommendations). Aby wyświetlić stan zaleceń, zobacz [Monitorowanie operacji](sql-database-advisor-portal.md#monitoring-operations).

Można również znaleźć pełną historię działań dostrajania, które zostały zastosowane w przeszłości.

## <a name="create-index-recommendations"></a>Tworzenie rekomendacji indeksu

Baza danych SQL stale monitoruje kwerendy, które są uruchomione i identyfikuje indeksy, które mogą zwiększyć wydajność. Po wystarczającej pewności, że brakuje określonego indeksu, zostanie utworzona nowa rekomendacja **create indeksu.**

Usługa Azure SQL Database buduje zaufanie, szacując przyrost wydajności indeksu przyniesie w czasie. W zależności od szacowanego przyrostu wydajności zalecenia są klasyfikowane jako wysokie, średnie lub niskie.

Indeksy, które są tworzone przy użyciu zaleceń są zawsze oflagowane jako indeksy utworzone automatycznie. Możesz zobaczyć, które indeksy są tworzone automatycznie, patrząc na [widok sys.indexes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql). Automatycznie utworzone indeksy nie blokują poleceń ALTER/RENAME.

Jeśli spróbujesz upuścić kolumnę, która ma automatycznie utworzony indeks nad nim, polecenie przechodzi. Automatycznie utworzony indeks jest również odrzucany za pomocą polecenia. Indeksy regularne blokują polecenie ALTER/RENAME w kolumnach, które są indeksowane.

Po zastosowaniu zalecenia indeksu tworzenia usługa Azure SQL Database porównuje wydajność zapytań z wydajnością planu bazowego. Jeśli nowy indeks poprawił wydajność, zalecenie zostanie oznaczone jako pomyślne i dostępny jest raport o wpływie. Jeśli indeks nie poprawił wydajności, zostanie automatycznie przywrócony. Baza danych SQL używa tego procesu, aby upewnić się, że zalecenia zwiększają wydajność bazy danych.

Każde zalecenie **tworzenia indeksu** ma zasady wycofywania, które nie zezwalają na stosowanie zalecenia, jeśli użycie zasobów bazy danych lub puli jest wysoka. Zasady wycofywania uwzględnia procesor CPU, we/wy danych, we/wy dziennika i dostępnej pamięci masowej.

Jeśli procesor CPU, data We/Wo lub log We/Wo jest wyższa niż 80% w ciągu ostatnich 30 minut, zalecenie create indeksu jest odroczone. Jeśli dostępny magazyn będzie poniżej 10% po utworzeniu indeksu, zalecenie przechodzi w stan błędu. Jeśli po kilku dniach automatyczne dostrajanie nadal uważa, że indeks byłby korzystny, proces rozpoczyna się ponownie.

Ten proces powtarza się, dopóki nie będzie wystarczającej ilości dostępnego magazynu, aby utworzyć indeks lub dopóki indeks nie jest już postrzegany jako korzystny.

## <a name="drop-index-recommendations"></a>Rekomendacje indeksu upuszczania

Oprócz wykrywania brakujących indeksów baza danych SQL stale analizuje wydajność istniejących indeksów. Jeśli indeks nie jest używany, usługa Azure SQL Database zaleca jego upuszczanie. Upuszczanie indeksu jest zalecane w dwóch przypadkach:

- Indeks jest duplikatem innego indeksu (tej samej indeksowanej i dołączonej kolumny, schematu partycji i filtrów).
- Indeks nie był używany przez dłuższy okres (93 dni).

Zalecenia indeksu upuszczenia również przejść przez weryfikacji po wdrożeniu. Jeśli wydajność się poprawi, raport o wpływie jest dostępny. Jeśli wydajność spada, zalecenie zostanie przywrócone.

## <a name="parameterize-queries-recommendations-preview"></a>Parametryzowanie zaleceń dotyczących kwerend (wersja zapoznawcza)

*Rekomendacje parametryzacji kwerend* są wyświetlane, gdy masz co najmniej jedno zapytanie, które są stale ponownie kompilowane, ale kończą się tym samym planem wykonywania kwerend. Ten warunek tworzy możliwość zastosowania wymuszonej parametryzacji. Wymuszona parametryzacja z kolei umożliwia plany kwerend, które mają być buforowane i ponownie w przyszłości, co zwiększa wydajność i zmniejsza użycie zasobów.

Każda kwerenda, która jest wystawiona dla PROGRAMU SQL Server początkowo musi być skompilowany do generowania planu wykonania. Każdy wygenerowany plan jest dodawany do pamięci podręcznej planu. Kolejne wykonania tej samej kwerendy można ponownie użyć tego planu z pamięci podręcznej, co eliminuje potrzebę dodatkowej kompilacji.

Kwerendy z wartościami niesparmetryzowanymi mogą prowadzić do obciążenia wydajności, ponieważ plan wykonania jest ponownie kompilowany za każdym razem, gdy wartości niesparmetryzowane są różne. W wielu przypadkach te same zapytania z różnymi wartościami parametrów generują te same plany wykonywania. Plany te są jednak nadal oddzielnie dodawane do pamięci podręcznej planu.

Proces ponownego komppilowania planów wykonania używa zasobów bazy danych, zwiększa czas trwania kwerendy i przepełnia pamięć podręczną planu. Te zdarzenia, z kolei, spowodować plany mają być eksmitowane z pamięci podręcznej. To zachowanie programu SQL Server można zmienić, ustawiając opcję wymuszonej parametryzacji w bazie danych.

Aby ułatwić oszacowanie wpływu tego zalecenia, są dostarczane z porównaniem rzeczywistego użycia procesora CPU i przewidywane użycie procesora CPU (tak, jakby zalecenie zostało zastosowane). To zalecenie może pomóc w uzyskaniu oszczędności procesora. Może również pomóc zmniejszyć czas trwania kwerendy i obciążenie dla pamięci podręcznej planu, co oznacza, że więcej planów może pozostać w pamięci podręcznej i być ponownie. To zalecenie można szybko zastosować, wybierając polecenie **Zastosuj.**

Po zastosowaniu tego zalecenia umożliwia wymuszoną parametryzację w ciągu kilku minut w bazie danych. Rozpoczyna proces monitorowania, który trwa około 24 godzin. Po tym okresie można zobaczyć raport sprawdzania poprawności. Ten raport pokazuje użycie procesora CPU bazy danych 24 godziny przed i po zastosowaniu zalecenia. DORADCA BAZY DANYCH SQL ma mechanizm bezpieczeństwa, który automatycznie przywraca zastosowane zalecenie, jeśli wykryto regresję wydajności.

## <a name="fix-schema-issues-recommendations-preview"></a>Rozwiązywanie zaleceń dotyczących problemów ze schematem (wersja zapoznawcza)

> [!IMPORTANT]
> Firma Microsoft obecnie przestarzałe "Fix schema problem" zalecenia. Zaleca się, aby używać [inteligentnego wglądu](sql-database-intelligent-insights.md) do monitorowania problemów z wydajnością bazy danych, w tym problemów ze schematem, które wcześniej dotyczyły zaleceń "Rozwiąż problem ze schematem".

Zalecenia dotyczące **rozwiązywania problemów ze schematem** pojawiają się, gdy usługa bazy danych SQL powiadamia o anomalii w liczbie błędów SQL związanych ze schematem, które występują w bazie danych SQL. To zalecenie pojawia się zazwyczaj, gdy baza danych napotka wiele błędów związanych ze schematem (nieprawidłowa nazwa kolumny, nieprawidłowa nazwa obiektu i tak dalej) w ciągu godziny.

"Problemy ze schematem" to klasa błędów składni w programie SQL Server. Występują one, gdy definicja kwerendy SQL i definicja schematu bazy danych nie są wyrównane. Na przykład w tabeli docelowej lub odwrotnie może brakować jednej z kolumn oczekiwanych przez kwerendę.

Zalecenie "Rozwiąż problem ze schematem" pojawia się, gdy usługa Azure SQL Database powiadamia o anomalii w liczbie błędów SQL związanych ze schematem, które występują w bazie danych SQL. W poniższej tabeli przedstawiono błędy związane z problemami ze schematem:

| Kod błędu SQL | Komunikat |
| --- | --- |
| 201 |Procedura lub funkcja '*' oczekuje parametru ',* który nie został dostarczony. |
| 207 |Nieprawidłowa nazwa kolumny "*". |
| 208 |Nieprawidłowa nazwa obiektu "*". |
| 213 |Nazwa kolumny lub liczba podanych wartości nie jest zgodna z definicją tabeli. |
| 2812 |Nie można odnaleźć procedury składowanej "*". |
| 8144 |Procedura lub funkcja * ma zbyt wiele argumentów określonych. |

## <a name="custom-applications"></a>Aplikacje niestandardowe

Deweloperzy mogą rozważyć tworzenie aplikacji niestandardowych przy użyciu zaleceń dotyczących wydajności dla usługi Azure SQL Database. Wszystkie zalecenia wymienione w portalu dla bazy danych są dostępne za pośrednictwem interfejsu API [Get-AzSqlDatabaseRecommendedAction.](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat automatycznego dostrajania indeksów baz danych i planów wykonywania kwerend, zobacz [automatyczne dostrajanie usługi Azure SQL Database](sql-database-automatic-tuning.md).
- Aby uzyskać więcej informacji na temat automatycznego monitorowania wydajności bazy danych za pomocą zautomatyzowanej diagnostyki i analizy przyczyn głównych problemów z wydajnością, zobacz [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md).
- Zobacz [Statystyki wydajności kwerendy,](sql-database-query-performance.md) aby dowiedzieć się więcej i wyświetlić wpływ na wydajność najważniejszych zapytań.
