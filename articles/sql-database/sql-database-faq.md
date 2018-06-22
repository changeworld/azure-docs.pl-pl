---
title: Baza danych Azure SQL — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na typowe pytania klientów poproś o baz danych w chmurze i baza danych SQL Azure, system zarządzania relacyjnymi bazami danych firmy Microsoft (RDBMS) oraz bazy danych jako usługa w chmurze.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 2aff7d98fde50c89e637572437a7c572911bce76
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309133"
---
# <a name="sql-database-faq"></a>SQL Database — często zadawane pytania

## <a name="what-is-the-current-version-of-sql-database"></a>Co to jest bieżąca wersja bazy danych SQL?
Bieżąca wersja bazy danych SQL jest w wersji 12. W wersji V11 została wycofana.

## <a name="what-is-the-sla-for-sql-database"></a>Co to jest umowy SLA dla bazy danych SQL?
Gwarantujemy 99,99% czasu, istnieje łączność między bazy danych SQL Microsoft Azure i naszych bramą internetową niezależnie od warstwę usług. Aby uzyskać więcej informacji, zobacz [SLA](http://azure.microsoft.com/support/legal/sla/).

## <a name="what-is-the-new-vcore-based-purchasing-model-preview-for-azure-sql-database"></a>Co to jest nowe na podstawie vCore model kupna (wersja zapoznawcza) w bazie danych SQL Azure?

Nowy model zakupów stanowi dodatek do już istniejącego modelu bazującego na jednostkach DTU. VCore na podstawie modelu zaprojektowano w celu zapewniają elastyczność klientom, w formancie, przezroczystość i łatwe do tłumaczenia lokalne wymagania obciążenia w chmurze. Umożliwia klientom skalowania ich rsources obliczeniowej i pamięci masowej, zależnie od potrzeb obciążenia. Pojedynczej bazy danych i opcje puli elastycznej za pomocą modelu vCore również kwalifikują się do się do 30 procent oszczędności za pomocą [korzyści Użyj hybrydowe platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Zobacz [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md) i [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md) Aby uzyskać więcej informacji. 

## <a name="what-is-a-vcore"></a>Co to jest rdzeń wirtualny? 
Wirtualne podstawowe reprezentuje Procesora logicznego oferowany z opcją wybór między generacje sprzętu. Gen 4 procesory logiczne są oparte na Intel E5-2673 v3 procesorów 2,4 GHz (Haswell) i procesorów logicznych Gen 5 są oparte na Intel E5-2673 v4 procesorów 2.3 GHz (Broadwell).

## <a name="is-moving-to-the-vcore-based-model-required"></a>Czy przejście do modelu opartego na rdzeniach wirtualnych jest wymagane?
Nie, wprowadzenie vCore na podstawie modelu do puli elastycznej i opcji wdrażania pojedynczej bazy danych odzwierciedla nasze zobowiązania szeroki wybór i elastyczność. Aby kontynuować korzystanie z modelu na podstawie jednostek dtu w warstwie klientów, nie trzeba wykonywać żadnych czynności to zawiadomienie i ich obsługi i rozliczeń pozostaną niezmienione. 

W wielu przypadkach aplikacje mogą korzystać z uproszczenia wstępnie skonfigurowane pakietu zasobów. W związku z tym w dalszym oferować i obsługi tych opcji na podstawie jednostek DTU dla naszych klientów. Jeśli są używane i spełnia wymagania biznesowe, powinno być kontynuowane w tym celu.

Modele oparte na jednostkach DTU i rdzeniach wirtualnych będą nadal istnieć obok siebie. Udostępnieniem vCore na podstawie modelu w odpowiedzi na żądania klientów więcej przezroczystości wokół ich zasobów bazy danych i możliwość osobnego skalowania zasobów obliczeniowych i przestrzeni dyskowej. VCore na podstawie modelu umożliwia również dodatkowe oszczędności w przypadku klientów z active Software Assurance korzyści hybrydowe platformy Azure dla programu SQL Server.

## <a name="how-should-i-choose-between-the-dtu-based-purchasing-model-vs-the-vcore-based-purchasing-model-preview"></a>Jak należy wybrać między vs na podstawie jednostek dtu w warstwie model kupna na podstawie vCore model kupna (wersja zapoznawcza)? 
Jednostka transmisji danych (DTU) jest oparta na kombinacji pomiarów procesora, pamięci, odczytów i zapisów. Poziomy wydajności oparte na jednostkach DTU reprezentują wstępnie skonfigurowane pakiety zasobów dostosowane do różnych poziomów wydajności aplikacji. Klienci, którzy nie chcą martwić zasobów i preferowanie prostotę wstępnie skonfigurowane pakietu podczas płatności stałą każdego miesiąca może znaleźć modelu na podstawie jednostek dtu w warstwie bardziej odpowiednie do potrzeb. Jednak dla klientów, którzy muszą uzyskać lepszy wgląd w zasobów lub musi przebiegać proces skalowania ich niezależnie w celu osiągnięcia optymalnej wydajności, na podstawie vCore modelu będzie najlepszym rozwiązaniem.  Ponadto, jeśli klient ma aktywne Software Assurance (SA) dla programu SQL Server, mogą korzystać z dotychczasowych inwestycji i Zapisz do 30% z [korzyści Użyj hybrydowe platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  W ramach każdego z zakupem modeli zapewniają korzyści pełni zarządzaną usługę, takich jak automatyczne kopie zapasowe, aktualizacji i poprawek. 

## <a name="what-is-the-azure-hybrid-benefit-for-sql-server"></a>Jakie są zalety hybrydowe platformy Azure dla programu SQL Server? 
[Korzyści Użyj hybrydowe platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) pomaga zmaksymalizowania wykorzystania wartości z bieżącego inwestycji licencjonowania i przyspieszyć ich migrację do chmury. Korzyści hybrydowe platformy Azure dla programu SQL Server jest korzyści bazujących na platformie Azure, która pozwala na użycie licencji programu SQL Server z Software Assurance zapłacenia obniżonej ("częstotliwość podstawową") w bazie danych SQL. Azure korzyści hybrydowego dla programu SQL Server jest dostępny na podstawie vCore zakupów modelu (wersja zapoznawcza) dla pojedynczych baz danych SQL Database i pul elastycznych w publicznej wersji zapoznawczej. Można zastosować takich korzyści, nawet jeśli jednostka SKU jest aktywny, ale należy pamiętać, że podstawowa stawka jest stosowana od czasu, wybierz je w portalu Azure. Żadne środki nie będą naliczane wstecznie.

## <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Dla programu SQL Server są prawa podwójnego zastosowania z korzyści hybrydowego Azure?
Masz prawa do używania dwóch licencji do upewnij się, że migracje działają doskonale na 180 dni. Po 180-dniowy okres, licencji programu SQL Server można używać tylko w chmurze w bazie danych SQL i nie ma podwójną wykorzystanie praw lokalnych i w chmurze.


## <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Jaka jest różnica korzyści hybrydowe platformy Azure dla programu SQL Server z przenośność licencji?
Obecnie oferowane korzyści wynikające z mobilności licencji do klientów programu SQL Server z Software Assurance, która umożliwia ponowne przypisanie licencji do udostępnionego serwerów innych firm. Świadczenie może służyć IaaS platformy Azure i usług AWS EC2.
Azure korzyści hybrydowego dla programu SQL Server różni się od przenośność licencji w dwóch podstawowych obszarach:
- Zapewnia korzyści ekonomicznych przenoszenia wysokiej zwirtualizowanych obciążeń na platformie Azure. Klienci SQL EE mogą uzyskać 4 rdzeni na platformie Azure w ogólne SKU cel dla każdego core są właścicielami lokalnymi dla dużej zwirtualizowanych aplikacji. Przenośność licencji nie zezwala na wszelkie specjalne oszczędności przenoszenia zwirtualizowanych obciążeń do chmury.
- Zapewnia miejsce docelowe PaaS na platformie Azure (wystąpienia zarządzane bazy danych SQL) jest wysoce zgodną z lokalnej instalacji programu SQL Server

## <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Jakie są określone prawa korzyści hybrydowe platformy Azure dla programu SQL Server?
Baza danych SQL konieczne będzie następujące prawa skojarzone z korzyści hybrydowe platformy Azure dla programu SQL Server:

|Wpływ licencji|Co oznacza korzyści hybrydowe platformy Azure dla programu SQL Server uzyskać możesz?|
|---|---|
|SQL Server Enterprise Edition core klientom SA|<li>Można zwrócić szybkość Base ogólnego przeznaczenia lub biznesowe krytyczne jednostki SKU</li><br><li>1 rdzeń lokalnymi = 4 rdzenie w ogólnego przeznaczenia jednostki SKU</li><br><li>1 rdzeń lokalnymi = 1 rdzeń w jednostce SKU krytyczne biznesowa</li>|
|SQL Server Standard Edition core klientom SA|<li>Można tylko zwrócić szybkość Base na ogólnego przeznaczenia jednostki SKU</li><br><li>1 rdzeń lokalnymi = 1 rdzeń w ogólnego przeznaczenia jednostki SKU</li>|
|||

## <a name="is-the-vcore-based-model-available-to-sql-database-managed-instance"></a>Jest dostępne dla zarządzanych wystąpienia bazy danych SQL na podstawie vCore modelu?
[Zarządzane wystąpienia](sql-database-managed-instance.md) jest dostępna tylko w przypadku vCore na podstawie modelu. Aby uzyskać więcej informacji, zobacz też [bazy danych SQL cennikiem](https://azure.microsoft.com/pricing/details/sql-database/managed/). 

## <a name="does-the-cost-of-compute-and-storage-depend-on-the-service-tier-that-i-choose"></a>Koszt zasobów obliczeniowych i magazynu zależą od warstwy usługi, którą można wybrać?
Koszt obliczeń odzwierciedla wydajności obliczeniowej całkowita, która zostanie zainicjowana dla aplikacji. W warstwie Business krytycznych usług firma Microsoft automatycznie Przydziel co najmniej 3 replik Always ON. Uwzględnienie tego dodatkowego przydziału zasobów obliczeniowych, ceny vCore jest około 2.7 x wyżej w biznesowe krytyczne. Z tego samego powodu wyższej cena pamięci masowej na GB w warstwie Business krytyczne odzwierciedla wysokiej We/Wy i małe opóźnienia magazynu SSD. W tym samym czasie koszt magazynowania kopii zapasowej nie jest inny, ponieważ w obu przypadkach używamy klasę magazynu w warstwie standardowa.

## <a name="how-am-i-charged-for-storage---based-on-what-i-configure-upfront-or-on-what-the-database-uses"></a>Jak m naliczane opłaty za magazyn — na podstawie co skonfigurować wyprzedzeniem lub baza danych używa?
Różne rodzaje magazynu są rozliczane inaczej. Do przechowywania danych są naliczane opłaty za zainicjowanego magazynu oparte na maksymalny rozmiar bazy danych lub puli, którą wybierzesz. Koszt nie zmieniać zmniejszenia lub zwiększenia tej maksymalnej. Magazyn kopii zapasowych jest skojarzony z automatycznie tworzonymi kopiami zapasowymi Twojego wystąpienia. Wydłużenie okresu przechowywania kopii zapasowych zwiększa ilość przestrzeni w magazynie kopii zapasowych używanej przez wystąpienie. Magazyn kopii zapasowych jest bezpłatny do poziomu 100 procent łącznie zaprowizowanej pojemności magazynu serwera. Dodatkowe zużycie magazynu kopii zapasowej jest pobierana w GB miesięcznie. Jeśli na przykład magazyn bazy danych ma rozmiar 100 GB, otrzymasz bezpłatnie 100 GB magazynu kopii zapasowych. Jednak jeśli kopia zapasowa jest 110 GB, płacisz za dodatkowe 10 GB.

W magazynie kopii zapasowej z jednej bazy danych są naliczane na podstawie proporcjonalnie dla magazynu, która została przydzielona do kopii zapasowych bazy danych minus rozmiar bazy danych. W magazynie kopii zapasowej z puli elastycznej są naliczane na podstawie proporcjonalnie dla magazynu, która została przydzielona do kopii zapasowych bazy danych wszystkich baz danych w puli minus maksymalnego rozmiaru danych w puli elastycznej. Wszelkie wzrost rozmiaru bazy danych lub puli elastycznej lub zwiększenie szybkości transakcji wymaga więcej pamięci masowej i w związku z tym zwiększa rachunku magazynu kopii zapasowej.  Zwiększanie maksymalnego rozmiaru danych tej nowej kwoty jest odejmowany od rozmiaru rachunku magazynu kopii zapasowej.

## <a name="how-do-i-select-the-right-sku-when-converting-an-existing-database-to-the-new-service-tiers"></a>Jak wybrać prawo jednostki SKU podczas konwertowania z istniejącej bazy danych na nowe warstwy usług? 
Istniejących aplikacji bazy danych SQL przy użyciu modelu na podstawie DTU warstwy usług ogólnego przeznaczenia jest porównywalna z warstwy standardowa. Warstwy usługi biznesowe krytyczne jest porównywalna z warstwy Premium. W obu przypadkach należy przydzielić co najmniej 1 vCore dla każdego 100 jednostek dtu w warstwie używanych przez aplikację w modelu na podstawie jednostek dtu w warstwie.

## <a name="do-the-new-vcore-based-service-tiers-offer-the-performance-levels-compatible-with-all-existing-service-level-objectives-slos"></a>Nowe warstwy usług opartych na vCore, są one zgodne z wszystkich istniejących celów poziomu usług (slo) poziomów wydajności?
Nowe warstwy usług vCore oferują wszystkie elastycznych pul i baz danych przy użyciu Dtu 100 lub więcej sposobów można porównywać pod względem wydajności.  Firma Microsoft będzie można dodać więcej cele slo wraz z upływem czasu, aby pomieścić sub 100 jednostek dtu w warstwie obciążeń.

## <a name="are-there-any-database-feature-differences-between-the-existing-dtu-based-and-new-vcore-based-service-tiers"></a>Czy istnieją wszystkie bazy danych różnice w funkcjach między istniejących warstw DTU i nowej usługi opartej na vCore? 
Nowe warstwy usług obsługuje podzbiorem funkcji dostępnych w bieżącym oferty na podstawie jednostek dtu w warstwie. Dodatkowe funkcje to zestaw dodatkowych dynamicznych widoków zarządzania (widoków DMV) i opcje konfiguracji dodatkowych zasobów. 

## <a name="if-my-database-is-cpu-bound-and-does-not-use-much-storage-can-i-increase-the-compute-without-paying-for-extra-storage"></a>Jeśli bazy danych jest powiązany z procesora CPU i nie używa najwięcej pamięci masowej, można zwiększyć mocy obliczeniowej bez płatność za dodatkowe miejsce do magazynowania?
Tak, można wybrać niezależnie poziomu obliczeniowej, aplikacja musi i Zachowaj magazynu bez zmian. Magazyn można ustawić możliwie jak 32GB. 

## <a name="will-the-new-vcore-based-tiers-support-point-in-time-restore-pitr-for-35-days-as-today"></a>Zostanie nowych warstw na podstawie vCore obsługuje punktu w czasie przywracania (PITR) jako dzisiaj 35 dni 
Przechowywania kopii zapasowych można skonfigurować dla PITR od 7 do 35 dni. Magazyn kopii zapasowych zostanie naliczona opłata osobno na podstawie użycia magazynu rzeczywista w razie przekroczenia wielkość magazynu równa maksymalnego rozmiaru danych. W wersji zapoznawczej domyślnie PITR okres przechowywania wynosi 7 dni. W wielu przypadkach maksymalnego rozmiaru danych jest wystarczająca do przechowywania kopii zapasowych 7 dni.

## <a name="why-do-you-allow-selection-of-the-hardware-generation-for-compute"></a>Dlaczego umożliwia wybór generowania sprzętu dla obliczania?
Naszym celem jest umożliwienie maksymalną elastyczność, w którym można wybrać konfigurację wydajności, która dokładnie odpowiada potrzebom aplikacji. Powyższej tabeli przedstawiono różnice między Gen4 i Gen5. W szczególności sprzętu Gen4 oferuje znacznie większej ilości pamięci na vCore. Jednak sprzęt Gen5 pozwala na skalowanie w górę zasoby obliczeniowe znacznie wyższa. Chcemy przezroczystego te różnice, dzięki czemu można osiągnąć współczynnik optymalne ceny/wydajności aplikacji.

## <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-database-to-a-vcore-based-service-tier"></a>Należy podjąć Moja aplikacja w trybie offline można przekonwertować z bazy danych programu DTU do warstwy usług opartych na vCore? 
Nowe warstwy usługi oferują prostą metodę konwersji online, która jest podobna do istniejącego procesu uaktualniania baz danych od warstwy Standard do Premium i odwrotnie. Ta konwersja może być inicjowana przy użyciu portalu Azure, programu PowerShell, interfejsu wiersza polecenia Azure, T-SQL lub interfejsu API REST. Zobacz [Zarządzanie pojedynczych baz danych](sql-database-single-database-scale.md) i [pule elastyczne zarządzanie](sql-database-elastic-pool.md).

## <a name="can-i-convert-a-database-from-a-vcore-based-service-tier-to-a-dtu-based-one"></a>Czy mogę przekonwertować bazy danych z warstwy usług opartych na vCore aby stworzyć plik na podstawie jednostek dtu w warstwie 
Tak, można z łatwością przekształcić bazy danych do dowolnego celu obsługiwanych wydajności przy użyciu portalu Azure, programu PowerShell, interfejsu wiersza polecenia Azure, T-SQL lub interfejsu API REST. Zobacz [Zarządzanie pojedynczych baz danych](sql-database-single-database-scale.md) i [pule elastyczne zarządzanie](sql-database-elastic-pool.md).

## <a name="can-i-upgrade-or-downgrade-between-the-general-purpose-and-business-critical-service-tiers"></a>Można uaktualnić lub starszą wersję między warstwami usługi ogólnego przeznaczenia i biznesowe krytyczne? 
Tak, z pewnymi ograniczeniami. Miejsce docelowe kopii jednostki SKU musi spełniać maksymalna bazy danych lub rozmiaru puli elastycznej, skonfigurowane dla istniejącego wdrożenia. Jeśli używasz [korzyści Użyj hybrydowe platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md), biznesowe krytyczne jednostka SKU jest dostępna tylko dla klientów z licencji Enterprise Edition. Tylko klienci, którzy migrowane z lokalnymi do ogólnego przeznaczenia przy użyciu korzyści hybrydowe platformy Azure dla programu SQL Server z licencjami, Enterprise Edition można uaktualnić do biznesowe krytyczne. Aby uzyskać więcej informacji, zobacz [jakie są określone prawa korzyści Użyj hybrydowe platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md)?

Ta konwersja nie powoduje Przestój i może być inicjowana przy użyciu portalu Azure, programu PowerShell, interfejsu wiersza polecenia Azure, T-SQL lub interfejsu API REST. Zobacz [Zarządzanie pojedynczych baz danych](sql-database-single-database-scale.md) i [pule elastyczne zarządzanie](sql-database-elastic-pool.md).

## <a name="i-am-using-a-premium-rs-database-that-will-not-be-generally-available---can-i-upgrade-it-to-a-new-tier-and-achieve-a-similar-priceperformance-benefit"></a>Używam Premium RS bazy danych, które nie są zwykle dostępne — można ją uaktualnić do nowej warstwy i osiągnięcia korzyści podobnych ceny do wydajności?
Ponieważ vCore model pozwala niezależną kontrolę nad ilość elastycznie obliczeniowej i pamięci masowej, można bardziej efektywnie zarządzać wynikowy kosztów, co atrakcyjne docelowy dla bazy danych — warstwa Premium r. Ponadto [korzyści Użyj hybrydowe platformy Azure dla programu SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) zapewnia znaczne zniżki, gdy jest używany model na podstawie vCore. 

## <a name="how-often-can-i-adjust-the-resources-per-pool"></a>Jak często można dostosować dla każdej puli zasobów
Tyle razy, ile ma. Zobacz [pule elastyczne zarządzanie](sql-database-elastic-pool.md).

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Jak długo trwa zmiana poziomu warstwy lub wydajności usług pojedynczej bazy danych lub przenieść bazę danych i elastycznej puli?
Zmiana warstwy usługi bazy danych i przenoszenia i puli wymaga bazy danych ma zostać skopiowany na platformie jako operacji w tle. Zmiana warstwy usług może zająć od kilku minut do kilku godzin w zależności od rozmiaru bazy danych. W obu przypadkach podczas przenoszenia baz danych pozostają online i dostępne. Aby uzyskać więcej informacji na temat zmieniania pojedynczych baz danych, zobacz [zmienić warstwy usługi bazy danych](sql-database-service-tiers-dtu.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Kiedy należy używać pojedynczej bazy danych, a elastycznych baz danych?
Ogólnie rzecz biorąc, elastyczne pule są przeznaczone dla typowe [oprogramowanie jako usługa (SaaS) aplikacji wzorzec](sql-database-design-patterns-multi-tenancy-saas-applications.md), gdy istnieje jedną bazę danych na dzierżawcy lub klienta. Kupowanie poszczególnych baz danych i przydzielanie dla każdej z nich nadmiernej ilości zasobów pod kątem zmiennego i szczytowego zapotrzebowania często nie jest ekonomiczne. Pule Zarządzanie łączną wydajność puli i baz danych górę i w dół Skalowanie automatyczne. Inteligentnego aparatu platformy Azure zaleca puli baz danych, gdy użycie wzorca gwarantuje on. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące puli elastycznej](sql-database-elastic-pool.md).

## <a name="how-does-the-usage-of-sql-database-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Jak jest użycie bazy danych SQL przy użyciu model kupna jednostek dtu w warstwie wyświetlany na mojego rachunku?
Na podstawie bazy danych SQL rachunków na przewidywalną, godzinową szybkość [zakupu modelu](sql-database-service-tiers-dtu.md). Rzeczywistego użycia jest obliczana i proporcjonalnie co godzinę, więc rachunku mogą być wyświetlane ułamków godzinę. Na przykład jeśli baza danych istnieje na 12 godzin w miesiącu, rachunku pokazuje użycie 0,5 dni. 

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Co zrobić, jeśli pojedynczej bazy danych jest aktywna na mniej niż godzinę lub używa wyższego poziomu usług mniej niż godzinę?
Opłaty są naliczane dla każdej godziny przy użyciu najwyższej warstwy usług istnieje baza danych + poziom wydajności, która zastosowana podczas tej godziny, niezależnie od użycia i czy baza danych była active przez mniej niż godzinę. Po utworzeniu pojedynczej bazy danych i usunąć go pięciu minut rachunku odzwierciedla opłat godzinę jedną bazę danych. 

Przykłady:

* Jeśli tworzenie podstawowej bazy danych, a następnie natychmiast uaktualnić go do standardowa S1, naliczane są opłaty szybkością standardowa S1 dla pierwszej godziny.
* Jeśli uaktualnienie bazy danych z Basic do Premium na 10:00 w dniu i zakończeniu uaktualniania 1:35 o godzinie następnego dnia są naliczane szybkością Premium, rozpoczynając od godziny 1:00 
* Jeśli starszą wersję bazy danych w warstwie Premium podstawowy o 11:00 wykonuje na 2:15:00, a następnie bazy danych jest rozliczana według szybkości Premium do 3:00 w dniu, po upływie którego jest rozliczana stawkami podstawowe.

## <a name="how-does-elastic-pool-usage-using-the-dtu-based-purchasing-model-show-up-on-my-bill"></a>Jak jest użycie puli elastycznej za pomocą model kupna jednostek dtu w warstwie wyświetlany na mojego rachunku?
Pula elastyczna opłaty Pokaż się na rachunku jako elastycznej Dtu (Edtu) lub vCores plus magazynu z przyrostem wyświetlany na [stronie cen](https://azure.microsoft.com/pricing/details/sql-database/). Jest bezpłatna dla bazy danych dla puli elastycznej. Opłaty są naliczane dla każdej godziny puli istnieje w najwyższym eDTU lub vCores, niezależnie od użycia i czy pula było aktywne przez mniej niż godzinę. 

Na podstawie jednostek dtu w warstwie: zakupów przykłady modelu:

* Po utworzeniu standardowej puli elastycznej z 200 Edtu o godzinie 11:18 Dodawanie pięć baz danych do puli, naliczane są opłaty dla 200 Edtu całego godzinę od o godzinie 11 do końca dnia.
* W dniu 2, 5 o godzinie: 05 1 bazy danych rozpocznie korzystanie z 50 jednostek Edtu i przechowuje stałej za pośrednictwem dnia. Bazy danych 2-5 zmieniają się między 0 a Edtu 80. W ciągu dnia można dodać pięć innych baz danych używających różnych jednostek Edtu w ciągu dnia. Dzień 2 jest pełny dzień rozliczane według 200 eDTU. 
* W dniu 3, 5 o godzinie Możesz dodać inny 15 baz danych. W ciągu dnia do punktu, gdzie chcesz zwiększyć jednostek Edtu puli 200 400 8 o godzinie: 05 powoduje zwiększenie użycia bazy danych Opłaty na poziomie 200 eDTU były obowiązywać do 20: 00 i zwiększa do 400 jednostek Edtu dla pozostałych czterech godzin. 

## <a name="how-are-elastic-pool-billed-for-the-dtu-based-purchasing-model"></a>Jak są rozliczane na podstawie DTU puli elastycznej zakupu modelu?
Elastyczne pule są rozliczane według następującej charakterystyce:

* Pula elastyczna jest on rozliczany po jego utworzeniu, nawet jeśli nie ma baz danych w puli.
* Pula elastyczna jest rozliczane co godzinę. Jest to taką samą częstotliwością pomiarów, podobnie jak w przypadku poziomy wydajności pojedynczej bazy danych.
* Jeśli zmieniono rozmiar puli elastycznej, następnie puli jest nie rozliczane zgodnie z nowego ilość zasobów przed zakończeniem operacji zmiany rozmiaru. Wynika z tego samego wzorca podczas zmieniania poziomu wydajności pojedynczej bazy danych.
* Cena puli elastycznej jest oparta na puli zasobów. Cena puli elastycznej zależy od liczby i wykorzystania elastycznych baz danych w niej.

Aby uzyskać więcej informacji, zobacz [SQL Database — cennik](https://azure.microsoft.com/pricing/details/sql-database/), [na podstawie jednostek dtu w warstwie model kupna](sql-database-service-tiers-dtu.md), i [na podstawie vCore model kupna (wersja zapoznawcza)](sql-database-service-tiers-vcore.md).

## <a name="how-does-the-vcore-based-usage-show-up-in-my-bill"></a>Jak jest użycie na podstawie vCore wyświetlany w mojego rachunku? 
W modelu na podstawie vCore usługi jest on rozliczany na przewidywalną, godzinową stawkę oparte na warstwie usługi elastycznie obliczeń w vCores, zainicjowaniu obsługi magazynu w GB/miesiąc i używane magazynu kopii zapasowej. Jeśli magazyn kopii zapasowych przekracza rozmiar całkowitą bazy danych (czyli 100% rozmiaru bazy danych), dostępne są dodatkowe opłaty. vCore godzin, magazyn baz danych skonfigurowanych wykorzystanych We/Wy i magazynu kopii zapasowej są wyraźnie wymienione w zestawieniu, ułatwiając szczegóły zasoby, które zostały użyte. Kopia zapasowa magazynu do 100% maksymalny rozmiar bazy danych jest dołączony, po której są rozliczane w GB/miesiąc używane w ciągu miesiąca.

Na przykład:
- Jeśli istnieje baza danych SQL na 12 godzin w miesiącu, BOM pokazuje użycie 12 godzin vCore. Jeśli baza danych SQL udostępnione dodatkowe 100 GB pamięci masowej, BOM pokazuje magazynu użycie w jednostkach GB/miesiąc proporcjonalnie co godzinę i liczby operacji We-Wy używane w ciągu miesiąca.
- Jeśli baza danych SQL jest aktywna na mniej niż 1 godzinę, są rozliczane co godzinę, który istnieje w bazie danych przy użyciu najwyższej warstwy usług, zaznaczona, udostępnione magazynu i we/wy, która zastosowana podczas tej godziny, niezależnie od użycia i czy baza danych była aktywna dla mniej godziny.
- Jeśli utworzysz wystąpienie zarządzane i usuniesz je 5 minut później, opłata zostanie naliczona za jedną godzinę korzystania z bazy danych.
- Jeśli utworzysz wystąpienie zarządzane w warstwie Przeznaczenie ogólne z ośmioma rdzeniami wirtualnymi, a następnie od razu zmodernizujesz je do 16 rdzeni wirtualnych, opłata za pierwszą godzinę zostanie naliczona na podstawie stawki za 16 rdzeni wirtualnych.

> [!NOTE]
> Przez ograniczony czas za pośrednictwem 2018 30 czerwca obciążenia kopii zapasowej i obciążeń We/Wy są bezpłatnie.

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Jak jest użycie aktywna replikacja geograficzna w będą wyświetlane puli elastycznej na mojego rachunku?
W przeciwieństwie do pojedynczych baz danych przy użyciu [aktywna replikacja geograficzna](sql-database-geo-replication-overview.md) z elastycznych baz danych nie ma to bezpośredni wpływ rozliczeń.  Naliczane są tylko opłaty za zasoby obsługi administracyjnej dla poszczególnych pul (puli głównej i dodatkowej puli)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>W jaki sposób funkcja inspekcji będzie obciążać mój rachunek?
Inspekcja jest wbudowana w usłudze SQL Database bez dodatkowych kosztów i jest dostępny na wszystkich warstwach usług. Jednak do przechowywania dzienników inspekcji, używa funkcji inspekcji konta usługi Azure Storage oraz szybkości dla tabel i kolejek w usłudze Azure Storage są stosowane na podstawie rozmiaru dziennika inspekcji.

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Jak zresetować hasła administratora serwera?
W [portalu Azure](https://portal.azure.com), kliknij przycisk **serwerów SQL**, wybierz serwer z listy, a następnie kliknij przycisk **Resetuj hasło**.

## <a name="how-do-i-manage-databases-and-logins"></a>Jak zarządzać baz danych i logowania
Zobacz [Zarządzanie bazami danych i logowaniami](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Jak utworzyć się, że tylko autoryzowani adresy IP mogą uzyskać dostęp do serwera?
Zobacz [porady: Konfigurowanie ustawień zapory w bazie danych SQL](sql-database-configure-firewall-settings.md).

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Co to jest opóźnienie replikacji oczekiwanego podczas replikowania geograficznie bazy danych między dwóch regionach w ramach tej samej lokalizacji geograficznej Azure?
Firma Microsoft są obecnie obsługiwane RPO pięciu sekund i opóźnienie replikacji została mniejszej niż że gdy dodatkowej geograficzna jest obsługiwana na platformie Azure zalecane sparowanego regionu i w tej samej warstwie usługi.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Co to jest opóźnienie replikacji oczekiwany po dodatkowej geograficzna jest tworzony w tym samym regionie co podstawowa baza danych?
Na podstawie empiryczne danych, nie istnieje za dużo różnica między wewnątrz regionu i opóźnienie replikacji między region podczas zalecanej platformy Azure sparowanym region jest używany. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>Jeśli występuje awaria sieci między dwóch regionach, jak logika ponowień działa po skonfigurowaniu replikacji geograficznej?
W przypadku rozłączenia, aby ponownie ustanowić połączenia wykonanie co 10 sekund.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Co można zrobić, aby zagwarantować, że zmiany krytyczne w głównej bazie danych są replikowane?
Dodatkowej geograficzna jest replika async i firma Microsoft nie należy go przechowywać w pełnej synchronizacji z serwera podstawowego. Ale zapewniamy metodę, aby wymusić synchronizację w celu zapewnienia replikacji krytycznej zmiany (na przykład aktualizacji hasła). Wymuszone synchronizacji wpływa na wydajność, ponieważ wątek wywołujący blokuje dopóki wszystkie zatwierdzone transakcje są replikowane. Aby uzyskać więcej informacji, zobacz [operacja sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Jakie narzędzia są dostępne do monitorowania opóźnienie replikacji między podstawowej bazy danych i dodatkowej geograficzna?
Uwidaczniamy opóźnienie replikacji w czasie rzeczywistym między podstawowej bazy danych i geograficzna / pomocniczych za pośrednictwem DMV. Aby uzyskać więcej informacji, zobacz [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Aby przenieść bazę danych do innego serwera w tej samej subskrypcji
W [portalu Azure](https://portal.azure.com), kliknij przycisk **baz danych SQL**, wybierz bazę danych z listy, a następnie kliknij przycisk **kopiowania**. Zobacz [kopiowania bazy danych Azure SQL](sql-database-copy.md) uzyskać więcej szczegółowych informacji.

## <a name="to-move-a-database-between-subscriptions"></a>Aby przenieść bazę danych między subskrypcjami
W [portalu Azure](https://portal.azure.com), kliknij przycisk **serwerów SQL** , a następnie wybierz serwer, który jest hostem bazy danych z listy. Kliknij przycisk **Przenieś**, a następnie wybierz zasoby do przeniesienia i subskrypcję docelową.

