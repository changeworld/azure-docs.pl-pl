---
title: Importowanie lub eksportowanie bazy danych SQL
description: Importowanie lub eksportowanie bazy danych SQL platformy Azure bez zezwolenia na dostęp do serwera usług platformy Azure.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/08/2020
ms.openlocfilehash: 61a85b2554bbd69541b3081f72525d2b7deed625
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529231"
---
# <a name="import-or-export-an-azure-sql-database-without-allowing-azure-services-to-access-the-server"></a>Importowanie lub eksportowanie bazy danych SQL platformy Azure bez zezwolenia na dostęp do serwera usług platformy Azure

W tym artykule pokazano, jak zaimportować lub wyeksportować bazę danych SQL platformy Azure, gdy *opcja Zezwalaj* na usługi Azure jest ustawiona na *WYŁĄCZONA* na serwerze SQL platformy Azure. Przepływ pracy używa maszyny wirtualnej platformy Azure do uruchamiania sqlpackage do wykonywania operacji importu lub eksportu.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="create-the-azure-virtual-machine"></a>Tworzenie maszyny wirtualnej platformy Azure

Utwórz maszynę wirtualną platformy Azure, wybierając przycisk **Wdrażanie na platformie Azure.**

Ten szablon umożliwia wdrożenie prostej maszyny wirtualnej systemu Windows przy użyciu kilku różnych opcji dla wersji systemu Windows przy użyciu najnowszej poprawionej wersji. Spowoduje to wdrożenie maszyny wirtualnej o rozmiarze A2 w lokalizacji grupy zasobów i zwróci w pełni kwalifikowaną nazwę domeny maszyny Wirtualnej.
<br><br>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-simple-windows%2Fazuredeploy.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Aby uzyskać więcej informacji, zobacz [Bardzo proste wdrożenie maszyny Wirtualnej systemu Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows).


## <a name="connect-to-the-virtual-machine"></a>Nawiązywanie połączenia z maszyną wirtualną

W poniższych krokach pokazano, jak połączyć się z maszyną wirtualną przy użyciu połączenia pulpitu zdalnego.

1. Po zakończeniu wdrażania przejdź do zasobu maszyny wirtualnej.

    ![VM](./media/import-export-from-vm/vm.png)  

2. Wybierz przycisk **Połącz**.

   Zostanie wyświetlony formularz pliku protokołu usługi pulpitu zdalnego (plik rdp) z publicznym adresem IP i numerem portu maszyny wirtualnej.

   ![Formularz RDP](./media/import-export-from-vm/rdp.png)  

3. Wybierz opcję **Pobierz plik RDP**.

   > [!NOTE]
   > Można również użyć SSH, aby połączyć się z maszyną wirtualną.

4. Zamknij formularz **Połącz z maszyną wirtualną.**
5. Aby połączyć się z maszyną wirtualną, otwórz pobrany plik RDP.
6. Po wyświetleniu monitu wybierz pozycję **Połącz**. Na komputerze Mac należy skorzystać z klienta RDP, takiego jak ten [klient pulpitu zdalnego](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12) ze sklepu Mac App Store.

7. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej, a następnie wybierz **przycisk OK**.

8. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz **pozycję Tak** lub **Kontynuuj,** aby kontynuować połączenie.



## <a name="install-sqlpackage"></a>Instalowanie pakietu SqlPackage

[Pobierz i zainstaluj najnowszą wersję programu SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).




Aby uzyskać dodatkowe informacje, zobacz [SqlPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage).

## <a name="create-a-firewall-rule-to-allow-the-vm-access-to-the-database"></a>Tworzenie reguły zapory umożliwiającej maszynom wirtualnym dostęp do bazy danych

Dodaj publiczny adres IP maszyny wirtualnej do zapory serwera bazy danych SQL.

Poniższe kroki tworzą regułę zapory IP na poziomie serwera dla publicznego adresu IP maszyny wirtualnej i umożliwia łączność z maszyną wirtualną.

