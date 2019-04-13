---
title: Reguły zapory usługi Azure SQL Database i Data Warehouse IP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować reguły zapory IP poziom serwera, aby zarządzać dostępem i skonfigurować bazę pojedynczego lub w puli za pomocą reguł zapory IP poziomu bazy danych SQL database lub SQL Data Warehouse zapory.
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
ms.date: 03/12/2019
ms.openlocfilehash: 513836257a292069da709ad7a71e480f2b4d069d
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549733"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Reguły zapory usługi Azure SQL Database i SQL Data Warehouse IP

Microsoft Azure [bazy danych SQL](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) to usługa relacyjnej bazy danych dla platformy Azure i innych aplikacji internetowych. Aby chronić dane, zapora uniemożliwia wszelki dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.

> [!NOTE]
> Ten artykuł dotyczy serwera Azure SQL i bazy danych SQL Database i SQL Data Warehouse baz danych, które są tworzone na serwerze Azure SQL. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.
> [!IMPORTANT]
> W tym artykule jest *nie* dotyczą **wystąpienia zarządzanego Azure SQL Database**. Zobacz następujący artykuł w [połączenie do wystąpienia zarządzanego](sql-database-managed-instance-connect-app.md) uzyskać więcej informacji o konfiguracji sieci niezbędnej.

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Reguły sieci wirtualnej jako alternatywy dla reguły IP

