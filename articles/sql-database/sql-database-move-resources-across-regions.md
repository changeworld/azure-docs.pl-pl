---
title: Jak przenieść zasoby do innego regionu
description: Dowiedz się, jak przenieść usługę Azure SQL Database, platformę Azure SQL elastic pool lub wystąpienie zarządzanego usługi Azure SQL do innego regionu.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 851ef49a5c066f12a95baa54daf5e267cb4278c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821431"
---
# <a name="how-to-move-azure-sql-resources-to-another-region"></a>Jak przenieść zasoby SQL platformy Azure do innego regionu

W tym artykule opisano ogólny przepływ pracy dotyczący sposobu przenoszenia pojedynczej bazy danych usługi Azure SQL Database, puli elastycznej i wystąpienia zarządzanego do nowego regionu. 

## <a name="overview"></a>Omówienie

Istnieją różne scenariusze, w których chcesz przenieść istniejące zasoby SQL platformy Azure z jednego regionu do drugiego. Na przykład można rozszerzyć działalność do nowego regionu i chcesz zoptymalizować go pod kątem nowej bazy klientów. Lub należy przenieść operacje do innego regionu ze względu na zgodność. Lub platforma Azure wydała zupełnie nowy region, który zapewnia lepszą bliskość i poprawia jakość obsługi klienta.  

Ten artykuł zawiera ogólny przepływ pracy do przenoszenia zasobów do innego regionu. Przepływ pracy składa się z następujących kroków: 

- Sprawdź wymagania wstępne dla przeniesienia 
- Przygotowanie do przenoszenia zasobów w zakresie
- Monitorowanie procesu przygotowania
- Testowanie procesu przenoszenia
- Inicjowanie rzeczywistego ruchu 
- Usuwanie zasobów z regionu źródłowego 


> [!NOTE]
> Ten artykuł dotyczy migracji w chmurze publicznej platformy Azure lub w tej samej suwerennej chmurze. 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-single-database"></a>Przenoszenie pojedynczej bazy danych

### <a name="verify-prerequisites"></a>Weryfikowanie wymagań wstępnych 

1. Utwórz docelowy serwer logiczny dla każdego serwera źródłowego. 
1. Skonfiguruj zaporę z odpowiednimi wyjątkami za pomocą [programu PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).  
1. Skonfiguruj serwery logiczne z prawidłowymi loginami. Jeśli nie jesteś administratorem subskrypcji lub administratorem serwera SQL, współpracuj z administratorem, aby przypisać potrzebne uprawnienia. Aby uzyskać więcej informacji, zobacz [Jak zarządzać zabezpieczeniami bazy danych SQL platformy Azure po odzyskiwaniu po awarii](sql-database-geo-replication-security-config.md). 
1. Jeśli bazy danych są szyfrowane za pomocą TDE i używają własnego klucza szyfrowania w magazynie kluczy platformy Azure, upewnij się, że poprawny materiał szyfrowania jest aprowigowany w regionach docelowych. Aby uzyskać więcej informacji, zobacz [Przezroczyste szyfrowanie danych SQL platformy Azure z kluczami zarządzanymi przez klienta w usłudze Azure Key Vault](transparent-data-encryption-byok-azure-sql.md)
1. Jeśli inspekcja na poziomie bazy danych jest włączona, wyłącz ją i włącz inspekcję na poziomie serwera. Po przekroczeniu trybu failover inspekcja na poziomie bazy danych będzie wymagać ruchu między regionami, który nie jest pożądany lub możliwy po przeprowadzce. 
1. W przypadku inspekcji na poziomie serwera upewnij się, że:
   - Kontener magazynu, usługa Log Analytics lub centrum zdarzeń z istniejącymi dziennikami inspekcji są przenoszone do regionu docelowego. 
   - Inspekcja jest skonfigurowana na serwerze docelowym. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do inspekcji bazy danych SQL](sql-database-auditing.md). 
