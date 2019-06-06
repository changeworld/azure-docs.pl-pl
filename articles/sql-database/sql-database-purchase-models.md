---
title: Usługa Azure SQL Database zakupu modeli | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat zakupu modeli, które są dostępne dla usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 98c19732c372fbcda3ca8e746d002f94c2687b22
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431371"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Wybieranie między rdzeni wirtualnych i jednostek DTU zakupu modeli

Usługa Azure SQL Database pozwala łatwo kupić w pełni zarządzana platforma jako usługa (PaaS) Aparat bazy danych, który spełnia Twoje potrzeby wydajności i kosztów. W zależności od modelu wdrażania, wybrany dla usługi Azure SQL Database można wybrać model zakupu, która pracuje dla Ciebie:

- [Rdzeń wirtualny (rdzeń wirtualny) — na podstawie modelu zakupu](sql-database-service-tiers-vcore.md) (zalecane). Ten model zakupu zapewnia wybór między warstwami zainicjowanych zasobów obliczeniowych i warstwa wystąpień obliczeniowych bez użycia serwera (wersja zapoznawcza). Z warstwą zainicjowanych zasobów obliczeniowych możesz wybrać dokładne zalecenia dotyczące ilości zasobów obliczeniowych, które zawsze są udostępniane dla obciążenia. Z warstwą bezserwerowe środowisko obliczeniowe należy określić w zakresie obliczeń można skonfigurować automatyczne skalowanie zasobów obliczeniowych. W tej warstwie obliczeniowej można również automatycznie wstrzymywanie i wznawianie bazy danych, w oparciu o obciążenie działanie. Cena jednostkowa (rdzeń wirtualny) na jednostkę czasu jest niżej w warstwie zainicjowanych zasobów obliczeniowych, niż w warstwie bezserwerowe środowisko obliczeniowe.
- [Jednostki transakcji bazy danych (DTU) — na podstawie modelu zakupu](sql-database-service-tiers-dtu.md). Ten model zakupu zawiera powiązane zasoby obliczeniowe i Magazyn pakiety ze zrównoważonym dla typowych obciążeń.

Dla różnych modeli wdrażania usługi Azure SQL Database dostępne są różne modele zakupu:

- [Pojedynczej bazy danych](sql-database-single-databases-manage.md) i [puli elastycznej](sql-database-elastic-pool.md) opcje wdrażania w [usługi Azure SQL Database](sql-database-technical-overview.md) oferują zarówno [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).
- [Wystąpienia zarządzanego](sql-database-managed-instance.md) opcja wdrożenia w usłudze Azure SQL Database oferuje tylko [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).
- [Warstwy usługi w hiperskali](sql-database-service-tier-hyperscale.md) jest dostępna dla pojedynczych baz danych, które korzystają z [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).

Następujących tabel i wykresów Porównaj i zestaw oparty na rdzeniach wirtualnych i w modelu zakupu opartego na jednostkach DTU:

