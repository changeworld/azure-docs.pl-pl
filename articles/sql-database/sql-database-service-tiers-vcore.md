---
title: Usługa Azure SQL Database — rdzeń wirtualny | Dokumentacja firmy Microsoft
description: Model zakupu opartego na rdzeniach wirtualnych umożliwia niezależnie skalować zasoby obliczeniowe i magazynowe, Dopasuj wydajność środowiska lokalnego i optymalizacja ceny.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: edba858f9be3350034ff48ea16d3c9137254bb97
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357946"
---
# <a name="vcore-service-tiers-azure-hybrid-benefit-and-migration"></a>warstwy usług (rdzeń wirtualny), korzyść użycia hybrydowego platformy Azure i migracji

Model zakupu opartego na rdzeniach wirtualnych umożliwia niezależnie skalować zasoby obliczeniowe i magazynowe, Dopasuj wydajność środowiska lokalnego i optymalizacja ceny. Umożliwia również można wybrać generacji sprzętu:

- 4. generacji — maksymalnie 24 logiczne procesory CPU, procesory Intel E5-2673 v3 (Haswell) 2,4 GHz procesorów, pamięci rdzeń wirtualny = 1 PP (rdzeni fizycznych), 7 GB na rdzeń procesora, dołączonych dysków SSD
- 5. generacji — maksymalnie 80 logiczne procesory CPU, procesory Intel E5-2673 v4 (broadwell z zegarem) 2,3 GHz, — rdzeń wirtualny = LP 1 (funkcja hyper wątek), 5.1 GB na rdzeń procesora, szybkie eNVM dysków SSD

4. generacji sprzętu oferuje znacznie więcej pamięci na rdzeń wirtualny. 5. generacji sprzętu pozwala jednak skalować zasoby obliczeniowe, które znacznie wyższa.