1. Jeśli wystąpienie ma długoterminowe zasady przechowywania (LTR), istniejące kopie zapasowe LTR pozostaną skojarzone z bieżącym serwerem. Ponieważ serwer docelowy jest inny, będzie można uzyskać dostęp do starszych kopii zapasowych LTR w regionie źródłowym przy użyciu serwera źródłowego, nawet jeśli serwer zostanie usunięty. 

  > [!NOTE]
  > Będzie to niewystarczające do przejścia między suwerenną chmurą a regionem publicznym. Taka migracja będzie wymagać przeniesienia kopii zapasowych LTR na serwer docelowy, który nie jest obecnie obsługiwany. 

### <a name="prepare-resources"></a>Przygotowywanie zasobów

1. Utwórz [grupę trybu failover](sql-database-single-database-failover-group-tutorial.md#2---create-the-failover-group) między serwerem logicznym źródła a serwerem logicznym obiektu docelowego.  
1. Dodaj bazy danych, które chcesz przenieść do grupy trybu failover. 
    - Replikacja wszystkich dodanych baz danych zostanie zainicjowana automatycznie. Aby uzyskać więcej informacji, zobacz [Najważniejsze wskazówki dotyczące używania grup trybu failover z pojedynczymi bazami danych](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 
 
### <a name="monitor-the-preparation-process"></a>Monitorowanie procesu przygotowania

Okresowo można wywołać [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) do monitorowania replikacji baz danych ze źródła do obiektu docelowego. Obiekt wyjściowy `Get-AzSqlDatabaseFailoverGroup` zawiera właściwość dla **ReplicationState:** 
   - **ReplicationState = 2** (CATCH_UP) wskazuje, że baza danych jest zsynchronizowana i może być bezpiecznie awaryjna. 
   - **ReplicationState = 0** (SEEDING) wskazuje, że baza danych nie jest jeszcze rozstawiony, a próba pracy awaryjnej zakończy się niepowodzeniem. 

### <a name="test-synchronization"></a>Synchronizacja testów

Gdy **ReplicationState** jest `2`, połączyć się z każdej bazy danych `<fog-name>.secondary.database.windows.net` lub podzbiór baz danych przy użyciu pomocniczego punktu końcowego i wykonać wszelkie kwerendy względem baz danych w celu zapewnienia łączności, odpowiedniej konfiguracji zabezpieczeń i replikacji danych. 

### <a name="initiate-the-move"></a>Inicjowanie ruchu

1. Połącz się z serwerem `<fog-name>.secondary.database.windows.net`docelowym przy użyciu pomocniczego punktu końcowego .
1. Użyj [Switch-AzSqlDatabaseFailoverGroup,](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) aby przełączyć pomocnicze wystąpienie zarządzane jako podstawowe z pełną synchronizacją. Ta operacja zakończy się pomyślnie lub zostanie wycofana. 
1. Sprawdź, czy polecenie zostało `nslook up <fog-name>.secondary.database.windows.net` pomyślnie ukończone, aby upewnić się, że wpis CNAME DNS wskazuje adres IP regionu docelowego. Jeśli polecenie switch nie powiedzie się, CNAME nie zostanie zaktualizowany. 

### <a name="remove-the-source-databases"></a>Usuwanie źródłowych baz danych

Po zakończeniu przenoszenia usuń zasoby w regionie źródłowym, aby uniknąć niepotrzebnych opłat. 

1. Usuń grupę trybu failover przy użyciu [programu Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). 
1. Usuń każdą źródłową bazę danych przy użyciu [remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) dla każdej z baz danych na serwerze źródłowym. Spowoduje to automatyczne zakończenie łączy replikacji geograficznej. 
1. Usuń serwer źródłowy za pomocą [remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Usuń magazyn kluczy, kontenery magazynu inspekcji, centrum zdarzeń, wystąpienie usługi AAD i inne zasoby zależne, aby zatrzymać naliczane za nie. 

## <a name="move-elastic-pools"></a>Przenoszenie elastycznych basenów

### <a name="verify-prerequisites"></a>Weryfikowanie wymagań wstępnych 

1. Utwórz docelowy serwer logiczny dla każdego serwera źródłowego. 
1. Skonfiguruj zaporę z odpowiednimi wyjątkami za pomocą [programu PowerShell](scripts/sql-database-create-and-configure-database-powershell.md). 
1. Skonfiguruj serwery logiczne z prawidłowymi loginami. Jeśli nie jesteś administratorem subskrypcji lub administratorem serwera SQL, współpracuj z administratorem, aby przypisać potrzebne uprawnienia. Aby uzyskać więcej informacji, zobacz [Jak zarządzać zabezpieczeniami bazy danych SQL platformy Azure po odzyskiwaniu po awarii](sql-database-geo-replication-security-config.md). 
1. Jeśli bazy danych są szyfrowane za pomocą TDE i używają własnego klucza szyfrowania w magazynie kluczy platformy Azure, upewnij się, że poprawny materiał szyfrowania jest aprowizowana w regionie docelowym.
1. Utwórz docelową pulę elastyczną dla każdej puli elastycznej źródła, upewniając się, że pula jest tworzona w tej samej warstwie usług, o tej samej nazwie i tym samym rozmiarze. 
1. Jeśli inspekcja na poziomie bazy danych jest włączona, wyłącz ją i włącz inspekcję na poziomie serwera. Po przekroczeniu trybu failover inspekcja na poziomie bazy danych będzie wymagać ruchu między regionami, który nie jest pożądany lub możliwy po przeprowadzce. 
1. W przypadku inspekcji na poziomie serwera upewnij się, że:
    - Kontener magazynu, usługa Log Analytics lub centrum zdarzeń z istniejącymi dziennikami inspekcji są przenoszone do regionu docelowego.
    - Konfiguracja inspekcji jest skonfigurowana na serwerze docelowym. Aby uzyskać więcej informacji, zobacz [Inspekcja bazy danych SQL](sql-database-auditing.md).
1. Jeśli wystąpienie ma długoterminowe zasady przechowywania (LTR), istniejące kopie zapasowe LTR pozostaną skojarzone z bieżącym serwerem. Ponieważ serwer docelowy jest inny, będzie można uzyskać dostęp do starszych kopii zapasowych LTR w regionie źródłowym przy użyciu serwera źródłowego, nawet jeśli serwer zostanie usunięty. 

  > [!NOTE]
  > Będzie to niewystarczające do przejścia między suwerenną chmurą a regionem publicznym. Taka migracja będzie wymagać przeniesienia kopii zapasowych LTR na serwer docelowy, który nie jest obecnie obsługiwany. 

### <a name="prepare-to-move"></a>Przygotowanie do ruchu
 
1.  Utwórz oddzielną [grupę trybu failover](sql-database-elastic-pool-failover-group-tutorial.md#3---create-the-failover-group) między każdą pulą elastyczną na źródłowym serwerze logicznym a jego odpowiednikową pulą elastyczną na serwerze docelowym. 
1.  Dodaj wszystkie bazy danych w puli do grupy trybu failover. 
    - Replikacja dodanych baz danych zostanie zainicjowana automatycznie. Aby uzyskać więcej informacji, zobacz [najważniejsze wskazówki dotyczące grup trybu failover z pulami elastycznymi](sql-database-auto-failover-group.md#best-practices-of-using-failover-groups-with-single-databases-and-elastic-pools). 

  > [!NOTE]
  > Chociaż istnieje możliwość utworzenia grupy trybu failover, która zawiera wiele pul elastycznych, zdecydowanie zaleca się utworzenie oddzielnej grupy trybu failover dla każdej puli. Jeśli masz dużą liczbę baz danych w wielu pulach elastycznych, które należy przenieść, można uruchomić kroki przygotowania równolegle, a następnie zainicjować krok przenoszenia równolegle. Ten proces będzie skalować się lepiej i zajmie mniej czasu w porównaniu do wielu pul elastycznych w tej samej grupie trybu failover. 

### <a name="monitor-the-preparation-process"></a>Monitorowanie procesu przygotowania

Okresowo można wywołać [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) do monitorowania replikacji baz danych ze źródła do obiektu docelowego. Obiekt wyjściowy `Get-AzSqlDatabaseFailoverGroup` zawiera właściwość dla **ReplicationState:** 
   - **ReplicationState = 2** (CATCH_UP) wskazuje, że baza danych jest zsynchronizowana i może być bezpiecznie awaryjna. 
   - **ReplicationState = 0** (SEEDING) wskazuje, że baza danych nie jest jeszcze rozstawiony, a próba pracy awaryjnej zakończy się niepowodzeniem. 

### <a name="test-synchronization"></a>Synchronizacja testów
 
Gdy **ReplicationState** jest `2`, połączyć się z każdej bazy danych `<fog-name>.secondary.database.windows.net` lub podzbiór baz danych przy użyciu pomocniczego punktu końcowego i wykonać wszelkie kwerendy względem baz danych w celu zapewnienia łączności, odpowiedniej konfiguracji zabezpieczeń i replikacji danych. 

### <a name="initiate-the-move"></a>Inicjowanie ruchu
 
1. Połącz się z serwerem `<fog-name>.secondary.database.windows.net`docelowym przy użyciu pomocniczego punktu końcowego .
1. Użyj [Switch-AzSqlDatabaseFailoverGroup,](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) aby przełączyć pomocnicze wystąpienie zarządzane jako podstawowe z pełną synchronizacją. Ta operacja zakończy się pomyślnie lub zostanie wycofana. 
1. Sprawdź, czy polecenie zostało `nslook up <fog-name>.secondary.database.windows.net` pomyślnie ukończone, aby upewnić się, że wpis CNAME DNS wskazuje adres IP regionu docelowego. Jeśli polecenie switch nie powiedzie się, CNAME nie zostanie zaktualizowany. 

### <a name="remove-the-source-elastic-pools"></a>Usuń źródłowe pule sprężyste
 
Po zakończeniu przenoszenia usuń zasoby w regionie źródłowym, aby uniknąć niepotrzebnych opłat. 

1. Usuń grupę trybu failover przy użyciu [programu Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup).
1. Usuń każdą grupę elastyczną źródła na serwerze źródłowym za pomocą [opcji Usuń-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool). 
1. Usuń serwer źródłowy za pomocą [remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver). 
1. Usuń magazyn kluczy, kontenery magazynu inspekcji, centrum zdarzeń, wystąpienie usługi AAD i inne zasoby zależne, aby zatrzymać naliczane za nie. 

## <a name="move-managed-instance"></a>Przenoszenie wystąpienia zarządzanego

### <a name="verify-prerequisites"></a>Weryfikowanie wymagań wstępnych
 
1. Dla każdego wystąpienia zarządzanego źródła utwórz docelowe wystąpienie zarządzane o tym samym rozmiarze w regionie docelowym.  
1. Skonfiguruj sieć dla wystąpienia zarządzanego. Aby uzyskać więcej informacji, zobacz [konfiguracja sieci](sql-database-howto-managed-instance.md#network-configuration).
1. Skonfiguruj docelową bazę danych wzorca z poprawnymi loginami. Jeśli nie jesteś administratorem subskrypcji lub administratorem serwera SQL, współpracuj z administratorem, aby przypisać potrzebne uprawnienia. 
1. Jeśli bazy danych są szyfrowane za pomocą TDE i używają własnego klucza szyfrowania w magazynie kluczy platformy Azure, upewnij się, że AKV z identycznymi kluczami szyfrowania istnieje zarówno w regionach źródłowych, jak i docelowych. Aby uzyskać więcej informacji, zobacz [TDE z kluczami zarządzanymi przez klienta w usłudze Azure Key Vault](transparent-data-encryption-byok-azure-sql.md).
1. Jeśli inspekcja jest włączona dla wystąpienia, upewnij się, że:
    - Kontener magazynu lub centrum zdarzeń z istniejącymi dziennikami jest przenoszony do regionu docelowego. 
    - Inspekcja jest skonfigurowana w wystąpieniu docelowym. Aby uzyskać więcej informacji, zobacz [inspekcja za pomocą wystąpienia zarządzanego](sql-database-managed-instance-auditing.md).
1. Jeśli wystąpienie ma długoterminowe zasady przechowywania (LTR), istniejące kopie zapasowe LTR pozostaną skojarzone z bieżącym serwerem. Ponieważ serwer docelowy jest inny, będzie można uzyskać dostęp do starszych kopii zapasowych LTR w regionie źródłowym przy użyciu serwera źródłowego, nawet jeśli serwer zostanie usunięty. 

  > [!NOTE]
  > Będzie to niewystarczające do przejścia między suwerenną chmurą a regionem publicznym. Taka migracja będzie wymagać przeniesienia kopii zapasowych LTR na serwer docelowy, który nie jest obecnie obsługiwany. 

### <a name="prepare-resources"></a>Przygotowywanie zasobów

Utwórz grupę trybu failover między każdym wystąpieniem źródłowym a odpowiednim wystąpieniem docelowym.
    - Replikacja wszystkich baz danych w każdym wystąpieniu zostanie zainicjowana automatycznie. Aby uzyskać więcej informacji, zobacz [Grupy automatycznego pracy awaryjnej.](sql-database-auto-failover-group.md)

 
### <a name="monitor-the-preparation-process"></a>Monitorowanie procesu przygotowania

Okresowo można wywołać [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup?view=azps-2.3.2) do monitorowania replikacji baz danych ze źródła do obiektu docelowego. Obiekt wyjściowy `Get-AzSqlDatabaseFailoverGroup` zawiera właściwość dla **ReplicationState:** 
   - **ReplicationState = 2** (CATCH_UP) wskazuje, że baza danych jest zsynchronizowana i może być bezpiecznie awaryjna. 
   - **ReplicationState = 0** (SEEDING) wskazuje, że baza danych nie jest jeszcze rozstawiony, a próba pracy awaryjnej zakończy się niepowodzeniem. 

### <a name="test-synchronization"></a>Synchronizacja testów

Gdy **ReplicationState** jest `2`, połączyć się z każdej bazy danych `<fog-name>.secondary.database.windows.net` lub podzbiór baz danych przy użyciu pomocniczego punktu końcowego i wykonać wszelkie kwerendy względem baz danych w celu zapewnienia łączności, odpowiedniej konfiguracji zabezpieczeń i replikacji danych. 

### <a name="initiate-the-move"></a>Inicjowanie ruchu 

1. Połącz się z serwerem `<fog-name>.secondary.database.windows.net`docelowym przy użyciu pomocniczego punktu końcowego .
1. Użyj [Switch-AzSqlDatabaseFailoverGroup,](/powershell/module/az.sql/switch-azsqldatabasefailovergroup?view=azps-2.3.2) aby przełączyć pomocnicze wystąpienie zarządzane jako podstawowe z pełną synchronizacją. Ta operacja zakończy się pomyślnie lub zostanie wycofana. 
1. Sprawdź, czy polecenie zostało `nslook up <fog-name>.secondary.database.windows.net` pomyślnie ukończone, aby upewnić się, że wpis CNAME DNS wskazuje adres IP regionu docelowego. Jeśli polecenie switch nie powiedzie się, CNAME nie zostanie zaktualizowany. 

### <a name="remove-the-source-managed-instances"></a>Usuwanie wystąpienia zarządzanych źródła
Po zakończeniu przenoszenia usuń zasoby w regionie źródłowym, aby uniknąć niepotrzebnych opłat. 

1. Usuń grupę trybu failover przy użyciu [programu Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup). Spowoduje to upuszczenie konfiguracji grupy trybu failover i zakończenie połączeń replikacji geograficznej między dwoma wystąpieniami. 
1. Usuń wystąpienie zarządzane źródło za pomocą [usuń-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance). 
1. Usuń wszelkie dodatkowe zasoby w grupie zasobów, takie jak klaster wirtualny, sieć wirtualna i grupa zabezpieczeń. 

## <a name="next-steps"></a>Następne kroki 

[Zarządzaj usługą](sql-database-manage-after-migration.md) Azure SQL Database po jej migracji. 

