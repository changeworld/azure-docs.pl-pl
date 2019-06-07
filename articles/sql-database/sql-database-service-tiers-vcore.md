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
ms.date: 05/06/2019
ms.openlocfilehash: 520dba611e6791fca990e21173424a914c3d8e14
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693337"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Można wybrać jedną z warstwy usługi (rdzeń wirtualny), a następnie przeprowadzenie migracji z warstwy usługi jednostki DTU

Rdzeń wirtualny (rdzeń wirtualny) — model zakupu w oparciu o pozwala niezależnie skalować zasoby obliczeniowe i magazynowe, Dopasuj wydajność środowiska lokalnego i optymalizacja ceny. Dzięki temu można także wybrać generację sprzętu:

- **4. generacji**: Maksymalnie 24 logiczne procesory CPU oparte na Intel E5-2673 v3 2,4 GHz (Haswell), procesory, — rdzeń wirtualny = 1 PP (rdzeni fizycznych), 7 GB na rdzeń procesora, dołączonych dysków SSD
- **5. generacji**: Maksymalnie 80 logiczne procesory CPU oparte na Intel E5-2673 v4 (broadwell z zegarem) 2.3 GHz procesorów, pamięci rdzeń wirtualny = LP 1 (funkcja hyper wątek), 5.1 GB na rdzeń procesora, szybkie eNVM dysków SSD

4. generacji sprzętu oferuje znacznie więcej pamięci na rdzeń wirtualny. 5. generacji sprzętu pozwala jednak skalować zasoby obliczeniowe, które znacznie wyższa.

