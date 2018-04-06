---
title: 'Usługa SQL Database: Co to jest jednostka DTU? | Microsoft Docs'
description: Opis jednostki transakcji usługi Azure SQL Database.
keywords: opcje bazy danych, wydajność bazy danych
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/14/2017
ms.author: carlrab
ms.openlocfilehash: b5d6ffc9aa13e6aaf948028fabe3087f8dea2a1d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Bazy danych (Dtu). jednostki transakcji i jednostek transakcji elastycznej bazy danych (Edtu)
W tym artykule wyjaśniono jednostki DTU (Database Transaction Unit) i jednostki eDTU (elastic Database Transaction Unit) oraz skutki osiągnięcia maksymalnej liczby jednostek DTU lub eDTU.  

## <a name="what-are-database-transaction-units-dtus"></a>Co to są jednostki transakcji bazy danych (Dtu)?
Dla jednej bazy danych Azure SQL w konkretnego poziomu wydajności w ramach [warstwy usług](sql-database-single-database-resources.md), Microsoft gwarantuje, że pewien poziom zasoby dla tej bazy danych (niezależnie od innych bazy danych w chmurze Azure) i zapewnia przewidywalną poziom wydajności. Ta ilość zasobów jest obliczany jako liczba jednostek transakcji bazy danych lub Dtu i powiązane miary obliczeniowej, magazynu i zasobów we/wy. Stosunek między tymi zasobami pierwotnie został określony poprzez [obciążenia OLTP testu porównawczego](sql-database-benchmark-overview.md) zaprojektowaną do typowe dla obciążeń OLTP rzeczywistych. Gdy obciążenie przekracza ilość żadnego z tych zasobów, przepustowość sieci jest ograniczeniem przepustowości — wynikowe w niska wydajność i przekroczeń limitu czasu. Zasoby używane przez obciążenie będzie mieć wpływu na zasoby dostępne dla innych baz danych w chmurze platformy Azure i zasobów, używany przez inne obciążenia nie wpływają na zasoby dostępne dla Twojej bazy danych SQL.

![obwiedni](./media/sql-database-what-is-a-dtu/bounding-box.png)

Liczba jednostek Dtu są najbardziej przydatny w przypadku zrozumieć względną ilość zasobów między bazami danych SQL Azure na różne poziomy wydajności i warstwy usług. Na przykład podwajając jednostek Dtu po podniesieniu poziomu wydajności bazy danych jest równa podwojenie zestaw zasobów dostępne dla tej bazy danych. Na przykład baza danych Premium P11 z 1750 jednostkami DTU zapewnia 350 razy więcej mocy obliczeniowej DTU niż podstawowa baza danych z 5 jednostkami DTU.  

Aby uzyskać lepszy wgląd w zużycie zasobów (bazy danych DTU), obciążenia, należy użyć [szczegółowe informacje o usłudze Azure SQL bazy danych zapytań wydajności](sql-database-query-performance.md) do:

- Identyfikowanie najważniejszych zapytań według liczby wykonywania-Procesor/czas trwania, który może potencjalnie dostroić zwiększonej wydajności. Na przykład kwerendy znacznym We/Wy mogą korzystać z użycia [techniki optymalizacji w pamięci](sql-database-in-memory.md) lepsze wykorzystanie dostępnej pamięci w niektórych warstwę i poziom wydajności usługi.
- Przechodzenie do szczegółów zapytania, Wyświetl tekst i historii wykorzystania zasobów.
- Dostosowywanie zaleceń, które zawierają akcje wykonywane przez wydajności dostępu [doradcy bazy danych SQL](sql-database-advisor.md).

Możesz [zmienić warstwy usług](sql-database-service-tiers.md) w dowolnym momencie przy minimalnych przestojach w działaniu aplikacji (zwykle poniżej czterech sekund). W przypadku wielu firm i aplikacji możliwość tworzenia baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza jeśli wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. W tym scenariuszu należy użyć puli elastycznej z określonej liczby jednostek Edtu, które są współużytkowane przez wiele bazy danych w puli.

