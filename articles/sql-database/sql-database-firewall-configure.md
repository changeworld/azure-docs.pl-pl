---
title: Reguły zapory bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie konfigurowania zapory bazy danych SQL do zarządzania dostępem za pomocą reguł zapory na poziomie serwera i bazy danych.
keywords: zapora bazy danych
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: d68ae802e37b5d0b8421099894da43a0559f2fc2
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="azure-sql-database-server-level-and-database-level-firewall-rules"></a>Reguły zapory poziomu serwera i bazy danych na poziomie bazy danych SQL Azure 

Usługa Microsoft Azure SQL Database udostępnia usługę relacyjnej bazy danych dla platformy Azure i innych aplikacji internetowych. Aby chronić dane, zapora uniemożliwia wszelki dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.

#### <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Zasady sieci wirtualnej jako alternatywy dla reguły IP

Oprócz reguł IP zarządza także zapory *zasady sieci wirtualnej*. Zasady sieci wirtualnej są oparte na punktów końcowych usługi sieci wirtualnej. Zasady sieci wirtualnej może być korzystniejsze IP reguły w niektórych przypadkach. Aby dowiedzieć się więcej, zobacz [punktów końcowych usługi sieci wirtualnej i zasady usługi Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Przegląd

Początkowo cały dostęp języka Transact-SQL do serwera Azure SQL jest blokowany przez zaporę. Aby rozpocząć korzystanie z serwera Azure SQL, należy określić co najmniej jedną regułę zapory poziomu serwera, które umożliwiają dostęp do serwera Azure SQL. Użyj reguł zapory do określenia zakresu dozwolonych adresów IP pochodzących z Internetu oraz możliwości podejmowania przez aplikacje platformy Azure prób połączenia się z serwerem Azure SQL.

Aby selektywnie udzielić dostępu do tylko jednej z baz danych na serwerze Azure SQL, należy utworzyć regułę na poziomie bazy danych dla wymaganej bazy danych. Określ zakres adresów IP dla reguły zapory bazy danych, który wykracza poza zakres adresów IP określony w regule zapory na poziomie serwera, i upewnij się, że adres IP klienta znajduje się w zakresie określonym w regule na poziomie bazy danych.

Próby połączenia z Internetu i platformy Azure muszą najpierw przejść przez zaporę, zanim dotrą do serwera Azure SQL lub usługi SQL Database, jak pokazano na poniższym diagramie:

   ![Diagram opisujący konfigurację zapory.][1]

* **Reguły zapory na poziomie serwera:** te reguły umożliwiają klientom dostęp do całego serwera Azure SQL, to znaczy do wszystkich baz danych na tym samym serwerze logicznym. Reguły te są przechowywane w **głównej** bazie danych. Reguły zapory na poziomie serwera można skonfigurować za pomocą portalu lub za pomocą instrukcji języka Transact-SQL. Aby utworzyć reguły zapory na poziomie serwera przy użyciu witryny Azure Portal lub programu PowerShell, musisz być właścicielem bądź współautorem subskrypcji. Aby utworzyć regułę zapory na poziomie serwera przy użyciu języka Transact-SQL, musisz połączyć się z wystąpieniem usługi SQL Database za pomocą identyfikatora logowania podmiotu zabezpieczeń na poziomie serwera lub jako administrator usługi Azure Active Directory (co oznacza, że reguła zapory na poziomie serwera musi zostać pierwotnie utworzona przez użytkownika mającego uprawnienia na poziomie platformy Azure).
* **Reguły zapory poziomu bazy danych:** reguły te umożliwiają klientom dostęp do niektórych (bezpieczny) bazy danych, w tym samym serwerze logicznym. Można utworzyć zasady dla każdej bazy danych (w tym **wzorca** bazy danych) i są przechowywane w poszczególnych bazach danych. Reguły zapory poziomu bazy danych dla baz danych master i użytkownika tylko wtedy można tworzyć i zarządzane za pomocą instrukcji języka Transact-SQL i dopiero po skonfigurowaniu pierwszego zapory poziomu serwera. Jeśli zakres adresów IP określony w regule zapory na poziomie bazy danych znajduje się poza zakresem określonym w regule zapory na poziomie serwera, dostęp do bazy danych mogą uzyskać tylko ci klienci, którzy mają adresy IP z zakresu ustalonego na poziomie bazy danych. Dla bazy danych można określić maksymalnie 128 reguł zapory na poziomie bazy danych. Aby uzyskać więcej informacji na temat konfigurowania reguł zapory na poziomie bazy danych, zobacz przykład później w tym artykuł i zobacz [sp_set_database_firewall_rule (baz danych SQL Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

**Zalecenie:** firma Microsoft zaleca korzystanie z reguł zapory na poziomie bazy danych zawsze wtedy, gdy jest to możliwe, aby zwiększyć poziom bezpieczeństwa i uczynić bazę danych bardziej przenośną. Reguły zapory na poziomie serwera powinny być używane dla administratorów i w przypadku, gdy wiele baz danych ma takie same wymagania dotyczące dostępu, a użytkownik nie chce poświęcać czasu na oddzielne konfigurowanie każdej bazy danych.

> [!Important]
> Baza danych SQL Azure z systemem Windows obsługuje maksymalnie 128 reguł zapory.
>

> [!Note]
> Aby uzyskać informacje o przenośnych bazach danych w kontekście ciągłości działalności biznesowej, zobacz [Wymagania dotyczące uwierzytelniania dla odzyskiwania po awarii](sql-database-geo-replication-security-config.md).
>

### <a name="connecting-from-the-internet"></a>Łączenie się z Internetu

Gdy komputer próbuje połączyć się z Internetu z serwerem bazy danych, zapora najpierw sprawdza źródłowy adres IP żądania i porównuje go z regułami zapory na poziomie bazy danych, której dotyczy żądanie:

* Jeśli adres IP żądania należy do jednego z zakresów określonych w regułach zapory na poziomie bazy danych, jest ustanawiane połączenie z bazą danych SQL Database zawierającą tę regułę.
* Jeśli adres IP żądania nie należy do jednego z zakresów określonych w regułach zapory na poziomie bazy danych, sprawdzane są reguły zapory na poziomie serwera. Jeśli adres IP żądania należy do jednego z zakresów określonych w regułach zapory na poziomie serwera, ustanawiane jest połączenie. Reguły zapory na poziomie serwera mają zastosowanie do wszystkich baz danych SQL na serwerze SQL platformy Azure.  
* Jeśli adres IP żądania nie jest w zakresie określony w żadnym z reguły zapory poziomu serwera lub na poziomie bazy danych, żądanie połączenia zakończy się niepowodzeniem.

> [!NOTE]
> Aby uzyskać dostęp do usługi Azure SQL Database z komputera lokalnego, upewnij się, że zapora w sieci i na komputerze lokalnym zezwala na komunikację wychodzącą na porcie TCP 1433.
> 

### <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom z platformy Azure łączenie się z serwerem Azure SQL, należy włączyć połączenia platformy Azure. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera do serwera usługi Azure SQL Database.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

## <a name="creating-and-managing-firewall-rules"></a>Tworzenie i zarządzanie nimi reguł zapory
Pierwsze ustawienie zapory na poziomie serwera można tworzyć przy użyciu [portalu Azure](https://portal.azure.com/) lub programowo przy użyciu [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql), [interfejsu wiersza polecenia Azure](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create), lub [ Interfejs API REST](https://docs.microsoft.com/rest/api/sql/firewallrules). Kolejne reguły zapory na poziomie serwera mogą być tworzone i zarządzane przy użyciu tych metod oraz za pomocą języka Transact-SQL. 

> [!IMPORTANT]
> Reguły zapory poziomu bazy danych można tworzyć tylko i zarządzane przy użyciu języka Transact-SQL. 
>

Aby poprawić wydajność, reguły zapory na poziomie serwera są tymczasowo przechowywane w pamięci podręcznej na poziomie bazy danych. Aby odświeżyć pamięć podręczną, zobacz [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). 

> [!TIP]
> Można użyć [SQL Database Auditing](sql-database-auditing.md) inspekcji zmian zapory poziomu serwera i poziom bazy danych.
>

## <a name="manage-firewall-rules-using-the-azure-portal"></a>Zarządzaj regułami zapory przy użyciu portalu Azure

Aby skonfigurować regułę zapory poziomu serwera w portalu Azure, można albo przejść do strony Przegląd dla Twojej bazy danych Azure SQL lub strony Przegląd dla serwera logicznego bazy danych Azure.

> [!TIP]
> Samouczek, zobacz [tworzenie bazy danych przy użyciu portalu Azure](sql-database-get-started-portal.md).
>

**Na stronie Przegląd bazy danych**

1. Aby ustawić regułę zapory poziomu serwera na stronie Przegląd bazy danych, kliknij przycisk **ustawić Zapora serwera** na pasku narzędzi, jak pokazano na poniższej ilustracji: **ustawienia zapory** strony serwera bazy danych SQL zostanie otwarty.

      ![reguła zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule.png) 

2. Kliknij przycisk **Dodaj adres IP klienta** na pasku narzędzi, aby dodać adres IP komputera obecnie używasz a następnie kliknij przycisk **zapisać**. Dla bieżącego adresu IP zostanie utworzona reguła zapory na poziomie serwera.

      ![ustawianie reguły zapory serwera](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

**Na stronie Przegląd serwera**

Zostanie otwarta strona Omówienie serwera, wyświetlając nazwę FQDN serwera (takich jak **mynewserver20170403.database.windows.net**) i udostępnia opcje dla dalszej konfiguracji.

1. Aby ustawić regułę poziomu serwera na stronie Przegląd serwera, kliknij przycisk **zapory** w menu po lewej stronie w obszarze Ustawienia: 

2. Kliknij przycisk **Dodaj adres IP klienta** na pasku narzędzi, aby dodać adres IP komputera obecnie używasz a następnie kliknij przycisk **zapisać**. Dla bieżącego adresu IP zostanie utworzona reguła zapory na poziomie serwera.

## <a name="manage-firewall-rules-using-transact-sql"></a>Zarządzaj regułami zapory przy użyciu języka Transact-SQL
| Widok wykazu lub procedura składowana | Poziom | Opis |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Serwer |Wyświetla bieżące reguły zapory na poziomie serwera |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Serwer |Tworzy lub aktualizuje reguły zapory na poziomie serwera |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Serwer |Usuwa reguły zapory na poziomie serwera |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database (Baza danych) |Wyświetla bieżące reguły zapory na poziomie bazy danych |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database (Baza danych) |Tworzy lub aktualizuje reguły zapory na poziomie bazy danych |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bazy danych |Usuwa reguły zapory na poziomie bazy danych |


Poniższe przykłady przejrzyj istniejące zasady, Włącz zakres adresów IP na serwerze Contoso i usuwa regułę zapory:
   
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

## <a name="manage-firewall-rules-using-azure-powershell"></a>Zarządzaj regułami zapory przy użyciu programu Azure PowerShell
| Polecenie cmdlet | Poziom | Opis |
| --- | --- | --- |
| [Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule) |Serwer |Zwraca bieżące reguły zapory na poziomie serwera |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) |Serwer |Tworzy nową regułę zapory na poziomie serwera |
| [Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule) |Serwer |Aktualizuje właściwości istniejącej reguły zapory na poziomie serwera |
| [Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule) |Serwer |Usuwa reguły zapory na poziomie serwera |


W poniższym przykładzie ustawiono regułę zapory poziomu serwera przy użyciu programu PowerShell:

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Przykłady programu PowerShell w kontekście tego szybki start, zobacz [utworzyć DB - PowerShell](sql-database-get-started-powershell.md) i [utworzyć pojedynczą bazę danych i skonfigurować regułę zapory przy użyciu programu PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)
>

## <a name="manage-firewall-rules-using-azure-cli"></a>Zarządzaj regułami zapory przy użyciu wiersza polecenia platformy Azure
| Polecenie cmdlet | Poziom | Opis |
| --- | --- | --- |
|[Utwórz az programu sql server — reguły zapory](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Serwer|Powoduje utworzenie reguły zapory serwera|
|[Lista reguł zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Serwer|Wyświetla listę reguł zapory na serwerze|
|[Pokaż reguły zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Serwer|Wyświetla szczegóły reguły zapory|
|[Aktualizacja reguły zapory programu sql server az](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Serwer|Aktualizuje regułę zapory|
|[Usuwanie reguły zapory serwera sql az](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Serwer|Usuwa regułę zapory|

W poniższym przykładzie ustawiono regułę zapory poziomu serwera przy użyciu wiersza polecenia platformy Azure: 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
    -n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Przykład wiersza polecenia platformy Azure w ramach szybkiego startu, zobacz [utworzyć DDB - Azure CLI](sql-database-get-started-cli.md) i [utworzyć pojedynczą bazę danych i skonfigurować regułę zapory przy użyciu wiersza polecenia platformy Azure](scripts/sql-database-create-and-configure-database-cli.md)
>

## <a name="manage-firewall-rules-using-rest-api"></a>Zarządzaj regułami zapory przy użyciu interfejsu API REST
| Interfejs API | Poziom | Opis |
| --- | --- | --- |
| [List Firewall Rules](https://docs.microsoft.com/rest/api/sql/FirewallRules/ListByServer) (Lista reguł zapory) |Serwer |Wyświetla bieżące reguły zapory na poziomie serwera |
| [Create or Update Firewall Rule](https://docs.microsoft.com/rest/api/sql/FirewallRules/CreateOrUpdate) (Tworzenie i aktualizowanie reguły zapory) |Serwer |Tworzy lub aktualizuje reguły zapory na poziomie serwera |
| [Delete Firewall Rule](https://docs.microsoft.com/rest/api/sql/FirewallRules/Delete) (Usuwanie reguły zapory) |Serwer |Usuwa reguły zapory na poziomie serwera |

## <a name="server-level-firewall-rule-versus-a-database-level-firewall-rule"></a>Reguły zapory poziomu serwera i regułę zapory poziomu bazy danych
Q. Użytkownicy z jednej bazy danych należy całkowicie odizolowane od innej bazy danych?   
  Jeśli tak, należy przyznać dostęp przy użyciu reguł zapory na poziomie bazy danych. Dzięki temu można uniknąć przy użyciu reguły zapory poziomu serwera, które zezwalają na dostęp przez zaporę do wszystkich baz danych, zmniejszenie głębokości dalszej.   
 
Q. Czy użytkownicy o adresie IP mają dostęp do wszystkich baz danych?   
  Aby zmniejszyć liczbę razy, należy skonfigurować reguły zapory, należy użyć reguły zapory poziomu serwera.   

Q. Osobie lub zespołowi Konfigurowanie reguł zapory tylko ma dostęp za pośrednictwem portalu Azure, programu PowerShell lub interfejsu API REST?   
  Należy użyć reguły zapory poziomu serwera. Reguły zapory poziomu bazy danych można skonfigurować tylko za pomocą języka Transact-SQL.  

Q. Jest osobie lub zespołowi Konfigurowanie reguł zapory zabroniony z konieczności wysokiego poziomu uprawnień na poziomie bazy danych?   
  Użyj reguł zapory na poziomie serwera. Konfigurowanie reguł zapory na poziomie bazy danych przy użyciu języka Transact-SQL, wymaga co najmniej `CONTROL DATABASE` uprawnienia na poziomie bazy danych.  

Q. Jest osobie lub zespołowi inspekcję reguły zapory i konfigurowanie centralne zarządzanie reguły zapory dla wielu (prawdopodobnie 100) baz danych?   
  Tego wyboru zależy od środowiska i potrzeb użytkowników. Reguły zapory poziomu serwera może być łatwiejsze do skonfigurowania, ale skryptów można skonfigurować zasady na poziomie bazy danych. A nawet wtedy, gdy używasz reguły zapory poziomu serwera, może być konieczne inspekcji reguły zapory bazy danych, aby sprawdzić, czy użytkownicy z `CONTROL` uprawnień w bazie danych zostały utworzone reguły zapory poziomu bazy danych.   

Q. Czy można używać różnych obie reguły zapory poziomu serwera i poziom bazy danych?   
  Tak. W przypadku niektórych użytkowników, takich jak Administratorzy mogą wymagać reguły zapory poziomu serwera. Innych użytkowników, takich jak użytkownicy aplikacji bazy danych, może być konieczne reguły zapory poziomu bazy danych.   

## <a name="troubleshooting-the-database-firewall"></a>Rozwiązywanie problemów z zaporą bazy danych
Jeśli dostęp do usługi Microsoft Azure SQL Database nie działa zgodnie z oczekiwaniami, należy rozważyć następujące kwestie:

* **Konfiguracja lokalnej zapory:** aby komputer mógł uzyskać dostęp do usługi Azure SQL Database, może być konieczne utworzenie wyjątku zapory na komputerze dla portu TCP 1433. W przypadku nawiązywania połączeń wewnątrz granic chmury Azure może być konieczne otwarcie dodatkowych portów. Aby uzyskać więcej informacji, zobacz **bazy danych SQL: poza vs wewnątrz** sekcji [porty inne niż 1433 ADO.NET 4.5 i bazy danych SQL](sql-database-develop-direct-route-ports-adonet-v12.md).
* **Translator adresów sieciowych (NAT):** z powodu translatora adresów sieciowych (NAT), adres IP używany przez komputer do połączenia z usługą Azure SQL Database może być inny niż adres IP wyświetlany w ustawieniach konfiguracji adresu IP komputera. Aby wyświetlić adres IP używany przez komputer do łączenia się z platformą Azure, zaloguj się do portalu i przejdź na kartę **Konfigurowanie** na serwerze, który hostuje bazę danych. W sekcji **Dozwolone adresy IP** wyświetlany jest **bieżący adres IP klienta**. Kliknij przycisk **Dodaj** do listy **Dozwolone adresy IP**, aby zezwolić temu komputerowi na dostęp do serwera.
* **Zmiany na liście dozwolonych jeszcze nie zaczęły obowiązywać:** może wystąpić do pięciu minut opóźnienia, zanim zmiany konfiguracji zapory usługi Azure SQL Database zostaną zastosowane.
* **Logowanie nie ma autoryzacji lub użyto nieprawidłowego hasła:** jeśli logowanie nie ma uprawnień na serwerze usługi Azure SQL Database lub użyte hasło jest nieprawidłowe, nastąpi odmowa połączenia z serwerem usługi Azure SQL Database. Utworzenie ustawień zapory zapewnia klientom jedynie możliwość próby nawiązania połączenia z serwerem, ale każdy klient musi podać niezbędne poświadczenia zabezpieczeń. Aby uzyskać więcej informacji na temat przygotowywania logowań, zobacz sekcję Zarządzanie bazami danych, logowaniami i użytkownikami w usłudze Azure SQL Database.
* **Dynamiczny adres IP:** jeśli używane jest połączenie internetowe za pomocą dynamicznego adresowania IP i występują problemy z przejściem przez zaporę, można wypróbować jedno z poniższych rozwiązań:
  
  * Poproś usługodawcę internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich uzyskujących dostęp do serwera usługi Azure SQL Database, a następnie dodaj ten zakres adresów IP jako regułę zapory.
  * Pobierz statyczne adresy IP dla komputerów klienckich, a następnie dodaj te adresy IP jako reguły zapory.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać szybki start dotyczące tworzenia bazy danych i regułę zapory poziomu serwera, zobacz [tworzenie bazy danych Azure SQL](sql-database-get-started-portal.md).
- Aby uzyskać pomoc podczas łączenia się z bazą danych Azure SQL z aplikacji innych firm lub aplikacji typu open source, zobacz [Client quick-start code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx) (Przykłady kodu umożliwiające szybki start dla klienta usługi SQL Database).
- Informacje dotyczące dodatkowych portów, które mogą wymagać, aby otworzyć znajdują się w temacie **bazy danych SQL: poza vs wewnątrz** sekcji [porty inne niż 1433 ADO.NET 4.5 i bazy danych SQL](sql-database-develop-direct-route-ports-adonet-v12.md)
- Omówienie zabezpieczeń usługi Azure SQL Database, zobacz [zabezpieczania bazy danych](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
