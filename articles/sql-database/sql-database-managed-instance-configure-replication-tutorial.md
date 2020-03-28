---
title: Konfigurowanie replikacji transakcyjnej między dwoma wystąpieniami zarządzanymi a programem SQL Server
description: Samouczek, który konfiguruje replikację między wystąpieniem zarządzanym programu Publisher, wystąpieniem zarządzanym dystrybutora i subskrybentem programu SQL Server na maszynie Wirtualnej platformy Azure, wraz z niezbędnymi składnikami sieciowymi, takimi jak prywatna strefa DNS i komunikacja równorzędna sieci VPN.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: fa6e393500e9deeb91ee84aa5255320003817f08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719895"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>Samouczek: Konfigurowanie replikacji transakcyjnej między dwoma wystąpieniami zarządzanymi a programem SQL Server


Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> - Konfigurowanie wystąpienia zarządzanego jako wydawcy replikacji. 
> - Konfigurowanie wystąpienia zarządzanego jako dystrybutora replikacji. 
> - Konfigurowanie programu SQL Server jako subskrybenta. 

![Replikacja między pubem SQL MI, identyfikatorem SQL MI i podprogramem SQL Server](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

Ten samouczek jest przeznaczony dla doświadczonych odbiorców i zakłada, że użytkownik jest zaznajomiony z wdrażaniem i łączeniem się z obu wystąpień zarządzanych i maszyn wirtualnych programu SQL Server na platformie Azure. W związku z tym niektóre kroki w tym samouczku są błyszczące. 

Aby dowiedzieć się więcej, zobacz [omówienie wystąpienia zarządzanego bazy danych SQL platformy Azure](sql-database-managed-instance-index.yml)— [możliwości](sql-database-managed-instance.md)i artykuły [replikacji transakcyjnej SQL.](sql-database-managed-instance-transactional-replication.md)

Aby skonfigurować replikację między wydawcą wystąpienia zarządzanego a subskrybentem wystąpienia zarządzanego, zobacz [Konfigurowanie replikacji transakcyjnej między dwoma wystąpieniami zarządzanymi](replication-with-sql-database-managed-instance.md). 

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, upewnij się, że dysponujesz następującymi elementami:

- [Subskrypcja platformy Azure](https://azure.microsoft.com/free/). 
- Środowisko wdrażania dwóch wystąpień zarządzanych w tej samej sieci wirtualnej. 
- Subskrybent programu SQL Server lokalnie lub na maszynie wirtualnej platformy Azure. W tym samouczku użyto maszyny Wirtualnej platformy Azure.  
- [SQL Server Management Studio (SSMS) 18.0 lub więcej](/sql/ssms/download-sql-server-management-studio-ssms).
- Najnowsza wersja [programu Azure Powershell](/powershell/azure/install-az-ps?view=azps-1.7.0).
- Porty 445 i 1433 umożliwiają ruch SQL zarówno na Zaporze platformy Azure, jak i w Zaporze systemu Windows. 

## <a name="1---create-the-resource-group"></a>1 - Tworzenie grupy zasobów
Aby utworzyć nową grupę zasobów, użyj następującego fragmentu kodu programu PowerShell:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2 - Tworzenie dwóch wystąpień zarządzanych
Utwórz dwa wystąpienia zarządzane w ramach tej nowej grupy zasobów za pomocą [portalu Azure](https://portal.azure.com). 

- Nazwa wystąpienia zarządzanego wydawcy `sql-mi-publisher` powinna być: (wraz z kilkoma znakami do randomizacji) i nazwa sieci wirtualnej powinna być `vnet-sql-mi-publisher`.
- Nazwa wystąpienia zarządzanego dystrybutora `sql-mi-distributor` powinna być następująca: (wraz z kilkoma znakami do randomizacji) i powinna znajdować się _w tej samej sieci wirtualnej, co wystąpienie zarządzane przez wydawcę._

   ![Użyj sieci wirtualnej wydawcy dla dystrybutora](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

Aby uzyskać więcej informacji na temat tworzenia wystąpienia zarządzanego, zobacz [Tworzenie wystąpienia zarządzanego w portalu](sql-database-managed-instance-get-started.md)

  > [!NOTE]
  > Ze względu na prostotę i ponieważ jest to najbardziej powszechna konfiguracja, ten samouczek sugeruje umieszczenie wystąpienia zarządzanego dystrybutora w tej samej sieci wirtualnej co wydawca. Istnieje jednak możliwość utworzenia dystrybutora w osobnej sieci wirtualnej. Aby to zrobić, należy skonfigurować komunikację równorzędnych sieci VPN między sieciami wirtualnymi wydawcy i dystrybutora, a następnie skonfigurować komunikację równorzędnych sieci VPN między sieciami wirtualnymi dystrybutora i subskrybenta. 

## <a name="3---create-a-sql-server-vm"></a>3 - Tworzenie maszyny Wirtualnej programu SQL Server
Utwórz maszynę wirtualną programu SQL Server za pomocą [portalu Azure](https://portal.azure.com). Maszyna wirtualna programu SQL Server powinna mieć następujące właściwości:

- Nazwa:`sql-vm-sub`
- Obraz: SQL Server 2016 lub większy
- Grupa zasobów: taka sama jak wystąpienie zarządzane
- Sieć wirtualna:`sql-vm-sub-vnet` 

Aby uzyskać więcej informacji na temat wdrażania maszyny Wirtualnej programu SQL Server na platformie Azure, zobacz [Szybki start: Tworzenie maszyny Wirtualnej programu SQL Server](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md).

## <a name="4---configure-vpn-peering"></a>4 - Konfigurowanie komunikacji równorzędnej sieci VPN
Skonfiguruj komunikację równorzędnych sieci VPN, aby włączyć komunikację między siecią wirtualną dwóch wystąpień zarządzanych a siecią wirtualną programu SQL Server. Aby to zrobić, należy użyć tego fragmentu kodu programu PowerShell:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet 

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VPN peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VPN peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

Po ustanowieniu komunikacji równorzędnej sieci VPN przetestuj łączność, uruchamiając program SQL Server Management Studio (SSMS) w programie SQL Server i łącząc się z obydwoma wystąpieniami zarządzanymi. Aby uzyskać więcej informacji na temat łączenia się z wystąpieniem zarządzanym przy użyciu usługi SSMS, zobacz [Łączenie się z mi za pomocą sms.](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance) 

![Testowanie łączności z wystąpieniami zarządzanymi](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5 - Tworzenie prywatnej strefy DNS

Prywatna strefa DNS umożliwia routing DNS między wystąpieniami zarządzanymi a programem SQL Server. 

### <a name="create-private-dns-zone"></a>Tworzenie prywatnej strefy DNS
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com).
1. Wybierz **pozycję Utwórz zasób,** aby utworzyć nowy zasób platformy Azure. 
1. Szukaj `private dns zone` w portalu Azure Marketplace. 
1. Wybierz zasób **strefy dns prywatny** opublikowany przez firmę Microsoft, a następnie wybierz pozycję **Utwórz,** aby utworzyć strefę DNS. 
1. Wybierz subskrypcję i grupę zasobów z listy rozwijanej. 
1. Podaj dowolną nazwę strefy DNS, taką jak `repldns.com`. 

   ![Tworzenie prywatnej strefy DNS](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. Wybierz pozycję **Przegląd + utwórz**. Przejrzyj parametry prywatnej strefy DNS, a następnie wybierz pozycję **Utwórz,** aby utworzyć zasób. 

### <a name="create-a-record"></a>Tworzenie rekordu

1. Przejdź do nowej **prywatnej strefy DNS** i wybierz **pozycję Przegląd**. 
1. Wybierz **+ Zestaw rekordów,** aby utworzyć nowy rekord A. 
1. Podaj nazwę maszyny Wirtualnej programu SQL Server oraz prywatny wewnętrzny adres IP. 

   ![Konfigurowanie rekordu A](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. Wybierz **przycisk OK,** aby utworzyć rekord A. 

### <a name="link-the-virtual-network"></a>Łączenie sieci wirtualnej

1. Przejdź do nowej **prywatnej strefy DNS** i wybierz **pozycję Łącza sieci wirtualnej**. 
1. Wybierz pozycję **+ Dodaj**. 
1. Podaj nazwę łącza, `Pub-link`na przykład . 
1. Wybierz subskrypcję z listy rozwijanej, a następnie wybierz sieć wirtualną dla wystąpienia zarządzanego przez wydawcę. 
1. Zaznacz pole wyboru obok pozycji **Włącz automatyczną rejestrację**. 

   ![Tworzenie łącza sieci vnet](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. Wybierz **przycisk OK,** aby połączyć sieć wirtualną. 
1. Powtórz te kroki, aby dodać łącze dla sieci wirtualnej subskrybenta o nazwie, takiej jak `Sub-link`. 


## <a name="6---create-azure-storage-account"></a>6 - Tworzenie konta usługi Azure Storage

[Utwórz konto usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) dla katalogu roboczego, a następnie utwórz udział [plików](../storage/files/storage-how-to-create-file-share.md) na koncie magazynu. 

Skopiuj ścieżkę udziału plików w formacie:`\\storage-account-name.file.core.windows.net\file-share-name`   

Przykład: `\\replstorage.file.core.windows.net\replshare`

Skopiuj parametry połączenia klucza dostępu do magazynu w formacie:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

Przykład: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](../storage/common/storage-account-keys-manage.md). 


## <a name="7---create-a-database"></a>7 - Tworzenie bazy danych
Utwórz nową bazę danych w programie PUBLISHER MI. Aby to zrobić, wykonaj następujące kroki:

1. Uruchom program SQL Server Management Studio (SSMS) na serwerze SQL Server. 
1. Połącz `sql-mi-publisher` się z wystąpieniem zarządzanym. 
1. Otwórz okno **Nowe zapytanie** i wykonaj następującą kwerendę T-SQL, aby utworzyć bazę danych:

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="8---configure-distribution"></a>8 - Konfigurowanie dystrybucji 
Po ustanowieniu łączności i masz przykładową bazę danych, `sql-mi-distributor` można skonfigurować dystrybucję w wystąpieniu zarządzanym. Aby to zrobić, wykonaj następujące kroki:

1. Uruchom program SQL Server Management Studio (SSMS) na serwerze SQL Server. 
1. Połącz `sql-mi-distributor` się z wystąpieniem zarządzanym. 
1. Otwórz okno **Nowe zapytanie** i uruchom następujący kod Transact-SQL, aby skonfigurować dystrybucję w wystąpieniu zarządzanym dystrybutora: 

   ```sql
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Pamiętaj, aby użyć tylko ukośników odwrotnych (`\`) dla tego parametru. @working_directory Użycie ukośnika do przodu (`/`) może spowodować błąd podczas łączenia się z udziałem plików. 

1. Połącz `sql-mi-publisher` się z wystąpieniem zarządzanym. 
1. Otwórz okno **Nowe zapytanie** i uruchom następujący kod Transact-SQL, aby zarejestrować dystrybutora u wydawcy: 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9 - Tworzenie publikacji
Po skonfigurowaniu dystrybucji można teraz utworzyć publikację. Aby to zrobić, wykonaj następujące kroki: 

1. Uruchom program SQL Server Management Studio (SSMS) na serwerze SQL Server. 
1. Połącz `sql-mi-publisher` się z wystąpieniem zarządzanym. 
1. W **Eksploratorze obiektów**rozwiń węzeł **Replikacja** i kliknij prawym przyciskiem myszy folder **Publikacja lokalna.** Wybierz **nową publikację...**. 
1. Wybierz **przycisk Dalej,** aby przejść obok strony powitalnej. 
1. Na stronie Baza danych `ReplTutorial` **publikacji** wybierz wcześniej utworzoną bazę danych. Wybierz **pozycję Dalej**. 
1. Na stronie **Typ publikacji** wybierz pozycję **Publikacja transakcyjna**. Wybierz **pozycję Dalej**. 
1. Na stronie **Artykuły** zaznacz pole wyboru obok **pozycji Tabele**. Wybierz **pozycję Dalej**. 
1. Na stronie **Wiersze tabeli filtrowania** wybierz pozycję **Dalej** bez dodawania filtrów. 
1. Na stronie **Agent migawek** zaznacz pole wyboru **przycisk Utwórz migawkę natychmiast i zachowaj dostęp migawki do zainicjowania subskrypcji**. Wybierz **pozycję Dalej**. 
1. Na stronie **Zabezpieczenia agenta** wybierz pozycję **Ustawienia zabezpieczeń..**. Podaj poświadczenia logowania programu SQL Server do użycia dla agenta migawki i aby połączyć się z wydawcą. Wybierz **przycisk OK,** aby zamknąć stronę **Zabezpieczenia agenta migawki.** Wybierz **pozycję Dalej**. 

   ![Konfigurowanie zabezpieczeń agenta migawek](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. Na stronie **Akcje kreatora** wybierz pozycję **Utwórz publikację** i (opcjonalnie) wybierz opcję **Generowanie pliku skryptu z instrukcjami tworzenia publikacji,** jeśli chcesz zapisać ten skrypt na później. 
1. Na stronie **Ukończono Kreator** nazwij publikację `ReplTest` i wybierz pozycję **Dalej,** aby utworzyć publikację. 
1. Po utworzeniu publikacji odśwież węzeł **Replikacja** w **Eksploratorze obiektów** i rozwiń pozycję **Publikacje lokalne,** aby wyświetlić nową publikację. 


## <a name="10---create-the-subscription"></a>10 - Tworzenie subskrypcji 

Po utworzeniu publikacji można utworzyć subskrypcję. Aby to zrobić, wykonaj następujące kroki: 

1. Uruchom program SQL Server Management Studio (SSMS) na serwerze SQL Server. 
1. Połącz `sql-mi-publisher` się z wystąpieniem zarządzanym. 
1. Otwórz okno **Nowe zapytanie** i uruchom następujący kod Transact-SQL, aby dodać agenta subskrypcji i dystrybucji. Użyj systemu DNS jako części nazwy subskrybenta. 

```sql
use [ReplTutorial]
exec sp_addsubscription 
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub', 
@subscription_type = N'Push', 
@sync_type = N'automatic', 
@article = N'all', 
@update_mode = N'read only', 
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub', 
@job_login = N'azureuser', 
@job_password = '<Complex Password>', 
@subscriber_security_mode = 0, 
@subscriber_login = N'azureuser', 
@subscriber_password = '<Complex Password>', 
@dts_package_location = N'Distributor'
GO
```

## <a name="11---test-replication"></a>11 - Replikacja testowa 

Po skonfigurowaniu replikacji można ją przetestować, wstawiając nowe elementy do wydawcy i obserwując, jak zmiany są propagowane przez subskrybenta. 

Uruchom następujący fragment kodu T-SQL, aby wyświetlić wiersze na subskrybenta:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Uruchom następujący fragment kodu T-SQL, aby wstawić dodatkowe wiersze w wydawcy, a następnie ponownie sprawdź wiersze subskrybenta. 

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

1. Przejdź do grupy zasobów w [witrynie Azure portal](https://portal.azure.com). 
1. Wybierz wystąpienia zarządzane, a następnie wybierz pozycję **Usuń**. Wpisz `yes` pole tekstowe, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. Ten proces może zająć trochę czasu, aby zakończyć w tle, a dopóki nie zostanie zrobione, nie będzie można usunąć *wirtualnego klastra* lub innych zasobów zależnych. Monitoruj usuwanie na karcie Działanie, aby potwierdzić, że wystąpienie zarządzane zostało usunięte. 
1. Po usunięciu wystąpienia zarządzanego usuń *klaster wirtualny,* zaznaczając go w grupie zasobów, a następnie wybierając pozycję **Usuń**. Wpisz `yes` pole tekstowe, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. 
1. Usuń wszystkie pozostałe zasoby. Wpisz `yes` pole tekstowe, aby potwierdzić, że chcesz usunąć zasób, a następnie wybierz pozycję **Usuń**. 
1. Usuń grupę zasobów, wybierając pozycję **Usuń grupę zasobów,** wpisując nazwę grupy zasobów, `myResourceGroup`a następnie wybierając polecenie **Usuń**. 

## <a name="known-errors"></a>Znane błędy

### <a name="windows-logins-are-not-supported"></a>Logowania systemu Windows nie są obsługiwane

`Exception Message: Windows logins are not supported in this version of SQL Server.`

Agent został skonfigurowany przy użyciu logowania systemu Windows i zamiast tego musi używać logowania programu SQL Server. Użyj **strony Zabezpieczenia agenta** **właściwości publikacji,** aby zmienić poświadczenia logowania na identyfikator SQL Server. 


### <a name="failed-to-connect-to-azure-storage"></a>Nie można połączyć się z usługą Azure Storage


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Otrzymany ciąg połączenia usługi Azure Storage dla replstorage 2019-11-19 02:21:05.07 Łączenie się z Usługa Azure Files Storage '\\replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 Nie można połączyć się z usługą Azure Storage '' z błędem systemu operacyjnego: 53.


Jest to prawdopodobne, ponieważ port 445 jest zamknięty w zaporze platformy Azure, zaporze systemu Windows lub obu. 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

Użycie ukośnika do przodu zamiast ukośnika odwrotnego w ścieżce pliku dla udziału plików może spowodować ten błąd. To jest `\\replstorage.file.core.windows.net\replshare` w porządku: Może to spowodować błąd systemu operacyjnego 55:`'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Nie można połączyć się z subskrybentem

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Możliwe rozwiązania:
- Upewnij się, że port 1433 jest otwarty. 
- Upewnij się, że protokół TCP/IP jest włączony przez subskrybenta. 
- Upewnij się, że nazwa DNS została użyta podczas tworzenia subskrybenta. 
- Sprawdź, czy sieci wirtualne są poprawnie połączone w prywatnej strefie DNS. 
- Sprawdź, czy rekord A jest poprawnie skonfigurowany. 
- Sprawdź, czy komunikacja równorzędna sieci VPN jest poprawnie skonfigurowana. 

### <a name="no-publications-to-which-you-can-subscribe"></a>Brak publikacji, do których można subskrybować

Podczas dodawania nowej subskrypcji za pomocą Kreatora **nowej subskrypcji** na stronie **Publikacja** może się okazać, że nie ma żadnych baz danych i publikacji wymienionych jako dostępne opcje i może zostać wyświetlony następujący komunikat o błędzie:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
Chociaż jest możliwe, że ten komunikat o błędzie jest dokładne i naprawdę nie są dostępne publikacje na wydawcy, do którego masz połączenie, lub brakuje wystarczających uprawnień, ten błąd może być również spowodowane przez starszą wersję programu SQL Server Management Studio. Spróbuj uaktualnić do programu SQL Server Management Studio 18.0 lub większej, aby wykluczyć to jako główną przyczynę. 


## <a name="next-steps"></a>Następne kroki

### <a name="enable-security-features"></a>Włączanie funkcji zabezpieczeń.

Zobacz następujący artykuł [funkcji zabezpieczeń funkcji wystąpienia zarządzanego,](sql-database-managed-instance.md#azure-sql-database-security-features) aby uzyskać wyczerpującą listę sposobów zabezpieczenia bazy danych. Omówione są następujące funkcje zabezpieczeń:

- [Inspekcja zarządzanych wystąpień](sql-database-managed-instance-auditing.md) 
- [Zawsze szyfrowane](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Wykrywanie zagrożeń](sql-database-managed-instance-threat-detection.md) 
- [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking)
- [Zabezpieczenia na poziomie wiersza](/sql/relational-databases/security/row-level-security) 
- [Przezroczyste szyfrowanie danych (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Funkcje wystąpienia zarządzanego

Aby zapoznać się z pełnym omówieniem funkcji wystąpienia zarządzanego, zobacz:

> [!div class="nextstepaction"]
> [Funkcje wystąpienia zarządzanego](sql-database-managed-instance.md)
