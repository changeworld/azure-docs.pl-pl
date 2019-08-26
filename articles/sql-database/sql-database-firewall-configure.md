---
title: Azure SQL Database i reguły zapory adresów IP magazynu danych | Microsoft Docs
description: Dowiedz się, jak skonfigurować bazę danych SQL lub SQL Data Warehouse zaporę z regułami zapory adresów IP na poziomie serwera w celu zarządzania dostępem i konfigurowania jednej lub puli baz danych z regułami zapory adresów IP na poziomie bazy danych.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 39d2dae28bde8ff35408733a1af886c302ec79bf
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568182"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Reguły zapory Azure SQL Database i SQL Data Warehouse IP

Microsoft Azure [SQL Database](sql-database-technical-overview.md) i [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) zapewnić usługę relacyjnej bazy danych dla platformy Azure i innych aplikacji internetowych. Aby chronić dane, zapora uniemożliwia wszelki dostęp do serwera bazy danych do momentu określenia komputerów, które mają uprawnienia. Zapora udziela dostępu do bazy danych na podstawie źródłowego adresu IP każdego żądania.

> [!NOTE]
> Ten artykuł ma zastosowanie do programu Azure SQL Server oraz do baz danych SQL Database i SQL Data Warehouse utworzonych na serwerze SQL platformy Azure. Dla uproszczenia usługi SQL Database i SQL Data Warehouse są łącznie nazywane usługą SQL Database.
> [!IMPORTANT]
> Ten artykuł *nie* dotyczy **Azure SQL Database wystąpienia zarządzanego**. Aby uzyskać więcej informacji na temat konfiguracji sieci, należy zapoznać się z poniższym artykułem dotyczącym [łączenia się z wystąpieniem zarządzanym](sql-database-managed-instance-connect-app.md) .

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>Reguły sieci wirtualnej jako alternatywy dla reguł IP

Oprócz reguł adresów IP Zapora zarządza również *regułami sieci wirtualnej*. Reguły sieci wirtualnej są oparte na punktach końcowych usługi Virtual Network. Reguły sieci wirtualnej mogą być preferowane z regułami adresów IP w niektórych przypadkach. Aby dowiedzieć się więcej, zobacz [Virtual Network punkty końcowe usługi i reguły dla Azure SQL Database](sql-database-vnet-service-endpoint-rule-overview.md).

## <a name="overview"></a>Omówienie

Początkowo cały dostęp do serwera Azure SQL jest blokowany przez zaporę SQL Database. Aby uzyskać dostęp do serwera bazy danych, należy określić co najmniej jedną regułę zapory adresu IP na poziomie serwera, która umożliwia dostęp do serwera Azure SQL. Użyj reguł zapory adresów IP, aby określić, które zakresy adresów IP z Internetu są dozwolone, oraz czy aplikacje platformy Azure mogą podejmować próby nawiązania połączenia z serwerem Azure SQL.

Aby selektywnie udzielić dostępu do tylko jednej z baz danych na serwerze Azure SQL, należy utworzyć regułę na poziomie bazy danych dla wymaganej bazy danych. Określ zakres adresów IP dla reguły zapory adresu IP bazy danych wykraczający poza zakres adresów IP określony w regule zapory adresów IP na poziomie serwera i upewnij się, że adres IP klienta mieści się w zakresie określonym w regule na poziomie bazy danych.

> [!IMPORTANT]
> SQL Data Warehouse obsługuje tylko reguły zapory adresów IP na poziomie serwera i nie obsługuje reguł zapory adresów IP na poziomie bazy danych.

Próby połączenia z Internetu i platformy Azure muszą najpierw przejść przez zaporę, zanim dotrą do serwera Azure SQL lub usługi SQL Database, jak pokazano na poniższym diagramie:

   ![Diagram opisujący konfigurację zapory.][1]