Oprócz reguły IP zarządza także zapory *reguł sieci wirtualnej*. Reguły sieci wirtualnej są oparte na punkty końcowe usługi sieci wirtualnej. Reguły sieci wirtualnej może być korzystniejsze IP reguł w niektórych przypadkach. Aby dowiedzieć się więcej, zobacz [punkty końcowe usługi sieci wirtualnej i zasad usługi Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Omówienie

Początkowo cały dostęp do serwera Azure SQL jest blokowany przez zaporę usługi SQL Database. Aby uzyskać dostęp do serwera bazy danych, należy określić co najmniej jeden reguły zapory IP poziom serwera, które umożliwiają dostęp do serwera Azure SQL. Użyj reguł zapory adresów IP, aby określić adresów IP pochodzących z Internetu są dozwolone i tego, czy aplikacje systemu Azure można spróbować połączyć się z serwerem Azure SQL.

Aby selektywnie udzielić dostępu do tylko jednej z baz danych na serwerze Azure SQL, należy utworzyć regułę na poziomie bazy danych dla wymaganej bazy danych. Określ zakres adresów IP dla reguły zapory IP bazy danych, który wykracza poza zakres adresów IP, które są określone w regule zapory na poziomie serwera IP i upewnij się, że adres IP klienta znajduje się w zakresie określonym w regule poziomu bazy danych.

> [!IMPORTANT]
> Usługa SQL Data Warehouse obsługuje reguły zapory IP poziomu serwera i tylko nie obsługuje reguły zapory IP poziomu bazy danych.

Próby połączenia z Internetu i platformy Azure muszą najpierw przejść przez zaporę, zanim dotrą do serwera Azure SQL lub usługi SQL Database, jak pokazano na poniższym diagramie:

   ![Diagram opisujący konfigurację zapory.][1]

- **Reguły zapory IP poziomu serwera:**

  Te reguły umożliwiają klientom dostęp do całego serwera Azure SQL, oznacza to, że wszystkie bazy danych na tym samym serwerze bazy danych SQL. Reguły te są przechowywane w **głównej** bazie danych. Reguły zapory IP poziomu serwera można skonfigurować za pomocą portalu lub za pomocą instrukcji języka Transact-SQL. Aby utworzyć reguły zapory IP na poziomie serwera przy użyciu witryny Azure portal lub programu PowerShell, musi być właścicielem bądź współautorem subskrypcji. Aby utworzyć regułę zapory na poziomie serwera IP przy użyciu języka Transact-SQL, musisz połączyć się z wystąpieniem bazy danych SQL jako głównego identyfikatora logowania poziomu serwera lub administrator usługi Azure Active Directory (co oznacza, że należy najpierw utworzyć regułę zapory na poziomie serwera IP przez Użytkownik z uprawnieniami na poziomie platformy Azure).

- **Reguły zapory IP poziomu bazy danych:**

  Te reguły umożliwiają klientom dostęp do niektórych (bezpiecznych) baz danych na tym samym serwerze bazy danych SQL. Możesz utworzyć te reguły dla każdej bazy danych (w tym **wzorca** bazy danych) i są przechowywane w poszczególnych bazach danych. Reguły zapory IP poziomu bazy danych dla baz danych master i użytkownika tylko mogą być tworzone i zarządzane za pomocą instrukcji języka Transact-SQL i tylko w przypadku, po skonfigurowaniu pierwszej zapory poziomu serwera. Jeśli określisz zakres adresów IP w regule zapory na poziomie bazy danych adresów IP, który jest poza zakresem określonym w regule zapory na poziomie serwera IP tylko klienci, którzy mają adresy IP w zakresie poziomu bazy danych można dostęp do bazy danych. Może mieć maksymalnie 128 reguł zapory IP poziomu bazy danych dla bazy danych. Aby uzyskać więcej informacji na temat konfigurowania reguły zapory IP poziomu bazy danych, zobacz przykład później w tym artykuł i zobacz [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Zalecenie

Firma Microsoft zaleca korzystanie z reguł zapory IP poziomu bazy danych w miarę możliwości, aby zwiększyć poziom bezpieczeństwa i uczynić bazę danych bardziej przenośny. Użyj reguły zapory IP poziomu serwera dla administratorów i, gdy wiele baz danych, które mają takie same wymagania dotyczące dostępu, a użytkownik nie chce poświęcać czasu na oddzielne Konfigurowanie każdej bazy danych.

> [!IMPORTANT]
> Windows Azure SQL Database obsługuje maksymalnie 128 reguł zapory adresów IP.
> [!NOTE]
> Aby uzyskać informacje o przenośnych bazach danych w kontekście ciągłości działalności biznesowej, zobacz [Wymagania dotyczące uwierzytelniania dla odzyskiwania po awarii](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Łączenie się z Internetu

Gdy komputer próbuje połączyć się z serwerem bazy danych z Internetu, Zapora najpierw sprawdza źródłowy adres IP żądania i porównuje reguły zapory IP poziomu bazy danych, bazy danych, który żąda połączenia:

- Jeśli adres IP żądania jednego z zakresów określonych w regułach zapory na poziomie bazy danych adresów IP, zostanie ustanowione połączenie bazy danych SQL, który zawiera daną zasadę.
- Jeśli adres IP żądania nie jest w ramach jednego z zakresów określonych w regule zapory na poziomie bazy danych adresów IP, sprawdzane są reguły zapory poziomu serwera IP. Jeśli adres IP żądania do jednego z zakresów określonych w regułach zapory na poziomie serwera IP, połączenie zostanie ustanowione. Reguły zapory IP w poziomie serwera dotyczą wszystkich baz danych SQL na serwerze SQL platformy Azure.  
- Jeśli adres IP żądania nie jest w zakresach określonych w dowolnym poziomie bazy danych lub reguły zapory IP poziom serwera, żądanie połączenia zakończy się niepowodzeniem.

> [!NOTE]
> Aby uzyskać dostęp do usługi Azure SQL Database z komputera lokalnego, upewnij się, że zapora w sieci i na komputerze lokalnym zezwala na komunikację wychodzącą na porcie TCP 1433.

### <a name="connecting-from-azure"></a>Łączenie z platformy Azure

Aby umożliwić aplikacjom z platformy Azure łączenie się z serwerem Azure SQL, należy włączyć połączenia platformy Azure. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Zapora ustawienie z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że dozwolone są połączenia platformy Azure. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera do serwera usługi Azure SQL Database.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.

## <a name="creating-and-managing-ip-firewall-rules"></a>Tworzenie i zarządzanie regułami zapory adresów IP

Pierwsze ustawienie zapory na poziomie serwera mogą być tworzone za pomocą [witryny Azure portal](https://portal.azure.com/) lub programowo przy użyciu [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), [wiersza polecenia platformy Azure](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create), lub [ Interfejs API REST](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Kolejne reguły zapory IP w poziomie serwera mogą być tworzone i zarządzane przy użyciu tych metod oraz za pomocą instrukcji języka Transact-SQL.

> [!IMPORTANT]
> Reguły zapory IP poziomu bazy danych tylko mogą być tworzone i zarządzane przy użyciu języka Transact-SQL.

Aby poprawić wydajność, reguły zapory IP poziomu serwera są tymczasowo przechowywane na poziomie bazy danych. Aby odświeżyć pamięć podręczną, zobacz [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Możesz użyć [inspekcji usługi SQL Database](sql-database-auditing.md) inspekcji zmian w zaporze serwera i na poziomie bazy danych.

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>Zarządzanie regułami zapory na IP na poziomie serwera przy użyciu witryny Azure portal

Aby ustawić regułę zapory na poziomie serwera IP w witrynie Azure portal, możesz albo przejść do strony Przegląd dla bazy danych Azure SQL lub na stronie Przegląd dla serwera usługi SQL Database.

> [!TIP]
> Aby zapoznać się z samouczkiem, zobacz [utworzyć BAZĘ danych przy użyciu witryny Azure portal](sql-database-single-database-get-started.md).

### <a name="from-database-overview-page"></a>Na stronie Przegląd bazy danych

1. Aby ustawić regułę zapory na poziomie serwera IP na stronie Przegląd bazy danych, kliknij **Ustaw zaporę serwera** na pasku narzędzi, jak pokazano na poniższej ilustracji: Zostanie otwarta strona **Ustawienia zapory** dla serwera SQL Database.

      ![reguła zapory adresów IP serwera](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Kliknij przycisk **Dodaj adres IP klienta** na pasku narzędzi, aby dodać adres IP komputera, obecnie używasz a następnie kliknij przycisk **Zapisz**. Dla bieżącego adresu IP zostanie utworzona reguła zapory IP poziomu serwera.

      ![reguła zapory adresów IP zestawu w poziomie serwera](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Na stronie Przegląd serwera

Zostanie otwarta strona przeglądu dla serwera, zawierająca w pełni kwalifikowaną nazwę serwera (takich jak **mynewserver20170403.database.windows.net**) i opcje dalszej konfiguracji.

1. Aby ustawić regułę poziomu serwera od stronie przeglądu serwera, kliknij przycisk **zapory** w menu po lewej stronie w obszarze Ustawienia:

2. Kliknij przycisk **Dodaj adres IP klienta** na pasku narzędzi, aby dodać adres IP komputera, obecnie używasz a następnie kliknij przycisk **Zapisz**. Dla bieżącego adresu IP zostanie utworzona reguła zapory IP poziomu serwera.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>Zarządzanie regułami zapory adresów IP przy użyciu języka Transact-SQL

| Widok wykazu lub procedura składowana | Poziom | Opis |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Serwer |Wyświetla bieżące reguły zapory na poziomie serwera IP |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Serwer |Tworzy lub aktualizuje reguły zapory IP poziomu serwera |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Serwer |Usuwa reguły zapory IP poziomu serwera |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database (Baza danych) |Wyświetla bieżące reguły zapory na poziomie bazy danych adresów IP |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database (Baza danych) |Tworzy lub aktualizuje reguły zapory na poziomie bazy danych adresów IP |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bazy danych |Poziom bazy danych usuwa reguły zapory IP |

Poniższe przykłady przejrzyj istniejące reguły, Włącz zakres adresów IP na serwerze Contoso i usuwa reguła zapory adresów IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Następnie dodaj regułę zapory na poziomie serwera IP.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Aby usunąć regułę zapory na poziomie serwera IP, należy wykonać sp_delete_firewall_rule procedurę składowaną. Reguła o nazwie ContosoFirewallRule można znaleźć w następującym przykładzie:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Zarządzanie regułami zapory na IP na poziomie serwera przy użyciu programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł programu PowerShell usługi Azure Resource Manager jest nadal obsługiwane przez usługę Azure SQL Database, ale wszystkie przyszłego rozwoju jest Az.Sql modułu. Dla tych poleceń cmdlet, zobacz [elementu AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w Az module, a w modułach AzureRm są zasadniczo identyczne.

| Polecenie cmdlet | Poziom | Opis |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Serwer |Zwraca bieżące reguły zapory na poziomie serwera |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Serwer |Tworzy nową regułę zapory na poziomie serwera |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Serwer |Aktualizuje właściwości istniejącej reguły zapory na poziomie serwera |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Serwer |Usuwa reguły zapory na poziomie serwera |

W poniższym przykładzie ustawiono regułę zapory na poziomie serwera IP przy użyciu programu PowerShell:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Przykłady programu PowerShell w kontekście z przewodnika Szybki start, zobacz [tworzenie bazy danych — PowerShell](sql-database-powershell-samples.md) i [tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory IP poziomu serwera bazy danych SQL przy użyciu programu PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Zarządzanie regułami zapory na IP na poziomie serwera przy użyciu wiersza polecenia platformy Azure

| Polecenie cmdlet | Poziom | Opis |
| --- | --- | --- |
|[Utwórz az sql server — reguły zapory](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Serwer|Tworzy regułę zapory adresu IP serwera|
|[AZ sql server reguły zapory na liście](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Serwer|Zawiera reguły zapory IP na serwerze|
|[Pokaż reguły zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Serwer|Wyświetla szczegóły reguły zapory IP|
|[Aktualizacja reguły zapory az sql server](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Serwer|Aktualizuje regułę zapory adresów IP|
|[Usuń regułę zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Serwer|Usuwa reguły zapory IP|

W poniższym przykładzie ustawiono regułę zapory na poziomie serwera IP przy użyciu wiersza polecenia platformy Azure:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Aby uzyskać przykład wiersza polecenia platformy Azure w ramach przewodnika Szybki start, zobacz [tworzenie bazy danych — interfejs wiersza polecenia platformy Azure](sql-database-cli-samples.md) i [tworzenie pojedynczej bazy danych i konfigurowanie reguły zapory IP bazy danych SQL przy użyciu wiersza polecenia platformy Azure](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>Zarządzanie regułami zapory na adresów IP na poziomie serwera przy użyciu interfejsu API REST

| Interfejs API | Poziom | Opis |
| --- | --- | --- |
| [List Firewall Rules](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) (Lista reguł zapory) |Serwer |Wyświetla bieżące reguły zapory na poziomie serwera IP |
| [Create or Update Firewall Rule](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) (Tworzenie i aktualizowanie reguły zapory) |Serwer |Tworzy lub aktualizuje reguły zapory IP poziomu serwera |
| [Delete Firewall Rule](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) (Usuwanie reguły zapory) |Serwer |Usuwa reguły zapory IP poziomu serwera |
| [Pobierz reguły zapory](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Serwer | Pobiera reguły zapory IP poziomu serwera |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Poziom serwera i adres IP na poziomie bazy danych reguły zapory

PYTANIE: Użytkownicy jedna baza danych powinna być w pełni odizolowane od innej bazy danych?
Jeśli tak, należy udzielić dostępu przy użyciu reguły zapory IP poziomu bazy danych. Umożliwia to uniknięcie przy użyciu adresu IP reguły zapory poziomu serwera, które zezwolenie na dostęp przez zaporę do wszystkich baz danych, zmniejszenie głębokość obrony.

PYTANIE: Czy użytkownicy pod adresem IP muszą mieć dostęp do wszystkich baz danych?
Aby zmniejszyć liczbę razy, należy skonfigurować reguły zapory IP, należy użyć reguły zapory IP poziomu serwera.

PYTANIE: Osobie lub zespołowi konfigurowanie reguły zapory IP tylko ma dostęp za pośrednictwem witryny Azure portal, programu PowerShell lub interfejsu API REST?
Należy użyć reguły zapory IP poziomu serwera. Reguły zapory IP poziomu bazy danych można skonfigurować tylko za pomocą języka Transact-SQL.  

PYTANIE: Osobie lub zespołowi konfiguruje reguły zapory IP, będzie to zabronione o wysokim poziomie uprawnień na poziomie bazy danych?
Użyj reguły zapory IP poziomu serwera. Konfigurowanie reguły zapory IP na poziomie bazy danych za pomocą instrukcji języka Transact-SQL wymaga co najmniej `CONTROL DATABASE` uprawnienia na poziomie bazy danych.  

PYTANIE: Jest osobę lub zespół, konfigurowania i przeprowadzania inspekcji reguły zapory IP centralne zarządzanie regułami zapory adresów IP dla wielu (np. 100) baz danych?
Zaznacz to pole wyboru zależy od potrzeb i środowiska. Reguły zapory IP poziomu serwera może być łatwiejsze do skonfigurowania, ale skryptów można skonfigurować reguły na poziomie bazy danych. I nawet wtedy, gdy używasz reguły zapory IP poziom serwera, może być konieczne inspekcji reguły zapory IP poziomu bazy danych, aby sprawdzić, czy użytkownicy z `CONTROL` uprawnień w bazie danych zostały utworzone reguły zapory IP poziomu bazy danych.

PYTANIE: Można użyć kombinacji reguły zapory IP zarówno serwera i na poziomie bazy danych?
Tak. Niektórzy użytkownicy, takich jak Administratorzy może być konieczne reguły zapory IP poziomu serwera. Innym użytkownikom, takie jak użytkownicy aplikacji bazy danych, może być konieczne bazy danych na poziomie reguły zapory IP.

## <a name="troubleshooting-the-database-firewall"></a>Rozwiązywanie problemów z zaporą bazy danych

Jeśli dostęp do usługi Microsoft Azure SQL Database nie działa zgodnie z oczekiwaniami, należy rozważyć następujące kwestie:

- **Konfiguracja lokalnej zapory:**

  Zanim komputer może uzyskać dostęp do usługi Azure SQL Database, może być konieczne utworzenie wyjątku zapory na komputerze dla portu TCP 1433. W przypadku nawiązywania połączeń wewnątrz granic chmury Azure może być konieczne otwarcie dodatkowych portów. Aby uzyskać więcej informacji, zobacz **bazy danych SQL: Poza programem vs wewnątrz** części [portów wyższych niż 1433 dla platformy ADO.NET 4.5 i usługi SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Translator adresów sieciowych (NAT):**

  Z powodu translatora adresów Sieciowych adres IP używany przez komputer do łączenia z usługą Azure SQL Database może być inny niż adres IP wyświetlany w ustawieniach konfiguracji adresu IP komputera. Aby wyświetlić adres IP używany przez komputer do łączenia się z platformą Azure, zaloguj się do portalu i przejdź na kartę **Konfigurowanie** na serwerze, który hostuje bazę danych. W sekcji **Dozwolone adresy IP** wyświetlany jest **bieżący adres IP klienta**. Kliknij przycisk **Dodaj** do listy **Dozwolone adresy IP**, aby zezwolić temu komputerowi na dostęp do serwera.

- **Zmiany do listy dozwolonych nie zostały uwzględnione jeszcze:**

  Może to być jak pięciu minut opóźnienia, zanim zmiany w konfiguracji zapory usługi Azure SQL Database zostały wprowadzone.

- **Nazwa logowania nie ma autoryzacji lub użyto nieprawidłowego hasła:**

  Jeśli logowanie nie ma uprawnień na serwerze usługi Azure SQL Database lub użyte hasło jest nieprawidłowe, odmowa połączenia z serwerem usługi Azure SQL Database. Utworzenie ustawień zapory zapewnia klientom jedynie możliwość próby nawiązania połączenia z serwerem, ale każdy klient musi podać niezbędne poświadczenia zabezpieczeń. Aby uzyskać więcej informacji na temat przygotowywania logowań, zobacz [Zarządzanie bazami danych, Logowaniami i użytkownikami w usłudze Azure SQL Database](sql-database-manage-logins.md).

- **Dynamiczny adres IP:**

  Jeśli masz połączenie internetowe za pomocą dynamicznego adresowania IP i problemy z przejściem przez zaporę, można wypróbować jedno z następujących rozwiązań:
  
  - Poproś usługodawcy internetowego (ISP) dla zakresu adresów IP, które są przypisane do komputerów klienckich uzyskujących dostęp do serwera Azure SQL Database, a następnie dodać zakres adresów IP jako reguła zapory adresów IP.
  - Pobierz statyczne adresy IP dla komputerów klienckich, a następnie dodaj adresy IP jako reguły zapory IP.

## <a name="next-steps"></a>Kolejne kroki

- Upewnij się, że w środowisku firmowym sieci umożliwia komunikacji przychodzącej z zakresów adresów IP zasobów obliczeniowych (w tym zakresy SQL) używane w centrach danych platformy Microsoft Azure. Może być konieczne do listy dozwolonych te adresy IP, zobacz [zakresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653)  
- W przewodniku Szybki start dotyczące tworzenia reguły zapory poziomu serwera IP, zobacz [utworzyć bazę danych Azure SQL](sql-database-single-database-get-started.md).
- Aby uzyskać pomoc podczas łączenia się z bazą danych Azure SQL z aplikacji innych firm lub aplikacji typu open source, zobacz [Client quick-start code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx) (Przykłady kodu umożliwiające szybki start dla klienta usługi SQL Database).
- Aby uzyskać informacji na temat dodatkowych portów, które może być konieczne otwarcie, zobacz **bazy danych SQL: Poza programem vs wewnątrz** części [portów wyższych niż 1433 dla platformy ADO.NET 4.5 i usługi SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- Aby uzyskać omówienie zabezpieczeń usługi Azure SQL Database, zobacz [Zabezpieczanie bazy danych](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
