---
title: Azure SQL Database Service-rdzeń wirtualny | Microsoft Docs
description: Model zakupu oparty na rdzeń wirtualny umożliwia niezależne skalowanie zasobów obliczeniowych i magazynu, dopasowanie wydajności lokalnej i optymalizację cen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 4af269faab21207e1a754e309cac16e5e0a94b69
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164339"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Wybierz spośród warstw usług rdzeń wirtualny i Przeprowadź migrację z warstw usług DTU

Model zakupu oparty na wirtualnym rdzeniu (rdzeń wirtualny) umożliwia niezależne skalowanie zasobów obliczeniowych i magazynu, dopasowanie wydajności lokalnej i optymalizację cen. Umożliwia również wybranie generacji sprzętu:

- **Obliczenia**: Do 24 procesorów logicznych opartych na procesorach Intel E5-2673 v3 (Haswell) 2,4 GHz, rdzeń wirtualny = 1 PP (rdzeń fizyczny), 7 GB na rdzeń wirtualny, podłączonym SSD
- **5 rdzeń**: Do 80 procesorów logicznych opartych na procesorze Intel E5-2673 v4 (Broadwell) 2,3 GHz, rdzeń wirtualny = 1 LP (Hyper-Thread), 5,1 GB na rdzeń wirtualny do obliczeń zainicjowanych i nawet 24 GB na rdzeń wirtualny w przypadku obliczeń bezserwerowych, Fast eNVM SSD

Sprzęt obliczenia zapewnia znacznie większą ilość pamięci na rdzeń wirtualny. Jednak sprzęt 5 rdzeń umożliwia skalowanie zasobów obliczeniowych znacznie więcej.

