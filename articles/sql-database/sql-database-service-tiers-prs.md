---
title: Emerytura z poziomu usług Usługi Premium RS
description: Warstwa usług usługi Usługi Usługi Usługi Premium RS jest wycofywana i kończy się obsługa — zobacz opcje migracji.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: f00ecd19877ba6236bde5de73d450967abc1fe15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821043"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Warstwa usługi Usługi Usługi Azure SQL Database Premium RS (wersja zapoznawcza) jest wycofywana — opcje migracji

W lutym 2018 r. firma Microsoft ogłosiła, że warstwa usług Usługi Premium RS w usłudze Azure SQL Database nie zostanie wydana w celu uzyskania ogólnej dostępności i nie będzie już obsługiwana po 31 stycznia 2019 r. Termin zakończenia wsparcia został przedłużony do 30 czerwca 2019 r. W tym artykule opisano opcje migracji z warstwy usług usługi Usługi Usługi Usługi Premium RS do innej warstwy usług. Po 30 czerwca 2019 r. firma Microsoft automatycznie migruje bazy danych usługi Premium RS do ogólnie dostępnej warstwy usług, która najbardziej odpowiada wymaganiom wydajności bazy danych Usługi Premium RS.

Poniżej przedstawiono miejsca docelowe migracji i opcje cenowe, które mogą być odpowiednie dla klientów usługi Premium RS:

- Warstwy usług vCore

  Warstwy usług **ogólnego przeznaczenia** i **krytyczne dla firmy** w modelu zakupu [opartym na wynikach wirtualnych](sql-database-service-tiers-vcore.md). Te dwie warstwy usług są ogólnie dostępne. Model zakupów oparty na wynikach wirtualnych oferuje również warstwę usług **hyperscale,** która dostosowuje się na żądanie do potrzeb obciążenia dzięki automatycznemu skalowaniu do 100 TB na bazę danych. Warstwa usług hiperskali zapewnia wydajność we/wy porównywalną z warstwą usług Premium w [modelu zakupów opartym na USŁUDZE DTU](sql-database-service-tiers-dtu.md) po cenie bliższej warstwie usług usługi Usługi Usługi Premium RS.