- **Reguły zapory adresów IP na poziomie serwera:**

  Te reguły umożliwiają klientom dostęp do całego serwera Azure SQL, czyli wszystkich baz danych w ramach tego samego serwera SQL Database. Reguły te są przechowywane w **głównej** bazie danych. Reguły zapory adresów IP na poziomie serwera można skonfigurować przy użyciu portalu lub instrukcji języka Transact-SQL. Aby utworzyć reguły zapory adresów IP na poziomie serwera przy użyciu Azure Portal lub programu PowerShell, musisz być właścicielem subskrypcji lub współautorem subskrypcji. Aby utworzyć regułę zapory adresów IP na poziomie serwera przy użyciu języka Transact-SQL, należy nawiązać połączenie z wystąpieniem SQL Database jako nazwę logowania podmiotu zabezpieczeń na poziomie serwera lub administratora Azure Active Directory (co oznacza, że reguła zapory adresów IP na poziomie serwera musi być najpierw utworzona przez Użytkownik z uprawnieniami na poziomie platformy Azure).

- **Reguły zapory adresów IP na poziomie bazy danych:**

  Te reguły umożliwiają klientom dostęp do określonych (zabezpieczonych) baz danych w ramach tego samego serwera SQL Database. Te reguły można utworzyć dla każdej bazy danych (w tym bazy danych **Master** ) i są one przechowywane w poszczególnych bazach danych. Reguły zapory adresów IP na poziomie bazy danych dla baz danych Master i User mogą być tworzone i zarządzane tylko przy użyciu instrukcji języka Transact-SQL i dopiero po skonfigurowaniu pierwszej zapory na poziomie serwera. W przypadku określenia zakresu adresów IP w regule zapory adresów IP na poziomie bazy danych, która znajduje się poza zakresem określonym w regule zapory adresów IP na poziomie serwera, tylko klienci, którzy mają adresy IP w zakresie poziomu bazy danych, mogą uzyskiwać dostęp do bazy danych. Dla bazy danych można uzyskać maksymalnie 128 reguł zapory adresów IP na poziomie bazy danych. Aby uzyskać więcej informacji na temat konfigurowania reguł zapory adresów IP na poziomie bazy danych, zobacz przykład w dalszej części tego artykułu i zobacz [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendation"></a>Zalecenie

Firma Microsoft zaleca korzystanie z reguł zapory adresów IP na poziomie bazy danych zawsze wtedy, gdy jest to możliwe, aby zwiększyć bezpieczeństwo i zwiększyć możliwości przenoszenia bazy danych. Użyj reguł zapory adresów IP na poziomie serwera dla administratorów i jeśli masz wiele baz danych, które mają te same wymagania dotyczące dostępu, i nie chcesz poświęcać czasu na konfigurowanie każdej bazy danych indywidualnie.

> [!IMPORTANT]
> System Windows Azure SQL Database obsługuje maksymalnie 128 reguł zapory adresów IP.
> [!NOTE]
> Aby uzyskać informacje o przenośnych bazach danych w kontekście ciągłości działalności biznesowej, zobacz [Wymagania dotyczące uwierzytelniania dla odzyskiwania po awarii](sql-database-geo-replication-security-config.md).

### <a name="connecting-from-the-internet"></a>Łączenie się z Internetu

Gdy komputer próbuje nawiązać połączenie z serwerem bazy danych z Internetu, Zapora najpierw sprawdza źródłowy adres IP żądania względem reguł zapory adresów IP na poziomie bazy danych dla bazy danych, której żąda połączenie:

- Jeśli adres IP żądania należy do jednego z zakresów określonych w regułach zapory adresów IP na poziomie bazy danych, połączenie zostanie przyznane SQL Database, który zawiera regułę.
- Jeśli adres IP żądania nie należy do jednego z zakresów określonych w regule zapory adresów IP na poziomie bazy danych, sprawdzane są reguły zapory adresów IP na poziomie serwera. Jeśli adres IP żądania należy do jednego z zakresów określonych w regułach zapory adresów IP na poziomie serwera, nawiązanie połączenia zostanie nadane. Reguły zapory adresów IP na poziomie serwera mają zastosowanie do wszystkich baz danych SQL na serwerze SQL platformy Azure.  
- Jeśli adres IP żądania nie należy do zakresów określonych w regułach zapory adresów IP na poziomie bazy danych lub serwera, żądanie połączenia kończy się niepowodzeniem.

> [!NOTE]
> Aby uzyskać dostęp do usługi Azure SQL Database z komputera lokalnego, upewnij się, że zapora w sieci i na komputerze lokalnym zezwala na komunikację wychodzącą na porcie TCP 1433.

### <a name="connecting-from-azure"></a>Łączenie z platformy Azure

Aby umożliwić aplikacjom z platformy Azure łączenie się z serwerem Azure SQL, należy włączyć połączenia platformy Azure. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Ustawienie zapory z adresem początkowym i końcowym równym 0.0.0.0 wskazuje, że połączenia platformy Azure są dozwolone. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera do serwera usługi Azure SQL Database.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.

## <a name="creating-and-managing-ip-firewall-rules"></a>Tworzenie reguł zapory adresów IP i zarządzanie nimi

Pierwsze ustawienie zapory na poziomie serwera można utworzyć przy użyciu [Azure Portal](https://portal.azure.com/) lub programowo przy użyciu [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), interfejsu [wiersza polecenia platformy Azure](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)lub [API REST](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate). Kolejne reguły zapory adresów IP na poziomie serwera można tworzyć i zarządzać nimi za pomocą tych metod oraz przy użyciu języka Transact-SQL.

> [!IMPORTANT]
> Reguły zapory adresów IP na poziomie bazy danych można tworzyć i zarządzać nimi za pomocą języka Transact-SQL.

Aby zwiększyć wydajność, reguły zapory adresów IP na poziomie serwera są tymczasowo przechowywane w pamięci podręcznej na poziomie bazy danych. Aby odświeżyć pamięć podręczną, zobacz [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> [Inspekcji SQL Database](sql-database-auditing.md) można użyć do inspekcji zmian w zaporze na poziomie serwera i na poziomie bazy danych.

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>Zarządzanie regułami zapory adresów IP na poziomie serwera przy użyciu Azure Portal

Aby ustawić regułę zapory adresów IP na poziomie serwera w Azure Portal, można przejść do strony przegląd dla usługi Azure SQL Database lub na stronie Przegląd dla serwera SQL Database.

> [!TIP]
> Aby zapoznać się z samouczkiem, zobacz [Tworzenie bazy danych przy użyciu Azure Portal](sql-database-single-database-get-started.md).

### <a name="from-database-overview-page"></a>Na stronie Przegląd bazy danych

1. Aby ustawić regułę zapory adresów IP na poziomie serwera na stronie Przegląd bazy danych, kliknij pozycję **Ustaw zaporę serwera** na pasku narzędzi, jak pokazano na poniższej ilustracji: Zostanie otwarta strona **Ustawienia zapory** dla serwera usługi SQL Database.

      ![Reguła zapory adresu IP serwera](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. Kliknij pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać adres IP aktualnie używanego komputera, a następnie kliknij przycisk **Zapisz**. Reguła zapory adresów IP na poziomie serwera jest tworzona dla bieżącego adresu IP.

      ![Ustaw regułę zapory adresów IP na poziomie serwera](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>Na stronie Przegląd serwera

Zostanie otwarta strona przegląd dla Twojego serwera wyświetlająca w pełni kwalifikowaną nazwę serwera (na przykład **mynewserver20170403.Database.Windows.NET**) i opcje dalszej konfiguracji.

1. Aby ustawić regułę poziomu serwera na stronie Przegląd serwera, kliknij przycisk **Zapora** w menu po lewej stronie w obszarze Ustawienia:

2. Kliknij pozycję **Dodaj adres IP klienta** na pasku narzędzi, aby dodać adres IP aktualnie używanego komputera, a następnie kliknij przycisk **Zapisz**. Reguła zapory adresów IP na poziomie serwera jest tworzona dla bieżącego adresu IP.

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>Zarządzanie regułami zapory adresów IP przy użyciu języka Transact-SQL

| Widok wykazu lub procedura składowana | Poziom | Opis |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Serwer |Wyświetla bieżące reguły zapory adresów IP na poziomie serwera |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Serwer |Tworzy lub aktualizuje reguły zapory adresów IP na poziomie serwera |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Serwer |Usuwa reguły zapory adresów IP na poziomie serwera |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database (Baza danych) |Wyświetla bieżące reguły zapory adresów IP na poziomie bazy danych |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database (Baza danych) |Tworzy lub aktualizuje reguły zapory adresów IP na poziomie bazy danych |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bazy danych |Usuwa reguły zapory adresów IP na poziomie bazy danych |

W poniższych przykładach zapoznaj się z istniejącymi regułami, Włącz zakres adresów IP na serwerze Contoso i Usuń regułę zapory adresów IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Następnie Dodaj regułę zapory adresów IP na poziomie serwera.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Aby usunąć regułę zapory adresów IP na poziomie serwera, wykonaj procedurę składowaną sp_delete_firewall_rule. Poniższy przykład usuwa regułę o nazwie ContosoFirewallRule:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Zarządzanie regułami zapory adresów IP na poziomie serwera przy użyciu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany przez Azure SQL Database, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

| Polecenia cmdlet | Poziom | Opis |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Serwer |Zwraca bieżące reguły zapory na poziomie serwera |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Serwer |Tworzy nową regułę zapory na poziomie serwera |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Serwer |Aktualizuje właściwości istniejącej reguły zapory na poziomie serwera |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Serwer |Usuwa reguły zapory na poziomie serwera |

Poniższy przykład ustawia regułę zapory adresów IP na poziomie serwera przy użyciu programu PowerShell:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> Przykłady programu PowerShell w kontekście przewodnika Szybki Start zawiera temat Tworzenie bazy danych [— PowerShell](sql-database-powershell-samples.md) i [Tworzenie pojedynczej bazy danych oraz konfigurowanie reguły zapory adresu IP na poziomie serwera SQL Database przy użyciu programu PowerShell](scripts/sql-database-create-and-configure-database-powershell.md)

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Zarządzanie regułami zapory adresów IP na poziomie serwera przy użyciu interfejsu wiersza polecenia platformy Azure

| Polecenia cmdlet | Poziom | Opis |
| --- | --- | --- |
|[AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Serwer|Tworzy regułę zapory adresu IP serwera|
|[AZ SQL Server firewall-Rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Serwer|Wyświetla reguły zapory adresów IP na serwerze|
|[AZ SQL Server firewall-Rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Serwer|Pokazuje szczegóły reguły zapory IP|
|[AZ SQL Server firewall-Rule Update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Serwer|Aktualizuje regułę zapory IP|
|[AZ SQL Server firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Serwer|Usuwa regułę zapory adresów IP|

Poniższy przykład ustawia regułę zapory adresów IP na poziomie serwera przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Przykład interfejsu wiersza polecenia platformy Azure w kontekście przewodnika Szybki Start zawiera temat [Tworzenie bazy danych — interfejs wiersza polecenia platformy Azure](sql-database-cli-samples.md) i [Tworzenie pojedynczej bazy danych oraz konfigurowanie reguły zapory SQL Database IP przy użyciu interfejsu wiersza polecenia platformy Azure](scripts/sql-database-create-and-configure-database-cli.md)

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>Zarządzanie regułami zapory adresów IP na poziomie serwera przy użyciu interfejsu API REST

| interfejs API | Poziom | Opis |
| --- | --- | --- |
| [List Firewall Rules](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) (Lista reguł zapory) |Serwer |Wyświetla bieżące reguły zapory adresów IP na poziomie serwera |
| [Create or Update Firewall Rule](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) (Tworzenie i aktualizowanie reguły zapory) |Serwer |Tworzy lub aktualizuje reguły zapory adresów IP na poziomie serwera |
| [Delete Firewall Rule](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) (Usuwanie reguły zapory) |Serwer |Usuwa reguły zapory adresów IP na poziomie serwera |
| [Pobierz reguły zapory](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Serwer | Pobiera reguły zapory adresów IP na poziomie serwera |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Reguły zapory adresów IP na poziomie serwera i na poziomie bazy danych

PYTANIE: Czy użytkownicy z jednej bazy danych mają być w pełni odizolowani od innej bazy danych?
Jeśli tak, Udziel dostępu przy użyciu reguł zapory adresów IP na poziomie bazy danych. Pozwala to uniknąć używania reguł zapory adresów IP na poziomie serwera, które zezwalają na dostęp przez zaporę do wszystkich baz danych, co zmniejsza głębokość obrony.

PYTANIE: Czy użytkownicy na adresie IP muszą mieć dostęp do wszystkich baz danych?
Reguły zapory adresów IP na poziomie serwera umożliwiają skrócenie czasu, w którym należy skonfigurować reguły zapory adresów IP.

PYTANIE: Czy osoba lub zespół konfigurujący reguły zapory IP ma dostęp tylko za pomocą Azure Portal, programu PowerShell lub interfejsu API REST?
Należy użyć reguł zapory adresów IP na poziomie serwera. Reguły zapory adresów IP na poziomie bazy danych można skonfigurować tylko przy użyciu języka Transact-SQL.  

PYTANIE: Czy osoba lub zespół konfiguruje reguły zapory IP zabronione przed posiadaniem wysokiego poziomu uprawnień na poziomie bazy danych?
Użyj reguł zapory adresów IP na poziomie serwera. Konfigurowanie reguł zapory adresów IP na poziomie bazy danych przy użyciu języka Transact-SQL `CONTROL DATABASE` wymaga co najmniej uprawnienia na poziomie bazy danych.  

PYTANIE: Czy osoba lub zespół konfiguruje lub przeprowadza inspekcję reguł zapory IP, centralne zarządzanie regułami zapory IP dla wielu (prawdopodobnie setek) baz danych?
Wybór ten zależy od potrzeb i środowiska. Reguły zapory adresów IP na poziomie serwera mogą być łatwiejsze do skonfigurowania, ale skrypty mogą konfigurować reguły na poziomie bazy danych. Nawet jeśli są używane reguły zapory adresów IP na poziomie serwera, może być konieczne przeprowadzenie inspekcji reguł zapory adresów IP na poziomie bazy danych, aby zobaczyć, `CONTROL` czy użytkownicy z uprawnieniami do bazy danych utworzyli reguły zapory adresów IP na poziomie bazy danych.

PYTANIE: Czy mogę użyć kombinacji reguł zapory adresów IP na poziomie serwera i na poziomie bazy danych?
Tak. Niektórzy użytkownicy, tacy jak Administratorzy, mogą potrzebować reguł zapory adresów IP na poziomie serwera. Inni użytkownicy, tacy jak użytkownicy aplikacji bazy danych, mogą potrzebować reguł zapory adresów IP na poziomie bazy danych.

## <a name="troubleshooting-the-database-firewall"></a>Rozwiązywanie problemów z zaporą bazy danych

Jeśli dostęp do usługi Microsoft Azure SQL Database nie działa zgodnie z oczekiwaniami, należy rozważyć następujące kwestie:

- **Konfiguracja zapory lokalnej:**

  Aby komputer mógł uzyskać dostęp do Azure SQL Database, może być konieczne utworzenie na komputerze wyjątku zapory dla portu TCP 1433. W przypadku nawiązywania połączeń wewnątrz granic chmury Azure może być konieczne otwarcie dodatkowych portów. Aby uzyskać więcej informacji, zobacz **SQL Database: Poza zakresem portów poza [1433 dla ADO.NET 4,5 i SQL Database.](sql-database-develop-direct-route-ports-adonet-v12.md)**

- **Translator adresów sieciowych (NAT):**

  Ze względu na translator adresów sieciowych adres IP używany przez komputer do łączenia się z Azure SQL Database może być inny niż adres IP wyświetlany w ustawieniach konfiguracji adresu IP komputera. Aby wyświetlić adres IP używany przez komputer do łączenia się z platformą Azure, zaloguj się do portalu i przejdź na kartę **Konfigurowanie** na serwerze, który hostuje bazę danych. W sekcji **Dozwolone adresy IP** wyświetlany jest **bieżący adres IP klienta**. Kliknij przycisk **Dodaj** do listy **Dozwolone adresy IP**, aby zezwolić temu komputerowi na dostęp do serwera.

- **Zmiany na liście dozwolonych nie zostały jeszcze zastosowane:**

  Aby zmiany w konfiguracji zapory Azure SQL Database zaczęły obowiązywać, może wystąpić nawet pięć minut.

- **Logowanie nie jest autoryzowane lub użyto nieprawidłowego hasła:**

  Jeśli logowanie nie ma uprawnień na serwerze Azure SQL Database lub użyte hasło jest nieprawidłowe, nastąpiło odmowa połączenia z serwerem Azure SQL Database. Utworzenie ustawień zapory zapewnia klientom jedynie możliwość próby nawiązania połączenia z serwerem, ale każdy klient musi podać niezbędne poświadczenia zabezpieczeń. Aby uzyskać więcej informacji na temat przygotowywania nazw logowania, zobacz [Zarządzanie bazami danych, nazwami logowania i użytkownikami w Azure SQL Database](sql-database-manage-logins.md).

- **Dynamiczny adres IP:**

  Jeśli masz połączenie internetowe z dynamicznym adresem IP i masz problemy z uzyskaniem przez zaporę, możesz wypróbować jedno z następujących rozwiązań:
  
  - Poproszenie usługodawcy internetowego (ISP) o zakres adresów IP przypisany do komputerów klienckich, które uzyskują dostęp do serwera Azure SQL Database, a następnie Dodaj zakres adresów IP jako regułę zapory adresów IP.
  - Zamiast tego Uzyskaj statyczne adresowanie IP dla komputerów klienckich, a następnie Dodaj adresy IP jako reguły zapory adresów IP.

## <a name="next-steps"></a>Następne kroki

- Potwierdź, że środowisko sieci firmowej zezwala na komunikację przychodzącą z zakresów adresów IP obliczeń (w tym zakresów SQL) używanych przez Microsoft Azure centrach danych. Może być konieczne dozwolonych tych adresów IP, zobacz zakres adresów [IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653)  
- Aby zapoznać się z przewodnikiem Szybki Start dotyczącym tworzenia reguły zapory adresów IP na poziomie serwera, zobacz [Tworzenie bazy danych Azure SQL Database](sql-database-single-database-get-started.md).
- Aby uzyskać pomoc podczas łączenia się z bazą danych Azure SQL Database z aplikacji innych firm lub aplikacji typu open source, zobacz [Client quick-start code samples to SQL Database](https://msdn.microsoft.com/library/azure/ee336282.aspx) (Przykłady kodu umożliwiające szybki start dla klienta usługi SQL Database).
- Aby uzyskać informacje na temat dodatkowych portów, które mogą być konieczne do otwarcia **, zobacz SQL Database: Poza zakresem portów poza [1433 dla ADO.NET 4,5 i SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)**
- Aby zapoznać się z omówieniem zabezpieczeń Azure SQL Database, zobacz [Zabezpieczanie bazy danych](sql-database-security-overview.md)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
