---
title: Migrowanie wystąpienia programu SQL Server do wystąpienia zarządzane bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację wystąpienia programu SQL Server do wystąpienia zarządzane bazy danych SQL Azure.
keywords: migracja bazy danych,migracja bazy danych programu sql server,narzędzia migracji bazy danych,migracja bazy danych,migracja bazy danych sql
services: sql-database
author: bonova
ms.reviewer: carlrab
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: article
ms.date: 04/10/2018
ms.author: bonova
ms.openlocfilehash: 5b8a2ec7e0401ac239acdefdd77a13b522f73960
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>Migracja wystąpienia programu SQL Server do wystąpienia zarządzane bazy danych SQL Azure

W tym artykule opisano metody migracji do usługi Azure bazy danych zarządzanych wystąpienia SQL (wersja zapoznawcza) programu SQL Server 2005 lub nowszej wersji. 

Wystąpienie zarządzane usługi SQL Database to rozszerzenie istniejącej usługi SQL Database, które zapewnia trzecią opcję wdrożenia (poza pojedynczymi bazami danych i pulami elastycznymi).  Aby włączyć bazę danych przyrostu i shift do w pełni zarządzane rozwiązanie typu PaaS, bez zmiany projektu aplikacji zaprojektowano go. Wystąpienie zarządzane usługi SQL Database zapewnia wysoką zgodność z lokalnym modelem programowania programu SQL Server oraz gotową do użycia obsługę znacznej większości funkcji programu SQL Server, a także towarzyszących narzędzi i usług.

Na wysokim poziomie proces migracji aplikacji wygląda na poniższym diagramie:

![proces migracji](./media/sql-database-managed-instance-migration/migration-process.png)

