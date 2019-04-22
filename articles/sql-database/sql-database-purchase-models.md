---
title: Usługa Azure SQL Database zakupu modeli | Dokumentacja firmy Microsoft
description: Więcej informacji na temat model zakupu modeli, które są dostępne bazy danych w usłudze Azure SQL Database.
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
ms.date: 02/08/2019
ms.openlocfilehash: 46a620900896d07273da22e53171330b85d3f1ec
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360189"
---
# <a name="azure-sql-database-purchasing-models"></a>Usługa Azure SQL Database zakupu modeli

Usługa Azure SQL Database umożliwia łatwy zakup w pełni zarządzany aparat bazy danych PaaS, który spełnia Twoje potrzeby wydajności i kosztów. W zależności od modelu wdrażania usługi Azure SQL Database można wybrać model zakupu, która spełnia Twoje potrzeby:

- [model zakupu w oparciu o rdzeń wirtualny](sql-database-service-tiers-vcore.md) (zalecane), pozwala wybrać dokładne zalecenia dotyczące ilości pojemność magazynu i obliczeń potrzebne dla danego obciążenia.
- [Model zakupu w oparciu o jednostki DTU](sql-database-service-tiers-dtu.md) , aby wybrać powiązane zasoby obliczeniowe i Magazyn pakiety ze zrównoważonym dla typowych obciążeń.

Różne modele zakupu są dostępne w modelach wdrażania usługi Azure SQL Database:

- [Pojedynczej bazy danych](sql-database-single-databases-manage.md) i [puli elastycznej](sql-database-elastic-pool.md) opcje wdrażania w [usługi Azure SQL Database](sql-database-technical-overview.md) oferują zarówno [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).
- [Wystąpienia zarządzanego](sql-database-managed-instance.md) opcja wdrożenia w usłudze Azure SQL Database tylko oferuje [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).

> [!IMPORTANT]
> [Warstwę usługi na dużą skalę (wersja zapoznawcza)](sql-database-service-tier-hyperscale.md) w publicznej wersji zapoznawczej tylko w przypadku pojedynczych baz danych za pomocą rdzeni wirtualnych zakupem modelu.

Następujących tabel i wykresów porównania i porównać te dwa modele zakupu.