> [!NOTE]
> Aby uzyskać informacji na temat warstwy usług oparte na jednostkach DTU, zobacz [warstwy modelu zakupu opartego na jednostkach DTU usług](sql-database-service-tiers-dtu.md). Aby uzyskać informacje o różnicach między warstwy usług dla opartego na jednostkach DTU, jak i w modelu zakupu opartego na rdzeniach wirtualnych, zobacz [usługi Azure SQL Database, zakup modeli](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Właściwości warstwy usług

Model zakupu opartego na rdzeniach wirtualnych zapewnia trzech warstwach usługi: ogólnego przeznaczenia, w hiperskali i krytyczne dla działania. Te warstwy usługi są zróżnicowane według szeroką gamę rozmiarów wystąpień obliczeniowych, projekty o wysokiej dostępności, izolację awarii metody, typy i rozmiary magazynów i zakresy operacji We/Wy.

Musisz oddzielnie skonfigurować wymagane okres przechowywania i przechowywania kopii zapasowych. Aby ustawić okres przechowywania kopii zapasowych, otwórz witrynę Azure portal, przejdź do serwera (bez bazy danych), a następnie przejdź do **Zarządzanie kopiami zapasowymi** > **Konfiguruj zasady**  >   **Punkt w czasie przywracania konfiguracji** > **7 – 35 dni**.

W poniższej tabeli przedstawiono różnice w trzech warstwach:

||**Zastosowania ogólne**|**Krytyczne dla działania**|**Na dużą skalę**|
|---|---|---|---|
|Najlepsze dla|Większości obciążeń biznesowych. Oferty ograniczonym budżetem, zrównoważonych oraz skalowalnych opcji obliczeniowych i magazynu.|Aplikacje biznesowe za pomocą wysokie wymagania dotyczące operacji We/Wy. Oferuje najwyższą odporność na awarie, korzystając z kilku izolowanych replik.|Większości obciążeń biznesowych za pomocą wysoce skalowalny magazyn i wymagań skali odczytu.|
|Wystąpienia obliczeniowe|**Zainicjowano obsługę administracyjną obliczeń**:<br/>Gen4: 1-24 rdzenie wirtualne<br/>5. generacji: 2-80 rdzeni wirtualnych<br/>**Bezserwerowe środowisko obliczeniowe**:<br/>5. generacji: 0.5 — 4 rdzenie wirtualne|**Zainicjowano obsługę administracyjną obliczeń**:<br/>Gen4: 1-24 rdzenie wirtualne<br/>5. generacji: 2-80 rdzeni wirtualnych|**Zainicjowano obsługę administracyjną obliczeń**:<br/>Gen4: 1-24 rdzenie wirtualne<br/>5. generacji: 2-80 rdzeni wirtualnych|
|Memory (Pamięć)|**Zainicjowano obsługę administracyjną obliczeń**:<br/>Gen4: 7 GB na rdzeń wirtualny<br/>5. generacji: 5.1 GB na rdzeń wirtualny<br/>**Bezserwerowe środowisko obliczeniowe**:<br/>5. generacji: 3 GB na rdzeń wirtualny|**Zainicjowano obsługę administracyjną obliczeń**:<br/>Gen4: 7 GB na rdzeń wirtualny<br/>5. generacji: 5.1 GB na rdzeń wirtualny |**Zainicjowano obsługę administracyjną obliczeń**:<br/>Gen4: 7 GB na rdzeń wirtualny<br/>5. generacji: 5.1 GB na rdzeń wirtualny|
|Magazyn|Używa zdalnego magazynu.<br/>**Pojedynczą bazę danych aprowizowane obliczeń**:<br/>5 GB – 4 TB<br/>**Pojedynczą bazę danych bezserwerowe środowisko obliczeniowe**:<br/>5 GB - 1 TB<br/>**Wystąpienie zarządzane**: 32 GB - 8 TB |Używa lokalny magazyn SSD.<br/>**Pojedynczą bazę danych aprowizowane obliczeń**:<br/>5 GB – 4 TB<br/>**Wystąpienie zarządzane**:<br/>32 GB - 4 TB |Elastyczne automatyczne zwiększanie magazynu zgodnie z potrzebami. Obsługuje maksymalnie 100 TB pamięci. Używa lokalny magazyn SSD w puli buforów w lokalnej pamięci podręcznej i lokalne przechowywanie danych. Używa usługi Azure storage zdalnego końcowego długoterminowego przechowywania danych. |
|Przepustowość operacji We/Wy (w przybliżeniu)|**Pojedynczą bazę danych**: 500 operacji We/Wy na rdzeniach wirtualnych za pomocą 7000 maksymalna liczba IOPS.<br/>**Wystąpienie zarządzane**: Zależy od [rozmiar pliku](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 operacji We/Wy na rdzeń z 200 000 maksymalna liczba IOPS|W Hiperskali to architektura wielowarstwowa z buforowaniem na różnych poziomach. Skuteczne operacje We/Wy zależy od obciążenia.|
|Dostępność|1 repliki, nie skali odczytu replik|3 repliki, 1 [skali odczytu replik](sql-database-read-scale-out.md),<br/>strefowo nadmiarowe wysokiej dostępności (HA)|1 repliki do odczytu i zapisu oraz 0 – 4 [skali odczytu replik](sql-database-read-scale-out.md)|
|Tworzenie kopii zapasowych|[Dostęp do odczytu magazynu geograficznie nadmiarowego (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 dni (domyślnie co 7 dni)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 dni (domyślnie co 7 dni)|Na podstawie migawki kopii zapasowych w usłudze Azure storage zdalnego. Przywracanie na użytek migawek Szybkie odzyskiwanie. Kopie zapasowe są natychmiastowe i nie wpływać na wydajność operacji We/Wy obliczeń. Operacje przywracania są szybkie i nie są to operacja danych o rozmiarze (biorąc minuty, a nie godziny lub dni).|
|W pamięci|Nieobsługiwane|Obsługiwane|Nieobsługiwane|
|||

> [!NOTE]
> Możesz uzyskać bezpłatne bazy danych Azure SQL w warstwie podstawowa w połączeniu z bezpłatnego konta platformy Azure. Aby uzyskać więcej informacji, zobacz [utworzyć bazę danych zarządzana usługa w chmurze przy użyciu bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/services/sql-database/).

- Aby uzyskać więcej informacji na temat limitów zasobów (rdzeń wirtualny) zobacz [limity zasobów — rdzeń wirtualny w pojedynczej bazy danych](sql-database-vcore-resource-limits-single-databases.md) i [limity zasobów — rdzeń wirtualny w wystąpieniu zarządzanym](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Aby uzyskać więcej informacji na temat warstwy krytyczne usługi biznesowe i ogólnego przeznaczenia, zobacz [ogólnego przeznaczenia i krytyczne dla warstwy usług](sql-database-service-tiers-general-purpose-business-critical.md).
- Aby uzyskać więcej informacji na temat warstwy usług na dużą skalę w modelu zakupu opartego na rdzeniach wirtualnych, zobacz [warstwy usługi w Hiperskali](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

W modelu zakupu opartego na rdzeniach wirtualnych w warstwie zainicjowanych zasobów obliczeniowych mogą wymieniać swoich istniejących licencji do korzystania z taryf rabatowych w bazie danych SQL przy użyciu [korzyść użycia hybrydowego platformy Azure dla programu SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Ta korzyść platformy Azure umożliwia zapisanie do 30 procent w usłudze Azure SQL Database przy użyciu licencji programu SQL Server w środowisku lokalnym z pakietem Software Assurance.

![cennik](./media/sql-database-service-tiers/pricing.png)

Dzięki korzyści użycia hybrydowego platformy Azure można wybrać tylko płacisz podstawową infrastrukturą platformy Azure przy użyciu istniejących licencji programu SQL Server dla aparatu bazy danych SQL, sama (cena podstawowa obliczeniowa) lub płacisz podstawową infrastrukturą i programu SQL Server Licencja (cena oferowała objęte licencją).

Można wybrać, lub zmień model licencjonowania, za pomocą witryny Azure portal lub przy użyciu jednej z poniższych interfejsów API:

- Aby ustawić lub zaktualizowania typu licencji przy użyciu programu PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

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

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migracja z modelu opartego na jednostkach DTU do modelu opartego na rdzeniach wirtualnych

### <a name="migrate-a-database"></a>Migrowanie bazy danych

Migrowanie bazy danych z modelu zakupu opartego na jednostkach DTU do modelu zakupu opartego na rdzeniach wirtualnych jest podobny do uaktualnienia lub zmiany na starszą wersję między warstwami standardowa i premium usługi w modelu zakupu opartego na jednostkach DTU.

### <a name="migrate-databases-with-geo-replication-links"></a>Migrowanie baz danych za pomocą łącza replikacji geograficznej

Migracja z modelu opartego na jednostkach DTU do modelu zakupu opartego na rdzeniach wirtualnych jest podobny do uaktualnienia lub zmiany na starszą wersję relacje replikacji geograficznej między bazami danych w warstwach standardowa i premium. Podczas migracji nie musisz zatrzymać replikację geograficzną, ale muszą wykonać następujące czynności sekwencji:

- Podczas uaktualniania, należy najpierw uaktualnić pomocnicze bazy danych, a następnie Uaktualnij podstawowy.
- Przed obniżeniem, odwrócić kolejność: należy najpierw obniżyć podstawowej bazy danych, a następnie obniżenia poziomu pomocniczej.

Podczas korzystania z replikacją geograficzną między dwie pule elastyczne, zaleca się wyznaczenie jednej puli jako podstawowy, a drugi jako pomocniczy. W takim przypadku podczas migrowania pul elastycznych należy użyć te same wskazówki sekwencjonowania. Jednak w przypadku pul elastycznych, które zawierają podstawowych i pomocniczych baz danych traktować puli z lepszego wykorzystania jako podstawowy i postępuj zgodnie z zasadami sekwencjonowania.  

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

\* Co 100 jednostek Dtu w warstwie standardowa wymaga co najmniej 1 rdzeń wirtualny, a każdy 125 jednostek Dtu w warstwie premium wymagają co najmniej 1 rdzeń wirtualny.

### <a name="migrate-failover-groups"></a>Przeprowadź migrację grupy trybu failover

Migracja grup trybu failover z wieloma bazami danych wymaga migracji poszczególnych baz danych podstawowych i pomocniczych. Podczas tego procesu rozważenia tych samych czynników i sekwencjonowania zasady są stosowane. Po przekonwertowaniu baz danych do modelu zakupu opartego na rdzeniach wirtualnych, grupy trybu failover będzie obowiązywać z tymi samymi ustawieniami zasad.

### <a name="create-a-geo-replication-secondary-database"></a>Tworzenie pomocniczej bazy danych replikacji geograficznej

Tylko przy użyciu tej samej warstwie usługi, jak używane do podstawowej bazy danych, można utworzyć pomocniczą bazę danych replikacji geograficznej (pomocnicza geograficzna). W przypadku baz danych z szybkością wysokiej Generowanie dziennika, zaleca się utworzenie pomocniczej geograficznej przy użyciu tego samego rozmiaru obliczeń jako podstawowy.

Jeśli tworzysz pomocniczej geograficznej w puli elastycznej dla pojedynczej podstawowej bazy danych, upewnij się, `maxVCore` ustawienie rozmiaru obliczeń podstawowej bazy danych jest zgodna z puli. Jeśli tworzysz pomocniczej geograficznej dla lokacji podstawowej w innej puli elastycznej, firma Microsoft zaleca pule mają taką samą `maxVCore` ustawienia.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Konwertowanie bazy danych oparty na jednostkach DTU na bazę danych oparty na rdzeniach wirtualnych za pomocą kopii bazy danych

Można skopiować dowolną bazę danych o rozmiarze mocą obliczeniową opartą na jednostkach DTU do bazy danych o rozmiarze oparty na rdzeniach wirtualnych obliczeń bez ograniczeń lub specjalne sekwencjonowania, tak długo, jak rozmiar obliczeń docelowej obsługuje maksymalny rozmiar bazy danych źródłowej bazy danych. Kopiowanie bazy danych tworzy migawkę danych od godziny rozpoczęcia operacji kopiowania i nie synchronizować dane między źródłowym a docelowym.

## <a name="next-steps"></a>Kolejne kroki

- Rozmiarów określonych wystąpień obliczeniowych i opcje rozmiaru magazynu jest dostępne dla pojedynczych baz danych, zobacz [limity zasobów opartych na rdzeniach wirtualnych bazy danych SQL Database dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md).
- Rozmiarów określonych wystąpień obliczeniowych i magazynu rozmiar dostępnych dla pul elastycznych, zobacz [bazy danych SQL Database oparty na rdzeniach wirtualnych zasobów limity dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