- [Ocena zgodności zarządzanego wystąpienia](sql-database-managed-instance-migrate.md#assess-managed-instance-compatibility)
- [Wybierz opcję połączenie aplikacji](sql-database-managed-instance-migrate.md#choose-app-connectivity-option)
- [Wdrażanie na wystąpienie optymalnie rozmiarze zarządzane](sql-database-managed-instance-migrate.md#deploy-to-an-optimally-sized-managed-instance)
- [Wybierz metodę migracji i migracji](sql-database-managed-instance-migrate.md#select-migration-method-and-migrate)
- [Monitorowanie aplikacji](sql-database-managed-instance-migrate.md#monitor-applications)

> [!NOTE]
> Aby przeprowadzić migrację jednej bazy danych do jednej bazy danych lub puli elastycznej, zobacz [migrację bazy danych programu SQL Server do bazy danych SQL Azure](sql-database-cloud-migrate.md).

## <a name="assess-managed-instance-compatibility"></a>Ocena zgodności zarządzanego wystąpienia

Najpierw Ustal, czy wystąpienie zarządzanych jest zgodny z wymaganiami bazy danych aplikacji. Zarządzane wystąpienia umożliwia łatwe przyrostu i shift migracji dla większości istniejących aplikacji korzystających z programu SQL Server — lokalnie lub w przypadku maszyn wirtualnych. Jednak czasami może wymagać funkcje lub możliwości, które nie są jeszcze obsługiwane i kosztów wdrożenia obejście tego problemu są zbyt duże. 

Użyj [Asystenta migracji danych (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) wykryć potencjalne problemy ze zgodnością wpływa na procesy funkcji bazy danych w bazie danych SQL Azure. DMA nie obsługuje jeszcze zarządzane wystąpienia jako miejsce docelowe migracji, ale zalecane jest uruchamianie oceny dla bazy danych SQL Azure, a dokładnie przejrzyj listę funkcji zgłoszone parzystości i problemy ze zgodnością z dokumentacją produktu. Większość problemów z blokowaniem uniemożliwia migracji do usługi Azure SQL Database zostały usunięte z wystąpieniem zarządzane. Wystąpienie, funkcje, takie jak zapytania między bazami danych, transakcje między bazami danych w tym samym wystąpieniu połączonego serwera do innych źródeł, CLR, globalne tymczasowego tabel SQL, widoki poziomu wystąpienia, brokera usług i podobne są dostępne w zarządzanych wystąpień. 

Jednak istnieją przypadki, gdy należy wziąć pod uwagę opcję alternatywnych, takich jak [programu SQL Server na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Oto kilka przykładów:

- Jeśli potrzebujesz bezpośredni dostęp do systemu operacyjnego lub systemu plików, na przykład do innej instalacji lub niestandardowych agentów na tę samą maszynę wirtualną z programem SQL Server.
- Jeśli masz strict zależności od funkcji, które jeszcze nie są obsługiwane, takie jak FileStream / FileTable, aparat PolyBase i wystąpienia między transakcji.
- Jeśli bezwzględnie konieczne pozostać na określonej wersji programu SQL Server (2012, na przykład).
- Jeśli wymagania obliczeniowe są znacznie niższe danego wystąpienia zarządzane oferuje w publicznej wersji zapoznawczej (jeden vCore dla wystąpienia) i konsolidacji bazy danych nie jest dopuszczalne opcji.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Wdrażanie na wystąpienie optymalnie rozmiarze zarządzane

Zarządzane wystąpienia został zoptymalizowany pod kątem obciążeń lokalnych, które są planowane przenosić do chmury. Podaj nowy model kupna, który zapewnia większą elastyczność w wyborze odpowiedniego poziomu zasobów dla obciążeń. Można na świecie lokalnych są prawdopodobnie przyzwyczajony do zmiany rozmiaru te obciążenia za pomocą fizycznych rdzeni. Nowy model kupna zarządzane wystąpienia opiera się na wirtualnych rdzeni, lub "vCores" dodatkowego miejsca do magazynowania i we/wy dostępne oddzielnie. VCore model jest prostszy sposób, aby zrozumieć wymagania obliczeniowych w chmurze czy należy użyć lokalnej dzisiaj. Ten nowy model umożliwia utworzenie odpowiedniego rozmiaru docelowego środowiska w chmurze.

Można wybrać obliczeniowych i zasobów magazynu na wdrożenie czasu, a następnie zmień ją później bez przestojów w przypadku aplikacji.

![zarządzane wystąpienia zmiany rozmiaru](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Informacje na temat tworzenia infrastruktury sieci wirtualnej i wystąpienie zarządzanych, zobacz [utworzyć wystąpienia zarządzanego](sql-database-managed-instance-create-tutorial-portal.md).

> [!IMPORTANT]
> Ważne jest, aby zachować sieci docelowej sieci wirtualnej i podsieci zawsze zgodnie z [wymagania zarządzane sieci Wirtualnej wystąpienia](sql-database-managed-instance-vnet-configuration.md#requirements). Niezgodności może uniemożliwić tworzenie nowych wystąpień lub przy użyciu tych, które zostały już utworzone.

## <a name="select-migration-method-and-migrate"></a>Wybierz metodę migracji i migracji

Zarządzane scenariusze użytkownika elementy docelowe wystąpienie wymagające masowej bazy danych migracji z lokalnymi lub IaaS implementacje bazy danych. Są one optymalny wybór, gdy trzeba Podnieś i przesunięcia zaplecza aplikacji, które regularnie Użyj wystąpienia poziomu i / lub między bazami danych funkcje. Jeśli jest to scenariusz, można przenieść całego wystąpienia odpowiedniego środowiska platformy Azure bez konieczności rearchitecture aplikacji. 

Aby przenieść te wystąpienia programu SQL, należy starannie zaplanować:

-   Migracja wszystkich baz danych, które muszą być wspólnie rozmieszczonych (uruchomiony na tym samym wystąpieniu te)
-   Migracja obiektów poziomie wystąpienia, które aplikacja jest zależna od, w tym logowania, poświadczenia zadania agenta SQL i operatory i wyzwalaczy na poziomie serwera. 

Zarządzanego wystąpienia jest w pełni zarządzaną usługę, która umożliwia delegowanie regularne działania DBA dla platformy, ponieważ zostały one utworzone. W związku z tym niektóre danych na poziomie wystąpienia nie trzeba poddane migracji, takie jak zadania konserwacji dla regularnych kopii zapasowych lub konfiguracji zawsze włączonej [wysokiej dostępności](sql-database-high-availability.md) jest wbudowane.

Zarządzane wystąpienie obsługuje następujące opcje migracji bazy danych (obecnie są to tylko migracji obsługiwanej metody):

- Usługa Azure migracji bazy danych — migracji przestojów bliskie zeru
- Natywny RESTORE z adresu URL - korzysta natywnych kopii zapasowych z programu SQL Server i wymaga Przestój
- Przeprowadzić migrację za pomocą pliku pliku BACPAC — używa pliku BACPAC pliku z serwera SQL lub bazy danych SQL i wymaga Przestój

### <a name="azure-database-migration-service"></a>Usługa migracji bazy danych platformy Azure

[Azure migracji usługi BAZĘ](../dms/dms-overview.md) to w pełni zarządzana usługa zaprojektowana w celu umożliwienia bezproblemowego migracji z wielu źródeł bazy danych do platformy Azure danych z minimalnym czasem przestojów. Ta usługa upraszcza zadania wymagane do przenoszenia istniejącego innych firm i baz danych programu SQL Server na platformie Azure. Opcje wdrażania w publicznej wersji zapoznawczej obejmują usługi Azure SQL Database, zarządzane wystąpienia i programu SQL Server w maszynie wirtualnej platformy Azure. Usługa DMS jest zalecaną metodą migracji obciążeń przedsiębiorstwa. 

Aby dowiedzieć się więcej na temat tego scenariusza i konfiguracji kroki dla DMS, zobacz [migracji lokalnej bazy danych do wystąpienia zarządzane przy użyciu DMS](../dms/tutorial-sql-server-to-managed-instance.md).  

### <a name="native-restore-from-url"></a>Natywny przywracania z adresu URL

Przywróć natywnego wykonywania kopii zapasowych (pliki z rozszerzeniem bak) pobranych z lokalnej instalacji programu SQL Server lub [programu SQL Server na maszynach wirtualnych](https://azure.microsoft.com/services/virtual-machines/sql-server/), dostępnych na [usługi Azure Storage](https://azure.microsoft.com/services/storage/), jest jednym z kluczowych możliwości wystąpienia zarządzane bazy danych serwera SQL z który Umożliwia szybkie i łatwe w tryb offline bazy danych migracji. 

Poniższy diagram opisano proces na wysokim poziomie:

![Przepływ migracji](./media/sql-database-managed-instance-migration/migration-flow.png)

Poniższa tabela zawiera więcej informacji dotyczących metody, których można używać w zależności od wersji programu SQL Server źródła, które są uruchomione:

|Krok|Aparat SQL i wersji|Backup / Restore — metoda|
|---|---|---|
|Umieść kopii zapasowych do magazynu Azure|Wcześniejsze SQL 2012 z dodatkiem SP1 CU2|Przekaż plik bak bezpośrednio do magazynu Azure|
||2012 Z DODATKIEM SP1 CU2 - 2016|Bezpośrednie kopii zapasowej za pomocą przestarzałe [WITH CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) składni|
||2016 i powyżej.|Przy użyciu kopii zapasowej bezpośrednio [z POŚWIADCZEŃ sygnatury dostępu Współdzielonego](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Przywróć z magazynu Azure do zarządzanego wystąpienia|[Przywróć z adresu URL z POŚWIADCZEŃ sygnatury dostępu Współdzielonego](sql-database-managed-instance-restore-from-backup-tutorial.md)|

> [!IMPORTANT]
> Przywracanie bazy danych systemu nie jest obsługiwane. Aby przeprowadzić migrację obiektów na poziomie wystąpienia (przechowywane w bazach danych master i msdb), zaleca się ich skryptów i uruchamianie skryptów T-SQL w wystąpieniu docelowym.

Pełna samouczek obejmuje przywracanie kopii zapasowej bazy danych do wystąpienia zarządzane przy użyciu poświadczeń sygnatury dostępu Współdzielonego, zobacz [Przywracanie z kopii zapasowej do wystąpienia zarządzane](sql-database-managed-instance-restore-from-backup-tutorial.md).

### <a name="migrate-using-bacpac-file"></a>Przeprowadzić migrację za pomocą pliku BACPAC pliku

Można zaimportować do bazy danych SQL Azure i zarządzane wystąpienia z Utwórz kopię oryginalnej bazy danych z danymi w pliku pliku BACPAC. Zobacz [Importowanie pliku pliku BACPAC do nowej bazy danych SQL Azure](sql-database-import.md).

## <a name="monitor-applications"></a>Monitorowanie aplikacji

Śledź zachowanie aplikacji oraz wydajności po migracji. W przypadku zarządzanych, niektóre zmiany tylko są włączone raz [poziom zgodności bazy danych został zmieniony](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database). Migracja bazy danych do bazy danych SQL Azure śledzi jego oryginalnej poziom zgodności, w większości przypadków. Jeśli poziom zgodności bazy danych użytkowników 100 lub nowszej przed migracją, jest taka sama po migracji. Jeśli poziom zgodności bazy danych użytkowników 90 przed migracją w uaktualniona baza danych, poziom zgodności wynosi 100, czyli poziom najniższa zgodności obsługiwanych w przypadku zarządzanych. Poziom zgodności bazy danych systemu jest 140.

Aby zmniejszyć ryzyko migracji, należy zmienić poziom zgodności bazy danych tylko po monitorowania wydajności. Użyj magazynu zapytań jako optymalne narzędzia do pobierania informacji o wydajności obciążeń przed i po zmiany poziomu zgodności bazy danych, zgodnie z objaśnieniem w [utrzymać wydajność stabilność podczas uaktualnienia do nowszej wersji programu SQL Server](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).

Po w pełni zarządzana platforma zająć korzyści, które znajdują się automatycznie w ramach usługi bazy danych SQL. Na przykład nie trzeba tworzyć kopie zapasowe w wystąpieniu zarządzane — usługa wykonuje kopie zapasowe można automatycznie. Już nie musisz martwić o planowania, pobieranie i zarządzanie kopiami zapasowymi. Zarządzane wystąpienia zapewnia możliwość przywrócenia do dowolnego punktu w czasie, w tym przechowywania okresu przy użyciu [punktu w czasie odzyskiwania (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Okres przechowywania w czasie publicznej wersji zapoznawczej, jest ustalona na siedem dni.
Ponadto nie trzeba martwić się o konfigurowaniu wysokiej dostępności jako [wysokiej dostępności](sql-database-high-availability.md) jest wbudowane.

Do zwiększenia poziomu zabezpieczeń, należy rozważyć użycie niektórych funkcji, które są dostępne:
- Uwierzytelniania usługi Azure Active Directory na poziomie bazy danych
- Inspekcja i wykrywanie zagrożeń można monitorować działania
- Kontrolowanie dostępu do danych poufnych i uprzywilejowane ([zabezpieczenia na poziomie wiersza](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) i [dynamicznego maskowania danych](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)).

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje na temat zarządzanych wystąpień, zobacz [co to jest wystąpieniem zarządzane?](sql-database-managed-instance.md).
- Samouczek, która obejmuje przywrócenie z kopii zapasowej, zobacz [utworzyć wystąpienia zarządzanego](sql-database-managed-instance-create-tutorial-portal.md).
- Samouczek przedstawiający migracji za pomocą DMS, zobacz [migracji lokalnej bazy danych do wystąpienia zarządzane przy użyciu DMS](../dms/tutorial-sql-server-to-managed-instance.md).  