> [!NOTE]
> Aby uzyskać informacji na temat warstwy usług oparte na jednostkach DTU, zobacz [warstwy usług oparte na jednostkach DTU](sql-database-service-tiers-dtu.md). Aby dowiedzieć się, jak rozróżnianie warstwy usług oparte na jednostkach DTU i warstwy usług oparte na rdzeniach wirtualnych, zobacz [usługi Azure SQL Database, zakup modeli](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Właściwości warstwy usług

Model rdzenia wirtualnego oferuje trzy warstwy usług ogólnego przeznaczenia, w Hiperskali i krytyczne dla działania firmy. Warstwy usługi są zróżnicowane według szeroką gamę rozmiarów wystąpień obliczeniowych, projekt wysokiej dostępności, odporności izolacji, typy i rozmiar magazynu i zakres operacji We/Wy. Musisz oddzielnie skonfigurować wymagane okres przechowywania i przechowywania kopii zapasowych. W witrynie Azure portal przejdź do serwera (bez bazy danych) > zarządzanych kopii zapasowych > Konfiguruj zasady > w czasie przywracania konfiguracji punktu > 7 – 35 dni.

Poniższa tabela pomoże Ci zrozumieć różnice między trzy warstwy:

||**Ogólnego przeznaczenia**|**Krytyczne dla działania**|**Na dużą skalę (wersja zapoznawcza)**|
|---|---|---|---|
|Najlepsze dla|Większości obciążeń biznesowych. Oferty budżetu, aby poznać podstawy zrównoważonych oraz skalowalnych opcji obliczeniowych i magazynu.|Aplikacje biznesowe z wysokimi wymaganiami w zakresie operacji wejścia/wyjścia. Oferuje najwyższą odporność na awarie, korzystając z kilku izolowanych replik.|Większości obciążeń biznesowych za pomocą wysoce skalowalny magazyn i wymagań skali odczytu|
|Wystąpienia obliczeniowe|Gen4: 1-24 (rdzeń wirtualny)<br/>5. generacji: 1 do 80 (rdzeń wirtualny)|Gen4: 1-24 (rdzeń wirtualny)<br/>5. generacji: 1 do 80 (rdzeń wirtualny)|Gen4: 1-24 (rdzeń wirtualny)<br/>5. generacji: 1 do 80 (rdzeń wirtualny)|
|Memory (Pamięć)|Gen4: 7 GB na rdzeń<br>5. generacji: 5.1 GB na rdzeń | Gen4: 7 GB na rdzeń<br>5. generacji: 5.1 GB na rdzeń |Gen4: 7 GB na rdzeń<br>5. generacji: 5.1 GB na rdzeń|
|Magazyn|Używa magazynu zdalnego:<br/>Pojedyncza baza danych: 5 GB – 4 TB<br/>Wystąpienie zarządzane: 32 GB - 8 TB |Używa lokalnego magazynu SSD:<br/>Pojedyncza baza danych: 5 GB – 4 TB<br/>Wystąpienie zarządzane: 32 GB - 4 TB |Elastyczne, automatyczne zwiększanie magazynu zgodnie z potrzebami. Obsługuje maksymalnie 100 TB pamięci masowej i nie tylko. Lokalny magazyn SSD w pamięci podręcznej puli bufora lokalnych i lokalne przechowywanie danych. Usługa Azure storage zdalnego końcowego długoterminowego przechowywania danych. |
|Przepustowość operacji We/Wy (w przybliżeniu)|Pojedyncza baza danych: 500 operacji We/Wy na rdzeniach wirtualnych za pomocą 7000 maksymalna liczba IOPS</br>Wystąpienie zarządzane: Zależy od [rozmiar pliku](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|5000 operacji We/Wy na rdzeń z 200 000 maksymalna liczba IOPS|TBD|
|Dostępność|1 repliki, bez skalowania odczytu|3 repliki, 1 [skali odczytu replik](sql-database-read-scale-out.md),<br/>Strefa nadmiarowe wysokiej dostępności|?|
|Tworzenie kopii zapasowych|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 dni (domyślnie co 7 dni)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 dni (domyślnie co 7 dni)|na podstawie migawki kopii zapasowej w magazynie zdalnym platformy Azure i przywracanie na użytek migawek Szybkie odzyskiwanie. Kopie zapasowe są natychmiastowe i nie wpływać na wydajność operacji We/Wy, mocy obliczeniowej. Operacje przywracania są bardzo szybkie i nie rozmiar operacji danych (trwa minuty, a nie godziny lub dni).|
|W pamięci|Nieobsługiwane|Obsługiwane|Nieobsługiwane|
|||

> [!NOTE]
> Możesz uzyskać bezpłatne bazy danych Azure SQL w warstwie podstawowa w połączeniu z bezpłatnego konta platformy Azure do eksplorowania platformy Azure. Aby uzyskać informacje, zobacz [utworzyć bazę danych zarządzana usługa w chmurze przy użyciu bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/services/sql-database/).

- Aby uzyskać więcej informacji, zobacz [limity zasobów — rdzeń wirtualny w pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów — rdzeń wirtualny w wystąpieniu zarządzanym](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Aby uzyskać więcej informacji na temat warstw usługi ogólnego przeznaczenia i krytyczne dla działania firmy, zobacz [warstwy usług ogólnego przeznaczenia i krytyczne dla działania firmy](sql-database-service-tiers-general-purpose-business-critical.md).
- Szczegółowe informacje na temat warstwy usług na dużą skalę w modelu zakupu opartego na rdzeniach wirtualnych, [warstwy usługi w Hiperskali](sql-database-service-tier-hyperscale.md).  

> [!IMPORTANT]
> Jeśli potrzebujesz mniejszej niż jeden rdzeń wirtualny mocy obliczeniowej, przy użyciu modelu zakupu opartego na jednostkach DTU.

## <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

Oparty na rdzeniach wirtualnych model zakupu mogą wymieniać swoich istniejących licencji do korzystania z taryf rabatowych na temat korzystania z bazy danych SQL [korzyść użycia hybrydowego platformy Azure dla programu SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Ta korzyść platformy Azure pozwala na używanie licencji programu SQL Server w środowisku lokalnym można zapisać do 30% w usłudze Azure SQL Database przy użyciu lokalnych licencji programu SQL Server z pakietem Software Assurance.

![cennik](./media/sql-database-service-tiers/pricing.png)

Dzięki korzyści użycia hybrydowego platformy Azure, możesz płacić tylko za podstawową infrastrukturą platformy Azure przy użyciu istniejących licencji programu SQL Server dla aparatu bazy danych SQL, sama (**BasePrice**) i płacić za zarówno podstawowej infrastruktury i licencję programu SQL Server (**LicenseIncluded**). Można wybrać lub zmienić modelu licencjonowania przy użyciu witryny Azure portal lub przy użyciu jednej z poniższych interfejsów API.

- Aby ustawić lub zaktualizowania typu licencji przy użyciu programu PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase):
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql)

- Aby ustawić lub zaktualizowania typu licencji przy użyciu wiersza polecenia platformy Azure:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [Tworzenie wystąpienia zarządzanego sql az](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [Aktualizacja wystąpienia zarządzanego sql az](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Aby ustawić lub zaktualizowania typu licencji przy użyciu interfejsu API REST:

  - [Bazy danych — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Bazy danych — aktualizacja](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Zarządzane wystąpienia — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Wystąpienia zarządzane — aktualizacja](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Migracja z modelu jednostek DTU do modelu rdzenia wirtualnego

### <a name="migration-of-a-database"></a>Migracja bazy danych

Migrowanie bazy danych z modelu zakupu opartego na jednostkach DTU do modelu zakupu opartego na rdzeniach wirtualnych jest podobny do uaktualnienia lub zmiany na starszą wersję między Standard i Premium baz danych w modelu zakupu opartego na jednostkach DTU.

### <a name="migration-of-databases-with-geo-replication-links"></a>Migracja baz danych za pomocą łącza replikacji geograficznej

Migracja z modelu opartego na jednostkach DTU do modelu opartego na rdzeniach wirtualnych jest podobny do uaktualnienia lub zmiany na starszą wersję relacje replikacji geograficznej między bazami danych w warstwach standardowa i Premium. Nie jest wymagane, czy zakończenie replikacji geograficznej, ale użytkownik musi być zgodna z zasadami sekwencjonowania. Podczas uaktualniania, należy najpierw uaktualnić pomocnicze bazy danych, a następnie Uaktualnij podstawowy. Przed obniżeniem, odwrócić kolejność: należy najpierw obniżyć podstawowej bazy danych, a następnie obniżenia poziomu pomocniczej.

Korzystając z replikacją geograficzną między dwie pule elastyczne, zaleca się wyznaczyć jedną pulę jako podstawowy, a druga — jako pomocnicza. W takim przypadku Migrowanie pul elastycznych, należy użyć te same wskazówki.  Jednak jest technicznie jest to możliwe, że pula elastyczna zawiera podstawowych i pomocniczych baz danych. W tym przypadku prawidłowo migracji możesz należy traktować puli z lepszego wykorzystania jako "primary" i postępuj zgodnie z zasadami sekwencjonowania.  

W poniższej tabeli przedstawiono wskazówki dotyczące scenariuszy migracji:

|Bieżąca warstwa usługi|Docelowej warstwy usług|Typ migracji|Akcje użytkownika|
|---|---|---|---|
|Standardowa (Standard)|Zastosowania ogólne|Boczna|Można migrować w dowolnej kolejności, ale musisz upewnić się, ustalanie rozmiaru odpowiedniego — rdzeń wirtualny *|
|Premium|Krytyczne dla działania firmy|Boczna|Można migrować w dowolnej kolejności, ale musisz upewnić się, ustalanie rozmiaru odpowiedniego — rdzeń wirtualny *|
|Standardowa (Standard)|Krytyczne dla działania firmy|Uaktualnienie|Należy przeprowadzić migrację pomocniczego najpierw|
|Krytyczne dla działania firmy|Standardowa (Standard)|Zmiana na starszą lub mniej zaawansowaną wersję|Należy przeprowadzić migrację podstawowej najpierw|
|Premium|Zastosowania ogólne|Zmiana na starszą lub mniej zaawansowaną wersję|Należy przeprowadzić migrację podstawowej najpierw|
|Zastosowania ogólne|Premium|Uaktualnienie|Należy przeprowadzić migrację pomocniczego najpierw|
|Krytyczne dla działania firmy|Zastosowania ogólne|Zmiana na starszą lub mniej zaawansowaną wersję|Należy przeprowadzić migrację podstawowej najpierw|
|Zastosowania ogólne|Krytyczne dla działania firmy|Uaktualnienie|Należy przeprowadzić migrację pomocniczego najpierw|
||||

\* Każdy 100 jednostek DTU w warstwie standardowa wymaga co najmniej 1 rdzeń wirtualny, a każdy 125 jednostek DTU w warstwie Premium co najmniej 1 rdzeń wirtualny

### <a name="migration-of-failover-groups"></a>Migracja grupy trybu failover

Migracja grup trybu failover z wieloma bazami danych wymaga migracji poszczególnych baz danych podstawowych i pomocniczych. Podczas tego procesu rozważenia tych samych czynników i sekwencjonowania zasady są stosowane. Po przekonwertowaniu baz danych do modelu opartego na rdzeniach wirtualnych, grupy trybu failover będzie obowiązywać z tymi samymi ustawieniami zasad.

### <a name="creation-of-a-geo-replication-secondary"></a>Tworzenie pomocniczej replikacji geograficznej

Można utworzyć tylko przy użyciu tej samej warstwy usługi jako podstawowy pomocniczej geograficznej. Baza danych o szybkości generowania rekordów dziennika wysoka zalecane jest, czy pomocniczy został utworzony przy użyciu tego samego rozmiaru obliczeń jako podstawowy. Jeśli tworzysz pomocniczej geograficznej w puli elastycznej dla pojedynczej podstawowej bazy danych, zaleca się że pula ma `maxVCore` ustawienie odpowiada rozmiarowi obliczeń podstawowej bazy danych. Jeśli tworzysz pomocniczej geograficznej w puli elastycznej dla podstawowego w innej puli elastycznej, zalecane pule mają taką samą `maxVCore` ustawienia

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Przy użyciu kopii bazy danych do przekonwertowania bazy danych oparty na jednostkach DTU na bazę danych oparty na rdzeniach wirtualnych

Można skopiować dowolną bazę danych o rozmiarze mocą obliczeniową opartą na jednostkach DTU do bazy danych o rozmiarze oparty na rdzeniach wirtualnych obliczeń bez ograniczeń lub specjalne sekwencjonowania, tak długo, jak rozmiar obliczeń docelowej obsługuje maksymalny rozmiar bazy danych źródłowej bazy danych. Kopiowanie bazy danych tworzy migawkę danych od godziny rozpoczęcia operacji kopiowania i nie wykonuje synchronizację danych między źródłowym a docelowym.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szczegółowe informacje na temat określonych zasobów obliczeniowych, rozmiary i opcje rozmiaru magazynu jest dostępny dla pojedynczej bazy danych, zobacz [limity zasobów opartych na rdzeniach wirtualnych bazy danych SQL Database dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- Aby uzyskać szczegółowe informacje na temat określonych zasobów obliczeniowych, rozmiary i opcje rozmiaru magazynu jest dostępne dla pul elastycznych, zobacz [bazy danych SQL Database oparty na rdzeniach wirtualnych zasobów limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