> [!IMPORTANT]
> Nowe bazy danych obliczenia nie są już obsługiwane w regionach Australia Wschodnia lub Brazylia Południowa.
> [!NOTE]
> Aby uzyskać informacje o warstwach usług opartych na jednostkach DTU, zobacz [warstwy usług dla modelu zakupu opartego na](sql-database-service-tiers-dtu.md)jednostkach DTU. Aby uzyskać informacje o różnicach między warstwami usług dla modeli zakupów opartych na jednostkach DTU i rdzeń wirtualny, zobacz [Azure SQL Database modele zakupu](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Charakterystyki warstwy usług

Model zakupu oparty na rdzeń wirtualny oferuje trzy warstwy usług: ogólnego przeznaczenia, skalowania i działania krytyczne dla działania firmy. Te warstwy usług są zróżnicowane przez różne rozmiary obliczeń, projekty o wysokiej dostępności, metody izolacji błędów, typy i rozmiary magazynu oraz zakresy operacji we/wy.

Należy oddzielnie skonfigurować wymagane przechowywanie i okres przechowywania kopii zapasowych. Aby ustawić okres przechowywania kopii zapasowych, Otwórz Azure Portal, przejdź do serwera (nie bazy danych), a następnie przejdź do pozycji **Zarządzanie kopiami zapasowymi** > **Konfiguracja** > **przywracania punktu w czasie w programie Configuration** > **7 — 35 dni**.

W poniższej tabeli opisano różnice między tymi trzema warstwami:

||**Zastosowania ogólne**|**Krytyczne dla działania firmy**|**Hiperskali**|
|---|---|---|---|
|Najlepsza dla|Większość obciążeń firmowych. Oferuje zorientowane na budżety, zrównoważone i skalowalne Opcje obliczeniowe i magazynowe.|Aplikacje biznesowe o wysokich wymaganiach we/wy. Oferuje największą odporność na błędy przy użyciu kilku izolowanych replik.|Większość obciążeń firmowych z wysoce skalowalnym magazynem i wymaganiami dotyczącymi skali odczytu.|
|Wystąpienia obliczeniowe|**Zainicjowane obliczenie**:<br/>Obliczenia od 1 do 24 rdzeni wirtualnych<br/>5 rdzeń od 2 do 80 rdzeni wirtualnych<br/>**Obliczenia**bezserwerowe:<br/>5 rdzeń 0,5 – 16 rdzeni wirtualnych|**Zainicjowane obliczenie**:<br/>Obliczenia od 1 do 24 rdzeni wirtualnych<br/>5 rdzeń od 2 do 80 rdzeni wirtualnych|**Zainicjowane obliczenie**:<br/>Obliczenia od 1 do 24 rdzeni wirtualnych<br/>5 rdzeń od 2 do 80 rdzeni wirtualnych|
|Memory (Pamięć)|**Zainicjowane obliczenie**:<br/>Obliczenia 7 GB na rdzeń wirtualny<br/>5 rdzeń 5,1 GB na rdzeń wirtualny<br/>**Obliczenia**bezserwerowe:<br/>5 rdzeń Do 24 GB na rdzeń wirtualny|**Zainicjowane obliczenie**:<br/>Obliczenia 7 GB na rdzeń wirtualny<br/>5 rdzeń 5,1 GB na rdzeń wirtualny |**Zainicjowane obliczenie**:<br/>Obliczenia 7 GB na rdzeń wirtualny<br/>5 rdzeń 5,1 GB na rdzeń wirtualny|
|Magazyn|Używa magazynu zdalnego.<br/>**Pojedyncze bazy danych i alokowane pule elastyczne**:<br/>5 GB – 4 TB<br/>**Obliczenia**bezserwerowe:<br/>5 GB — 3 TB<br/>**Wystąpienie zarządzane**: 32 GB — 8 TB |Używa lokalnego magazynu SSD.<br/>**Pojedyncze bazy danych i alokowane pule elastyczne**:<br/>5 GB – 4 TB<br/>**Wystąpienie zarządzane**:<br/>32 GB — 4 TB |Elastyczna automatyczne zwiększanie magazynu zgodnie z wymaganiami. Obsługuje do 100 TB pamięci masowej. Używa lokalnego magazynu SSD dla lokalnej pamięci podręcznej puli buforów i lokalnego magazynu danych. Używa magazynu zdalnego platformy Azure jako końcowego długoterminowego magazynu danych. |
|Przepływność we/wy (przybliżona)|**Pojedyncza baza danych i Pula elastyczna**: 500 operacji we/wy na sekundę na 40000 rdzeń wirtualny maksymalnej liczby operacji we/wy na sekundę.<br/>**Wystąpienie zarządzane**: Zależy od [rozmiaru pliku](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 operacji we/wy na rdzeń do 200 000 maksymalnej liczby IOPS|Skalowanie jest architekturą wielowarstwową z buforowaniem na wielu poziomach. Efektywne operacje we/wy będą zależeć od obciążenia.|
|Dostępność|1 replika, brak replik w skali odczytu|3 repliki, 1 replika w [skali odczytu](sql-database-read-scale-out.md),<br/>Strefa — nadmiarowa wysoka dostępność (HA)|1 replika odczytu i zapisu oraz 0-4 [replik w skali odczytu](sql-database-read-scale-out.md)|
|Tworzenie kopii zapasowych|[Magazyn Geograficznie nadmiarowy do odczytu (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dni (domyślnie 7 dni)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dni (domyślnie 7 dni)|Tworzenie kopii zapasowych opartych na migawce w magazynie zdalnym platformy Azure. Przywraca używanie tych migawek do szybkiego odzyskiwania. Kopie zapasowe są natychmiast i nie wpływają na wydajność obliczeń we/wy. Przywracanie odbywa się szybko i nie jest operacją o rozmiarze danych (w minutach, a nie w godzinach lub dniach).|
|W pamięci|Nieobsługiwane|Obsługiwane|Nieobsługiwane|
|||

> [!NOTE]
> Możesz uzyskać bezpłatną bazę danych SQL Azure w warstwie Podstawowa usługi w połączeniu z bezpłatnym kontem platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie zarządzanej bazy danych w chmurze przy użyciu bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/services/sql-database/).

- Aby uzyskać więcej informacji na temat limitów zasobów rdzeń wirtualny, zobacz [limity zasobów rdzeń wirtualny w ramach jednej bazy danych](sql-database-vcore-resource-limits-single-databases.md) i [limitów zasobów rdzeń wirtualny w wystąpieniu zarządzanym](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Aby uzyskać więcej informacji o warstwach usług ogólnego przeznaczenia i krytycznych dla firmy, zobacz [Ogólne zastosowania i najważniejsze usługi dla działalności biznesowej](sql-database-service-tiers-general-purpose-business-critical.md).
- Aby uzyskać więcej informacji na temat warstwy usługi na potrzeby skalowania w modelu zakupu opartego na rdzeń wirtualny, zobacz podskalowanie [warstwy usług](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Korzyści użycia hybrydowego platformy Azure

W warstwie obliczeniowej z zainicjowaną obsługą modelu zakupu opartego na rdzeń wirtualny można wymienić istniejące licencje dla obniżonych stawek na SQL Database przy użyciu [korzyść użycia hybrydowego platformy Azure do SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Ta korzyść platformy Azure umożliwia oszczędzanie do 30 procent na Azure SQL Database przy użyciu lokalnych licencji SQL Server z programem Software Assurance.

![cennik](./media/sql-database-service-tiers/pricing.png)

Za pomocą Korzyść użycia hybrydowego platformy Azure Możesz wybrać opcję płacenia wyłącznie za podstawową infrastrukturę platformy Azure, korzystając z istniejącej licencji SQL Server dla aparatu bazy danych SQL (podstawowa cena obliczeniowa) lub płacisz za podstawową infrastrukturę i SQL Server Licencja (cena uwzględniona w licencji).

Możesz wybrać lub zmienić model licencjonowania przy użyciu Azure Portal lub przy użyciu jednego z następujących interfejsów API:

- Aby ustawić lub zaktualizować typ licencji przy użyciu programu PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Aby ustawić lub zaktualizować typ licencji przy użyciu interfejsu wiersza polecenia platformy Azure:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [AZ SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Aby ustawić lub zaktualizować typ licencji przy użyciu interfejsu API REST:

  - [Bazy danych — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Bazy danych — aktualizacja](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Wystąpienia zarządzane — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Wystąpienia zarządzane — aktualizacja](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrowanie z modelu opartego na jednostkach DTU do modelu opartego na rdzeń wirtualny

### <a name="migrate-a-database"></a>Migrowanie bazy danych

Migrowanie bazy danych z modelu zakupu opartego na jednostkach DTU do modelu zakupu opartego na rdzeń wirtualny jest podobne do uaktualnienia lub obniżenia poziomu warstwy usług standardowa i Premium w modelu zakupu opartego na jednostkach DTU.

### <a name="migrate-databases-with-geo-replication-links"></a>Migrowanie baz danych za pomocą linków replikacji geograficznej

Migracja z modelu opartego na jednostkach DTU do modelu zakupu opartego na rdzeń wirtualny jest podobna do uaktualnienia lub obniżenia poziomu relacji replikacji geograficznej między bazami danych w warstwach usług standardowa i Premium. Podczas migracji nie trzeba zatrzymać replikacji geograficznej, ale należy przestrzegać następujących reguł sekwencjonowania:

- Podczas uaktualniania należy najpierw uaktualnić pomocniczą bazę danych, a następnie uaktualnić podstawową.
- W przypadku zmiany wersji na starszą należy odwrócić kolejność: najpierw należy zmienić podstawową bazę danych, a następnie ponownie obniżyć poziom pomocniczy.

W przypadku korzystania z replikacji geograficznej między dwoma elastycznymi pulami zalecamy wyznaczanie jednej puli jako głównej, a drugiej jako pomocniczej. W takim przypadku podczas migrowania pul elastycznych należy stosować te same wskazówki dotyczące sekwencjonowania. Jeśli jednak istnieją pule elastyczne zawierające podstawowe i pomocnicze bazy danych, należy traktować pulę przy użyciu wyższego użycia jako podstawowego i odpowiednio przestrzegać reguł sekwencjonowania.  

W poniższej tabeli przedstawiono wskazówki dotyczące określonych scenariuszy migracji:

|Bieżąca warstwa usługi|Docelowa warstwa usługi|Typ migracji|Akcje użytkownika|
|---|---|---|---|
|Standardowa (Standard)|Ogólnego przeznaczenia|Linię|Można migrować w dowolnej kolejności, ale trzeba zapewnić odpowiednie rozmiary rdzeń wirtualny *|
|Premium|Krytyczne dla działania firmy|Linię|Można migrować w dowolnej kolejności, ale trzeba zapewnić odpowiednie rozmiary rdzeń wirtualny *|
|Standardowa (Standard)|Krytyczne dla działania firmy|Uaktualnienie|Najpierw należy przeprowadzić migrację pomocniczą|
|Krytyczne dla działania firmy|Standardowa (Standard)|Zmień na starszą wersję|Najpierw należy zmigrować podstawowe|
|Premium|Ogólnego przeznaczenia|Zmień na starszą wersję|Najpierw należy zmigrować podstawowe|
|Ogólnego przeznaczenia|Premium|Uaktualnienie|Najpierw należy przeprowadzić migrację pomocniczą|
|Krytyczne dla działania firmy|Ogólnego przeznaczenia|Zmień na starszą wersję|Najpierw należy zmigrować podstawowe|
|Ogólnego przeznaczenia|Krytyczne dla działania firmy|Uaktualnienie|Najpierw należy przeprowadzić migrację pomocniczą|
||||

\*Co 100 DTU w warstwie Standardowa wymaga co najmniej 1 rdzeń wirtualny, a każdy 125 DTU w warstwie Premium wymaga co najmniej 1 rdzeń wirtualny.

### <a name="migrate-failover-groups"></a>Migrowanie grup trybu failover

Migracja grup trybu failover z wieloma bazami danych wymaga pojedynczej migracji podstawowych i pomocniczych baz danych. W tym procesie obowiązują te same zagadnienia i reguły sekwencjonowania. Po konwersji baz danych do modelu zakupu opartego na rdzeń wirtualny Grupa trybu failover będzie obowiązywać z tymi samymi ustawieniami zasad.

### <a name="create-a-geo-replication-secondary-database"></a>Tworzenie pomocniczej bazy danych replikacji geograficznej

Pomocniczą bazę danych replikacji geograficznej (geograficzną) można utworzyć tylko przy użyciu tej samej warstwy usług, która została użyta dla podstawowej bazy danych. W przypadku baz danych o wysokim współczynniku generowania dzienników zalecamy utworzenie pomocniczej lokalizacji geograficznej z tym samym rozmiarem obliczeniowym co podstawowy.

Jeśli tworzysz geograficzną lokację w puli elastycznej dla pojedynczej podstawowej bazy danych, upewnij się, że `maxVCore` ustawienie dla puli odpowiada rozmiarowi obliczeniowemu podstawowej bazy danych. Jeśli tworzysz geograficzną lokację główną w innej puli elastycznej, zalecamy, aby pule miały te same `maxVCore` ustawienia.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Użycie kopii bazy danych w celu przekonwertowania bazy danych opartej na jednostkach DTU na bazę danych opartą na rdzeń wirtualny

Możesz skopiować dowolną bazę danych z rozmiarem obliczeń opartym na jednostkach DTU do bazy danych o rozmiarze obliczeń opartym na rdzeń wirtualny bez ograniczeń lub specjalnej sekwencjonowania, o ile docelowy rozmiar obliczeń obsługuje maksymalny rozmiar bazy danych źródłowej bazy danych. Kopia bazy danych tworzy migawkę danych w czasie rozpoczęcia operacji kopiowania i nie synchronizuje danych między źródłem a obiektem docelowym.

## <a name="next-steps"></a>Następne kroki

- Dla określonych rozmiarów obliczeń i opcji rozmiaru magazynu dostępnych dla pojedynczych baz danych zobacz [SQL Database limity zasobów opartych na rdzeń wirtualny dla pojedynczych baz danych](sql-database-vcore-resource-limits-single-databases.md).
- W przypadku określonych rozmiarów obliczeń i opcji rozmiaru magazynu dla pul elastycznych zobacz [SQL Database limity zasobów opartych na rdzeń wirtualny dla pul elastycznych](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