- Ustalanie cen deweloperskich/testowych

  [Ceny deweloperskie/testowe](https://azure.microsoft.com/pricing/dev-test/) zapewniają oszczędności do 55% w porównaniu z stawkami dołączonymi do licencji w ramach subskrypcji programu Visual Studio.
- Korzyści hybrydowe platformy Azure i ceny pojemności zarezerwowanej

  [Usługa Azure Hybrid Benefit i ceny pojemności zarezerwowanej](https://azure.microsoft.com/pricing/details/sql-database/) zapewniają oszczędności do 80% w porównaniu ze stawkami dołączonymi do licencji. Aby uzyskać więcej informacji na temat tych opcji, zobacz [Korzyści hybrydowe platformy Azure dla programu SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) i [pojemność rezerwowa usługi Azure SQL Database.](sql-database-reserved-capacity.md)

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Działaj teraz, aby migrować bazy danych usługi Premium RS do alternatywnych warstw usług bazy danych SQL

Zapoznaj się ze wskazówkami zawartymi w tym artykule wraz z naszymi cenami i dokumentacją, aby określić odpowiednie miejsca docelowe migracji dla obciążeń usługi Premium RS.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migrowanie obciążeń intensywnie korzystających z danych obliczeniowych i zapisywanie

W przypadku obciążeń usług WS w warstwie Premium w warstwie RS wymagających dużej mocy obliczeniowej zalecamy migrację do naszej ogólnie dostępnej warstwy usług ogólnego przeznaczenia opartej na językach wirtualnych i zapisywanie większej liczby stawek uwzględnionych w licencji przy użyciu korzyści hybrydowej platformy Azure dla programu SQL Server i ofert pojemności zarezerwowanej. Jeśli wolisz pozostać na podstawie DTU opcji zakupu, można migrować bazy danych premium usług WS intensywnie obliczeniowych do warstwy usług standardowych i nadal zapisać w porównaniu z cennikiem ogólnej dostępności usługi Premium RS (jeśli poszedł do ogólnej dostępności).

> [!WARNING]
> Migracja obciążeń usług WS premium do warstw usług Premium opartych na jednostkach DTU może zwiększyć koszty miesięczne w porównaniu z bieżącymi cenami usług Premium RS. Zalecamy rozważenie warstwy Hyperscale lub Business Critical z korzyścią hybrydową platformy Azure i ceną pojemności zarezerwowanej, aby utrzymać podobne lub niższe koszty niż usługi Premium RS.

### <a name="premium-rs-databases"></a>Bazy danych usługi Premium RS

|**Jeśli jesteś obecnie na...**|**Migrowanie do porównywalnych opartych na vCore...**|**Migrowanie do porównywalnych opartych na UDU...**|
|---|---|---|
|Premium RS 1|Ogólny cel 1 vCore (Gen4)|Norma 3|
|Premium RS 2|2 vcore ogólnego przeznaczenia (Gen4)|Norma 4|
|Premium RS 4|4 vcore ogólnego przeznaczenia (Gen4)|Norma 6|
|Premium RS 6|6 vcorów ogólnego przeznaczenia (Gen4)|Norma 7|

### <a name="premium-rs-pools"></a>Pule Premium RS

|**Jeśli jesteś obecnie na...**|**Migrowanie do porównywalnych opartych na vCore...**|**Migrowanie do porównywalnych opartych na UDU...**|
|---|---|---|
|Pula premium RS 125 DTU|Ogólny cel 1 vCore (Gen4)|Standardowa pula 100 eDTU|
|Pula premium RS 250 DTU|2 vcore ogólnego przeznaczenia (Gen4)|Standardowa pula 250 eDTU|
|Pula 500 DTU premium RS|4 vcore ogólnego przeznaczenia (Gen4)|Standardowa pula 500 eDTU|
|Pula 1000 DTU premium RS|8 vCorów ogólnego przeznaczenia (Gen4)|Standardowa pula 1000 eDTU|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Optymalizacja oszczędności i wydajności w przypadku obciążeń wymagających dużej liczby we/wy

Firma Microsoft zaleca migrację pojedynczych baz danych intensywnie korzystających z we/wy do naszej warstwy hiperskali opartej na językach wirtualnych, obecnie w wersji zapoznawczej, oraz pul baz danych intensywnie korzystających z we/wy do naszej ogólnie dostępnej warstwy Krytyczna dla firmy, aby uzyskać optymalne połączenie wydajności i kosztów.  Następujące opcje oparte na wynikach wirtualnych będą utrzymywać lub poprawiać bieżącą wydajność i mogą zaoszczędzić pieniądze w połączeniu z korzyścią hybrydową platformy Azure i ceną pojemności zarezerwowanej.

|**Jeśli jesteś obecnie na...**|**Migrowanie do porównywalnych opartych na vCore...**|**Migrowanie do porównywalnych opartych na UDU...**|
|---|---|---|
|Premium RS 1| 1-owy ryp (Gen4) lub ryp 1 w skali biznesowej (Gen4)|Premia 1|
|Premium RS 2| 2 cory wirtualne hyperscale (Gen4) lub rejy wirtualne o krytycznym znaczeniu dla firmy (Gen4|Premia 2|
|Premium RS 4| 4 cory wirtualne (Gen4) lub 4 bizny (Gen4)|Premium 4
|Premium RS 6| 6-owe vcore (Gen4) lub 6-owe wirtualne (Gen4)|Premia 6|

|**Jeśli jesteś obecnie na...**|**Migrowanie do porównywalnych opartych na vCore...**|**Migrowanie do porównywalnych opartych na UDU...**|
|---|---|---|
|Pula premium RS 125 DTU|Krytyczne dla biznesu 2 bramki wirtualne (Gen4)|Pula premium 125 eDTU|
|Pula premium RS 250 DTU|Krytyczne dla biznesu 2 bramki wirtualne (Gen4)|Pula premium 250 eDTU|
|Pula 500 DTU premium RS|Krytyczne dla biznesu 4 vcore (Gen4)|Pula premium 500 eDTU|
|Pula 1000 DTU premium RS|Krytyczne dla biznesu 8 vCores (Gen4)|Pula premium 1000 eDTU|

## <a name="take-advantage-of-our-new-offers"></a>Skorzystaj z naszych nowych ofert

Nasze warstwy usług w modelu zakupów opartym na wynikach vCore kwalifikują się do ofert specjalnych, które mogą zaoszczędzić do 80% w porównaniu z cenami dołączonymi do licencji. Użyj licencji SQL Server Standard lub Enterprise edition z aktywnym pakietem Software Assurance, aby zaoszczędzić do 55% w porównaniu z cenami dołączonymi do licencji dzięki korzyści [hybrydowej platformy Azure dla programu SQL Server.](https://azure.microsoft.com/pricing/hybrid-benefit/) Korzyści hybrydowe można połączyć z cennikiem [pojemności zarezerwowanej usługi Azure SQL Database](sql-database-reserved-capacity.md) i zaoszczędzić do 80% po zatwierdzeniu z góry na okres jednego lub trzech lat.  Uaktywnij obie korzyści już dziś w witrynie Azure portal.

W razie jakichkolwiek pytań lub wątpliwości dotyczących tej zmiany lub wymagających pomocy w migracji należy skontaktować się z firmą [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migracja z warstwy usług usługi Usługi Premium RS do warstwy usług w modelu DTU lub vCore

### <a name="migration-of-a-database"></a>Migracja bazy danych

Migrowanie bazy danych z warstwy usług Usługi Usługi Usługi Premium RS do warstwy usług w modelu DTU lub vCore jest podobne do uaktualniania lub obniżania poziomu między warstwami usług w warstwie usług Usługi Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Używanie kopii bazy danych do konwertowania bazy danych usługi Premium RS na bazę danych opartą na uatorach dtu lub w oparciu o rzędy

Można skopiować dowolną bazę danych z rozmiarem obliczeniowym usługi Premium RS do bazy danych o rozmiarze obliczeniowym opartym na jednostce DTU lub na podstawie klasy wirtualnej bez ograniczeń lub specjalnym sekwencjonowaniem, o ile docelowy rozmiar obliczeń obsługuje maksymalny rozmiar bazy danych źródłowej bazy danych. Kopia bazy danych tworzy migawkę danych w czasie rozpoczęcia operacji kopiowania i nie wykonuje synchronizacji danych między źródłem a obiektem docelowym.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje na temat określonych rozmiarów obliczeń i opcji rozmiaru magazynu dostępnych dla pojedynczej bazy danych, zobacz [Limity zasobów opartych na parcie wirtualne bazy danych SQL dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md)
- Aby uzyskać szczegółowe informacje na temat określonych rozmiarów obliczeń i opcji rozmiaru magazynu dostępnych dla pul elastycznych, zobacz Limity zasobów opartych na polach [elastycznych bazy danych SQL.](sql-database-vcore-resource-limits-elastic-pools.md)