|**Model zakupu**|**Opis**|**Najlepsze dla**|
|---|---|---|
|Model oparty na jednostkach DTU|Ten model opiera się na powiązane miary obliczeniowe, magazynu i zasobów we/wy. Obliczenia rozmiarów są wyrażone w jednostkach transakcji bazy danych (Dtu) dla pojedynczych baz danych i jednostek transakcji elastic Database (Edtu) dla pul elastycznych. Aby uzyskać więcej informacji na temat jednostek Dtu i Edtu, zobacz [co to są jednostki Dtu i Edtu?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Najlepsze dla klientów chcących zasobów proste, wstępnie skonfigurowanych opcji.|
|Model oparty na rdzeniach wirtualnych|Ten model umożliwia niezależne Wybierz zasoby obliczeniowe i magazynowe. Model zakupu opartego na rdzeniach wirtualnych pozwala również na używanie [korzyść użycia hybrydowego platformy Azure dla programu SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) uzyskanie oszczędności kosztów.|Najlepsze dla klientów, którzy wartości elastyczności, kontroli i przejrzystości.|
||||  

![model cen](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Koszty operacji obliczeniowych

Koszt obliczeń odpowiada moc obliczeniową całkowita, które jest obsługiwane dla aplikacji. W warstwie krytyczne usługi biznesowej firma Microsoft automatycznie Przydziel co najmniej 3 replik. Aby uwzględnić ten dodatkowy przydział zasobów obliczeniowych, cena w modelu zakupu opartego na rdzeniach wirtualnych jest większa w warstwie krytyczne usługi biznesowe, niż w warstwie ogólnego przeznaczenia około 2.7 x. Z tego samego powodu wyższe cena magazynu za GB w warstwie krytyczne biznesowych odzwierciedla wysokiej We/Wy i małe opóźnienia magazyn SSD. W tym samym czasie koszt magazynu kopii zapasowych nie jest różnica między tymi warstwami dwóch usług, ponieważ w obu przypadkach używamy klasę magazynu w warstwie standardowa.

## <a name="storage-costs"></a>Koszty magazynowania

Różnych typów pamięci masowej są rozliczane w różny sposób. Do przechowywania danych opłaty są naliczane za aprowizowanego magazynu na podstawie maksymalny rozmiar bazy danych lub puli, którą wybierzesz. Koszt nie ulega zmianie, chyba że zmniejszyć lub zwiększyć maksymalną tego. Magazyn kopii zapasowych jest skojarzony z automatycznie tworzonymi kopiami zapasowymi Twojego wystąpienia i jest przydzielany dynamicznie. Wydłużenie okresu przechowywania kopii zapasowych zwiększa ilość przestrzeni w magazynie kopii zapasowych używanej przez wystąpienie.

Domyślnie automatyczne kopie zapasowe baz danych z 7 dni są kopiowane do magazynu obiektów blob w warstwie RA-GRS Standardowa. Magazyn jest używany przez tygodniowe pełne kopie zapasowe, codzienne różnicowe kopie zapasowe oraz kopie zapasowe dzienników transakcji kopiowane co 5 minut. Rozmiar dziennika transakcji, zależy od szybkości zmian bazy danych. Bez dodatkowych kosztów jest zapewniany minimalny magazyn o rozmiarze wynoszącym 100% rozmiaru bazy danych. Dodatkowe użycie magazynu kopii zapasowych wiąże się z comiesięczną opłatą za ilość używanych GB.

Aby uzyskać więcej informacji na temat cen magazynu, zobacz [ceny](https://azure.microsoft.com/pricing/details/sql-database/single/) strony.

## <a name="vcore-based-purchasing-model"></a>Model zakupu bazujący na rdzeniach wirtualnych

Rdzeń wirtualny reprezentuje logiczny Procesor CPU z opcją wyboru generacji sprzętu i cechy fizyczne sprzętu (na przykład liczba rdzeni, pamięć, rozmiar magazynu). Model zakupu opartego na rdzeniach wirtualnych zapewnia elastyczności, kontroli, przejrzystości użycia poszczególnych zasobów i prostą metodę tłumaczenia wymagań dotyczących obciążenia lokalne do chmury. Ten model umożliwia wybierz obliczeniowych, pamięci i magazynu, w zależności od ich potrzeb obciążenia. W opartych na rdzeniach wirtualnych model zakupu, można wybrać między [ogólnego przeznaczenia](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) i [krytyczne dla działania](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) warstwy dla usługi [pojedyncze bazy danych](sql-database-single-database-scale.md), [ pule elastyczne](sql-database-elastic-pool.md), i [wystąpienia zarządzane](sql-database-managed-instance.md). Dla pojedynczych baz danych, można także [warstwę usługi na dużą skalę (wersja zapoznawcza)](sql-database-service-tier-hyperscale.md).

Model zakupu opartego na rdzeniach wirtualnych umożliwia niezależnie wybrać zasoby obliczeniowe i magazynowe, Dopasuj wydajność środowiska lokalnego i optymalizacja ceny. Oparty na rdzeniach wirtualnych model zakupu klienci płacą za zasoby:

- Obliczenia (warstwy usług + liczba rdzeni wirtualnych i ilość pamięci i generowanie sprzętu)
- Typ i ilość miejsca w magazynie danych i dziennika
- Magazyn kopii zapasowych (RA-GRS)

> [!IMPORTANT]
> Moc obliczeniowa, IOs, dane i Magazyn dzienników są naliczane zgodnie z bazy danych lub elastycznej puli. Magazyn kopii zapasowych jest rozliczane na każdej bazy danych. Aby uzyskać więcej informacji na temat opłat za wystąpienie zarządzane zobacz [wystąpienia zarządzane](sql-database-managed-instance.md).
> **Ograniczenia regionu:** Aby uzyskać bieżącą listę obsługiwanych regionów, zobacz [dostępność produktów według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Jeśli chcesz utworzyć wystąpienie zarządzane w regionie, który nie jest obecnie obsługiwane, możesz to zrobić [Wyślij żądanie pomocy technicznej za pośrednictwem witryny Azure portal](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).
.

Jeśli z pojedynczej bazy danych lub elastycznej puli zużywa ponad 300 jednostek Dtu, konwertowanie do modelu zakupu opartego na rdzeniach wirtualnych może zmniejszyć koszt. Jeśli zdecydujesz się przekonwertować, możesz przekonwertować przy użyciu wybranego interfejsu API lub portalu Azure, bez przestojów. Jednak konwersja nie jest wymagana i nie odbywa się automatycznie. Jeśli model zakupu opartego na jednostkach DTU spełnia swoje wymagania biznesowe i wydajności, można nadal go używać. Jeśli zdecydujesz się przekonwertować z modelu zakupu opartego na jednostkach DTU do modelu zakupu opartego na rdzeniach wirtualnych, wybierz rozmiar obliczeń przy użyciu następujące reguły akceptacji:

- Każdy 100 jednostek DTU w warstwie standardowa wymaga co najmniej 1 rdzeń wirtualny w warstwie przeznaczenie ogólne
- Każdy 125 jednostek DTU w warstwie Premium wymaga co najmniej 1 rdzeń wirtualny w warstwie krytyczne dla działania firmy

## <a name="dtu-based-purchasing-model"></a>Model zakupu w oparciu o jednostki DTU

Jednostki transakcji bazy danych (DTU) reprezentuje mieszany pomiar procesora CPU, pamięci, odczytuje i zapisuje. Model zakupu opartego na jednostkach DTU oferuje zestaw wstępnie skonfigurowane pakiety zasobów obliczeniowych i magazynu dostosowane do różnych poziomów wydajności aplikacji w pakiecie. Klienci, którzy preferują prostotę wstępnie skonfigurowanego pakietu i płatności w stałej kwocie każdego miesiąca, może się okazać modelu opartego na jednostkach DTU bardziej odpowiednie do ich potrzeb. W oparty na jednostkach DTU model zakupu, klienci mogą wybierać między **podstawowe**, **standardowa**, i **premium** warstwy usług dla obu [pojedyncze bazy danych](sql-database-single-database-scale.md) i [pul elastycznych](sql-database-elastic-pool.md). Ten model zakupu nie jest dostępna w [wystąpienia zarządzane](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Jednostki transakcji bazy danych (Dtu)

Dla pojedynczej bazy danych na określonym obliczenia rozmiaru na [warstwy usług](sql-database-single-database-scale.md), firma Microsoft gwarantuje więc pewnego poziomu zasobów dla tej bazy danych (niezależnie od innej bazy danych w chmurze platformy Azure), zapewniając przewidywalne poziom wydajność. Ilość zasobów jest obliczany jako liczba jednostek transakcji bazy danych lub liczby jednostek Dtu i jest miarą powiązane obliczeń, magazynu i zasobów we/wy. Współczynnik obejmujący te zasoby był pierwotnie określany za [obciążenia porównawczego OLTP](sql-database-benchmark-overview.md), jest przeznaczona do typowym, rzeczywistym obciążeniom OLTP. Gdy obciążenie przekracza ilość dowolnego z następujących zasobów, przepływność jest ograniczone — wynikowy niska wydajność i przekroczenia limitu czasu. Zasoby używane przez obciążenie nie miało wpływu na zasoby dostępne dla innych baz danych SQL w chmurze platformy Azure, a zasoby używane przez inne obciążenia nie miało wpływu na zasoby dostępne dla usługi SQL database.

![obwiedni](./media/sql-database-what-is-a-dtu/bounding-box.png)

Liczba jednostek Dtu są najbardziej przydatne do zrozumienia względna ilość zasobów między bazami danych Azure SQL w różnych rozmiarów wystąpień obliczeniowych i warstwy usług. Na przykład podwojenie liczby jednostek Dtu przez zwiększenie rozmiaru obliczeń bazy danych odpowiada dwukrotnemu zwiększeniu zestawu zasobów dostępnych dla tej bazy danych. Na przykład baza danych Premium P11 z 1750 jednostkami DTU zapewnia 350 razy więcej mocy obliczeniowej DTU niż podstawowa baza danych z 5 jednostkami DTU.  

Aby uzyskać lepszy wgląd w użycie zasobów (DTU), obciążenia, należy użyć [szczegółowe informacje o wydajności zapytań](sql-database-query-performance.md) do:

- Zidentyfikuj najpopularniejsze zapytania według liczby Procesor/czas trwania/wykonywania, które potencjalnie mogą być dostosowane w celu zwiększenia wydajności. Na przykład, zapytanie intensywnie korzystających z operacji We/Wy organizowanych przez użycie [techniki optymalizacji w pamięci](sql-database-in-memory.md) skuteczniej wykorzystać dostępnej pamięci w pewnym warstwę usługi i obliczenia rozmiaru.
- Przechodzenie do szczegółów kwerendy, wyświetlanie historii wykorzystania zasobów i tekstem.
- Zalecenia dotyczące, które pokazują akcje wykonywane przez dostrajania wydajności dostępu [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Jednostki transakcji elastic Database (Edtu)

Raczej niż zapewniają dedykowany zestaw zasobów (Dtu), które może nie zawsze być wymagane dla usługi SQL Database, która jest zawsze dostępna, można umieścić bazy danych [puli elastycznej](sql-database-elastic-pool.md) na serwerze bazy danych SQL, który udostępnia pulę zasobów między te bazy danych. Współdzielone zasoby w puli elastycznej są mierzone w elastycznych jednostkach transakcji bazy danych lub Edtu. Pule elastyczne zapewniają proste i ekonomiczne rozwiązanie umożliwiające zarządzanie celami wydajności dla wielu baz danych o znacznie zróżnicowanych i nieprzewidywalnych wzorcach użycia. Pula elastyczna gwarantuje, że zasoby nie mogą być używane przez jedną bazę danych w puli, gdy zawsze zapewnienie każdej bazy danych w puli ma minimalną ilość wymaganych zasobów dostępnych.

Puli jest przydzielana określona liczba jednostek Edtu za określoną cenę. Poszczególne bazy danych w ramach puli elastycznej mają możliwość elastycznego skalowania automatycznego w skonfigurowanych granicach. Bazy danych przy większych obciążeniach zajmie więcej jednostek Edtu w celu spełnienia określonych wymagań. Bazy danych pod obciążeniem jaśniejszy zajmie mniej jednostek Edtu. Bazy danych bez obciążenia będą nie zużywają jednostek Edtu. Aprowizowanie zasobów dla całej puli, zamiast na bazę danych, upraszcza zadania zarządzania, zapewniając przewidywalny budżet dla puli.

Dodatkowe jednostki eDTU można dodać do istniejącej puli bez przerwy w działaniu baz danych ani wpływu na bazy danych w puli. Podobnie jeśli dodatkowe jednostki eDTU nie są już potrzebne, można je usunąć z istniejącej puli w dowolnej chwili. Możesz dodawać lub odejmować baz danych do puli lub ograniczyć liczbę jednostek Edtu bazy danych można używać pod dużym obciążeniem, aby zarezerwować je dla innych baz danych. Jeśli bazy danych jest przewidywalnie niewystarczająco wykorzystuje zasoby, możesz przenieść ją z puli i skonfigurować go jako pojedynczą bazę danych z wymaganą ilością wymaganych zasobów.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Określenie liczby jednostek Dtu wymaganych przez obciążenie

Jeśli chcesz migrować istniejące lokalne lub obciążenie maszyny wirtualnej programu SQL Server do usługi Azure SQL Database, można użyć [kalkulatora DTU](https://dtucalculator.azurewebsites.net/) obliczyć przybliżoną liczbę potrzebnych jednostek Dtu. W przypadku istniejących obciążeń usługi Azure SQL Database, możesz użyć [szczegółowych informacji o wydajności zapytań](sql-database-query-performance.md) Aby poznać użycie zasobów bazy danych (Dtu), aby uzyskać lepszy wgląd w celu optymalizacji działalności obciążenie. Można również użyć [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV, aby wyświetlić zużycie zasobów w ciągu ostatniej godziny. Alternatywnie widoku wykazu [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) przedstawia użycie zasobów w ciągu ostatnich 14 dni, ale o mniejszej dokładności pięciu minut.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Obciążenia, które korzystają z elastycznej puli zasobów

Pule są odpowiednie dla wielu baz danych o określonych wzorcach użycia. Dla danej bazy danych ten wzorzec charakteryzuje się średnio o niskim zapotrzebowaniu, przy użyciu stosunkowo rzadkimi okresami zwiększonego. Usługa SQL Database automatycznie ocenia historyczne użycie zasobów przez bazy danych na istniejącym serwerze usługi SQL Database i zaleca odpowiednią konfigurację puli w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Kiedy należy użyć puli elastycznej?](sql-database-elastic-pool.md)

## <a name="purchase-model-frequently-asked-questions-faq"></a>Model zakupowy — często zadawane pytania (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Czy muszę przenieść aplikację do trybu offline można przekonwertować z bazy danych oparty na jednostkach DTU do warstwy usług oparte na rdzeniach wirtualnych

Nowe warstwy usługi oferują prostą metodę konwersji online, która jest podobna do istniejącego procesu uaktualniania baz danych od warstwy Standard do Premium i odwrotnie. Ta konwersja może być inicjowane przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, T-SQL lub interfejsu API REST. Zobacz [zarządzanie pojedynczymi bazami danych](sql-database-single-database-scale.md) i [Zarządzanie elastycznymi pulami](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-using-the-vcore-based-purchase-to-a-service-tier-using-the-dtu-based-purchasing-model"></a>Czy mogę przekonwertować bazę danych z warstwy usług przy użyciu zakupu opartego na rdzeniach wirtualnych do warstwy usług przy użyciu modelu zakupu opartego na jednostkach DTU

Tak, można z łatwością przekształcić bazę danych do dowolnego celu obsługiwanych wydajności przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, T-SQL lub interfejsu API REST. Zobacz [zarządzanie pojedynczymi bazami danych](sql-database-single-database-scale.md) i [Zarządzanie elastycznymi pulami](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Kolejne kroki

- Dla modelu zakupu opartego na rdzeniach wirtualnych, zobacz [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md)
- Oparte na jednostkach DTU model zakupu, można zobaczyć [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md).
