---
title: Usługa Azure SQL Database — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na typowe pytania klientów zapytaj o bazach danych w chmurze i usługi Azure SQL Database, system zarządzania relacyjnymi bazami danych firmy Microsoft (danych RDBMS) i bazy danych jako usługa w chmurze.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: carlrab
ms.openlocfilehash: 7f784689e8437d92cf4fc4974983d3ae1455f11b
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091828"
---
# <a name="sql-database-faq"></a>SQL Database — często zadawane pytania

## <a name="what-is-the-current-version-of-sql-database"></a>Co to jest bieżąca wersja bazy danych SQL?
Bieżąca wersja bazy danych SQL jest wersja V12. W wersji V11 został wycofany.

## <a name="what-is-the-sla-for-sql-database"></a>Co to jest umowa SLA dla usługi SQL Database?
Firma Microsoft gwarantuje dostępność przez 99,99% czasu, możesz korzystać z łączności między usługi Microsoft Azure SQL Database i bramą internetową firmy Microsoft niezależnie od tego, w warstwie usługi. Aby uzyskać więcej informacji, zobacz [SLA](http://azure.microsoft.com/support/legal/sla/).

## <a name="what-is-the-new-vcore-based-purchasing-model-for-azure-sql-database"></a>Co to jest nowy oparty na rdzeniach wirtualnych zakupu model usługi Azure SQL Database?

Nowy model zakupów stanowi dodatek do już istniejącego modelu bazującego na jednostkach DTU. Model oparty na rdzeniach wirtualnych jest przeznaczona do zapewnić klientom elastyczność, kontrola, przejrzystości i prostą metodę tłumaczenia wymagań obciążenia w chmurze lokalnie. Umożliwia także klienci mogą skalować swoje rsources zasobów obliczeniowych i magazynu, na podstawie ich potrzeby związane z obciążeniem. Pojedynczą bazę danych i opcje puli elastycznej za pomocą modelu rdzenia wirtualnego są również kwalifikuje się do 30 procent oszczędności w przypadku [korzyści z używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Zobacz [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md) i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md) Aby uzyskać więcej informacji. 

## <a name="what-is-a-vcore"></a>Co to jest rdzeń wirtualny? 
Rdzeń wirtualny reprezentuje logiczny Procesor CPU z opcją wyboru generacji sprzętu. Logiczne procesory CPU 4 generacji wykorzystują procesory Intel E5-2673 v3 procesorów 2,4 GHz (Haswell) i logiczne procesory CPU 5 ogólnego są oparte na Intel E5-2673 v4 (broadwell z zegarem) 2.3 GHz procesorów.

## <a name="is-moving-to-the-vcore-based-model-required"></a>Czy przejście do modelu opartego na rdzeniach wirtualnych jest wymagane?
Nie, wprowadzenie model oparty na rdzeniach wirtualnych w puli elastycznej i opcje wdrażania pojedynczej bazy danych odzwierciedla naszego zobowiązania do szeroki wybór i elastyczność. Klienci chcący nadal korzystać z modelu opartego na jednostkach DTU, nie trzeba podejmować żadnych działań, z tym ogłoszeniem i ich środowisko pracy i rozliczenia pozostaną niezmienione. 

W wielu przypadkach aplikacje mogą korzystać z prostoty wstępnie skonfigurowanego pakietu zasobów. W związku z tym firma Microsoft nadal oferuje i obsługuje te opcje oparte na jednostkach DTU dla naszych klientów. Jeśli są używane i spełnia Twoje wymagania biznesowe, powinny być nadal w tym celu.

Modele oparte na jednostkach DTU i rdzeniach wirtualnych będą nadal istnieć obok siebie. Udostępniamy model oparty na rdzeniach wirtualnych w odpowiedzi na prośby klientów większą przejrzystość w zakresie ich zasobów bazy danych oraz możliwość oddzielnie skalować swoje zasoby obliczeniowe i magazynowe. Model oparty na rdzeniach wirtualnych umożliwia również dodatkowe oszczędności dla klientów z aktywnym pakietem Software Assurance za pośrednictwem korzyści użycia hybrydowego platformy Azure dla programu SQL Server.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model"></a>Jak wybrać między vs oparty na jednostkach DTU model zakupu modelu zakupu opartego na rdzeniach wirtualnych? 
Jednostka transmisji danych (DTU) jest oparta na kombinacji pomiarów procesora, pamięci, odczytów i zapisów. Poziomy wydajności oparte na jednostkach DTU reprezentują wstępnie skonfigurowane pakiety zasobów dostosowane do różnych poziomów wydajności aplikacji. Klienci, którzy nie chcesz martwić się o podstawowe zasoby i preferujesz prostotę wstępnie skonfigurowanego pakietu podczas płacenia stałą każdego miesiąca może się okazać modelu opartego na jednostkach DTU bardziej odpowiednie do ich potrzeb. Jednak w przypadku klientów, którzy potrzebujesz szczegółowego wglądu w dostępne zasoby lub chcesz skalować je niezależnie, aby osiągnąć optymalną wydajność, model oparty na rdzeniach wirtualnych będzie najlepszym wyborem.  Ponadto jeśli klient ma aktywne Software Assurance (SA) dla programu SQL Server, ich można korzystać z ich istniejących inwestycji i Zaoszczędź do 30% z [korzyści z używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  Opcje w ramach zakupu modeli zapewniają korzyści w pełni zarządzanej usługi takie jak automatyczne kopie zapasowe, aktualizacje oprogramowania i poprawek. 

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>Co to jest korzyść użycia hybrydowego platformy Azure dla programu SQL Server? 
[Korzyści z używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) pomaga zmaksymalizować wykorzystanie obecnych inwestycji licencjonowania i przyspieszyć ich migrację do chmury. Azure korzyść użycia hybrydowego dla programu SQL Server jest oparta na platformie Azure korzyści, która umożliwia użycie licencji programu SQL Server z pakietem Software Assurance zapłacić obniżonych stawek ("stawka podstawowa") w bazie danych SQL. Korzyść użycia hybrydowego platformy Azure dla programu SQL Server jest dostępna w publicznej wersji zapoznawczej modelu zakupu opartego na rdzeniach wirtualnych dla pojedynczych baz danych SQL Database i pul elastycznych. Można stosować tej korzyści, nawet jeśli jednostka SKU jest aktywna, ale należy pamiętać, że podstawowa stawka jest stosowana od momentu wybrania jej w witrynie Azure portal. Żadne środki nie będą naliczane wstecznie.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Czy istnieją praw produktów podwójnego zastosowania dzięki korzyści użycia hybrydowego platformy Azure dla programu SQL Server?
Masz prawa do używania dwóch licencji na upewnij się, że migracja działają bezproblemowo na 180 dni. Po tym okresie 180-dniowy licencję programu SQL Server można używać tylko w chmurze w usłudze SQL Database i nie ma podwójnego zastosowania praw w środowisku lokalnym i w chmurze.

## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Czym różni się korzyści użycia hybrydowego platformy Azure dla programu SQL Server z przenoszenia licencji?
Obecnie firma Microsoft oferuje korzyści przenoszenia licencji dla klientów programu SQL Server z pakietem Software Assurance, która umożliwia ponowne przypisanie licencji do udostępnionego serwerów innych firm. Ta korzyść może służyć w modelu IaaS platformy Azure i usługi EC2 usług AWS.
Korzyść użycia hybrydowego platformy Azure dla programu SQL Server różni się od przenoszenia licencji w dwóch podstawowych obszarach:
- Zapewnia korzyści ekonomiczne przenoszenie wysoce zwirtualizowanych obciążeń na platformę Azure. SQL EE klienci mogą uzyskać 4 rdzenie na platformie Azure w jednostki SKU ogólnego przeznaczenia na każdy rdzeń posiadanych on-premises dla wysoko zwirtualizowane aplikacje. Przenośność licencji nie zezwala na żadnych specjalnych oszczędności przenoszenia zwirtualizowanych obciążeń do chmury.
- Zapewnia miejsce docelowe PaaS na platformie Azure (wystąpienie zarządzane SQL Database), wysoce zgodną z programu SQL Server w środowisku lokalnym

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Co to są określone prawa korzyść użycia hybrydowego platformy Azure dla programu SQL Server?
Baza danych SQL, klienci będą mogli korzystać następujące prawa, które są skojarzone z korzyścią użycia hybrydowego platformy Azure dla programu SQL Server:

|Zużycie licencji|Jaki jest korzyść użycia hybrydowego platformy Azure dla programu SQL Server uzyskać Ciebie?|
|---|---|
|SQL Server Enterprise Edition core klientom SA|<li>Płacić można stawka podstawowa, ogólnego przeznaczenia lub jednostki SKU krytycznej biznesowych</li><br><li>1 rdzeń w środowisku lokalnym = 4 rdzenie w jednostki SKU ogólnego przeznaczenia</li><br><li>1 rdzeń w środowisku lokalnym = 1 rdzeń w jednostce SKU krytycznej biznesowych</li>|
|SQL Server Standard Edition core klientom SA|<li>Można tylko płacić stawka podstawowa według jednostki SKU ogólnego przeznaczenia</li><br><li>1 rdzeń w środowisku lokalnym = 1 rdzeń w jednostki SKU ogólnego przeznaczenia</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>Model oparty na rdzeniach wirtualnych jest dostępny do wystąpienia zarządzanego usługi SQL Database?
[Wystąpienie zarządzane](sql-database-managed-instance.md) jest dostępna tylko w przypadku modelu opartego na rdzeniach wirtualnych. Aby uzyskać więcej informacji, zobacz też [SQL Database, cennik](https://azure.microsoft.com/pricing/details/sql-database/managed/). 

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>Czy koszt zasobów obliczeniowych i magazynu zależy od warstwy usług, które można wybrać?
Koszt obliczeń odpowiada moc obliczeniową całkowita, które jest obsługiwane dla aplikacji. W warstwie krytyczne dla działania firmy firma Microsoft automatycznie Przydziel co najmniej 3 replik Always ON. Aby uwzględnić ten dodatkowy przydział zasobów obliczeniowych, cena — rdzeń wirtualny jest około 2.7 x wyżej w krytyczne dla działania firmy. Z tego samego powodu wyższych cen magazynu za GB w warstwie krytyczne dla działania firmy odzwierciedla wysokiej We/Wy i małe opóźnienia magazyn SSD. W tym samym czasie koszt magazynu kopii zapasowych nie jest inny, ponieważ w obu przypadkach używamy klasę magazynu w warstwie standardowa.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>Jak jest naliczana opłata magazynu — na podstawie co można skonfigurować z wyprzedzeniem lub baza danych używa?
Różnych typów pamięci masowej są rozliczane w różny sposób. Do przechowywania danych opłaty są naliczane za aprowizowanego magazynu na podstawie maksymalny rozmiar bazy danych lub puli, którą wybierzesz. Koszt nie ulega zmianie, chyba że zmniejszyć lub zwiększyć maksymalną tego. Magazyn kopii zapasowych jest skojarzony z automatycznie tworzonymi kopiami zapasowymi Twojego wystąpienia. Wydłużenie okresu przechowywania kopii zapasowych zwiększa ilość przestrzeni w magazynie kopii zapasowych używanej przez wystąpienie. Magazyn kopii zapasowych jest bezpłatny do poziomu 100 procent łącznie zaprowizowanej pojemności magazynu serwera. Dodatkowe użycie magazynu kopii zapasowych jest rozliczane w GB na miesiąc. Jeśli na przykład magazyn bazy danych ma rozmiar 100 GB, otrzymasz bezpłatnie 100 GB magazynu kopii zapasowych. Ale jeśli kopia zapasowa ma rozmiar 110 GB, płacisz za dodatkowe 10 GB.

W magazynie kopii zapasowej pojedynczej bazy danych opłaty są naliczane proporcjonalnie do magazynowania, która została przydzielona do kopii zapasowych bazy danych o wielkości bazy danych. W przypadku magazynu kopii zapasowych elastycznej puli jest naliczana proporcjonalnie do magazynowania, która została przydzielona do kopii zapasowych bazy danych wszystkich baz danych w puli minus danych maksymalny rozmiar puli elastycznej. Wszelkie wzrost rozmiaru bazy danych lub puli elastycznej lub zwiększenie szybkości transakcji, wymaga więcej pamięci masowej, a zatem zwiększa opłata za magazyn kopii zapasowych.  Wraz ze zwiększeniem rozmiaru maksymalnego danych to nowa kwota jest odejmowany od rozmiar magazynu kopii zapasowych rozliczane.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>Jak wybrać odpowiednie jednostki SKU, podczas konwersji istniejącej bazy danych do nowych warstw usług? 
Istniejących aplikacji SQL Database przy użyciu modelu opartego na jednostkach DTU w warstwie usług ogólnego przeznaczenia jest porównywalna z warstwą standardowa. Krytyczne dla działania firmy warstwy usług jest porównywalna z warstwy Premium. W obu przypadkach należy przydzielić co najmniej 1 rdzeń wirtualny dla każdego 100 DTU, której aplikacja korzysta z modelu opartego na jednostkach DTU.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-performance-levels-compatible-with-all-existing-service-level-objectives-slos"></a>Nowe warstwy usług oparte na rdzeniach wirtualnych, są one zgodne z wszystkich istniejących celów poziomu usług (slo) poziomów wydajności?
Nowe warstwy usług oparte na rdzeniach wirtualnych oferuje opcje wydajnościowe porównywalny dla wszystkich elastycznych pul i baz danych przy użyciu 100 jednostek Dtu lub więcej.  Będziemy nadal dodawać więcej cele slo wraz z upływem czasu, aby pomieścić sub 100 jednostek DTU obciążeń.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>Czy istnieją wszystkie bazy danych różnicach w funkcjonalności między istniejących warstw usługi oparte na jednostkach DTU i nowy oparty na rdzeniach wirtualnych? 
Nowe warstwy usługi obsługuje nadzbiór funkcji, które są dostępne w bieżących ofert oparty na jednostkach DTU. Dodatkowe funkcje obejmują zestaw dodatkowych dynamicznych widoków zarządzania (DMV) i opcje konfiguracji dodatkowych zasobów. 

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>Jeśli Moja baza danych jest zależne od Procesora CPU i nie używa dużej ilości pamięci masowej, można zwiększyć zasoby obliczeniowe bez konieczności płacenia za magazyn dodatkowy?
Tak, można wybrać niezależnie poziomu obliczeniowego musi i zachować magazynu bez zmian w aplikacji. Magazyn można ustawić najmniejsza 32GB. 

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>Nowe warstwy usługi opartej na rdzeniach wirtualnych obejmie punktu w czasie przywracania (Odzyskiwanie) jako już dziś 35 dni? 
Przechowywanie kopii zapasowych można skonfigurować dla Odzyskiwanie od 7 do 35 dni. Magazyn kopii zapasowych jest naliczana oddzielnie w zależności od użycia rzeczywisty magazyn w przypadku przekroczenia równy rozmiarowi danych maksymalna wielkość magazynu. W wersji zapoznawczej domyślnie okres przechowywania Odzyskiwanie jest ustawiona na 7 dni. W wielu przypadkach rozmiar maksymalny danych jest wystarczająca do przechowywania kopie zapasowe z 7 dni.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>Dlaczego chcesz zezwolić wyboru generacji sprzętu do obliczeń?
Naszym celem jest umożliwienie maksymalną elastyczność, tak, aby można było wybrać konfigurację wydajności, który dokładnie pasuje do potrzeb aplikacji. 4. generacji sprzętu oferuje znacznie więcej pamięci na rdzeń wirtualny. 5. generacji sprzętu pozwala jednak skalować zasoby obliczeniowe, które znacznie wyższa. Aby uzyskać więcej informacji, zobacz [model zakupu rdzenia wirtualnego](sql-database-service-tiers-vcore.md)

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Czy muszę przenieść aplikację do trybu offline można przekonwertować z bazy danych oparty na jednostkach DTU do warstwy usług oparte na rdzeniach wirtualnych? 
Nowe warstwy usługi oferują prostą metodę konwersji online, która jest podobna do istniejącego procesu uaktualniania baz danych od warstwy Standard do Premium i odwrotnie. Ta konwersja może być inicjowane przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, T-SQL lub interfejsu API REST. Zobacz [zarządzanie pojedynczymi bazami danych](sql-database-single-database-scale.md) i [Zarządzanie elastycznymi pulami](sql-database-elastic-pool.md).

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>Czy mogę przekonwertować bazę danych z warstwy usług oparte na rdzeniach wirtualnych się na konto oparte na jednostkach DTU? 
Tak, można z łatwością przekształcić bazę danych do dowolnego celu obsługiwanych wydajności przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, T-SQL lub interfejsu API REST. Zobacz [zarządzanie pojedynczymi bazami danych](sql-database-single-database-scale.md) i [Zarządzanie elastycznymi pulami](sql-database-elastic-pool.md).

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>Czy mogę przełączać się między warstwami usług ogólnego przeznaczenia i krytyczne dla działania firmy? 
Tak, z pewnymi ograniczeniami. Lokalizacji docelowej jednostki SKU musi spełniać maksymalna bazy danych lub rozmiaru puli elastycznej, skonfigurowanego dla istniejącego wdrożenia. Jeśli używasz [korzyści z używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md), jednostki SKU krytycznej firmy jest dostępna tylko dla klientów z licencjami Enterprise Edition. Tylko w przypadku klientów, którzy przeprowadzili migracji ze środowiska lokalnego do ogólnego przeznaczenia, za pomocą korzyści użycia hybrydowego platformy Azure dla programu SQL Server z licencjami Enterprise Edition można uaktualnić do krytyczne dla działania firmy. Aby uzyskać szczegółowe informacje, zobacz [co to są określone prawa używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md)?

Ta konwersja nie powoduje przestojów i mogą być inicjowane przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia platformy Azure, T-SQL lub interfejsu API REST. Zobacz [zarządzanie pojedynczymi bazami danych](sql-database-single-database-scale.md) i [Zarządzanie elastycznymi pulami](sql-database-elastic-pool.md).

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>Korzystam z bazy danych Premium RS nie będzie ogólnie dostępna — można uaktualnić ją do nowej warstwy i osiągnięcia korzyści podobnych ceny do wydajności?
Ponieważ model rdzenia wirtualnego umożliwia niezależną kontrolę nad ilość zainicjowanych zasobów obliczeniowych i magazynu, można efektywniej zarządzać związane z nimi koszty, ułatwiając atrakcyjny miejsce docelowe dla baz danych Premium RS. Ponadto [korzyści z używania hybrydowej platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) zapewnia znaczny rabat w wysokości, gdy model oparty na rdzeniach wirtualnych jest używany. 

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>Jak często można dostosować zasoby na pulę
Tak często, jak chcesz. Zobacz [Zarządzanie elastycznymi pulami](sql-database-elastic-pool.md).

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Jak długo trwa zmiana poziomu wydajności lub Warstwa usługi pojedynczej bazy danych lub przenosić bazy danych do i z puli elastycznej?
Zmiana warstwy usługi bazy danych i przeniesienie do i z puli wymaga bazy danych do skopiowania na platformie jako operacji w tle. Zmiana warstwy usługi może potrwać od kilku minut do kilku godzin w zależności od rozmiaru bazy danych. W obu przypadkach bazy danych pozostają online i dostępne podczas przenoszenia. Aby uzyskać więcej informacji o zmienianiu pojedynczych baz danych, zobacz [Zmienianie warstwy usługi bazy danych](sql-database-service-tiers-dtu.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Kiedy należy używać pojedynczej bazy danych i elastycznych baz danych?
Ogólnie rzecz biorąc, pule elastyczne są przeznaczone dla typowej [oprogramowanie jako usługa (SaaS) aplikacji wzorca](sql-database-design-patterns-multi-tenancy-saas-applications.md), w którym występuje jedna baza danych dla dzierżawcy lub klienta. Kupowanie poszczególnych baz danych i przydzielanie dla każdej z nich nadmiernej ilości zasobów pod kątem zmiennego i szczytowego zapotrzebowania często nie jest ekonomiczne. Zarządzanie zbiorczą wydajnością puli pul i baz danych automatyczne skalowanie w górę i w dół. Inteligentnego aparatu platformy Azure zaleca puli baz danych, gdy wzorzec użycia na to pozwala. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące puli elastycznej](sql-database-elastic-pool.md).

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Jak użycie jest SQL Database przy użyciu modelu zakupu opartego na jednostkach DTU pokazywane na rachunku?
W ramach bazy danych SQL na podstawie przewidywalnej stawki godzinowej na podstawie [zakupem modelu](sql-database-service-tiers-dtu.md). Rzeczywiste użycie jest obliczane i proporcjonalnie co godzinę, dlatego na rachunku mogą być widoczne części godziny. Na przykład jeśli baza danych istnieje przez 12 godzin w miesiącu, na rachunku pokazuje użycie pół dnia. 

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Co zrobić, jeśli pojedynczej bazy danych jest aktywna krócej niż godzinę lub korzystanie z wyższej warstwy usługi dla mniej niż godzinę?
Opłata jest naliczana za każdy godzinę istnienia bazy danych przy użyciu najwyższej warstwy usługi i poziom wydajności zastosowany w ciągu tej godziny, niezależnie od użycia lub tego, czy baza danych była Aktywna krócej niż godzinę. Po utworzeniu pojedynczej bazy danych i usuniesz ją 5 minut później rachunku odzwierciedla za godzinę korzystania z jednej bazy danych. 

Przykłady:

* Jeśli utworzysz bazę danych Basic i natychmiast uaktualnisz ją do wersji Standard S1, są naliczane zgodnie ze stawką Standard S1 za pierwszą godzinę.
* W przypadku uaktualnienia bazy danych Basic do wersji Premium o 10:00 a zakończyło się ono o godzinie 1:35 następnego dnia, opłaty są naliczane zgodnie ze stawką — wersja Premium, zaczynając od godziny 1:00 
* Jeśli obniżanie poziomu bazy danych z wersji Premium do warstwy podstawowa o godzinie 11:00 zakończyło się o 2:15:00, a następnie bazy danych jest naliczana stawka Premium do 3:00 w dniu, po upływie którego jest naliczana stawek za podstawowy.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Jak użycie puli elastycznej przy użyciu modelu zakupu opartego na jednostkach DTU widoczne na rachunku?
Pula elastyczna opłaty za show się na rachunku jako elastycznych jednostek Dtu (Edtu) lub rdzeni wirtualnych i magazynu z przyrostem wyświetlane na [stronie cennika](https://azure.microsoft.com/pricing/details/sql-database/). Brak opłat za bazę danych dla pul elastycznych. Opłaty są naliczane za każdą godzinę istnienia puli o najwyższej liczby jednostek eDTU lub rdzeni wirtualnych, niezależnie od użycia lub tego, czy pula był aktywny przez mniej niż godzinę. 

Oparte na jednostkach DTU: zakupu przykłady modelu:

* Po utworzeniu puli elastycznej standardowa z 200 Edtu o godzinie 11:18 Dodawanie pięć baz danych do puli, opłaty są naliczane za 200 jednostek Edtu przez całą godzinę, począwszy od o godzinie 11 za pomocą pozostałą część dnia.
* W dniu 2, 5 o godzinie: 05 1 bazy danych rozpocznie korzystanie z 50 jednostek Edtu i posiada stały za pośrednictwem dnia. Bazy danych w 2 – 5 zmieniają się między 0 a 80 jednostek Edtu. W ciągu dnia należy dodać pięć innych baz danych korzystających z różnych jednostek Edtu przez cały dzień. 2 dni jest pełny dzień naliczane 200 eDTU. 
* W dniu 3, 5, o godzinie Możesz dodać innego 15 baz danych. Użycie bazy danych zwiększa się przez cały dzień do punktu, możesz wybrać, aby zwiększyć liczbę jednostek Edtu dla puli z 200 do 400 o godzinie 8:05 Opłaty za na poziomie 200 jednostek eDTU obowiązuje były do 20: 00 i zwiększyć do 400 jednostek Edtu za pozostałe cztery godziny. 

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>Jak są rozliczane na podstawie jednostek DTU puli elastycznej zakupem modelu
Pule elastyczne są naliczane opłaty za następujące cechy:

* Pula elastyczna jest rozliczane po jego utworzeniu, nawet wtedy, gdy istnieją żadnych baz danych w puli.
* Pula elastyczna jest rozliczana co godzinę. Jest to taką samą częstotliwością pomiarów, jak w przypadku poziomów wydajności dla pojedynczych baz danych.
* Jeśli zmiany rozmiaru puli elastycznej następnie puli nie jest rozliczane według nowych ilość zasobów do momentu ukończenia operacji zmiany rozmiaru. To jest zgodna z tym samym wzorcem podczas zmieniania poziomu wydajności pojedynczej bazy danych.
* Cena elastycznej puli opiera się na zasoby w puli. Cena elastycznej puli jest niezależna od liczby i użycie elastycznych baz danych w nim.

Aby uzyskać więcej informacji, zobacz [cennik usługi SQL Database](https://azure.microsoft.com/pricing/details/sql-database/), [modelu zakupu opartego na jednostkach DTU](sql-database-service-tiers-dtu.md), i [modelu zakupu opartego na rdzeniach wirtualnych](sql-database-service-tiers-vcore.md).

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>Jak oparty na rdzeniach wirtualnych użycie jest pokazywane na rachunku? 
W modelu opartego na rdzeniach wirtualnych za usługę jest naliczana opłata przewidywalnej stawki godzinowej na podstawie warstwy usługi, zaprowizowanych zasobów obliczeniowych w rdzeniach wirtualnych, zasoby zaprowizowanej pojemności magazynu w GB na miesiąc i używane magazynu kopii zapasowych. Jeśli magazyn kopii zapasowych przekracza łączny rozmiar bazy danych (czyli 100% rozmiaru bazy danych), istnieją dodatkowe opłaty. godziny pracy rdzeni wirtualnych, skonfigurowany Magazyn bazy danych, wykorzystanych operacji We/Wy i magazynu kopii zapasowych są wyraźnie oznaczone na rachunku, ułatwiając szczegóły zasobów, które były używane. Magazyn kopii zapasowych do 100% maksymalny rozmiar bazy danych jest uwzględniony, po przekroczeniu którego są rozliczane w zużytych GB w miesiącu.

Na przykład:
- Jeśli bazy danych SQL istnieje przez 12 godzin w miesiącu, rachunek pokazuje użycie przez 12 godzin (rdzeń wirtualny). Jeśli baza danych SQL zaaprowizowała dodatkowe 100 GB miejsca, rachunek pokazuje użycie magazynu w jednostkach GB/miesiąc proporcjonalnie rozliczone godzinowo oraz liczba wykorzystanych w miesiącu.
- Jeśli bazy danych SQL jest aktywna przez mniej niż godzinę, stosowana jest stawka za każdą godzinę istnienia bazy danych przy użyciu najwyższej wybranej warstwy usługi, aprowizowanego magazynu oraz operacji wejścia/wyjścia zastosowanych podczas tej godziny, niezależnie od użycia lub tego, czy baza danych była aktywny przez mniej niż godziny.
- Jeśli utworzysz wystąpienie zarządzane i usuniesz je 5 minut później, opłata zostanie naliczona za jedną godzinę korzystania z bazy danych.
- Jeśli utworzysz wystąpienie zarządzane w warstwie Przeznaczenie ogólne z ośmioma rdzeniami wirtualnymi, a następnie od razu zmodernizujesz je do 16 rdzeni wirtualnych, opłata za pierwszą godzinę zostanie naliczona na podstawie stawki za 16 rdzeni wirtualnych.

> [!NOTE]
> Przez ograniczony czas tworzenia kopii zapasowej opłaty za magazyn i we/wy są bezpłatne.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Jak odbywa się korzystanie z aktywnej replikacji geograficznej w puli elastycznej wyświetlane są na rachunku?
W przeciwieństwie do pojedynczych baz danych przy użyciu [aktywnej replikacji geograficznej](sql-database-geo-replication-overview.md) dzięki elastycznym bazom danych nie ma bezpośredni wpływ rozliczeń.  Opłaty są naliczane tylko dla zasobów aprowizowanych dla poszczególnych pul (puli podstawowego i pomocniczego puli)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>W jaki sposób funkcja inspekcji będzie obciążać mój rachunek?
Inspekcja jest wbudowana w usługę SQL Database, które nie wymagają ponoszenia dodatkowych kosztów i jest dostępny w wszystkich warstwach usługi. Jednak do przechowywania dzienników inspekcji inspekcji używa funkcji, które konto usługi Azure Storage i opłaty za tabele i kolejki w usłudze Azure Storage są stosowane na podstawie rozmiaru dziennika inspekcji.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Jak zresetować hasło administratora serwera?
W [witryny Azure portal](https://portal.azure.com), kliknij przycisk **serwerów SQL**, wybierz serwer z listy, a następnie kliknij przycisk **resetowania hasła**.

## <a name="how-do-i-manage-databases-and-logins"></a>Jak zarządzać bazami danych i logowaniami
Zobacz [Zarządzanie bazami danych i logowaniami](sql-database-manage-logins.md). 

> [!NOTE]
> Nie można zmienić nazwy konta administratora serwera, po jego utworzeniu.

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Jak upewnić się, że tylko adresów IP autoryzowanych mogą uzyskać dostęp do serwera?
Zobacz [porady: Konfigurowanie ustawień zapory w bazie danych SQL](sql-database-configure-firewall-settings.md).

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Co to jest opóźnienie replikacji oczekiwane, podczas replikacji geograficznej bazy danych między dwoma regionami w ramach tej samej lokalizacji geograficznej z platformy Azure?
Firma Microsoft obecnie obsługują RPO pięć sekund i została opóźnienie replikacji mniejsza, gdy pomocnicza geograficzna jest hostowany na platformie Azure zalecane sparowanym regionie i tej samej warstwie usługi.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Co to jest opóźnienie replikacji oczekiwany po utworzeniu pomocniczej geograficznej w tym samym regionie co podstawowa baza danych?
Na podstawie danych empiryczne nie ma zbyt dużo różnią się wewnątrz regionu i opóźnienie replikacji między regionami przy Azure zalecane jest używany w sparowanym regionie. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>W przypadku awarii sieci między dwoma regionami, jak Logika ponawiania działa po skonfigurowaniu replikacji geograficznej?
W przypadku rozłączenia, firma Microsoft ponów próbę wykonania co 10 sekund, aby ponownie ustanowić połączenia.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Co można zrobić, aby zagwarantować, że krytycznych zmian w podstawowej bazie danych są replikowane?
Pomocnicza geograficzna jest repliką async i firma Microsoft nie należy go przechowywać w pełnej synchronizacji z podstawowym. Jednak firma Microsoft zapewnia metodę, aby wymusić synchronizację, aby upewnić się, replikacji krytycznej zmian (na przykład, aktualizowania haseł). Wymuszone synchronizacji ma wpływ na wydajność, ponieważ blokuje wątek wywołujący, aż wszystkie zatwierdzone transakcje są replikowane. Aby uzyskać więcej informacji, zobacz [operacja sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Jakie narzędzia są dostępne dla monitorowania opóźnienie replikacji między podstawowej bazy danych i pomocniczej geograficznej?
Możemy ujawnić opóźnienie replikacji w czasie rzeczywistym pomiędzy podstawowej bazy danych i pomocnicza geograficzna za pośrednictwem DMV. Aby uzyskać więcej informacji, zobacz [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Aby przenieść bazę danych do innego serwera w tej samej subskrypcji
W [witryny Azure portal](https://portal.azure.com), kliknij przycisk **baz danych SQL**, wybierz bazę danych z listy, a następnie kliknij **kopiowania**. Zobacz [skopiować bazę danych Azure SQL database](sql-database-copy.md) Aby uzyskać więcej szczegółów.

## <a name="to-move-a-database-between-subscriptions"></a>Przenoszenie bazy danych między subskrypcjami
W [witryny Azure portal](https://portal.azure.com), kliknij przycisk **serwerów SQL** a następnie wybierz serwer, który hostuje bazę danych z listy. Kliknij przycisk **przenieść**, a następnie wybierz zasoby do przeniesienia oraz subskrypcję, której chcesz przenieść.

