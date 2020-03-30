---
title: Modele zakupów
description: Dowiedz się więcej o modelach zakupu dostępnych dla usługi Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 97ce402045cfd2c990b457c5d4d06888cda632d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255992"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Wybierz między modelami zakupu vCore i DTU

Usługa Azure SQL Database umożliwia łatwe zakup w pełni zarządzanej platformy jako usługi (PaaS) aparat bazy danych, który pasuje do potrzeb wydajności i kosztów. W zależności od modelu wdrażania wybranego dla usługi Azure SQL Database można wybrać model zakupu, który działa dla Ciebie:

- [Model zakupów oparty na rdzeniu wirtualnym (vCore)](sql-database-service-tiers-vcore.md) (zalecane). Ten model zakupów zapewnia wybór między aprowizowanymi warstwami obliczeniowmi a warstwą obliczeniową bezserwerową. Za pomocą aprowizowanego poziomu obliczeniowego można wybrać dokładną ilość zasobów obliczeniowych, które są zawsze aprowiowane dla obciążenia. W warstwie obliczeniowej bezserwerowej można określić skalowanie automatyczne zasobów obliczeniowych w konfigurowalnym zakresie obliczeniowym. Za pomocą tej warstwy obliczeniowej można również automatycznie wstrzymać i wznowić bazę danych na podstawie aktywności obciążenia. Cena jednostkowa typu vCore za jednostkę czasu jest niższa w warstwie obliczeniowej aprowizowana niż w warstwie obliczeniowej bezserwerowej.
- [Model zakupu oparty na jednostce transakcji bazy danych (DTU)](sql-database-service-tiers-dtu.md). Ten model zakupów zapewnia pakiety obliczeniowe i pakiety magazynu zrównoważone dla typowych obciążeń.

Różne modele zakupów są dostępne dla różnych modeli wdrażania usługi Azure SQL Database:

- Opcje wdrażania [pojedynczej bazy danych](sql-database-single-databases-manage.md) i [puli elastycznej](sql-database-elastic-pool.md) w [usłudze Azure SQL Database](sql-database-technical-overview.md) oferują zarówno model zakupów oparty na [jednostce DTU,](sql-database-service-tiers-dtu.md) jak i [model zakupów oparty na pokazach wirtualnych.](sql-database-service-tiers-vcore.md)
- Opcja wdrażania [wystąpienia zarządzanego](sql-database-managed-instance.md) w usłudze Azure SQL Database oferuje tylko [model zakupów oparty na językach wirtualnych.](sql-database-service-tiers-vcore.md)
- Warstwa [usług hiperskali](sql-database-service-tier-hyperscale.md) jest dostępna dla pojedynczych baz danych korzystających z [modelu zakupów opartego na językach wirtualnych.](sql-database-service-tiers-vcore.md)

Poniższa tabela i wykres porównują i kontrastują modele zakupów oparte na wynikach typu vCore i DTU:

