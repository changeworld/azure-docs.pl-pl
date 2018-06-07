---
title: Wdrażanie usługi scalania podziału | Dokumentacja firmy Microsoft
description: Użyj scalania podziału zbyt do przenoszenia danych między bazami danych podzielonej.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 51a5f70cc56b2a4196ee7b151be0af3a9e16fc4f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646936"
---
# <a name="deploy-a-split-merge-service"></a>Wdrażanie usługi z podziałem i scalaniem
Narzędzia do scalania podziału umożliwia przenoszenie danych między podzielonej baz danych. Zobacz [przenoszenia danych między bazami danych w chmurze skalowalnych w poziomie](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Pobieranie pakietów scalania podziału
1. Pobierz najnowszą wersję NuGet z [NuGet](http://docs.nuget.org/docs/start-here/installing-nuget).
2. Otwórz wiersz polecenia i przejdź do katalogu, którego pobrano nuget.exe. Pobieranie zawiera polecenia programu PowerShell.
3. Pobierz najnowszy pakiet podziału scalania w bieżącym katalogu z poniższych poleceń:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Pliki są umieszczane w katalogu o nazwie **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** gdzie *x.x.xxx.x* odzwierciedla numer wersji. Znajdź pliki usługi podziału scalania w **content\splitmerge\service** podkatalogu oraz skrypty programu PowerShell podziału scalania (i bibliotek DLL klienta wymagane) w **content\splitmerge\powershell** podkatalogu.

## <a name="prerequisites"></a>Wymagania wstępne
1. Utwórz bazę danych bazy danych SQL Azure, która będzie służyć jako stan scalania podziału bazy danych. Przejdź do witryny [Azure Portal](https://portal.azure.com). Utwórz nową **bazy danych SQL**. Nadaj nazwę bazy danych i utworzenie nowego administratora i hasła. Pamiętaj zapisać nazwę i hasło do późniejszego użycia.
2. Upewnij się, że serwer bazy danych SQL Azure umożliwia usługom Azure się z nią połączyć. W portalu w **ustawienia zapory**, upewnij się, **zezwolić na dostęp do usług Azure** mają ustawioną wartość **na**. Kliknij ikonę "Zapisz".
   
   ![Dozwolone usług][1]
3. Tworzenie konta usługi Azure Storage, który będzie używany dla danych wyjściowych diagnostyki. Przejdź do witryny Azure Portal. Na pasku po lewej stronie kliknij **Utwórz zasób**, kliknij przycisk **dane i magazyn**, następnie **magazynu**.
4. Tworzenie usługi w chmurze Azure zawierających usługi podziału scalania.  Przejdź do witryny Azure Portal. Na pasku po lewej stronie kliknij **Utwórz zasób**, następnie **obliczeniowe**, **usługi w chmurze**, i **Utwórz**. 

## <a name="configure-your-split-merge-service"></a>Konfigurowanie usługi scalania podziału
### <a name="split-merge-service-configuration"></a>Konfiguracja usługi scalania podziału
1. W folderze, do którego pobrano zestawy podziału scalania, Utwórz kopię **ServiceConfiguration.Template.cscfg** plik obok **SplitMergeService.cspkg** i zmień jego nazwę **Pliku ServiceConfiguration.cscfg**.
2. Otwórz **pliku ServiceConfiguration.cscfg** w edytorze tekstu, takiego jak Visual Studio, która sprawdza poprawność danych wejściowych, takich jak format odciski palców certyfikatu.
3. Utwórz nową bazę danych lub wybierz istniejącą bazę danych jako stan bazy danych dla operacji scalania podziału i pobrać parametry połączenia bazy danych. 
   
   > [!IMPORTANT]
   > W tej chwili stan bazy danych musi używać alfabetu łacińskiego sortowania (SQL\_Latin1\_ogólne\_CP1\_CI\_AS). Aby uzyskać więcej informacji, zobacz [Nazwa sortowania systemu Windows (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Z bazy danych SQL Azure ciąg połączenia jest zwykle w postaci:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Wprowadź parametry połączenia w pliku cscfg w obu **SplitMergeWeb** i **SplitMergeWorker** sekcje roli w ustawieniu ElasticScaleMetadata.
5. Dla **SplitMergeWorker** roli, wprowadź prawidłowy ciąg połączenia z magazynem platformy Azure dla **WorkerRoleSynchronizationStorageAccountConnectionString** ustawienie.

### <a name="configure-security"></a>Konfigurowanie zabezpieczeń
Szczegółowe instrukcje dotyczące konfigurowania zabezpieczeń usługi, zapoznaj się [konfiguracji zabezpieczeń scalania podziału](sql-database-elastic-scale-split-merge-security-configuration.md).

Do celów wdrożenia simple testu, w tym samouczku minimalny zestaw kroków konfiguracji będzie wykonywane do uruchomienia usługi i uruchomione. Kroki te włączają tylko jeden/konto komputera wykonywanie ich do komunikowania się z usługą.

### <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym
Utwórz nowy katalog i z tego katalogu wykonywanie następujących za pomocą polecenia [wiersz polecenia dla programu Visual Studio deweloperów](http://msdn.microsoft.com/library/ms229859.aspx) okno:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha1 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Zostanie wyświetlona prośba o podanie hasła do ochrony klucza prywatnego. Wpisz silne hasło i potwierdź je. Następnie zostanie wyświetlony monit o hasło do użycia po raz. Kliknij przycisk **tak** na końcu zaimportować go do magazynu zaufanych głównych urzędów certyfikacji.

### <a name="create-a-pfx-file"></a>Utwórz plik PFX
Uruchom następujące polecenie z tym samym oknie, w którym wykonano makecert; Użyj tego samego hasła, który został użyty do utworzenia certyfikatu:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Zaimportuj certyfikat klienta do magazynu osobistego
1. W Eksploratorze Windows, kliknij dwukrotnie **MyCert.pfx**.
2. W **Kreatora importu certyfikatów** wybierz **bieżącego użytkownika** i kliknij przycisk **dalej**.
3. Sprawdź ścieżkę pliku i kliknij przycisk **dalej**.
4. Wpisz hasło, pozostaw **obejmują wszystkie rozszerzone właściwości** zaznaczony, a następnie kliknij przycisk **dalej**.
5. Pozostaw **automatycznie wybierz magazyn certyfikatów [...]**  zaznaczony, a następnie kliknij przycisk **dalej**.
6. Kliknij przycisk **Zakończ** i **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Przekaż plik PFX do usługi w chmurze
1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **usługi w chmurze**.
3. Wybierz usługę w chmurze, utworzoną wcześniej dla usługi podziału/Merge.
4. Kliknij przycisk **certyfikaty** w górnym menu.
5. Kliknij przycisk **przekazać** w dolnym pasku.
6. Wybierz plik PFX i wprowadź to samo hasło jak powyżej.
7. Po zakończeniu skopiuj odcisk palca certyfikatu z nowy wpis na liście.

### <a name="update-the-service-configuration-file"></a>Aktualizuj plik konfiguracji usługi
Wklej skopiowane wcześniej do atrybutu odcisk palca i wartości tych ustawień odcisk palca certyfikatu.
Dla roli procesu roboczego:
   ```
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Dla roli sieci web:

   ```
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Należy pamiętać, w środowisku produkcyjnym wdrożeń oddzielne certyfikaty powinny można używać dla urzędu certyfikacji, szyfrowania, certyfikat serwera i certyfikaty klientów. Aby uzyskać szczegółowe instrukcje na to, zobacz [konfiguracji zabezpieczeń](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Wdrażanie usługi
1. Przejdź do witryny [Azure Portal](https://manage.windowsazure.com).
2. Kliknij przycisk **usługi w chmurze** po lewej stronie, a następnie wybierz usługę w chmurze, który został utworzony wcześniej.
3. Kliknij przycisk **pulpitu nawigacyjnego**.
4. Wybierz środowisko przejściowe, a następnie kliknij przycisk **przekazania nowego wdrożenia przemieszczania**.
   
   ![Przemieszczanie][3]
5. W oknie dialogowym wprowadź etykietę wdrożenia. "Pakietu" i "Konfiguracja", kliknij przycisk "Z lokalnego" i wybierz polecenie **SplitMergeService.cspkg** plików i skonfigurowaną wcześniej pliku cscfg.
6. Upewnij się, że pole wyboru **Wdróż, nawet jeśli co najmniej jedna rola zawiera pojedyncze wystąpienie** jest zaznaczony.
7. Kliknij przycisk znaczników w prawym dolnym rogu, aby rozpocząć wdrażanie. Powinien zająć kilka minut.

   ![Upload][4]

## <a name="troubleshoot-the-deployment"></a>Rozwiązywanie problemów z wdrażaniem
Jeśli roli sieci web nie może przejść w tryb online, prawdopodobnie jest to problem z konfiguracją zabezpieczeń. Sprawdź, czy protokół SSL jest skonfigurowany zgodnie z powyższym opisem.

Jeśli swojej roli procesu roboczego nie przechodzi do trybu online, ale roli sieci web zakończy się powodzeniem, najprawdopodobniej jest to problem łączenia z bazą danych stanu, który został utworzony wcześniej.

* Upewnij się, że parametry połączenia w Twojej cscfg dokładne.
* Sprawdź, czy serwer i baza danych istnieje i czy identyfikator użytkownika i hasło są poprawne.
* Dla bazy danych SQL Azure ciąg połączenia powinny mieć postać:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Upewnij się, że nazwa serwera nie zaczyna się od **https://**.
* Upewnij się, że serwer bazy danych SQL Azure umożliwia usługom Azure się z nią połączyć. Aby to zrobić, otwórz https://manage.windowsazure.com, po lewej stronie kliknij pozycję "Baz danych SQL", kliknij przycisk "Serwery" u góry, a następnie wybierz serwer. Kliknij przycisk **Konfiguruj** u góry i upewnij się, że **usług Azure** ustawienie ma wartość "Yes". (Zobacz sekcja wymagania wstępne w górnej części tego artykułu).

## <a name="test-the-service-deployment"></a>Testowanie wdrożenia usługi
### <a name="connect-with-a-web-browser"></a>Połącz przy użyciu przeglądarki sieci web
Określ punkt końcowy sieci web usługi podziału scalania. Możesz znaleźć tego w klasycznym portalu Azure, przechodząc do **pulpitu nawigacyjnego** usługi w chmurze oraz pod **adres URL witryny** po prawej stronie. Zastąp **http://** z **https://** ponieważ domyślne ustawienia zabezpieczeń wyłączyć punkt końcowy HTTP. Załadowanie strony dla tego adresu URL do przeglądarki.

### <a name="test-with-powershell-scripts"></a>Testowanie za pomocą skryptów środowiska PowerShell
Uruchamiając skrypty programu PowerShell dołączonego przykładowego można przetestować wdrożenie i środowiska.

Uwzględnione pliki skryptów są:

1. **SetupSampleSplitMergeEnvironment.ps1** — konfiguruje warstwy danych testu dla podziału/Merge (zobacz tabelę poniżej, aby uzyskać szczegółowy opis)
2. **ExecuteSampleSplitMerge.ps1** — wykonuje operacje testów w teście warstwy danych (zobacz tabelę poniżej, aby uzyskać szczegółowy opis)
3. **GetMappings.ps1** — najwyższego poziomu przykładowy skrypt, który bieżący stan mapowania niezależnego fragmentu do drukowania.
4. **ShardManagement.psm1** -pomocnika skrypt, który opakowuje ShardManagement interfejsu API
5. **SqlDatabaseHelpers.psm1** -skryptu pomocnika do tworzenia i zarządzania bazy danych SQL
   
   <table style="width:100%">
     <tr>
       <th>Plik programu PowerShell</th>
       <th>Kroki</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    Tworzy bazę danych Menedżera niezależnego fragmentu mapy</td>
     </tr>
     <tr>
       <td>2.    Tworzy 2 niezależnego fragmentu bazy danych.
     </tr>
     <tr>
       <td>3.    Tworzy mapę niezależnych dla tych baz danych (usuwa wszystkie istniejące niezależnego fragmentu mapy na tych baz danych). </td>
     </tr>
     <tr>
       <td>4.    Tworzy tabelę małej przykładowej w obu odłamków i wypełnia tabelę w jeden z fragmentów.</td>
     </tr>
     <tr>
       <td>5.    Deklaruje SchemaInfo podzielony na niezależne fragmenty tabeli.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Plik programu PowerShell</th>
       <th>Kroki</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    Wysyła żądanie podziału do fronton sieci web usługi scalania podziału dzieli połowa danych z pierwszego niezależnego fragmentu, do drugiego niezależnego fragmentu.</td>
     </tr>
     <tr>
       <td>2.    Sonduje frontonu sieci web dla podziału stan żądania i czeka, aż do zakończenia żądania.</td>
     </tr>
     <tr>
       <td>3.    Wysyła żądanie scalania do fronton sieci web usługi scalania podziału przeniesienie danych z drugiego niezależnego fragmentu pierwszego niezależnego fragmentu.</td>
     </tr>
     <tr>
       <td>4.    Sonduje frontonu sieci web dla scalania stan żądania i czeka, aż do zakończenia żądania.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Sprawdź wdrożenie przy użyciu programu PowerShell
1. Otwórz nowe okno programu PowerShell i przejdź do katalogu, w której pobrano pakiet scalania podziału, a następnie przejdź do katalogu "powershell".
2. Tworzenie serwera bazy danych Azure SQL (lub wybierz istniejący serwer) której zostanie utworzona menedżera map niezależnego fragmentu i fragmentów.
   
   > [!NOTE]
   > Domyślnie do zachowania prostego skryptu skryptu SetupSampleSplitMergeEnvironment.ps1 tworzy tych baz danych na tym samym serwerze. To nie jest ograniczeniem usługi scalania podziału samej siebie.
   >
   
   Identyfikator logowania uwierzytelniania SQL z dostępem do odczytu i zapisu do baz danych będą potrzebne do przenoszenia danych i zaktualizuj mapy niezależnego fragmentu usługi podziału scalania. Ponieważ usługa podziału scalania działa w chmurze, go nie obsługuje obecnie zintegrowane uwierzytelnianie.
   
   Upewnij się, że serwer Azure SQL jest skonfigurowane i umożliwiają dostęp z adresu IP maszyny uruchamianie tych skryptów. To ustawienie w obszarze serwera Azure SQL można znaleźć / configuration / dozwolone adresy IP.
3. Uruchom skrypt SetupSampleSplitMergeEnvironment.ps1 Aby utworzyć środowisko próbki.
   
   Uruchomienie tego skryptu zostanie wymazać wszystkie istniejące dane zarządzania mapy niezależnego fragmentu struktury bazy danych Menedżera mapy niezależnego fragmentu i fragmentów. Może to być przydatne ponownie uruchomić skrypt, aby ponownie zainicjować mapy niezależnego fragmentu lub odłamków.
   
   Przykładowy wiersz polecenia:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Wykonanie skryptu Getmappings.ps1, aby wyświetlić mapowania, które obecnie istnieją w środowisku próbki.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Uruchom skrypt ExecuteSampleSplitMerge.ps1 można wykonać operacji podziału (przejściem połowa danych pierwszego niezależnego fragmentu do drugiego niezależnego fragmentu), a następnie operacji scalania (przeniesienie danych powrotem na pierwszy niezależnego fragmentu). Jeśli został skonfigurowany SSL i pozostanie wyłączony punkt końcowy http, upewnij się, użyj punktu końcowego https://.
   
   Przykładowy wiersz polecenia:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Jeśli zostanie wyświetlony poniżej błąd, jest prawdopodobnie problem z certyfikatem punkt końcowy sieci Web. Spróbuj nawiązać połączenie z punktem końcowym sieci Web z ulubionej przeglądarce sieci Web i sprawdź, czy błąd certyfikatu.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Jeśli zakończyło się pomyślnie, dane wyjściowe powinny wyglądać jak poniżej:
   
   ```
   > .\ExecuteSampleSplitMerge.ps1 -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net' -SplitMergeServiceEndpoint 'http://mysplitmergeservice.cloudapp.net' -CertificateThumbprint 0123456789abcdef0123456789abcdef01234567
   > Sending split request
   > Began split operation with id dc68dfa0-e22b-4823-886a-9bdc903c80f3
   > Polling split-merge request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Waiting for reference tables copy     completion.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > Sending merge request
   > Began merge operation with id 6ffc308f-d006-466b-b24e-857242ec5f66
   > Polling request status. Press Ctrl-C to end
   > Progress: 0% | Status: Queued | Details: [Informational] Queued request
   > Progress: 5% | Status: Starting | Details: [Informational] Starting split-merge state machine for request.
   > Progress: 5% | Status: Starting | Details: [Informational] Performing data consistency checks on target     shards.
   > Progress: 20% | Status: CopyingReferenceTables | Details: [Informational] Moving reference tables from     source to target shard.
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Moving key range [100:110) of     Sharded tables
   > Progress: 44% | Status: CopyingShardedTables | Details: [Informational] Successfully copied key range     [100:110) for table [dbo].[MyShardedTable]
   > ...
   > ...
   > Progress: 90% | Status: Completing | Details: [Informational] Successfully deleted shardlets in table     [dbo].[MyShardedTable].
   > Progress: 90% | Status: Completing | Details: [Informational] Deleting any temp tables that were created     while processing the request.
   > Progress: 100% | Status: Succeeded | Details: [Informational] Successfully processed request.
   > 
   ```
6. Poeksperymentuj z innymi typami danych! Wszystkie te skrypty zająć opcjonalny parametr - ShardKeyType można określić typ klucza. Wartość domyślna to Int32, ale można również określić Int64, Guid lub dane binarne.

## <a name="create-requests"></a>Żądania utworzenia
Usługa może służyć za pomocą interfejsu użytkownika sieci web lub przez zaimportowanie i przy użyciu modułu SplitMerge.psm1 PowerShell, który będzie przesyłanie żądań za pośrednictwem roli sieci web.

Usługę można przenieść dane w tabelach podzielonej i tabele odwołań. Podzielony na niezależne fragmenty tabeli ma kolumny klucza dzielenia na fragmenty i ma inny wiersz danych na każdym niezależnego fragmentu. Odwołanie do tabeli nie jest podzielony na niezależne fragmenty, więc zawiera te same dane wiersza w każdym niezależnego fragmentu. Tabele odwołań są przydatne w przypadku danych, które nie zmieniają się często, jest używany do łączenia z tabelami podzielonej w zapytaniach.

W celu wykonywania operacji scalania podziału, należy zadeklarować podzielonej tabele i tabele odwołań, które mają być zostały przeniesione. Jest to realizowane przy użyciu **SchemaInfo** interfejsu API. Ten interfejs API jest w **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** przestrzeni nazw.

1. Dla każdej tabeli podzielonej, Utwórz **ShardedTableInfo** Obiekt opisujący nazwy schematu tabeli nadrzędnej (opcjonalne, wartość domyślna to "właściciela"), nazwę tabeli i nazwę kolumny w tabeli zawierającej klucz dzielenia na fragmenty.
2. Dla każdej tabeli odwołania Utwórz **ReferenceTableInfo** Obiekt opisujący nazwy schematu tabeli nadrzędnej (opcjonalne, wartość domyślna to "właściciela") i nazwy tabeli.
3. Dodaj powyżej obiektów TableInfo na nowy **SchemaInfo** obiektu.
4. Pobierz odwołanie do **ShardMapManager** obiekt i wywołanie **GetSchemaInfoCollection**.
5. Dodaj **SchemaInfo** do **SchemaInfoCollection**, podanie nazwy mapy niezależnego fragmentu.

Na przykład może być widoczny w skrypcie SetupSampleSplitMergeEnvironment.ps1.

Usługa podziału scalania nie tworzy docelowa baza danych (lub schemat dla wszystkich tabel w bazie danych). Musi być wstępnie utworzone przed wysłaniem żądania do usługi.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Może zostać wyświetlony poniżej wiadomości podczas uruchamiania przykładowe skrypty programu powershell:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Ten błąd oznacza, że certyfikat SSL nie jest poprawnie skonfigurowany. Postępuj zgodnie z instrukcjami w sekcji "Łączenie z przeglądarką sieci web".

Jeśli nie można przesłać żądania może wystąpić błąd to:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

W takim przypadku sprawdź plik konfiguracji, w szczególności ustawienie **WorkerRoleSynchronizationStorageAccountConnectionString**. Ten błąd zazwyczaj oznacza, że rola proces roboczy nie może pomyślnie zainicjować bazy danych metadanych przy pierwszym użyciu. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

