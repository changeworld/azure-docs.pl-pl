---
title: Reguły zapory usługi Azure SQL Database i Data Warehouse | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować bazy danych SQL i zapory SQL Data Warehouse przy użyciu reguł zapory na poziomie serwera, aby zarządzać dostępem i konfigurowanie reguł zapory na poziomie bazy danych SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 4f6c98533a2ab1289ca5f1da25c44fe1a77a983c
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353669"
---
# <a name="azure-sql-database-and-sql-data-warehouse-firewall-rules"></a>Reguły zapory usługi Azure SQL Database i SQL Data Warehouse

Microsoft Azure [bazy danych SQL](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) to usługa relacyjnej bazy danych dla platformy Azure i innych aplikacji internetowych. Aby chronić dane, zapora uniemożliwia wszelki dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.

> [!NOTE]
> Ten temat dotyczy serwera Azure SQL oraz baz danych zarówno usługi SQL Database, jak i SQL Data Warehouse utworzonych na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Reguły sieci wirtualnej jako alternatywy dla reguły IP

Oprócz reguły IP zarządza także zapory *reguł sieci wirtualnej*. Reguły sieci wirtualnej są oparte na punkty końcowe usługi sieci wirtualnej. Reguły sieci wirtualnej może być korzystniejsze IP reguł w niektórych przypadkach. Aby dowiedzieć się więcej, zobacz [punkty końcowe usługi sieci wirtualnej i zasad usługi Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Przegląd

Początkowo cały dostęp języka Transact-SQL do serwera Azure SQL jest blokowany przez zaporę. Aby rozpocząć korzystanie z serwera Azure SQL, należy określić co najmniej jedną regułę zapory na poziomie serwera, które umożliwiają dostęp do serwera Azure SQL. Użyj reguł zapory do określenia zakresu dozwolonych adresów IP pochodzących z Internetu oraz możliwości podejmowania przez aplikacje platformy Azure prób połączenia się z serwerem Azure SQL.

Aby selektywnie udzielić dostępu do tylko jednej z baz danych na serwerze Azure SQL, należy utworzyć regułę na poziomie bazy danych dla wymaganej bazy danych. Określ zakres adresów IP dla reguły zapory bazy danych, który wykracza poza zakres adresów IP określony w regule zapory na poziomie serwera, i upewnij się, że adres IP klienta znajduje się w zakresie określonym w regule na poziomie bazy danych.

> [!IMPORTANT]
> Usługa SQL Data Warehouse obsługuje reguły zapory na poziomie serwera i tylko nie obsługuje reguły zapory na poziomie bazy danych.

Próby połączenia z Internetu i platformy Azure muszą najpierw przejść przez zaporę, zanim dotrą do serwera Azure SQL lub usługi SQL Database, jak pokazano na poniższym diagramie:

   ![Diagram opisujący konfigurację zapory.][1]

- **Reguły zapory na poziomie serwera:**

  Te reguły umożliwiają klientom dostęp do całego serwera Azure SQL, oznacza to, że wszystkie bazy danych na tym samym serwerze logicznym. Reguły te są przechowywane w **głównej** bazie danych. Reguły zapory na poziomie serwera można skonfigurować za pomocą portalu lub za pomocą instrukcji języka Transact-SQL. Aby utworzyć reguły zapory na poziomie serwera przy użyciu witryny Azure Portal lub programu PowerShell, musisz być właścicielem bądź współautorem subskrypcji. Aby utworzyć regułę zapory na poziomie serwera przy użyciu języka Transact-SQL, musisz połączyć się z wystąpieniem usługi SQL Database za pomocą identyfikatora logowania podmiotu zabezpieczeń na poziomie serwera lub jako administrator usługi Azure Active Directory (co oznacza, że reguła zapory na poziomie serwera musi zostać pierwotnie utworzona przez użytkownika mającego uprawnienia na poziomie platformy Azure).

- **Reguły zapory na poziomie bazy danych:**

  Te reguły umożliwiają klientom dostęp do niektórych (bezpiecznych) baz danych na tym samym serwerze logicznym. Możesz utworzyć te reguły dla każdej bazy danych (w tym **wzorca** bazy danych) i są przechowywane w poszczególnych bazach danych. Reguły zapory na poziomie bazy danych dla baz danych master i użytkownika tylko mogą być tworzone i zarządzane za pomocą instrukcji języka Transact-SQL i tylko w przypadku, po skonfigurowaniu pierwszej zapory poziomu serwera. Jeśli zakres adresów IP określony w regule zapory na poziomie bazy danych znajduje się poza zakresem określonym w regule zapory na poziomie serwera, dostęp do bazy danych mogą uzyskać tylko ci klienci, którzy mają adresy IP z zakresu ustalonego na poziomie bazy danych. Dla bazy danych można określić maksymalnie 128 reguł zapory na poziomie bazy danych. Aby uzyskać więcej informacji na temat konfigurowania reguł zapory na poziomie bazy danych, zobacz przykład później w tym artykuł i zobacz [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Zalecenie

Firma Microsoft zaleca używanie reguły zapory na poziomie bazy danych w miarę możliwości, aby zwiększyć poziom bezpieczeństwa i uczynić bazę danych bardziej przenośny. Reguły zapory na poziomie serwera powinny być używane dla administratorów i w przypadku, gdy wiele baz danych ma takie same wymagania dotyczące dostępu, a użytkownik nie chce poświęcać czasu na oddzielne konfigurowanie każdej bazy danych.

> [!Important]
> Windows Azure SQL Database obsługuje maksymalnie 128 reguł zapory.
> [!Note]
> Aby uzyskać informacje o przenośnych bazach danych w kontekście ciągłości działalności biznesowej, zobacz [Wymagania dotyczące uwierzytelniania dla odzyskiwania po awarii](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Łączenie się z Internetu

Gdy komputer próbuje połączyć się z Internetu z serwerem bazy danych, zapora najpierw sprawdza źródłowy adres IP żądania i porównuje go z regułami zapory na poziomie bazy danych, której dotyczy żądanie:

- Jeśli adres IP żądania należy do jednego z zakresów określonych w regułach zapory na poziomie bazy danych, jest ustanawiane połączenie z bazą danych SQL Database zawierającą tę regułę.
- Jeśli adres IP żądania nie należy do jednego z zakresów określonych w regułach zapory na poziomie bazy danych, sprawdzane są reguły zapory na poziomie serwera. Jeśli adres IP żądania należy do jednego z zakresów określonych w regułach zapory na poziomie serwera, ustanawiane jest połączenie. Reguły zapory na poziomie serwera mają zastosowanie do wszystkich baz danych SQL na serwerze SQL platformy Azure.  
- Jeśli adres IP żądania nie jest w zakresach określonych w dowolnej reguły zapory na poziomie serwera lub na poziomie bazy danych, żądanie połączenia kończy się niepowodzeniem.

> [!NOTE]
> Aby uzyskać dostęp do usługi Azure SQL Database z komputera lokalnego, upewnij się, że zapora w sieci i na komputerze lokalnym zezwala na komunikację wychodzącą na porcie TCP 1433.

### <a name="connecting-from-azure"></a>Łączenie z platformy Azure

Aby umożliwić aplikacjom z platformy Azure łączenie się z serwerem Azure SQL, należy włączyć połączenia platformy Azure. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera do serwera usługi Azure SQL Database.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.

## <a name="creating-and-managing-firewall-rules"></a>Tworzenie i zarządzanie regułami zapory

Pierwsze ustawienie zapory na poziomie serwera mogą być tworzone za pomocą [witryny Azure portal](https://portal.azure.com/) lub programowo przy użyciu [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql), [wiersza polecenia platformy Azure](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create), lub [ Interfejs API REST](https://docs.microsoft.com/rest/api/sql/firewallrules/firewallrules_createorupdate). Kolejne reguły zapory na poziomie serwera mogą być tworzone i zarządzane przy użyciu tych metod oraz za pomocą języka Transact-SQL.

> [!IMPORTANT]
> Reguły zapory na poziomie bazy danych tylko mogą być tworzone i zarządzane przy użyciu języka Transact-SQL.

Aby poprawić wydajność, reguły zapory na poziomie serwera są tymczasowo przechowywane w pamięci podręcznej na poziomie bazy danych. Aby odświeżyć pamięć podręczną, zobacz [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Możesz użyć [inspekcji usługi SQL Database](sql-database-auditing.md) inspekcji zmian w zaporze serwera i na poziomie bazy danych.

## <a name="manage-firewall-rules-using-the-azure-portal"></a>Zarządzanie regułami zapory za pomocą witryny Azure portal

Aby ustawić regułę zapory na poziomie serwera w witrynie Azure portal, możesz albo przejść do strony Przegląd dla bazy danych Azure SQL lub na stronie Przegląd serwera logicznego usługi Azure Database.

> [!TIP]
> Aby zapoznać się z samouczkiem, zobacz [utworzyć BAZĘ danych przy użyciu witryny Azure portal](sql-database-get-started-portal.md).

### <a name="from-database-overview-page"></a>Na stronie Przegląd bazy danych

1. Aby ustawić regułę zapory na poziomie serwera, na stronie Przegląd bazy danych, kliknij **Ustaw zaporę serwera** na pasku narzędzi, jak pokazano na poniższej ilustracji: **ustawienia zapory** strony dla serwera bazy danych SQL zostanie otwarty.

      ![reguła zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Kliknij przycisk **Dodaj adres IP klienta** na pasku narzędzi, aby dodać adres IP komputera, obecnie używasz a następnie kliknij przycisk **Zapisz**. Dla bieżącego adresu IP zostanie utworzona reguła zapory na poziomie serwera.

      ![ustawianie reguły zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Na stronie Przegląd serwera

Zostanie otwarta strona przeglądu dla serwera, zawierająca w pełni kwalifikowaną nazwę serwera (takich jak **mynewserver20170403.database.windows.net**) i opcje dalszej konfiguracji.

1. Aby ustawić regułę poziomu serwera od stronie przeglądu serwera, kliknij przycisk **zapory** w menu po lewej stronie w obszarze Ustawienia:

2. Kliknij przycisk **Dodaj adres IP klienta** na pasku narzędzi, aby dodać adres IP komputera, obecnie używasz a następnie kliknij przycisk **Zapisz**. Dla bieżącego adresu IP zostanie utworzona reguła zapory na poziomie serwera.

## <a name="manage-firewall-rules-using-transact-sql"></a>Zarządzanie regułami zapory za pomocą języka Transact-SQL

| Widok wykazu lub procedura składowana | Poziom | Opis |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Serwer |Wyświetla bieżące reguły zapory na poziomie serwera |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Serwer |Tworzy lub aktualizuje reguły zapory na poziomie serwera |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Serwer |Usuwa reguły zapory na poziomie serwera |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database (Baza danych) |Wyświetla bieżące reguły zapory na poziomie bazy danych |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database (Baza danych) |Tworzy lub aktualizuje reguły zapory na poziomie bazy danych |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bazy danych |Usuwa reguły zapory na poziomie bazy danych |

Poniższe przykłady przejrzyj istniejące reguły, Włącz zakres adresów IP na serwerze Contoso i usuwa regułę zapory:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Następnie dodaj regułę zapory.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Aby usunąć regułę zapory na poziomie serwera, wykonaj procedurę składowaną sp_delete_firewall_rule. Reguła o nazwie ContosoFirewallRule można znaleźć w następującym przykładzie:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-firewall-rules-using-azure-powershell"></a>Zarządzanie regułami zapory za pomocą programu Azure PowerShell

| Polecenie cmdlet | Poziom | Opis |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |Serwer |Zwraca bieżące reguły zapory na poziomie serwera |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |Serwer |Tworzy nową regułę zapory na poziomie serwera |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |Serwer |Aktualizuje właściwości istniejącej reguły zapory na poziomie serwera |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |Serwer |Usuwa reguły zapory na poziomie serwera |

W poniższym przykładzie ustawiono regułę zapory na poziomie serwera przy użyciu programu PowerShell:

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Przykłady programu PowerShell w kontekście z przewodnika Szybki start, zobacz [tworzenie bazy danych — PowerShell](sql-database-powershell-samples.md) i [tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory przy użyciu programu PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-firewall-rules-using-azure-cli"></a>Zarządzanie regułami zapory za pomocą wiersza polecenia platformy Azure

| Polecenie cmdlet | Poziom | Opis |
| --- | --- | --- |
|[Utwórz az sql server — reguły zapory](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Serwer|Tworzy regułę zapory serwera|
|[AZ sql server reguły zapory na liście](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Serwer|Wyświetla listę reguł zapory na serwerze|
|[Pokaż reguły zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Serwer|Wyświetla szczegóły reguły zapory|
|[Aktualizacja reguły zapory az sql server](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Serwer|Aktualizuje regułę zapory|
|[Usuń regułę zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Serwer|Usuwa reguły zapory|

W poniższym przykładzie ustawiono regułę zapory na poziomie serwera przy użyciu wiersza polecenia platformy Azure:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Aby uzyskać przykład wiersza polecenia platformy Azure w ramach przewodnika Szybki start, zobacz [tworzenie bazy danych — interfejs wiersza polecenia platformy Azure](sql-database-cli-samples.md) i [tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory przy użyciu wiersza polecenia platformy Azure](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-firewall-rules-using-rest-api"></a>Zarządzanie regułami zapory za pomocą interfejsu API REST

| Interfejs API | Poziom | Opis |
| --- | --- | --- |
| [List Firewall Rules](https://docs.microsoft.com/rest/api/sql/firewallrules/firewallrules_listbyserver) (Lista reguł zapory) |Serwer |Wyświetla bieżące reguły zapory na poziomie serwera |
| [Create or Update Firewall Rule](https://docs.microsoft.com/rest/api/sql/firewallrules/firewallrules_createorupdate) (Tworzenie i aktualizowanie reguły zapory) |Serwer |Tworzy lub aktualizuje reguły zapory na poziomie serwera |
| [Delete Firewall Rule](https://docs.microsoft.com/rest/api/sql/firewallrules/firewallrules_delete) (Usuwanie reguły zapory) |Serwer |Usuwa reguły zapory na poziomie serwera |
| [Pobierz reguły zapory](https://docs.microsoft.com/rest/api/sql/firewallrules/firewallrules_get) | Serwer | Pobiera reguły zapory na poziomie serwera |

## <a name="server-level-firewall-rule-versus-a-database-level-firewall-rule"></a>Reguły zapory na poziomie serwera i regułę zapory na poziomie bazy danych

PYTANIE: Użytkownicy jedna baza danych powinna być w pełni odizolowane od innej bazy danych?
Jeśli tak, należy udzielić dostępu przy użyciu reguł zapory na poziomie bazy danych. Umożliwia to uniknięcie przy użyciu reguł zapory na poziomie serwera, które zezwolenie na dostęp przez zaporę do wszystkich baz danych, zmniejszenie głębokość obrony.

PYTANIE: Czy użytkownicy pod adresem IP muszą mieć dostęp do wszystkich baz danych?
Aby zmniejszyć liczbę razy, należy skonfigurować reguły zapory, należy użyć reguły zapory na poziomie serwera.

PYTANIE: Osobie lub zespołowi Konfigurowanie reguł zapory tylko ma dostęp za pośrednictwem witryny Azure portal, programu PowerShell lub interfejsu API REST?
Należy użyć reguły zapory na poziomie serwera. Reguły zapory na poziomie bazy danych można skonfigurować tylko za pomocą języka Transact-SQL.  

PYTANIE: Jest osobie lub zespołowi Konfigurowanie reguł zapory, będzie to zabronione o wysokim poziomie uprawnień na poziomie bazy danych?
Użyj reguł zapory na poziomie serwera. Konfigurowanie reguł zapory na poziomie bazy danych przy użyciu języka Transact-SQL wymaga co najmniej `CONTROL DATABASE` uprawnienia na poziomie bazy danych.  

PYTANIE: Jest osobę lub zespół, konfigurowania i przeprowadzania inspekcji reguły zapory, centralne zarządzanie regułami zapory dla wielu (np. 100) baz danych?
Zaznacz to pole wyboru zależy od potrzeb i środowiska. Reguły zapory na poziomie serwera może być łatwiejsze do skonfigurowania, ale skryptów można skonfigurować reguły na poziomie bazy danych. I nawet wtedy, gdy używasz reguły zapory na poziomie serwera, może być konieczne inspekcji reguły zapory bazy danych, aby sprawdzić, czy użytkownicy z `CONTROL` uprawnień w bazie danych zostały utworzone reguły zapory na poziomie bazy danych.

PYTANIE: Można użyć kombinacji obu reguły zapory na poziomie serwera i na poziomie bazy danych?
Tak. Niektórzy użytkownicy, takich jak Administratorzy może być konieczne reguły zapory na poziomie serwera. Inni użytkownicy, takich jak użytkownicy aplikacji bazy danych, mogą wymagać reguł zapory na poziomie bazy danych.

## <a name="troubleshooting-the-database-firewall"></a>Rozwiązywanie problemów z zaporą bazy danych

Jeśli dostęp do usługi Microsoft Azure SQL Database nie działa zgodnie z oczekiwaniami, należy rozważyć następujące kwestie:

- **Konfiguracja lokalnej zapory:**

  Zanim komputer może uzyskać dostęp do usługi Azure SQL Database, może być konieczne utworzenie wyjątku zapory na komputerze dla portu TCP 1433. W przypadku nawiązywania połączeń wewnątrz granic chmury Azure może być konieczne otwarcie dodatkowych portów. Aby uzyskać więcej informacji, zobacz **bazy danych SQL Database: poza vs wewnątrz** części [portów wyższych niż 1433 dla platformy ADO.NET 4.5 i usługi SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Translator adresów sieciowych (NAT):**

  Z powodu translatora adresów Sieciowych adres IP używany przez komputer do łączenia z usługą Azure SQL Database może być inny niż adres IP wyświetlany w ustawieniach konfiguracji adresu IP komputera. Aby wyświetlić adres IP używany przez komputer do łączenia się z platformą Azure, zaloguj się do portalu i przejdź na kartę **Konfigurowanie** na serwerze, który hostuje bazę danych. W sekcji **Dozwolone adresy IP** wyświetlany jest **bieżący adres IP klienta**. Kliknij przycisk **Dodaj** do listy **Dozwolone adresy IP**, aby zezwolić temu komputerowi na dostęp do serwera.

- **Zmiany do listy dozwolonych nie zostały uwzględnione jeszcze:**

  Może to być jak pięciu minut opóźnienia, zanim zmiany w konfiguracji zapory usługi Azure SQL Database zostały wprowadzone.

- **Nazwa logowania nie ma autoryzacji lub użyto nieprawidłowego hasła:**

  Jeśli logowanie nie ma uprawnień na serwerze usługi Azure SQL Database lub użyte hasło jest nieprawidłowe, odmowa połączenia z serwerem usługi Azure SQL Database. Utworzenie ustawień zapory zapewnia klientom jedynie możliwość próby nawiązania połączenia z serwerem, ale każdy klient musi podać niezbędne poświadczenia zabezpieczeń. Aby uzyskać więcej informacji na temat przygotowywania logowań, zobacz [Zarządzanie bazami danych, Logowaniami i użytkownikami w usłudze Azure SQL Database](sql-database-manage-logins.md).

- **Dynamiczny adres IP:**

  Jeśli masz połączenie internetowe za pomocą dynamicznego adresowania IP i problemy z przejściem przez zaporę, można wypróbować jedno z następujących rozwiązań:
  
  - Poproś usługodawcę internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich uzyskujących dostęp do serwera usługi Azure SQL Database, a następnie dodaj ten zakres adresów IP jako regułę zapory.
  - Pobierz statyczne adresy IP dla komputerów klienckich, a następnie dodaj te adresy IP jako reguły zapory.

## <a name="next-steps"></a>Kolejne kroki

- W przewodniku Szybki start dotyczące tworzenia bazy danych i regułę zapory na poziomie serwera, zobacz [utworzyć bazę danych Azure SQL](sql-database-get-started-portal.md).
- Aby uzyskać pomoc podczas łączenia się z bazą danych Azure SQL z aplikacji innych firm lub aplikacji typu open source, zobacz [Client quick-start code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx) (Przykłady kodu umożliwiające szybki start dla klienta usługi SQL Database).
- Aby uzyskać informacji na temat dodatkowych portów, które może być konieczne otwarcie, zobacz **bazy danych SQL: poza vs wewnątrz** części [portów wyższych niż 1433 dla platformy ADO.NET 4.5 i usługi SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- Aby uzyskać omówienie zabezpieczeń usługi Azure SQL Database, zobacz [Zabezpieczanie bazy danych](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
