---
title: Kod systemu XEvent zdarzeń pliku bazy danych SQL | Dokumentacja firmy Microsoft
description: Przykład dwufazowego kod, który demonstruje obiekt docelowy plik zdarzeń rozszerzonych zdarzeń w bazie danych SQL Azure zapewnia środowiska PowerShell i języka Transact-SQL. Usługa Azure Storage jest wymagany częścią tego scenariusza.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: c8a8561c6241968419c5014bf2a37cca05723e81
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Kod docelowego pliku zdarzenia rozszerzonego zdarzeń w bazie danych SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Ma przykład kompletny kod w niezawodny sposób przechwytywania i przekazuje informacje dla zdarzeń rozszerzonych.

Program Microsoft SQL Server [docelowy plik zdarzeń](http://msdn.microsoft.com/library/ff878115.aspx) służy do przechowywania danych wyjściowych zdarzeń do pliku lokalnego dysku twardego. Jednak takie pliki nie są dostępne dla bazy danych SQL Azure. Zamiast tego używamy usługi Magazyn Azure do obsługi zdarzeń pliku docelowego.

W tym temacie przedstawiono przykład dwufazowego kodu:

* PowerShell można utworzyć kontenera magazynu Azure w chmurze.
* Transact-SQL:
  
  * Aby przypisać kontenera magazynu Azure do obiektu docelowego pliku zdarzenia.
  * Aby utworzyć i uruchomić sesji zdarzenia i tak dalej.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Po zarejestrowaniu się możesz skorzystać z [bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Wszystkie bazy danych, które można utworzyć tabeli.
  
  * Opcjonalnie można [utworzyć **AdventureWorksLT** demonstracyjna baza danych](sql-database-get-started.md) w minutach.
* SQL Server Management Studio (ssms.exe), najlepiej najnowszej miesięcznej wersji aktualizacji. 
  Możesz pobrać najnowszą ssms.exe od:
  
  * Temat zatytułowany [pobierania programu SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
  * [Łącze do pobrania.](http://go.microsoft.com/fwlink/?linkid=616025)
* Musi mieć [modułów programu Azure PowerShell](http://go.microsoft.com/?linkid=9811175) zainstalowane.
  
  * Moduły udostępniają polecenia, takich jak - **AzureStorageAccount nowy**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Faza 1: Kod programu PowerShell dla usługi Azure Storage kontenera

Tego programu PowerShell jest faza 1 przykład dwufazowego kodu.

Skrypt rozpoczyna się od poleceń, aby wyczyścić po poprzednim potencjalnie Uruchom i jest rerunnable.

1. Wkleić do edytora tekstu prosty przykład Notepad.exe skrypt programu PowerShell i Zapisz skrypt jako plik z rozszerzeniem **ps1**.
2. Uruchom ISE programu PowerShell jako Administrator.
3. W wierszu polecenia wpisz<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>a następnie naciśnij klawisz Enter.
4. Otwórz w programie PowerShell ISE z **ps1** pliku. Uruchom skrypt.
5. Skrypt uruchamia najpierw nowe okno, w którym możesz zalogować się w usłudze Azure.
   
   * Jeśli uruchomisz skryptu bez przerywania sesji ma wygodny opcja komentowania limit **Add-AzureAccount** polecenia.

![PowerShell ISE z modułem Azure zainstalowanych, gotowy do uruchomienia skryptu.][30_powershell_ise]

### <a name="powershell-code"></a>Kod programu PowerShell

Ten skrypt programu PowerShell przyjęto założenie, że polecenia cmdlet Import-Module modułu AzureRm zostało już uruchomione. Dokumentację referencyjną dla [przeglądarki modułu programu PowerShell](https://docs.microsoft.com/powershell/module/).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzureRmAccount (or  Connect-AzureRmAccount), just one time.';
#Connect-AzureRmAccount;   # Same as  Connect-AzureRmAccount.

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

$storageAccountLocation = 'West US';
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

Select-AzureRmSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

If ($storageAccountName)
{
    Remove-AzureRmStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzureRmStorageAccount `
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
    (Get-AzureRmStorageAccountKey `
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

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
Catch 
{
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
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzureRmStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```


Zwróć uwagę na kilka nazwanych wartości, które skrypt programu PowerShell wyświetla po jej zakończeniu. Należy edytować tych wartości do skryptu języka Transact-SQL, znajdujący się w fazie 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Faza 2: Kod języka Transact-SQL, który używa kontenera magazynu Azure

* W tym przykładowym kodzie faza 1 uruchomienia skryptu programu PowerShell, można utworzyć kontenera magazynu Azure.
* Następnie w Faza 2 poniższy skrypt języka Transact-SQL należy użyć kontenera.

Skrypt rozpoczyna się od poleceń, aby wyczyścić po poprzednim potencjalnie Uruchom i jest rerunnable.

Skrypt programu PowerShell podane kilka nazwanych wartości, czas zakończenia. Należy edytować skryptu języka Transact-SQL, aby użyć tych wartości. Znajdź **TODO** skryptu języka Transact-SQL Aby zlokalizować Edycja punktów.

1. Otwórz program SQL Server Management Studio (ssms.exe).
2. Połączenia z bazą danych bazy danych SQL Azure.
3. Kliknij, aby otworzyć nowe okienko zapytania.
4. Wklej poniższy skrypt języka Transact-SQL w okienku zapytania.
5. Znajdź co **TODO** w skrypt i wprowadź odpowiednie zmiany.
6. Zapisz, a następnie uruchom skrypt.


> [!WARNING]
> Wartość klucza sygnatury dostępu Współdzielonego generowane przez powyższy skrypt programu PowerShell może rozpoczynać się od "?" (znak zapytania). Użycie klucza sygnatury dostępu Współdzielonego w poniższym skrypcie T-SQL, należy najpierw *Usuń znaku "?"* . W przeciwnym razie wysiłków mogą być blokowane przez zabezpieczenia.


### <a name="transact-sql-code"></a>Kod języka Transact-SQL

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

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


Jeśli element docelowy nie może dołączyć podczas uruchamiania, należy zatrzymać i ponownie uruchomić sesji zdarzeń:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Dane wyjściowe

Po ukończeniu działania skryptu języka Transact-SQL, kliknij komórkę w obszarze **event_data_XML** nagłówka kolumny. Jeden **<event>** element jest wyświetlany, która zawiera jedną instrukcję aktualizacji.

Oto jeden **<event>** element, który został wygenerowany podczas testowania:


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


Powyższy skrypt języka Transact-SQL użyć następujących funkcji systemu w celu odczytania event_file:

* [sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)

Wyjaśnienie zaawansowane opcje wyświetlania danych ze zdarzeń rozszerzonych jest dostępne pod adresem:

* [Zaawansowane wyświetlanie danych docelowych od zdarzeń rozszerzonych](http://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Konwertowanie przykładowy kod do uruchomienia na serwerze SQL

Załóżmy, że ma być uruchomiony powyższego przykładu języka Transact-SQL w programie Microsoft SQL Server.

* Dla uproszczenia, czy chcesz całkowicie zastąpić użycie kontenera magazynu Azure prostego pliku takich jak **C:\myeventdata.xel**. Plik będzie zapisany na lokalnym dysku twardym komputera, który jest hostem serwera SQL.
* Nie musisz dowolnego rodzaju instrukcji języka Transact-SQL dla **CREATE MASTER KEY** i **poświadczenie tworzenia**.
* W **tworzenie zdarzeń sesji** instrukcji w jego **dodać DOCELOWY** klauzuli, należy zastąpić wartość Http przypisane do **filename =** z ciągiem pełnej ścieżki, takie jak **C:\myfile.xel**.
  
  * Muszą być wykonywane żadne konto magazynu Azure.

## <a name="more-information"></a>Więcej informacji

Aby uzyskać więcej informacji na temat kont i kontenerów w usłudze Azure Storage zobacz:

* [Jak używać magazynu obiektów Blob w .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Nazewnictwo i odwołania do kontenerów, obiektów blob i metadanych](http://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Praca z kontenera Root](http://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Lekcja 1: Utworzenie zasad dostępu przechowywane i sygnatury dostępu współdzielonego kontenera platformy Azure](http://msdn.microsoft.com/library/dn466430.aspx)
  * [Lekcja 2: Tworzenie poświadczeń programu SQL Server przy użyciu sygnatury dostępu współdzielonego](http://msdn.microsoft.com/library/dn466435.aspx)
* [Rozszerzone zdarzenia dla programu Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