|**Model zakupu**|**Opis**|**Najlepsze dla**|
|---|---|---|
|Model oparty na jednostkach DTU|Ten model jest oparty na powiązanej miary zasobów obliczeniowych, magazynowych i we/wy. Rozmiary obliczeń są wyrażone w jednostkach DTU dla pojedynczych baz danych i w jednostkach transakcji elastycznej bazy danych (eDTU) dla pul elastycznych. Aby uzyskać więcej informacji na temat procesorów DTU i eDTU, zobacz [Czym są procesory DTU i eDTU?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Najlepsze rozwiązanie dla klientów, którzy chcą prostych, wstępnie skonfigurowanych opcji zasobów.|
|Model oparty na rdzeniach wirtualnych|Ten model umożliwia niezależne wybieranie zasobów obliczeniowych i magazynowych. Model zakupów oparty na wynikach wirtualnych umożliwia również korzystanie z [usługi Azure Hybrid Benefit dla programu SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) w celu uzyskania oszczędności kosztów.|Najlepsze rozwiązanie dla klientów, którzy cenią sobie elastyczność, kontrolę i przejrzystość.|
||||  

![porównanie modeli cenowych](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Koszty obliczeń

### <a name="provisioned-compute-costs"></a>Aprowizowane koszty obliczeń

W warstwie obliczeń aprowizowanych koszt obliczeń odzwierciedla całkowitą pojemność obliczeniową, która jest aprowizowana dla aplikacji.

W warstwie usługi Krytyczne dla firmy automatycznie przydzielamy co najmniej 3 repliki. Aby odzwierciedlić tę dodatkową alokację zasobów obliczeniowych, cena w modelu zakupów opartym na procesorze wirtualnym jest około 2,7 x wyższa w warstwie usług Krytyczne dla firmy niż w warstwie usług ogólnego przeznaczenia. Podobnie wyższa cena magazynu za GB w warstwie usługi Krytyczne dla firm odzwierciedla wyższe limity we/wy i mniejsze opóźnienia magazynu SSD.

Koszt magazynu kopii zapasowych jest taki sam dla warstwy usług o krytycznym znaczeniu dla firmy i warstwy usług ogólnego przeznaczenia, ponieważ obie warstwy używają standardowego magazynu do tworzenia kopii zapasowych.

### <a name="serverless-compute-costs"></a>Koszty obliczeniowe bezserwerowe

Aby uzyskać opis sposobu definiowania pojemności obliczeniowej i obliczania kosztów dla warstwy obliczeniowej bezserwerowej, zobacz [Baza danych SQL bez serwerowa](sql-database-serverless.md).

## <a name="storage-costs"></a>Koszty magazynowania

Różne rodzaje pamięci masowej są rozliczane inaczej. W przypadku magazynu danych naliczana jest opłata za aprowizowanym magazynie na podstawie maksymalnego rozmiaru bazy danych lub puli, który wybierzesz. Koszt nie zmienia się, chyba że zmniejszysz lub zwiększysz to maksimum. Magazyn kopii zapasowych jest skojarzony z automatycznymi kopiami zapasowymi wystąpienia i jest przydzielany dynamicznie. Zwiększenie okresu przechowywania kopii zapasowej zwiększa magazyn kopii zapasowej, który jest zużywany przez wystąpienie.

Domyślnie 7 dni automatycznych kopii zapasowych baz danych są kopiowane do standardowego konta magazynu obiektów Blob dostępu do odczytu (RA-GRS). Ten magazyn jest używany przez cotygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe i kopie zapasowe dziennika transakcji, które są kopiowane co 5 minut. Rozmiar dzienników transakcji zależy od szybkości zmiany bazy danych. Minimalna kwota magazynu równa 100 procent rozmiaru bazy danych jest dostarczana bez dodatkowych opłat. Dodatkowe zużycie magazynu kopii zapasowych jest naliczane w GB miesięcznie.

Aby uzyskać więcej informacji na temat cen magazynu, zobacz stronę [cennika.](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="vcore-based-purchasing-model"></a>Model zakupów oparty na rdzeniach wirtualnych

Rdzeń wirtualny (vCore) reprezentuje logiczny procesor i oferuje możliwość wyboru między generacjami sprzętu i fizycznymi cechami sprzętu (na przykład liczbę rdzeni, pamięć i rozmiar magazynu). Model zakupów oparty na wynikach wirtualnych zapewnia elastyczność, kontrolę, przejrzystość zużycia poszczególnych zasobów i prosty sposób tłumaczenia lokalnych wymagań dotyczących obciążenia w chmurze. Ten model umożliwia wybór zasobów obliczeniowych, pamięci i magazynu na podstawie potrzeb obciążenia.

W modelu zakupów opartym na wynikach wirtualnych można wybierać między warstwami usług [ogólnego przeznaczenia](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) i [Krytyczne dla firmy](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) dla [pojedynczych baz danych,](sql-database-single-database-scale.md) [pul elastycznych](sql-database-elastic-pool.md)i [wystąpień zarządzanych.](sql-database-managed-instance.md) W przypadku pojedynczych baz danych można również wybrać [warstwę usług hiperskali](sql-database-service-tier-hyperscale.md).

Model zakupów oparty na wynikach wirtualnych umożliwia samodzielne wybieranie zasobów obliczeniowych i magazynowych, dopasowanie wydajności lokalnej i optymalizację ceny. W modelu zakupów opartym na wynikach vCore płacisz za:

- Zasoby obliczeniowe (warstwa usług + liczba owo wirtualnych i ilość pamięci + generowanie sprzętu).
- Typ i ilość danych i magazynu dziennika.
- Magazyn kopii zapasowych (RA-GRS).

> [!IMPORTANT]
> Zasoby obliczeniowe, we/wy oraz magazyn danych i dzienników są naliczane na jedną bazę danych lub pulę elastyczną. Magazyn kopii zapasowej jest naliczany za każdą bazę danych. Aby uzyskać więcej informacji na temat opłat za [wystąpienie](sql-database-managed-instance.md)zarządzane, zobacz wystąpienia zarządzane .
> **Ograniczenia regionu:** Aby uzyskać aktualną listę obsługiwanych regionów, zobacz [produkty dostępne według regionu](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Aby utworzyć wystąpienie zarządzane w regionie, który obecnie nie jest obsługiwany, [wyślij żądanie pomocy technicznej za pośrednictwem witryny Azure portal](quota-increase-request.md).

Jeśli pojedyncza baza danych lub pula elastyczna zużywa więcej niż 300 jednostek DTU, konwersja na model zakupów oparty na procesorach wirtualnych może obniżyć koszty. Można konwertować przy użyciu interfejsu API wyboru lub przy użyciu witryny Azure portal, bez przestojów. Konwersja nie jest jednak wymagana i nie jest wykonywana automatycznie. Jeśli model zakupów oparty na UJ spełnia twoje wymagania dotyczące wydajności i działalności, należy nadal go używać.

Aby przekonwertować model zakupu oparty na jednostce DTU na model zakupu oparty na wynikach wersji wirtualnej, wybierz rozmiar obliczeń, używając następujących reguł:

- Co 100 procesorów DTU w warstwie standardowej wymaga co najmniej 1 genera vCore w warstwie usług ogólnego przeznaczenia.
- Co 125 procesorów DTU w warstwie premium wymaga co najmniej 1 bramki wirtualnej w warstwie usługi Krytyczne dla firm.

> [!NOTE]
> Wytyczne dotyczące rozmiaru dtu do wersji wirtualnej są przybliżone i są dostarczane w celu pomocy w początkowym szacowaniu celu usługi bazy danych docelowej. Optymalna konfiguracja docelowej bazy danych jest zależna od obciążenia. 
> 
> Osiągnięcie optymalnego stosunku ceny do wydajności może wymagać wykorzystania elastyczności modelu vCore w celu dostosowania liczby przewodów wirtualnych, [generowania sprzętu,](sql-database-service-tiers-vcore.md#hardware-generations) [warstw usług](sql-database-service-tiers-vcore.md#service-tiers) i [obliczeń,](sql-database-service-tiers-vcore.md#compute-tiers) a także dostrajania innych parametrów konfiguracji bazy danych, takich jak [maksymalny stopień równoległości.](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)

## <a name="dtu-based-purchasing-model"></a>Model zakupów oparty na UTU

Jednostka transakcji bazy danych (DTU) reprezentuje mieszaną miarę procesora CPU, pamięci, odczytów i zapisów. Model zakupów oparty na jednostce DTU oferuje zestaw wstępnie skonfigurowanych pakietów zasobów obliczeniowych i zawiera pamięć masową, aby zwiększyć wydajność aplikacji. Jeśli wolisz prostotę wstępnie skonfigurowanego pakietu i stałych płatności każdego miesiąca, model oparty na UTU może być bardziej odpowiedni dla Twoich potrzeb.

W modelu zakupów opartym na jednostce DTU można wybierać między warstwami usług podstawowych, standardowych i premium zarówno dla [pojedynczych baz danych,](sql-database-single-database-scale.md) jak i [pule elastyczne.](sql-database-elastic-pool.md) Model zakupów oparty na jednostkach DTU nie jest dostępny dla [wystąpień zarządzanych](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Jednostki transakcji bazy danych (DTU)

Dla pojedynczej bazy danych o określonym rozmiarze obliczeniowym w [ramach warstwy usług](sql-database-single-database-scale.md)firma Microsoft gwarantuje określony poziom zasobów dla tej bazy danych (niezależnie od jakiejkolwiek innej bazy danych w chmurze platformy Azure). Ta gwarancja zapewnia przewidywalny poziom wydajności. Ilość zasobów przydzielonych dla bazy danych jest obliczana jako liczba jednostek DTU i jest powiązaną miarą zasobów obliczeniowych, magazynowych i we/wy.

Stosunek tych zasobów jest pierwotnie określany przez [obciążenie testowe przetwarzania transakcji online (OLTP),](sql-database-benchmark-overview.md) które ma być typowe dla rzeczywistych obciążeń OLTP. Gdy obciążenie przekracza ilość tych zasobów, przepływność jest ograniczana, co powoduje wolniejsze wykonanie i limity czasu.

Zasoby używane przez obciążenie nie mają wpływu na zasoby dostępne dla innych baz danych SQL w chmurze platformy Azure. Podobnie zasoby używane przez inne obciążenia nie mają wpływu na zasoby dostępne dla bazy danych SQL.

![Obwiedni](./media/sql-database-what-is-a-dtu/bounding-box.png)

Procesory DTU są najbardziej przydatne do zrozumienia względnych zasobów, które są przydzielane do baz danych SQL platformy Azure w różnych rozmiarach obliczeniowych i warstwach usług. Przykład:

- Podwojenie jednostek DTU przez zwiększenie rozmiaru obliczeń bazy danych równa się podwojenie zestawu zasobów dostępnych dla tej bazy danych.
- Baza danych P11 warstwy usług premium z 1750 jednostek DTU zapewnia 350x więcej mocy obliczeniowej jednostek DTU niż podstawowa baza danych warstwy usług z 5 procesorami DTU.  

Aby uzyskać lepszy wgląd w zużycie zasobów (DTU) obciążenia, użyj [szczegółowych informacji o wydajności zapytań,](sql-database-query-performance.md) aby:

- Zidentyfikuj najlepsze zapytania według liczby procesorów CPU/czas trwania/wykonanie, które potencjalnie mogą być dostrojone w celu zwiększenia wydajności. Na przykład we/wy intensywnie kwerendy mogą korzystać z [technik optymalizacji w pamięci,](sql-database-in-memory.md) aby lepiej wykorzystać dostępną pamięć w określonej warstwie usługi i rozmiar obliczeń.
- Przejdź do szczegółów kwerendy, aby wyświetlić jej tekst i historię użycia zasobów.
- Uzyskaj dostęp do zaleceń dotyczących dostrajania wydajności, które pokazują akcje podjęte przez [klasyfikatora bazy danych SQL](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Jednostki transakcji elastycznej bazy danych (eDTU)

W przypadku baz danych SQL, które są zawsze dostępne, zamiast zapewniać dedykowany zestaw zasobów (DTU), który nie zawsze może być potrzebny, można umieścić te bazy danych w [puli elastycznej.](sql-database-elastic-pool.md) Bazy danych w puli elastycznej znajdują się na jednym serwerze usługi Azure SQL Database i współużytkują pulę zasobów.

Zasoby udostępnione w puli elastycznej są mierzone za pomocą jednostek transakcji elastycznej bazy danych (eDTU). Pule elastyczne zapewniają proste, ekonomiczne rozwiązanie do zarządzania celami wydajności dla wielu baz danych, które mają bardzo różne i nieprzewidywalne wzorce użycia. Pula elastyczna gwarantuje, że wszystkie zasoby nie mogą być używane przez jedną bazę danych w puli, zapewniając jednocześnie, że każda baza danych w puli zawsze ma minimalną ilość niezbędnych zasobów dostępnych.

Pula otrzymuje określoną liczbę eDTU dla ustalonej ceny. W puli elastycznej poszczególne bazy danych mogą automatycznie skalować się w skonfigurowanych granicach. Baza danych przy większym obciążeniu zużywa więcej eDTU, aby zaspokoić zapotrzebowanie. Bazy danych przy mniejszych obciążeniach zużywają mniej eDTU. Bazy danych bez obciążenia nie zużywają żadnych eDTU. Ponieważ zasoby są aprowizowana dla całej puli, a nie dla bazy danych, pule elastyczne upraszczają zadania zarządzania i zapewniają przewidywalny budżet dla puli.

Można dodać dodatkowe eDTU do istniejącej puli bez przestojów bazy danych i bez wpływu na bazy danych w puli. Podobnie, jeśli nie potrzebujesz już dodatkowych eDTU, usuń je z istniejącej puli w dowolnym momencie. Można również dodać bazy danych do lub odjąć bazy danych z puli w dowolnym momencie. Aby zarezerwować eDTU dla innych baz danych, ogranicz liczbę eDTU, których baza danych może używać przy dużym obciążeniu. Jeśli baza danych konsekwentnie nie wykorzystuje zasobów, przenieść go z puli i skonfigurować go jako pojedynczej bazy danych z przewidywalną ilość wymaganych zasobów.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Określanie liczby procesorów DTU potrzebnych przez obciążenie

Jeśli chcesz przeprowadzić migrację istniejącego obciążenia maszyny lokalnej lub sql server do bazy danych SQL Database, użyj [kalkulatora DTU,](https://dtucalculator.azurewebsites.net/) aby przybliżyć liczbę potrzebnych procesorów DTU. W przypadku istniejącego obciążenia usługi Azure SQL Database użyj [szczegółowych informacji o wydajności zapytań,](sql-database-query-performance.md) aby zrozumieć zużycie zasobów bazy danych (DTU) i uzyskać więcej szczegółowych informacji w celu optymalizacji obciążenia. Widok dynamicznego zarządzania (DMV) [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) umożliwia wyświetlanie zużycia zasobów przez ostatnią godzinę. Widok katalogu [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) wyświetla zużycie zasobów w ciągu ostatnich 14 dni, ale przy niższej wierności średnich pięciominutowych.

### <a name="determine-dtu-utilization"></a>Określanie wykorzystania dtu

Aby określić średni procent wykorzystania identyfikatora DTU/eDTU względem limitu DTU/eDTU bazy danych lub puli elastycznej, użyj następującej formuły:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

Wartości wejściowe dla tej formuły można uzyskać z [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)i [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMVs. Innymi słowy, aby określić procent wykorzystania DTU/eDTU w kierunku limitu DTU/eDTU bazy danych lub puli elastycznej, wybierz największą wartość procentową z następujących wartości: `avg_cpu_percent`, `avg_data_io_percent`i `avg_log_write_percent` w danym momencie.

> [!NOTE]
> Limit jednostki DTU bazy danych jest określany przez procesor, odczyty, zapisy i pamięć dostępną w bazie danych. Jednak ponieważ aparat bazy danych programu SQL Server zazwyczaj używa całej dostępnej `avg_memory_usage_percent` pamięci dla pamięci podręcznej danych w celu zwiększenia wydajności, wartość będzie zwykle bliska 100% niezależnie od bieżącego obciążenia bazy danych. W związku z tym, mimo że pamięć pośrednio wpływa na limit DTU, nie jest używany w formule wykorzystania DTU.
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Obciążenia korzystające z elastycznej puli zasobów

Pule są dobrze przystosowane do baz danych o niskim średnim wykorzystaniu zasobów i stosunkowo rzadkich skokach wykorzystania. Aby uzyskać więcej informacji, zobacz [Kiedy należy wziąć pod uwagę pulę elastyczną bazy danych SQL?](sql-database-elastic-pool.md).

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Generacje sprzętu w modelu zakupów opartym na UDU

W modelu zakupów opartym na UJ klienci nie mogą wybrać generacji sprzętu używanego dla ich baz danych. Podczas gdy dana baza danych zwykle pozostaje na określonej generacji sprzętu przez długi czas (zwykle przez wiele miesięcy), istnieją pewne zdarzenia, które mogą powodować przenoszenie bazy danych do innej generacji sprzętu.

Na przykład bazę danych można przenieść do innego generowania sprzętu, jeśli jest skalowana w górę lub w dół do innego celu usługi lub jeśli bieżąca infrastruktura w centrum danych zbliża się do limitów pojemności lub jeśli aktualnie używany sprzęt jest wycofany ze względu na jego koniec eksploatacji.

Jeśli baza danych zostanie przeniesiona do innego sprzętu, wydajność obciążenia może ulec zmianie. Model DTU gwarantuje, że przepływność i czas odpowiedzi obciążenia [testu porównawczego DTU](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark) pozostaną zasadniczo identyczne, ponieważ baza danych przenosi się do innego generowania sprzętu, o ile jego cel usługi (liczba procesorów DTU) pozostanie taki sam. 

Jednak w szerokim spektrum obciążeń klientów działających w usłudze Azure SQL Database wpływ używania innego sprzętu dla tego samego celu usługi może być bardziej wyraźny. Różne obciążenia będą korzystać z różnych konfiguracji sprzętu i funkcji. W związku z tym dla obciążeń innych niż benchmark DTU, jest możliwe, aby zobaczyć różnice w wydajności, jeśli baza danych przenosi się z jednego generowania sprzętu do drugiego.

Na przykład aplikacja, która jest wrażliwa na opóźnienie sieci, może zobaczyć lepszą wydajność na sprzęcie Gen5 w porównaniu z Gen4 ze względu na użycie przyspieszonej sieci w gen5, ale aplikacja używająca intensywnego odczytu we/wy może zobaczyć lepszą wydajność na sprzęcie Gen4 vs. Gen5 ze względu na wyższy współczynnik pamięci na rdzeń w gen4.

Klienci z obciążeniami, które są wrażliwe na zmiany sprzętowe lub klienci, którzy chcą kontrolować wybór generowania sprzętu dla swojej bazy danych, mogą użyć modelu [vCore,](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore) aby wybrać preferowane generowanie sprzętu podczas tworzenia i skalowania bazy danych. W modelu vCore limity zasobów każdego celu usługi dla każdej generacji sprzętu są udokumentowane zarówno dla [pojedynczych baz danych,](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) jak i [pule elastyczne](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools). Aby uzyskać więcej informacji na temat generacji sprzętu w modelu vCore, zobacz [Generacje sprzętu](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Często zadawane pytania (często zadawane pytania)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Czy muszę przekonwertować aplikację do trybu offline, aby przekonwertować z warstwy usług opartej na usłudze DTU na warstwę usług opartą na łańce wirtualnej?

Nie. Nie musisz przetraktować aplikacji w tryb offline. Nowe warstwy usług oferują prostą metodę konwersji online, która jest podobna do istniejącego procesu uaktualniania baz danych ze standardu do warstwy usług premium i na odwrót. Tę konwersję można uruchomić przy użyciu witryny Azure portal, powershell, interfejsu wiersza polecenia platformy Azure, T-SQL lub interfejsu API REST. Zobacz [Zarządzanie pojedynczymi bazami danych](sql-database-single-database-scale.md) i zarządzanie [pulami elastycznymi](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Czy można przekonwertować bazę danych z warstwy usług w modelu zakupów opartym na wynikach wirtualnych na warstwę usług w modelu zakupu opartym na USŁUDZE DTU?

Tak, można łatwo przekonwertować bazę danych do dowolnego obsługiwanego celu wydajności przy użyciu witryny Azure portal, PowerShell, interfejsu wiersza polecenia platformy Azure, T-SQL lub interfejsu API REST. Zobacz [Zarządzanie pojedynczymi bazami danych](sql-database-single-database-scale.md) i zarządzanie [pulami elastycznymi](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat modelu zakupu opartego na platformach wirtualnych, zobacz [model zakupu oparty na wynikach wirtualnych](sql-database-service-tiers-vcore.md).
- Aby uzyskać więcej informacji na temat modelu zakupów opartego na u jednostka DTU, zobacz [Model zakupów oparty na UJ](sql-database-service-tiers-dtu.md).