![Wprowadzenie do usługi SQL Database: jednostki DTU z jedną bazą danych według warstwy i poziomu.](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Co to są jednostek transakcji elastycznej bazy danych (Edtu)?
Raczej nie udostępniają dedykowany zestaw zasobów (Dtu) do bazy danych SQL, która jest zawsze dostępna niezależnie od tego, czy potrzebne nie, możesz umieścić baz danych do [puli elastycznej](sql-database-elastic-pool.md) na serwerze bazy danych SQL, który udostępnia puli zasobów między tymi bazy danych. Udostępnione zasoby w puli elastycznej, mierząc elastycznych jednostkach transakcji bazy danych lub Edtu. Pule elastyczne zapewniają proste i ekonomiczne rozwiązanie umożliwiające zarządzanie celami wydajności dla wielu baz danych o znacznie zróżnicowanych i nieprzewidywalnych wzorcach użycia. W puli elastycznej może zagwarantować nie jedna baza danych używa wszystkich zasobów w puli oraz czy minimalna ilość zasobów jest zawsze dostępny dla bazy danych w puli elastycznej. 

![Wprowadzenie do usługi SQL Database: jednostki eDTU według warstwy i poziomu](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Puli jest przydzielana określona liczba jednostek eDTU za określoną cenę. Poszczególne bazy danych w ramach puli elastycznej mają możliwość elastycznego skalowania automatycznego w skonfigurowanych granicach. Pod dużym obciążeniem baza danych może używać więcej jednostek eDTU, aby spełnić wymagania, a baza danych pod mniejszym obciążeniem używa ich mniej — przy czym przy braku obciążenia jednostki eDTU nie są używane. Aprowizowanie zasobów dla całej puli, a nie dla poszczególnych baz danych, upraszcza zadania zarządzania i oferuje przewidywalny budżet dla puli.

Dodatkowe jednostki eDTU można dodać do istniejącej puli bez przerwy w działaniu baz danych ani wpływu na bazy danych w puli. Podobnie jeśli dodatkowe jednostki eDTU nie są już potrzebne, można je usunąć z istniejącej puli w dowolnej chwili. Można zwiększyć lub zmniejszyć liczbę baz danych w puli lub ograniczyć liczbę jednostek eDTU, których baza danych może używać pod dużym obciążeniem, aby zarezerwować je dla innych baz danych. Jeśli baza danych regularnie używa mniej zasobów, niż jest przydzielone, można przenieść ją z puli i skonfigurować jako pojedynczą bazę danych z wymaganą ilością zasobów.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Jak określić wymaganą liczbę jednostek DTU dla mojego obciążenia?
Jeśli chcesz zmigrować istniejące obciążenie lokalne lub obciążenie maszyny wirtualnej programu SQL Server do usługi Azure SQL Database, możesz skorzystać z [Kalkulatora jednostek DTU ](http://dtucalculator.azurewebsites.net/), aby obliczyć przybliżoną liczbę wymaganych jednostek DTU. Dla istniejącego obciążenia usługi Azure SQL Database można użyć narzędzia [Szczegółowe informacje o wydajności zapytań usługi SQL Database](sql-database-query-performance.md), aby poznać użycie zasobów bazy danych (jednostek DTU) w celu uzyskania bardziej szczegółowych informacji umożliwiających zoptymalizowanie obciążenia. Informacje o użyciu zasobów w ostatniej godzinie można również uzyskać przy użyciu dynamicznego widoku zarządzania [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx). Alternatywnie można przesłać zapytanie widoku katalogu [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx), aby uzyskać te dane dla ostatnich 14 dni, chociaż o mniejszej dokładności wynoszącej średnią z pięciu minut.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Jak sprawdzić, czy elastyczna pula zasobów jest dla mnie korzystnym rozwiązaniem?
Pule są odpowiednie dla wielu baz danych o określonych wzorcach użycia. Dla danej bazy danych ten wzorzec charakteryzuje się niskim średnim wykorzystaniem oraz stosunkowo rzadkimi okresami zwiększonego użycia. Usługa SQL Database automatycznie ocenia historyczne użycie zasobów przez bazy danych na istniejącym serwerze usługi SQL Database i zaleca odpowiednią konfigurację puli w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Kiedy należy użyć puli elastycznej?](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Co się stanie po naciśnięciu I Moje maksymalna liczba jednostek Dtu?
Poziomy wydajności są kalibrowane i określane w celu zapewnienia zasobów wymaganych do uruchomienia obciążenia baz danych do maksymalnego limitu dozwolonego dla wybranej warstwy usługi/poziomu wydajności. Jeśli obciążenie powoduje osiągnięcie limitu procesora CPU, operacji we/wy danych lub operacji we/wy dziennika, nadal zapewniane będą zasoby na maksymalnym dozwolonym poziomie, ale mogą wystąpić opóźnienia zapytań. Te limity nie powodują występowania błędów, ale raczej spowalniają działanie obciążenia, o ile obciążenie nie stanie się tak duże, by powodować przekroczenie limitu czasu zapytań. W przypadku osiągnięcia limitów maksymalnej dozwolonej liczby równoczesnych sesji/żądań użytkowników (wątków roboczych) występują jawne błędy. Zobacz [limity zasobów bazy danych SQL Azure]( sql-database-dtu-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) uzyskać informacji o limit zasobów niż procesora CPU, pamięci, we/wy danych i dziennika transakcji, we/wy.

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [warstwy usług](sql-database-service-tiers.md) informacji na temat jednostek Dtu a Edtu dostępnych dla pojedynczych baz danych i pul elastycznych, a także ograniczenia dotyczące zasobów niż procesora CPU, pamięci, we/wy danych i dziennika transakcji, we/wy.
* Zobacz [Szczegółowe informacje o wydajności zapytań usługi SQL Database](sql-database-query-performance.md), aby zrozumieć wykorzystanie jednostek DTU.
* Zobacz [Omówienie testu porównawczego usługi SQL Database](sql-database-benchmark-overview.md), aby zrozumieć metodologię obciążenia porównawczego OLTP używanego w celu określenia połączonego wskaźnika w postaci jednostek DTU.