1. Wybierz **bazy danych SQL** z menu po lewej stronie, a następnie wybierz bazę danych na stronie bazy danych **SQL.** Zostanie otwarta strona przeglądu bazy danych z w pełni kwalifikowaną nazwą serwera (na przykład **servername.database.windows.net)** i zawiera opcje dalszej konfiguracji.

2. Skopiuj tę w pełni kwalifikowaną nazwę serwera do użycia podczas łączenia się z serwerem i jego bazami danych.

   ![nazwa serwera](./media/sql-database-get-started-portal/server-name.png)

3. Wybierz pozycję **Ustaw zaporę serwera** na pasku narzędzi. Zostanie otwarta strona **Ustawienia zapory** dla serwera bazy danych.

   ![reguła zapory bazująca na adresach IP na poziomie serwera](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Wybierz **pozycję Dodaj adres IP klienta** na pasku narzędzi, aby dodać publiczny adres IP maszyny wirtualnej do nowej reguły zapory IP na poziomie serwera. Reguła zapory bazująca na adresach IP na poziomie serwera może otworzyć port 1433 dla pojedynczego adresu IP lub zakresu adresów IP.

5. Wybierz **pozycję Zapisz**. Reguła zapory IP na poziomie serwera jest tworzona dla publicznego portu otwierania adresu IP maszyny wirtualnej 1433 na serwerze bazy danych SQL.

6. Zamknij stronę **Ustawienia zapory**.



## <a name="export-a-database-using-sqlpackage"></a>Eksportowanie bazy danych przy użyciu pakietu SqlPackage

Aby wyeksportować bazę danych SQL za pomocą narzędzia wiersza polecenia [SqlPackage,](https://docs.microsoft.com/sql/tools/sqlpackage) zobacz [Eksportowanie parametrów i właściwości](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Narzędzie SqlPackage jest dostarczane z najnowszymi wersjami [programu SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i SQL Server Data [Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)lub można pobrać najnowszą wersję [programu SqlPackage.](https://docs.microsoft.com/sql/tools/sqlpackage-download)

Zaleca się użycie narzędzia SqlPackage do skalowania i wydajności w większości środowisk produkcyjnych. Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).

W tym przykładzie pokazano, jak wyeksportować bazę danych przy użyciu programu SqlPackage.exe z uwierzytelnianiem uniwersalnym usługi Active Directory. Zamień na wartości, które są specyficzne dla danego środowiska.

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=<servername>.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```




## <a name="import-a-database-using-sqlpackage"></a>Importowanie bazy danych przy użyciu pakietu SqlPackage

Aby zaimportować bazę danych programu SQL Server przy użyciu narzędzia wiersza polecenia [SqlPackage,](https://docs.microsoft.com/sql/tools/sqlpackage) zobacz [parametry i właściwości importu](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage posiada najnowsze [narzędzia SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) i SQL Server Data [Tools.](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) Możesz również pobrać najnowszą wersję [Programu SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage-download).

Aby skalować i wydajność, zaleca się przy użyciu SqlPackage w większości środowisk produkcyjnych, a nie przy użyciu witryny Azure portal. Aby zapoznać się z blogiem zespołu `BACPAC` doradczego klienta programu SQL Server na temat migracji przy użyciu plików, zobacz [migracja z programu SQL Server do usługi Azure SQL Database przy użyciu plików BACPAC](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Następujące polecenie SqlPackage importuje bazę danych **AdventureWorks2017** z magazynu lokalnego do serwera bazy danych SQL Database platformy Azure. Tworzy nową bazę danych o nazwie **myMigratedDatabase** z warstwą usługi **Premium** i celem usługi **P6.** Zmień te wartości odpowiednio dla danego środowiska.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=myMigratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2017.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Aby połączyć się z serwerem bazy danych SQL, który zarządza pojedynczą bazą danych zza zapory firmowej, zapora musi być otwarta. Aby połączyć się z wystąpieniem zarządzanym, musisz mieć [połączenie typu "punkt-lokacja"](sql-database-managed-instance-configure-p2s.md) lub połączenie trasy ekspresowej.

W tym przykładzie pokazano, jak zaimportować bazę danych przy użyciu programu SqlPackage z powszechnym uwierzytelnianiem usługi Active Directory.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Szybkość eksportu jest różna ze względu na wiele czynników (na przykład kształt danych), więc nie można przewidzieć, jakiej prędkości należy się spodziewać. SqlPackage może zająć dużo czasu, szczególnie dla dużych baz danych.

Aby uzyskać najlepszą wydajność, możesz wypróbować następujące strategie:

1. Upewnij się, że żadne inne obciążenie nie jest uruchomione w bazie danych. Utwórz kopię przed eksportem może być najlepszym rozwiązaniem, aby upewnić się, że żadne inne obciążenia są uruchomione.
2. Zwiększ cel poziomu usług bazy danych (SLO), aby lepiej obsługiwać obciążenie eksportu (przede wszystkim odczyt we/wy). Jeśli baza danych jest obecnie GP_Gen5_4, być może warstwa Krytyczna dla firm pomoże w obciążeniu odczytu.
3. Upewnij się, że istnieją indeksy klastrowane szczególnie dla dużych tabel. 
4. Maszyny wirtualne (maszyny wirtualne) powinny znajdować się w tym samym regionie co baza danych, aby uniknąć ograniczeń sieciowych.
5. Maszyny wirtualne powinny mieć dysk SSD o odpowiednim rozmiarze do generowania artefaktów tymczasowych przed przekazaniem do magazynu obiektów blob.
6. Maszyny wirtualne powinny mieć odpowiednią konfigurację rdzenia i pamięci dla określonej bazy danych.

## <a name="store-the-imported-or-exported-bacpac-file"></a>Przechowywać zaimportowane lub wyeksportowane . Plik BACPAC

Tthe. Bacpac plik może być przechowywany w [azure obiektów blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)lub [usługi Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction). 

Aby uzyskać najlepszą wydajność, użyj usługi Azure Files. SqlPackage działa z systemem plików, dzięki czemu można uzyskać dostęp do usługi Azure Files bezpośrednio.

Aby zmniejszyć koszty, należy użyć obiektów blob platformy Azure, które kosztują mniej niż udział plików platformy Azure w wersji premium. Jednak będzie to wymagało skopiowania [pliku . BACPAC między obiektem](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) blob a lokalnym systemem plików przed operacją importu lub eksportu. W rezultacie proces ten potrwa dłużej.

Aby przesłać lub pobrać plik . BACPAC, zobacz [Transfer danych z pamięcią AzCopy i Blob](../storage/common/storage-use-azcopy-blobs.md)oraz Transfer danych za pomocą [AzCopy i przechowywania plików](../storage/common/storage-use-azcopy-files.md).

W zależności od środowiska może być konieczne [skonfigurowanie zapór usługi Azure Storage i sieci wirtualnych.](../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak połączyć się z zaimportowanymi bazami danych SQL i wysyłać [kwerendy, zobacz Szybki start: Baza danych SQL: Łączenie i wykonywanie zapytań o dane](sql-database-connect-query-ssms.md)programu SQL Server Management Studio za pomocą programu SQL Server Management Studio.
- Aby poczytać o migracji za pomocą plików BACPAC na blogu SQL Server Customer Advisory Team, zobacz [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407) (Migrowanie z programu SQL Server do usługi Azure SQL Database za pomocą plików BACPAC).
- Aby zapoznać się z omówienia całego procesu migracji bazy danych programu SQL Server, w tym zaleceniami dotyczącymi wydajności, zobacz [Migracja bazy danych programu SQL Server do bazy danych SQL Sql Database](sql-database-single-database-migrate.md).
- Aby dowiedzieć się, jak bezpiecznie zarządzać kluczami magazynu i sygnaturami dostępu współdzielonego oraz udostępniać je, zobacz [Przewodnik po zabezpieczeniach usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
