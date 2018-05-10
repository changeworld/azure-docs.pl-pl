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
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 3fb0d0f73f475e246580f7ea6e4aea60b069c230
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Bazy danych (Dtu). jednostki transakcji i jednostek transakcji elastycznej bazy danych (Edtu)
W tym artykule wyjaśniono jednostki DTU (Database Transaction Unit) i jednostki eDTU (elastic Database Transaction Unit) oraz skutki osiągnięcia maksymalnej liczby jednostek DTU lub eDTU. Aby uzyskać określone informacje o cenach, zobacz [cennik usługi Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="what-are-database-transaction-units-dtus"></a>Co to są jednostki transakcji bazy danych (Dtu)?
Dla jednej bazy danych Azure SQL w konkretnego poziomu wydajności w ramach [warstwy usług](sql-database-single-database-resources.md), Microsoft gwarantuje pewnego poziomu zasobów dla tej bazy danych (niezależnie od innych bazy danych w chmurze Azure), zapewniając przewidywalna poziom wydajności. Ilość zasobów jest obliczany jako liczba jednostek transakcji bazy danych lub Dtu i powiązane miary obliczeniowej, magazynu i zasobów we/wy. Stosunek między tymi zasobami pierwotnie został określony poprzez [obciążenia OLTP testu porównawczego](sql-database-benchmark-overview.md), zaprojektowany jako typowe dla obciążeń OLTP rzeczywistych. Gdy obciążenie przekracza ilość żadnego z tych zasobów, przepustowość sieci jest ograniczeniem przepustowości — wynikowe w niska wydajność i przekroczeń limitu czasu. Zasoby używane przez obciążenie będzie mieć wpływu na zasoby dostępne dla innych baz danych w chmurze Azure oraz zasoby używane przez inne obciążenia nie wpływają na zasoby dostępne dla Twojej bazy danych SQL.

![obwiedni](./media/sql-database-what-is-a-dtu/bounding-box.png)

Liczba jednostek Dtu są najbardziej przydatny w przypadku zrozumieć względną ilość zasobów między bazami danych SQL Azure na różne poziomy wydajności i warstwy usług. Na przykład podwajając jednostek Dtu po podniesieniu poziomu wydajności bazy danych jest równa podwojenie zestaw zasobów, które są dostępne dla tej bazy danych. Na przykład baza danych Premium P11 z 1750 jednostkami DTU zapewnia 350 razy więcej mocy obliczeniowej DTU niż podstawowa baza danych z 5 jednostkami DTU.  

Aby uzyskać lepszy wgląd w zużycie zasobów (bazy danych DTU), obciążenia, należy użyć [szczegółowe informacje o usłudze Azure SQL bazy danych zapytań wydajności](sql-database-query-performance.md) do:

- Identyfikowanie najważniejszych zapytań według liczby wykonywania-Procesor/czas trwania, który może potencjalnie dostroić zwiększonej wydajności. Na przykład kwerendy znacznym We/Wy mogą korzystać z użycia [techniki optymalizacji w pamięci](sql-database-in-memory.md) lepsze wykorzystanie dostępnej pamięci w niektórych warstwę i poziom wydajności usługi.
- Przechodzenie do szczegółów zapytania, Wyświetl tekst i historii wykorzystania zasobów.
- Dostosowywanie zaleceń, które zawierają akcje wykonywane przez wydajności dostępu [doradcy bazy danych SQL](sql-database-advisor.md).

Możesz zmienić [DTU warstwy usług](sql-database-service-tiers-dtu.md) w dowolnym momencie z minimalnym czasem przestojów w aplikacji (zwykle uśrednianie poniżej cztery sekund). W przypadku wielu firm i aplikacji możliwość tworzenia baz danych i dostosowywania wydajności na żądanie jest wystarczająca, zwłaszcza jeśli wzorce użycia są względnie przewidywalne. Jednak w przypadku nieprzewidywalnych wzorców zarządzanie kosztami i modelem biznesowym może być trudne. W tym scenariuszu należy użyć puli elastycznej z określonej liczby jednostek Edtu, które są współużytkowane przez wiele baz danych w puli.

![Wprowadzenie do usługi SQL Database: jednostki DTU z jedną bazą danych według warstwy i poziomu.](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Co to są jednostek transakcji elastycznej bazy danych (Edtu)?
Raczej nie udostępniają dedykowany zestaw zasobów (Dtu), które może nie zawsze być wymagane dla bazy danych SQL, która jest zawsze dostępna, możesz umieścić baz danych do [puli elastycznej](sql-database-elastic-pool.md) na serwerze bazy danych SQL, który udostępnia puli zasobów między te bazy danych. Udostępnione zasoby w puli elastycznej są mierzone w elastycznych jednostkach transakcji bazy danych lub Edtu. Pule elastyczne zapewniają prosty ekonomiczne rozwiązanie do zarządzania cele wydajności dla wielu baz danych o różnymi i nieprzewidywalnych wzorców. Pula elastyczna gwarantuje, że zasoby nie mogą być używane przez jedną bazę danych w puli, podczas sprawdzeniu każdej bazy danych w puli, zawsze ma minimalna ilość niezbędne dostępnych zasobów. 

![Wprowadzenie do usługi SQL Database: jednostki eDTU według warstwy i poziomu](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Pulę otrzymuje przez liczbę jednostek Edtu ustalonej cenie. Poszczególne bazy danych w ramach puli elastycznej mają możliwość elastycznego skalowania automatycznego w skonfigurowanych granicach. Większe obciążenie bazy danych zajmie więcej jednostek Edtu do zapotrzebowania. Bazy danych w ramach obciążeń jaśniejszy zajmie mniej Edtu. Baz danych bez obciążenia będą korzystać z nie jednostek Edtu. Przez Inicjowanie obsługi zasobów dla całej puli, a nie na bazę danych, zadania zarządzania są uproszczone, zapewnienie przewidywalnej budżetu puli.

Dodatkowe jednostki eDTU można dodać do istniejącej puli bez przerwy w działaniu baz danych ani wpływu na bazy danych w puli. Podobnie jeśli dodatkowe jednostki eDTU nie są już potrzebne, można je usunąć z istniejącej puli w dowolnej chwili. Można dodać lub odjąć baz danych w puli lub limit ilości Edtu bazy danych mogą być duże obciążenie zarezerwować jednostek Edtu dla innych baz danych. Jeśli bazy danych jest jednoznacznie w obszarze wykorzystaniem zasobów, można wychodzenia puli i skonfigurować go jako pojedynczej bazy danych z przewidywalną ilość wymaganych zasobów.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Jak określić wymaganą liczbę jednostek DTU dla mojego obciążenia?
Jeśli chcesz zmigrować istniejące obciążenie lokalne lub obciążenie maszyny wirtualnej programu SQL Server do usługi Azure SQL Database, możesz skorzystać z [Kalkulatora jednostek DTU ](http://dtucalculator.azurewebsites.net/), aby obliczyć przybliżoną liczbę wymaganych jednostek DTU. Istniejące obciążenia pracą bazy danych SQL Azure, można użyć [Insight wydajności kwerendy bazy danych SQL](sql-database-query-performance.md) zrozumienie użycia zasobów bazy danych (Dtu), aby uzyskać bardziej szczegółowe informacje o optymalizacji obciążenie. Można również użyć [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV, aby wyświetlić zużycie zasobów w ciągu ostatniej godziny. Alternatywnie widoku wykazu [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) Wyświetla zużycia zasobów przez ostatnie 14 dni, ale także w niższych wierności średnich 5 minutową.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Jak sprawdzić, czy elastyczna pula zasobów jest dla mnie korzystnym rozwiązaniem?
Pule są odpowiednie dla wielu baz danych o określonych wzorcach użycia. Ten wzorzec określonej bazy danych, charakteryzuje się to średnia niskiego poziomu wykorzystania z wzrostów użycia stosunkowo rzadko. Usługa SQL Database automatycznie ocenia historyczne użycie zasobów przez bazy danych na istniejącym serwerze usługi SQL Database i zaleca odpowiednią konfigurację puli w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Kiedy należy użyć puli elastycznej?](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Co się stanie po naciśnięciu I Moje maksymalna liczba jednostek Dtu?
Poziomy wydajności są kalibrować i postanowieniom dostarczać zasoby potrzebne do uruchomienia obciążenie bazy danych do maksymalny dozwolony poziom warstwy wydajności wybranej usługi. Jeżeli obciążenie jest naciśnięcie jednego z ograniczeń we/wy procesora CPU/dane we/wy/Log, nadal będziesz otrzymywać maksymalny poziom dopuszczalny zasobów, ale również prawdopodobnie doświadczy opóźnienia zwiększona zapytania. Te limity nie powodują występowania błędów, ale raczej spowalniają działanie obciążenia, o ile obciążenie nie stanie się tak duże, by powodować przekroczenie limitu czasu zapytań. Jeśli osiągnąć maksymalną dozwoloną użytkownika równoczesnych sesji/żądań (wątków roboczych), zostanie wyświetlony jawne błędy. Zobacz [limity zasobów bazy danych SQL Azure]( sql-database-dtu-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) dla informacji na temat limitów zasobów nie są powiązane z Procesora, pamięci, we/wy danych lub dziennika transakcji we/wy.

## <a name="next-steps"></a>Kolejne kroki
* Zobacz [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md) informacji na temat jednostek Dtu a Edtu dostępnych dla pojedynczych baz danych i pul elastycznych, a także ograniczenia dotyczące zasobów niż procesora CPU, pamięci, we/wy danych i dziennika transakcji, we/wy.
* Zobacz [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md) Aby uzyskać informacje dotyczące zasobów na podstawie vCore warstwy alokacji i usługi. 
* Zobacz [Szczegółowe informacje o wydajności zapytań usługi SQL Database](sql-database-query-performance.md), aby zrozumieć wykorzystanie jednostek DTU.
* Zobacz [Omówienie testu porównawczego usługi SQL Database](sql-database-benchmark-overview.md), aby zrozumieć metodologię obciążenia porównawczego OLTP używanego w celu określenia połączonego wskaźnika w postaci jednostek DTU.