|**Model zakupu**|**Opis**|**Najlepsze dla**|
|---|---|---|
|Model oparty na jednostkach DTU|Ten model opiera się na pakiecie miary obliczeniowe, Magazyn i zasoby we/wy. Rozmiarów wystąpień obliczeniowych są wyrażane w jednostkach Dtu dla pojedynczej bazy danych i jednostek transakcji elastic database (Edtu) dla pul elastycznych. Aby uzyskać więcej informacji na temat jednostek Dtu i Edtu, zobacz [co to są jednostki Dtu i Edtu?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Najlepsze dla klientów, którzy mają opcje zasobu proste, wstępnie skonfigurowane.|
|Model oparty na rdzeniach wirtualnych|Ten model umożliwia niezależne Wybierz zasoby obliczeniowe i magazynowe. Model zakupu opartego na rdzeniach wirtualnych pozwala również na używanie [korzyść użycia hybrydowego platformy Azure dla programu SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) uzyskanie oszczędności kosztów.|Najlepsze dla klientów, którzy wartości elastyczności, kontroli i przejrzystości.|
||||  

![Porównanie modeli cenowych](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Koszty operacji obliczeniowych

### <a name="provisioned-compute-costs"></a>Koszty zainicjowanych zasobów obliczeniowych

W warstwie zainicjowanych zasobów obliczeniowych koszt obliczeń odzwierciedla moc obliczeniową całkowita, które jest obsługiwane dla aplikacji.

W warstwie krytyczne usługi biznesowej firma Microsoft automatycznie Przydziel co najmniej 3 replik. Aby uwzględnić ten dodatkowy przydział zasobów obliczeniowych, cena w modelu zakupu opartego na rdzeniach wirtualnych jest około 2.7 x w warstwie krytyczne biznesowych wyższe niż w warstwie ogólnego przeznaczenia. Podobnie wyższe cena magazynu za GB w warstwie krytyczne biznesowych odzwierciedla wysokiej We/Wy i małe opóźnienia magazyn SSD.

Koszt magazynu kopii zapasowej jest taka sama dla warstwy usług krytycznych biznesowej i warstwy usług ogólnego przeznaczenia, ponieważ korzystają z obu warstwach standard storage.

### <a name="serverless-compute-costs"></a>Koszty operacji obliczeniowych bez użycia serwera

Aby uzyskać opis sposobu zdefiniowania moc obliczeniową i koszty są obliczane dla warstwy bezserwerowe środowisko obliczeniowe, zobacz [bez użycia serwera bazy danych SQL Database (wersja zapoznawcza)](sql-database-serverless.md).

## <a name="storage-costs"></a>Koszty magazynowania

Różnych typów pamięci masowej są rozliczane w różny sposób. Do przechowywania danych opłaty są naliczane dla aprowizowanego magazynu na podstawie maksymalny rozmiar bazy danych lub puli, którą wybierzesz. Koszt nie ulegnie zmianie, chyba że zmniejszyć lub zwiększyć maksymalną tego. Magazyn kopii zapasowych jest skojarzony z automatycznie tworzonymi kopiami zapasowymi Twojego wystąpienia i jest przydzielany dynamicznie. Wydłużenie okresu przechowywania kopii zapasowych zwiększa magazynu kopii zapasowych, które jest używane przez wystąpienie usługi.

Domyślnie przez 7 dni, automatyczne kopie zapasowe baz danych są kopiowane do magazynu geograficznie nadmiarowego dostęp do odczytu (RA-GRS) standardowego konta usługi Blob storage. Ten magazyn jest używany przez tygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe i kopie zapasowe dziennika transakcji, które są kopiowane co 5 minut. Rozmiar dzienników transakcji, zależy od szybkości zmian bazy danych. Minimalna wielkość magazynu wynosi 100 procent rozmiaru bazy danych znajduje się bez dodatkowych opłat. Dodatkowe użycie magazynu kopii zapasowych jest rozliczane w GB na miesiąc.

Aby uzyskać więcej informacji na temat cen magazynu, zobacz [ceny](https://azure.microsoft.com/pricing/details/sql-database/single/) strony.

## <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

Rdzeń () reprezentuje logiczny procesor CPU i oferuje możliwość wyboru generacji sprzętu i cechy fizyczne sprzętu (na przykład liczba rdzeni, pamięć i rozmiar magazynu). Model zakupu opartego na rdzeniach wirtualnych zapewnia elastyczność możesz, kontrola, przejrzystości użycia poszczególnych zasobów i prostą metodę tłumaczenia wymagań obciążenia w chmurze lokalnie. Ten model można wybrać zasobów obliczeniowych, pamięci i magazynu na podstawie Twoich potrzeb obciążenia.

W opartych na rdzeniach wirtualnych model zakupu, można wybrać między [ogólnego przeznaczenia](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) i [krytyczne dla działania](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) warstwy dla usługi [pojedyncze bazy danych](sql-database-single-database-scale.md), [ pule elastyczne](sql-database-elastic-pool.md), i [wystąpienia zarządzane](sql-database-managed-instance.md). Dla pojedynczych baz danych, można także [warstwy usługi w hiperskali](sql-database-service-tier-hyperscale.md).

Model zakupu opartego na rdzeniach wirtualnych umożliwia niezależnie wybrać zasoby obliczeniowe i magazynowe, Dopasuj wydajność środowiska lokalnego i optymalizacja ceny. W opartych na rdzeniach wirtualnych model zakupu płacisz za:

- Obliczenia zasobów (warstwy usług + liczba rdzeni wirtualnych i ilość pamięci i generowanie sprzętu).
- Typ i ilość miejsca w magazynie danych i dziennika.
- Magazyn kopii zapasowych (RA-GRS).

> [!IMPORTANT]
> Zasoby obliczeniowe, operacji We/Wy i magazynu danych i dzienników są naliczane zgodnie bazy danych lub elastycznej puli. Magazyn kopii zapasowych jest rozliczane na każdej bazy danych. Aby uzyskać więcej informacji na temat opłat za wystąpienie zarządzane zobacz [wystąpienia zarządzane](sql-database-managed-instance.md).
> **Ograniczenia regionu:** Aby uzyskać bieżącą listę obsługiwanych regionów, zobacz [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Aby utworzyć wystąpienie zarządzane w regionie, który obecnie nie jest obsługiwany [Wyślij żądanie pomocy technicznej za pośrednictwem witryny Azure portal](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Jeśli Twoje pojedynczą bazę danych lub elastycznej puli zużywa ponad 300 jednostek Dtu, konwertowanie do modelu zakupu opartego na rdzeniach wirtualnych może zmniejszyć koszty. Możesz również przekonwertować przy użyciu wybranego interfejsu API lub przy użyciu witryny Azure portal, bez przestojów. Jednak konwersja nie jest wymagana i nie jest wykonywane automatycznie. Jeśli model zakupu opartego na jednostkach DTU spełnia swoje wymagania biznesowe i wydajności, można nadal go używać.

Aby przekonwertować z modelu zakupu opartego na jednostkach DTU modelu zakupu opartego na rdzeniach wirtualnych, wybierz rozmiar obliczeń przy użyciu następujące reguły akceptacji:

- Co 100 jednostek Dtu w warstwie standardowa wymaga co najmniej 1 rdzeń wirtualny w warstwie ogólnego przeznaczenia.
- Każdy 125 jednostek Dtu w warstwie premium wymagają co najmniej 1 rdzeń wirtualny w warstwie krytyczne biznesowych.

## <a name="dtu-based-purchasing-model"></a>Model zakupu w oparciu o jednostki DTU

Jednostki transakcji bazy danych (DTU) reprezentuje mieszany pomiar procesora CPU, pamięci, odczytów i zapisów. Model zakupu opartego na jednostkach DTU oferuje zestaw wstępnie skonfigurowane pakiety zasobów obliczeniowych i magazynu dostosowane do różnych poziomów wydajności aplikacji w pakiecie. Jeśli preferujesz prostotę wstępnie skonfigurowanego pakietu i płatności w stałej kwocie co miesiąc, model oparty na jednostkach DTU może być bardziej odpowiednie do potrzeb.

W oparty na jednostkach DTU model zakupu, można wybrać między podstawowa, standardowa i premium warstwy usług dla obu [pojedyncze bazy danych](sql-database-single-database-scale.md) i [pul elastycznych](sql-database-elastic-pool.md). Model zakupu opartego na jednostkach DTU jest niedostępna dla [wystąpienia zarządzane](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Jednostki transakcji bazy danych (Dtu)

Dla pojedynczej bazy danych na określonym obliczenia rozmiaru na [warstwy usług](sql-database-single-database-scale.md), firma Microsoft gwarantuje, więc pewnego poziomu zasobów dla tej bazy danych jest (niezależnie od innej bazy danych w chmurze platformy Azure). Gwarancja zapewnia przewidywalną poziom wydajności. Ilość zasobów przydzielonych dla bazy danych jest obliczany jako liczba jednostek dtu i jest miarą powiązane obliczeń, magazynu i zasoby we/wy.

Stosunek między te zasoby pierwotnie jest określana przez [obciążenia porównawczego przetwarzanie transakcji online (OLTP)](sql-database-benchmark-overview.md) zaprojektowanego jako typowe rzeczywiste obciążeń OLTP. Gdy obciążenie przekracza ilość dowolnego z następujących zasobów, przepływność jest dławiona, co spowoduje wolniejsze wydajność i przekroczenia limitu czasu.

Zasoby używane przez obciążenie nie miały wpływu na zasoby dostępne dla innych baz danych SQL w chmurze platformy Azure. Podobnie zasoby używane przez inne obciążenia nie miały wpływu na zasoby dostępne dla usługi SQL database.

![obwiedni](./media/sql-database-what-is-a-dtu/bounding-box.png)

Liczba jednostek Dtu są najbardziej przydatne do zrozumienia względne zasoby, które są przydzielane dla baz danych Azure SQL na różnych rozmiarów wystąpień obliczeniowych i warstwy usług. Na przykład:

- Podwojenie liczby jednostek Dtu przez zwiększenie rozmiaru obliczeń bazy danych odpowiada dwukrotnemu zwiększeniu zestawu zasobów dostępnych dla tej bazy danych.
- Danych premium usługi warstwy P11 z 1750 jednostkami Dtu zapewnia 350 razy więcej jednostek DTU mocy obliczeniowej niż warstwa bazy danych usług w warstwie podstawowa z 5 jednostkami Dtu.  

Aby uzyskać lepszy wgląd w użycie zasobów (DTU), obciążenia, należy użyć [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) do:

- Zidentyfikuj najpopularniejsze zapytania według liczby Procesor/czas trwania/wykonywania, które potencjalnie mogą być dostosowane w celu zwiększenia wydajności. Na przykład, zapytania I wyjścia z wieloma operacjami/mogą skorzystać z [techniki optymalizacji w pamięci](sql-database-in-memory.md) skuteczniej wykorzystać dostępnej pamięci w pewnym warstwę usługi i obliczenia rozmiaru.
- Przejść do szczegółów szczegółowe zapytania, aby wyświetlić jego tekstu i jego historię użycia zasobów.
- Dostęp do zalecenia dostrajania wydajności, pokazujące działania podejmowane przez [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Jednostki transakcji elastic database (Edtu)

Dla baz danych SQL, które są zawsze dostępne, zamiast niż to dedykowany zestaw zasobów (Dtu), które nie zawsze konieczne może być można umieścić te bazy danych do [puli elastycznej](sql-database-elastic-pool.md). Bazy danych w puli elastycznej znajdują się na jednym serwerze usługi Azure SQL Database i korzystają ze wspólnej puli zasobów.

Współdzielone zasoby w puli elastycznej są mierzone w jednostkach transakcji elastycznych baz danych (Edtu). Pule elastyczne zapewniają proste i niedrogie rozwiązanie umożliwiające zarządzanie celami wydajności dla wielu baz danych o znacznie zróżnicowanych i nieprzewidywalnych wzorcach użycia. Puli elastycznej gwarantuje, że wszystkie zasoby nie mogą być używane przez jedną bazę danych w puli, przy jednoczesnym zapewnieniu, że każda baza danych w puli ma zawsze minimalną ilość wymaganych zasobów dostępnych.

Puli jest przydzielana określona liczba jednostek Edtu za określoną cenę. W elastycznej puli pojedyncze bazy danych można skalowania automatycznego w skonfigurowanych granicach. Bazy danych przy większych obciążeniach zajmie więcej jednostek Edtu w celu spełnienia określonych wymagań. Bazy danych pod obciążeniem jaśniejszy zajmie mniej jednostek Edtu. Bazy danych bez obciążenia będą nie zużywają jednostek Edtu. Ponieważ aprowizacji zasobów dla całej puli zamiast na bazę danych, pul elastycznych uprościć zadania związane z zarządzaniem i zapewnić przewidywalny budżet dla puli.

Dodatkowe jednostki Edtu można dodać do istniejącej puli bez przestoju bazy danych i bez wpływu na bazy danych w puli. Podobnie jeśli dodatkowe jednostki Edtu nie są już potrzebne, usuń je z istniejącej puli w dowolnej chwili. Można również dodać baz danych lub zmniejszyć liczbę baz danych z puli, w dowolnym momencie. Aby zarezerwować je dla innych baz danych, należy ograniczyć liczbę jednostek Edtu, których baza danych może używać pod dużym obciążeniem. Jeśli bazy danych jest stale underuses zasobów, przenieść ją z puli i skonfigurować go jako pojedynczą bazę danych z wymaganą ilością wymaganych zasobów.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Określenie liczby jednostek Dtu wymaganych przez obciążenie

Jeśli użytkownik chce migrować istniejące lokalne lub obciążenie maszyny wirtualnej programu SQL Server do usługi Azure SQL Database, użyj [kalkulatora DTU](https://dtucalculator.azurewebsites.net/) obliczyć przybliżoną liczbę potrzebnych jednostek Dtu. W przypadku istniejących obciążeń usługi Azure SQL Database, należy użyć [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) zrozumieć swoje użycie zasobów bazy danych (Dtu) i uzyskać bardziej szczegółowe informacje dotyczące optymalizacji obciążenia. [Sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dynamicznego widoku zarządzania (DMV) umożliwia wyświetlanie zużycia zasobów w ciągu ostatniej godziny. [Sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) widoku wykazu przedstawia użycie zasobów w ciągu ostatnich 14 dni, ale o mniejszej dokładności pięciu minut.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Obciążenia, które korzystają z elastycznej puli zasobów

Pule są odpowiednie dla baz danych za pomocą Średnio niski wykorzystanie zasobów i stosunkowo rzadkimi okresami zwiększonego użycia. Bazy danych SQL Database automatycznie ocenia historyczne użycie zasobów baz danych na istniejącym serwerze bazy danych SQL Database i zaleca odpowiednią konfigurację puli w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [kiedy należy rozważyć pulę elastyczną SQL Database?](sql-database-elastic-pool.md).

## <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Czy muszę przenieść aplikację do trybu offline można przekonwertować z warstwy usług oparte na jednostkach DTU do warstwy usług oparte na rdzeniach wirtualnych?

Nie. Nie ma potrzeby przejdź do trybu offline. Nowe warstwy usługi oferują proste metodę konwersji online, która jest podobna do istniejącego procesu uaktualniania baz danych od standardowej warstwy premium i odwrotnie. Ta konwersja można uruchomić przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, T-SQL lub interfejsu API REST. Zobacz [zarządzanie pojedynczymi bazami danych](sql-database-single-database-scale.md) i [Zarządzanie elastycznymi pulami](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Czy mogę przekonwertować bazę danych z warstwy usług w modelu zakupu opartego na rdzeniach wirtualnych do warstwy usług w modelu zakupu opartego na jednostkach DTU?

Tak, można z łatwością przekształcić bazę danych do dowolnego celu obsługiwanych wydajności przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, T-SQL lub interfejsu API REST. Zobacz [zarządzanie pojedynczymi bazami danych](sql-database-single-database-scale.md) i [Zarządzanie elastycznymi pulami](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat modelu zakupu opartego na rdzeniach wirtualnych, zobacz [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).
- Aby uzyskać więcej informacji na temat modelu zakupu opartego na jednostkach DTU, zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md).
