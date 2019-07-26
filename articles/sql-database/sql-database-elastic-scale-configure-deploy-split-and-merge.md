---
title: Wdrażanie usługi dzielenia i scalania | Dokumentacja firmy Microsoft
description: Użyj dzielenia i scalania zbyt do przenoszenia danych między bazami danych podzielonych na fragmenty.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 5aff7e93dcfaa5320be0d6f7d427abcdc88c69e4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60585512"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Wdrażanie usługi dzielenia i scalania do przenoszenia danych między bazami danych podzielonych na fragmenty

Narzędzie do dzielenia i scalania umożliwia przenoszenie danych między bazami danych podzielonych na fragmenty. Zobacz [przenoszenie danych między bazami danych w chmurze skalowanych w poziomie](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Pobierz pakiety dzielenia i scalania
1. Pobierz najnowszą wersję NuGet [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).
2. Otwórz wiersz polecenia i przejdź do katalogu, do której pobrano nuget.exe. Pliki do pobrania zawiera polecenia programu PowerShell.
3. Pobierz najnowszy pakiet dzielenia i scalania do bieżącego katalogu przy użyciu poniższego polecenia:
   ```
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Pliki są umieszczane w katalogu o nazwie **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x** gdzie *x.x.xxx.x* odzwierciedla numer wersji. Znajdź pliki usługi dzielenia i scalania w **content\splitmerge\service** podkatalogu oraz skrypty programu PowerShell dzielenia i scalania (i bibliotek DLL wymagany klient) w **content\splitmerge\powershell** podkatalogu.

## <a name="prerequisites"></a>Wymagania wstępne
1. Utwórz bazę danych bazy danych SQL Azure, która będzie służyć jako baza danych stanie dzielenia i scalania. Przejdź do witryny [Azure Portal](https://portal.azure.com). Utwórz nową **bazy danych SQL**. Nadaj nazwę bazy danych i utworzenie nowego administratora i hasła. Pamiętaj zarejestrować nazwę i hasło do późniejszego użycia.
2. Upewnij się, że serwer bazy danych SQL Azure pozwala nawiązać z nim usług systemu Azure. W portalu w **ustawienia zapory**, upewnij się, **zezwolić na dostęp do usług platformy Azure** jest ustawiana **na**. Kliknij ikonę "Zapisz".
3. Utwórz konto usługi Azure Storage dla danych wyjściowych diagnostyki.
4. Tworzenie usługi w chmurze Azure dla usługi dzielenia i scalania.

## <a name="configure-your-split-merge-service"></a>Konfigurowanie usługi dzielenia i scalania
### <a name="split-merge-service-configuration"></a>Konfiguracja usługi dzielenia i scalania
1. W folderze, do którego pobrano zestawy dzielenia i scalania, należy utworzyć kopię **ServiceConfiguration.Template.cscfg** pliku, który jest dostarczany wraz z **SplitMergeService.cspkg** i zmień jego nazwę **ServiceConfiguration.cscfg**.
2. Otwórz **ServiceConfiguration.cscfg** w edytorze tekstów, takiego jak Visual Studio, która weryfikuje dane wejściowe, takie jak format odciski palców certyfikatu.
3. Utwórz nową bazę danych lub wybierz istniejącą bazę danych, aby służyć jako stan bazy danych dla operacji dzielenia i scalania i pobrać parametry połączenia bazy danych. 
   
   > [!IMPORTANT]
   > W tej chwili stan bazy danych musi używać alfabetu łacińskiego sortowania (SQL\_Latin1\_ogólne\_CP1\_CI\_AS). Aby uzyskać więcej informacji, zobacz [Nazwa sortowania Windows (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).
   >

   Za pomocą usługi Azure SQL DB parametry połączenia zazwyczaj mają postać:
      ```
      Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
      ```

4. Wprowadź parametry połączenia w pliku cscfg w obu **SplitMergeWeb** i **SplitMergeWorker** sekcje roli w ustawieniach ElasticScaleMetadata.
5. Aby uzyskać **SplitMergeWorker** roli, wprowadź prawidłowe parametry połączenia do usługi Azure storage dla **WorkerRoleSynchronizationStorageAccountConnectionString** ustawienie.

### <a name="configure-security"></a>Konfigurowanie zabezpieczeń
Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania zabezpieczeń usługi, dotyczą [konfiguracji zabezpieczeń dzielenia i scalania](sql-database-elastic-scale-split-merge-security-configuration.md).

Do celów wdrożenia prosty test, na potrzeby tego samouczka to minimalny zestaw kroków konfiguracji będzie wykonywane do uruchomienia usługi i uruchomione. Kroki te włączają tylko jeden/konto komputera wykonywanie ich do komunikowania się z usługą.

### <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym
Utwórz nowy katalog i z tego katalogu, wykonaj następujące polecenie, używając [wiersz polecenia programisty dla programu Visual Studio](https://msdn.microsoft.com/library/ms229859.aspx) okna:

   ```
    makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Zostanie wyświetlona prośba o podanie hasła do ochrony klucza prywatnego. Wpisz silne hasło i potwierdź je. Następnie zostanie wyświetlony monit o hasło, które ma być używany ponownie później. Kliknij przycisk **tak** na końcu zaimportować go do magazynu zaufanych głównych urzędów certyfikacji.

### <a name="create-a-pfx-file"></a>Utwórz plik PFX
Wykonaj następujące polecenie w tym samym oknie, w którym wykonano makecert; Użyj tego samego hasła, który został użyty do utworzenia certyfikatu:

    pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>

### <a name="import-the-client-certificate-into-the-personal-store"></a>Zaimportuj certyfikat klienta do osobistego magazynu
1. W Eksploratorze Windows, kliknij dwukrotnie **MyCert.pfx**.
2. W **Kreatora importu certyfikatów** wybierz **bieżącego użytkownika** i kliknij przycisk **dalej**.
3. Upewnij się, ścieżkę pliku, a następnie kliknij przycisk **dalej**.
4. Wpisz hasło, pozostaw **obejmują wszystkie rozszerzone właściwości** zaznaczone, a następnie kliknij przycisk **dalej**.
5. Pozostaw **automatycznie wybierz magazyn certyfikatów [...]**  zaznaczone, a następnie kliknij przycisk **dalej**.
6. Kliknij przycisk **Zakończ** i **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Przekaż plik PFX do usługi w chmurze
1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz **usług w chmurze**.
3. Wybierz usługę w chmurze, utworzoną wcześniej dla usługi dzielenia i scalania.
4. Kliknij przycisk **certyfikaty** w górnym menu.
5. Kliknij przycisk **przekazywanie** w dolnym pasku.
6. Wybierz plik PFX, a następnie wprowadź to samo hasło jak powyżej.
7. Po zakończeniu skopiuj odcisk palca certyfikatu z nowy wpis na liście.

### <a name="update-the-service-configuration-file"></a>Zaktualizuj plik konfiguracji usługi
Wklej odcisk palca certyfikatu, skopiowane powyżej do atrybutu odcisk palca i wartości z tych ustawień.
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

Należy pamiętać, w środowisku produkcyjnym wdrożenia oddzielnych certyfikatów, należy można używać w dla urzędu certyfikacji do szyfrowania, certyfikat serwera i certyfikatów klientów. Aby uzyskać szczegółowe instrukcje w tym temacie [konfiguracji zabezpieczeń](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Wdrażanie usługi
1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz usługę w chmurze, która została utworzona wcześniej.
3. Kliknij pozycję **Przegląd**.
4. Wybierz środowisko przejściowe, a następnie kliknij przycisk **przekazywanie**.
5. W oknie dialogowym wprowadź etykietę wdrożenia. "Package" i "Konfiguracja", kliknij przycisk "Z Local", a następnie wybierz **SplitMergeService.cspkg** plików i pliku cscfg, które zostały wcześniej skonfigurowane.
6. Upewnij się, że pola wyboru **Wdróż, nawet jeśli co najmniej jedna rola zawiera pojedyncze wystąpienie** jest zaznaczone.
7. Naciśnij przycisk znaczników w prawym dolnym rogu, aby rozpocząć wdrażanie. Powinien zająć kilka minut.


## <a name="troubleshoot-the-deployment"></a>Rozwiązywanie problemów z wdrożenia
Jeśli roli sieci web nie przejdzie w tryb online, jest prawdopodobnie problem z konfiguracją zabezpieczeń. Sprawdź, czy skonfigurowano protokół SSL zgodnie z powyższym opisem.

Jeśli swojej roli procesu roboczego nie przechodzi do trybu online, ale roli sieci web kończy się powodzeniem, jest prawdopodobnie problem z połączeniem z bazy danych stanu, która została utworzona wcześniej.

* Upewnij się, że w Twojej cscfg ciągu połączenia jest prawidłowo wprowadzony.
* Upewnij się, że serwer i baza danych istnieje i czy identyfikator użytkownika i hasło są poprawne.
* Bazy danych SQL Azure parametry połączenia powinny mieć postać:

   ```  
   Server=myservername.database.windows.net; Database=mydatabasename;User ID=myuserID; Password=mypassword; Encrypt=True; Connection Timeout=30
   ```

* Upewnij się, że nazwa serwera nie zaczyna się od **https://** .
* Upewnij się, że serwer bazy danych SQL Azure pozwala nawiązać z nim usług systemu Azure. Aby to zrobić, Otwórz swoją bazę danych w portalu i upewnij się, że **zezwolić na dostęp do usług platformy Azure** jest ustawiana **na** **.

## <a name="test-the-service-deployment"></a>Testowanie wdrażania usługi
### <a name="connect-with-a-web-browser"></a>Łączenie z przeglądarką sieci web
Określ internetowego punktu końcowego usługi dzielenia i scalania. Możesz znaleźć to w portalu, przechodząc do **Przegląd** usługi w chmurze i wyszukiwanie w obszarze **adres URL witryny** po prawej stronie. Zastąp **http://** z **https://** ponieważ domyślne ustawienia zabezpieczeń wyłączenie punktu końcowego HTTP. Załadowanie strony dla tego adresu URL do przeglądarki.

### <a name="test-with-powershell-scripts"></a>Testowanie za pomocą skryptów programu PowerShell
Wdrożenie i środowiska można przetestować, uruchamiając uwzględnione przykładowych skryptów programu PowerShell.

Uwzględnione pliki skryptów są:

1. **SetupSampleSplitMergeEnvironment.ps1** — konfiguruje warstwę danych testowych do dzielenia i scalania (zobacz w poniższej tabeli przedstawiono szczegółowy opis)
2. **ExecuteSampleSplitMerge.ps1** — wykonuje operacje testu w teście Warstwa danych (zobacz w poniższej tabeli przedstawiono szczegółowy opis)
3. **GetMappings.ps1** — najwyższego poziomu przykładowy skrypt, który wyświetla bieżący stan mapowania fragmentów.
4. **ShardManagement.psm1** — skrypt pomocnika, która opakowuje interfejs API ShardManagement
5. **SqlDatabaseHelpers.psm1** — skrypt pomocnika dla tworzenia i zarządzania bazami danych SQL
   
   <table style="width:100%">
     <tr>
       <th>Plik programu PowerShell</th>
       <th>Kroki</th>
     </tr>
     <tr>
       <th rowspan="5">SetupSampleSplitMergeEnvironment.ps1</th>
       <td>1.    Tworzy bazy danych Menedżera mapowań fragmentów</td>
     </tr>
     <tr>
       <td>2.    Tworzy 2 baz danych fragmentów.
     </tr>
     <tr>
       <td>3.    Tworzy mapowania fragmentów w postaci tych baz danych (usuwa wszelkie istniejące dzielenie map na fragmenty w tych bazach danych). </td>
     </tr>
     <tr>
       <td>4.    Tworzy tabelę małą próbkę w obu fragmentach, jak i wypełnia tabelę w jednym z fragmentami.</td>
     </tr>
     <tr>
       <td>5.    Deklaruje może podzielonej na fragmenty tabeli.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Plik programu PowerShell</th>
       <th>Kroki</th>
     </tr>
   <tr>
       <th rowspan="4">ExecuteSampleSplitMerge.ps1 </th>
       <td>1.    Wysyła do usługi dzielenia i scalania Frontony sieci web, która dzieli połowa danych z pierwszego fragmentu do fragmentu drugi żądanie podziału.</td>
     </tr>
     <tr>
       <td>2.    Sonduje frontonu sieci web do podziału stan żądania i oczekuje na zakończenie żądania.</td>
     </tr>
     <tr>
       <td>3.    Wysyła żądanie scalania do frontonu sieci web usługi dzielenia i scalania, które przenosi dane z fragmentu drugi powrót do pierwszego fragmentu.</td>
     </tr>
     <tr>
       <td>4.    Sonduje frontonu sieci web dla stanu żądania scalania i oczekuje na zakończenie żądania.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Aby zweryfikować wdrożenie przy użyciu programu PowerShell
1. Otwórz nowe okno programu PowerShell i przejdź do katalogu, w której pobrano pakiet w dzielenia i scalania, a następnie przejdź do katalogu "powershell".
2. Tworzenie serwera usługi Azure SQL Database (lub wybierz istniejący serwer) Menedżera mapowań fragmentów i fragmentów tworzona.
   
   > [!NOTE]
   > Domyślnie w celu uproszczenia skrypt skrypt SetupSampleSplitMergeEnvironment.ps1 tworzy tych baz danych na tym samym serwerze. To nie jest ograniczeniem usługi dzielenia i łączenia się.
   >
   
   Identyfikator logowania uwierzytelniania SQL z dostępem odczytu/zapisu do bazy danych będzie potrzebna do przenoszenia danych i zaktualizuj mapowanie fragmentów usługi dzielenia i scalania. Ponieważ usługi dzielenia i scalania działa w chmurze, go nie obsługuje obecnie uwierzytelniania zintegrowanego.
   
   Upewnij się, że serwer Azure SQL jest skonfigurowana do Zezwalaj na dostęp z adresu IP komputera, uruchamianie tych skryptów. Można to ustawienie można znaleźć w obszarze serwera Azure SQL / configuration / dozwolone adresy IP.
3. Uruchom skrypt SetupSampleSplitMergeEnvironment.ps1, aby utworzyć środowisko przykładowe.
   
   Uruchomienie tego skryptu spowoduje wyczyszczenie się istniejących danych zarządzania mapy fragmentów struktury bazy danych Menedżera mapowań fragmentów i fragmenty. Może być przydatne do ponownego uruchomienia skryptu, jeśli chcesz ponownie zainicjować mapowania fragmentów lub fragmentów.
   
   Przykładowy wiersz polecenia:

   ```   
     .\SetupSampleSplitMergeEnvironment.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```      
4. Uruchom skrypt Getmappings.ps1, aby wyświetlić mapowania, które obecnie istnieją w środowisku próbki.
   
   ```
     .\GetMappings.ps1 
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net'

   ```         
5. Uruchom skrypt ExecuteSampleSplitMerge.ps1 można wykonać operacji podziału (przeniesienie połowy danych w pierwszym fragmencie, na drugi fragment), a następnie Operacja scalania (przenoszenie danych powrót do pierwszego fragmentu). Jeśli skonfigurowana usługa SSL i pozostanie wyłączony punkt końcowy http, upewnij się, użyj punktu końcowego https://.
   
   Przykładowy wiersz polecenia:

   ```   
     .\ExecuteSampleSplitMerge.ps1
   
         -UserName 'mysqluser' 
         -Password 'MySqlPassw0rd' 
         -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
         -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
         -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```      
   
   Jeśli wystąpi poniższy błąd, jest prawdopodobnie problem z certyfikatem usługi sieci Web punktu końcowego. Spróbuj nawiązać połączenie z internetowego punktu końcowego w ulubionej przeglądarce sieci Web i sprawdź, czy błąd certyfikatu.
   
     ```
     Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.
     ```
   
   Jeśli zakończyło się pomyślnie, dane wyjściowe powinny wyglądać poniżej:
   
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
6. Poeksperymentuj z innymi typami danych! Wszystkie te skrypty zająć opcjonalny parametr - ShardKeyType, który pozwala określić typ klucza. Wartością domyślną jest wartość Int32, ale można również określić Int64, Guid lub dane binarne.

## <a name="create-requests"></a>Żądania utworzenia
Usługi można użyć za pomocą interfejsu użytkownika sieci web lub importowania i wykorzystywania modułu programu SplitMerge.psm1 PowerShell, który będzie przesyłanie żądań za pośrednictwem roli sieci web.

Usługę można przenosić dane w tabelach podzielonej na fragmenty i tabele odwołań. Podzielonej na fragmenty tabeli jest kolumną klucza fragmentowania i ma innego wiersza danych w poszczególnych fragmentach. Tabeli referencyjnej nie jest podzielonej na fragmenty, dlatego zawiera ten sam wiersz danych na każdy fragment. Tabele odwołań są przydatne w przypadku danych, które nie zmieniają się często, jest używany do łączenia z tabelami podzielonej na fragmenty w zapytaniach.

W celu wykonywania operacji dzielenia i scalania, należy zadeklarować tabele podzielonej na fragmenty i tabele odwołań, które zostały przeniesione. Jest to realizowane przy **może** interfejsu API. Ten interfejs API znajduje się w **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema** przestrzeni nazw.

1. Dla każdej tabeli podzielonej na fragmenty, Utwórz **ShardedTableInfo** Obiekt opisujący Nazwa schematu nadrzędną tabeli (opcjonalne, wartość domyślna to "dbo"), nazwę tabeli i nazwę kolumny w tej tabeli, który zawiera klucz fragmentowania.
2. Dla każdej tabeli referencyjnej Utwórz **ReferenceTableInfo** Obiekt opisujący Nazwa schematu nadrzędną tabeli (opcjonalne, wartość domyślna to "dbo") i nazwę tabeli.
3. Dodaj powyżej obiektów TableInfo na nową **może** obiektu.
4. Pobierz odwołanie do **ShardMapManager** obiektu i wywołania **GetSchemaInfoCollection**.
5. Dodaj **może** do **SchemaInfoCollection**, podając nazwę mapy fragmentów.

Przykładem tego są widoczne w skrypcie SetupSampleSplitMergeEnvironment.ps1.

Usługi dzielenia i scalania nie tworzy docelowej bazy danych (lub schemat dla wszystkich tabel w bazie danych). Musi być wstępnie utworzonych przed wysłaniem żądania do usługi.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Może zostać wyświetlony poniżej komunikat podczas uruchamiania przykładowych skryptów programu powershell:

   ```
   Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.
   ```

Ten błąd oznacza, że certyfikat SSL nie jest poprawnie skonfigurowany. Wykonaj instrukcje podane w sekcji "Łączenie z przeglądarką sieci web".

Jeśli nie można przesłać żądania mogą pojawić się to:

```
[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'. 
```

W takim przypadku sprawdź plik konfiguracji, w szczególności ustawienie dla **WorkerRoleSynchronizationStorageAccountConnectionString**. Ten błąd zazwyczaj oznacza, że rola procesu roboczego nie może pomyślnie zainicjować bazy danych metadanych przy pierwszym użyciu. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png

