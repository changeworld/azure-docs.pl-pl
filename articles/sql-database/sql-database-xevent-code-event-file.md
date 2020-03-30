---
title: Kod pliku zdarzenia XEvent
description: Udostępnia programach PowerShell i Transact-SQL dla przykładowego kodu dwufazowego, który pokazuje miejsce docelowe pliku zdarzeń w rozszerzonym zdarzeniu w bazie danych SQL azure. Usługa Azure Storage jest wymaganą częścią tego scenariusza.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 03/12/2019
ms.openlocfilehash: a9bf28fb1b3c5278d25b417fc646d2ad3d6f1abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213993"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Kod docelowy pliku zdarzeń dla zdarzeń rozszerzonych w bazie danych SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Chcesz kompletny przykład kodu dla niezawodnego sposobu przechwytywania i raportowania informacji dla zdarzenia rozszerzonego.

W programie Microsoft SQL Server [obiekt docelowy pliku zdarzeń](https://msdn.microsoft.com/library/ff878115.aspx) służy do przechowywania wyjść zdarzeń w pliku lokalnego dysku twardego. Ale takie pliki nie są dostępne dla usługi Azure SQL Database. Zamiast tego używamy usługi Azure Storage do obsługi obiektu docelowego pliku zdarzeń.

W tym temacie przedstawiono przykładowy kod dwufazowy:

- Program PowerShell, aby utworzyć kontener usługi Azure Storage w chmurze.
- Transact-SQL:
  
  - Aby przypisać kontener usługi Azure Storage do obiektu docelowego pliku zdarzeń.
  - Aby utworzyć i rozpocząć sesję wydarzenia itd.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł usługi PowerShell Azure Resource Manager jest nadal obsługiwany przez usługę Azure SQL Database, ale wszystkie przyszłe prace rozwojowe są przeznaczone dla modułu Az.Sql. Aby uzyskać następujące polecenia cmdlet, zobacz [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty dla poleceń w module Az i w modułach AzureRm są zasadniczo identyczne.

- Konto i subskrypcja platformy Azure. Po zarejestrowaniu się możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
- Dowolna baza danych, w której można utworzyć tabelę.
  
  - Opcjonalnie można [utworzyć **adventureworkslt** demonstracji bazy danych](sql-database-get-started.md) w ciągu kilku minut.

- SQL Server Management Studio (ssms.exe), najlepiej jego najnowsza miesięczna wersja aktualizacji.
  Możesz pobrać najnowsze ssms.exe z:
  
  - Temat zatytułowany [Pobierz program SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  - [Bezpośredni link do pobrania.](https://go.microsoft.com/fwlink/?linkid=616025)

- Musisz mieć zainstalowane [moduły programu Azure PowerShell.](https://go.microsoft.com/?linkid=9811175)

  - Moduły zapewniają polecenia, takie jak - **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Faza 1: Kod programu PowerShell dla kontenera usługi Azure Storage

Ten program PowerShell jest faza 1 przykładu kodu dwufazowego.

Skrypt rozpoczyna się od poleceń, aby oczyścić po ewentualnym poprzednim uruchomieniu i jest można go ponownie uruchomić.

1. Wklej skrypt programu PowerShell do prostego edytora tekstu, takiego jak Notepad.exe, i zapisz skrypt jako plik z rozszerzeniem **.ps1**.
2. Uruchom program PowerShell ISE jako administrator.
3. W wierszu polecenia wpisz<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>a następnie naciśnij klawisz Enter.
4. W programie PowerShell ISE otwórz plik **.ps1.** Uruchom skrypt.
5. Skrypt najpierw uruchamia nowe okno, w którym można zalogować się do platformy Azure.

   - Jeśli ponownie uruchomić skrypt bez zakłócania sesji, masz wygodną opcję komentowania **polecenia Add-AzureAccount.**

![Program PowerShell ISE z zainstalowanym modułem platformy Azure, gotowy do uruchomienia skryptu.][30_powershell_ise]

### <a name="powershell-code"></a>Kod programu PowerShell

Ten skrypt programu PowerShell zakłada, że masz już zainstalowany moduł Az. Aby uzyskać więcej informacji, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-Az-ps).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

if ($storageAccountName) {
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
try {
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
catch {
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```

Należy wziąć pod uwagę kilka nazwanych wartości, które skrypt programu PowerShell drukuje po jego zakończeniu. Należy edytować te wartości w skrypcie Transact-SQL, który jest następujący jako faza 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Faza 2: Kod Transact-SQL, który używa kontenera usługi Azure Storage

- W fazie 1 tego przykładu kodu uruchomiono skrypt programu PowerShell w celu utworzenia kontenera usługi Azure Storage.
- Następnie w fazie 2 następujący skrypt Transact-SQL musi używać kontenera.

Skrypt rozpoczyna się od poleceń, aby oczyścić po ewentualnym poprzednim uruchomieniu i jest można go ponownie uruchomić.

Skrypt programu PowerShell wydrukował kilka nazwanych wartości po jego zakończeniu. Aby użyć tych wartości, należy edytować skrypt Transact-SQL. Znajdź **TODO** w skrypcie Transact-SQL, aby zlokalizować punkty edycji.

1. Otwórz program SQL Server Management Studio (ssms.exe).
2. Połącz się z bazą danych usługi Azure SQL Database.
3. Kliknij, aby otworzyć nowe okienko kwerendy.
4. Wklej następujący skrypt Transact-SQL do okienka kwerendy.
5. Znajdź każdy **TODO** w skrypcie i dokonać odpowiednich zmian.
6. Zapisz, a następnie uruchom skrypt.

> [!WARNING]
> Wartość klucza sygnatury dostępu Współdzielonego wygenerowana przez poprzedni skrypt programu PowerShell może zaczynać się od '? (znak zapytania). Korzystając z klucza Sygnatury dostępu Współdzielonego w następującym skrypcie T-SQL, należy *usunąć wiodący '?'*. W przeciwnym razie twoje wysiłki mogą zostać zablokowane przez zabezpieczenia.

### <a name="transact-sql-code"></a>Kod Transact-SQL

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.

SET NOCOUNT ON;
GO

----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.

IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO

CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO

INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).

IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO

IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO

CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO

------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO

CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO

------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO

-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO

-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```

Jeśli nie można dołączyć obiektu docelowego po uruchomieniu, należy zatrzymać i ponownie uruchomić sesję zdarzenia:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>Dane wyjściowe

Po zakończeniu skryptu Transact-SQL kliknij komórkę pod nagłówkiem kolumny **event_data_XML.** Wyświetlany jest jeden ** \<element>zdarzenia,** który pokazuje jedną instrukcję UPDATE.

Oto jedno ** \<zdarzenie>** element, który został wygenerowany podczas testowania:

```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

Poprzedni skrypt Transact-SQL używał następującej funkcji systemowej do odczytu event_file:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](https://msdn.microsoft.com/library/cc280743.aspx)

Wyjaśnienie zaawansowanych opcji wyświetlania danych z rozszerzonych zdarzeń jest dostępne pod adresem:

- [Zaawansowane wyświetlanie danych docelowych z zdarzeń rozszerzonych](https://msdn.microsoft.com/library/mt752502.aspx)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Konwertowanie przykładu kodu do uruchomienia na programie SQL Server

Załóżmy, że chcesz uruchomić poprzedni przykład Transact-SQL na microsoft SQL Server.

- Dla uproszczenia należy całkowicie zastąpić użycie kontenera usługi Azure Storage prostym plikiem, takim jak *C:\myeventdata.xel.* Plik zostanie zapisany na lokalnym dysku twardym komputera, na którym znajduje się program SQL Server.
- Nie potrzebujesz żadnych instrukcji Transact-SQL dla **CREATE MASTER KEY** i CREATE **CREDENTIAL**.
- W instrukcji **CREATE EVENT SESSION** w klauzuli DODAJ WARTOŚĆ **DOCELOWĄ** należy zastąpić przypisaną do **nazwy pliku** http ciągiem pełnej ścieżki, taką jak *C:\myfile.xel*.
  
  - Nie trzeba angażować konta usługi Azure Storage.

## <a name="more-information"></a>Więcej informacji

Aby uzyskać więcej informacji na temat kont i kontenerów w usłudze Azure Storage, zobacz:

- [Jak korzystać z magazynu obiektów Blob z platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [Nazywanie i odwoływanie się do kontenerów, obiektów blob i metadanych](https://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Praca z kontenerem głównym](https://msdn.microsoft.com/library/azure/ee395424.aspx)
- [Lekcja 1: Tworzenie przechowywanych zasad dostępu i podpisu dostępu współdzielonego w kontenerze platformy Azure](https://msdn.microsoft.com/library/dn466430.aspx)
  - [Lekcja 2: Tworzenie poświadczeń programu SQL Server przy użyciu podpisu dostępu współdzielonego](https://msdn.microsoft.com/library/dn466435.aspx)
- [Zdarzenia rozszerzone dla programu Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!-- Image references. -->
[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png
