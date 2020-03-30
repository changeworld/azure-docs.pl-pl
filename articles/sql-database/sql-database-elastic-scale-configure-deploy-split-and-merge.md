---
title: Wdrażanie usługi z podziałem i scalaniem
description: Użyj scalania podziału zbyt przenieść dane między podzielonymi bazami danych.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 50dbca0b3a761b72134eaa6cfed57e231be4ef13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74421034"
---
# <a name="deploy-a-split-merge-service-to-move-data-between-sharded-databases"></a>Wdrażanie usługi scalania dzielonego w celu przenoszenia danych między podzielonymi bazami danych

Narzędzie scalania podziału umożliwia przenoszenie danych między podzielonymi bazami danych. Zobacz [Przenoszenie danych między skalowane w poziomie bazami danych w chmurze](sql-database-elastic-scale-overview-split-and-merge.md)

## <a name="download-the-split-merge-packages"></a>Pobierz pakiety scalania podziału

1. Pobierz najnowszą wersję NuGet z [NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

1. Otwórz wiersz polecenia i przejdź do katalogu, w którym pobrano plik nuget.exe. Plik do pobrania zawiera polecenia programu PowerShell.

1. Pobierz najnowszy pakiet Split-Merge do bieżącego katalogu za pomocą poniższego polecenia:

   ```cmd
   nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge
   ```  

Pliki są umieszczane w katalogu o nazwie **Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge.x.x.xxx.x,** gdzie *x.x.xxx.x* odzwierciedla numer wersji. Znajdź pliki usługi korespondencji seryjnej w katalogu podkatarzy **content\splitmerge\service** oraz skrypty programu PowerShell (i wymagane biblioteki DLL klienta) w podkatarzye **zawartości\splitmerge\powershell.**

## <a name="prerequisites"></a>Wymagania wstępne

1. Utwórz bazę danych bazy danych usługi Azure SQL DB, która będzie używana jako baza danych stanu korespondencji seryjnej. Przejdź do [witryny Azure portal](https://portal.azure.com). Utwórz nową **bazę danych SQL**. Nadaj bazie danych nazwę i utwórz nowego administratora i hasło. Pamiętaj, aby zapisać nazwę i hasło do późniejszego użycia.

1. Upewnij się, że serwer usługi Azure SQL DB umożliwia usługom Platformy Azure łączenie się z nim. W portalu w **ustawieniach zapory**upewnij się, że ustawienie **Zezwalaj na dostęp do usług Platformy Azure** jest ustawione na **Włączone**. Kliknij ikonę "Zapisz".

1. Utwórz konto usługi Azure Storage dla danych wyjściowych diagnostyki.

1. Utwórz usługę Azure Cloud Service dla usługi scalania podzielonego.

## <a name="configure-your-split-merge-service"></a>Konfigurowanie usługi scalania dzielonego

### <a name="split-merge-service-configuration"></a>Konfiguracja usługi dzielenia i scalania

1. W folderze, do którego pobrano zestawy scalania podziału, utwórz kopię pliku *ServiceConfiguration.Template.cscfg,* który został dostarczony wraz z *splitmergeservice.cspkg* i zmień jego nazwę *serviceconfiguration.cscfg*.

1. Otwórz *serviceconfiguration.cscfg* w edytorze tekstu, takim jak Visual Studio, który sprawdza poprawność danych wejściowych, takich jak format odcisków palców certyfikatów.

1. Utwórz nową bazę danych lub wybierz istniejącą bazę danych, która będzie służyć jako baza danych stanu dla operacji scalania podziału i pobierz ciąg połączenia tej bazy danych.

   > [!IMPORTANT]
   > W tej chwili baza danych stanu musi używać\_sortowania łacińskiego (SQL\_Latin1 General\_CP1\_CI\_AS). Aby uzyskać więcej informacji, zobacz [Nazwa sortowania systemu Windows (Transact-SQL)](https://msdn.microsoft.com/library/ms188046.aspx).

   W usłudze Azure SQL DB parametry połączenia zazwyczaj mają postać:

      `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<userId>; Password=<password>; Encrypt=True; Connection Timeout=30`

1. Ten ciąg połączenia należy wpisać w pliku *cscfg* w sekcjach roli **SplitMergeWeb** i **SplitMergeWorker** w ustawieniu Elastyczne rozmiary.

1. Dla roli **SplitMergeWorker** wprowadź prawidłowy parametry połączenia z magazynem platformy Azure dla ustawienia **WorkerRoleSynchronizationStorageAccountConnectionString.**

### <a name="configure-security"></a>Konfigurowanie zabezpieczeń

Szczegółowe instrukcje dotyczące konfigurowania zabezpieczeń usługi można znaleźć w [konfiguracji zabezpieczeń scalania podziału.](sql-database-elastic-scale-split-merge-security-configuration.md)

Na potrzeby prostego wdrożenia testowego dla tego samouczka zostanie wykonany minimalny zestaw kroków konfiguracji, aby rozpocząć i uruchomić usługę. Te kroki umożliwiają tylko jeden komputer/konto wykonujące je do komunikowania się z usługą.

### <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Utwórz nowy katalog i z tego katalogu wykonaj następujące polecenie przy użyciu [wiersza polecenia dewelopera dla](https://msdn.microsoft.com/library/ms229859.aspx) programu Visual Studio:

   ```cmd
   makecert ^
    -n "CN=*.cloudapp.net" ^
    -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2" ^
    -a sha256 -len 2048 ^
    -sr currentuser -ss root ^
    -sv MyCert.pvk MyCert.cer
   ```

Zostaniesz poproszony o podanie hasła do ochrony klucza prywatnego. Wprowadź silne hasło i potwierdź go. Następnie zostanie wyświetlony monit o podanie hasła do użycia ponownie po tym. Kliknij **przycisk Tak** na końcu, aby zaimportować go do magazynu głównego zaufanych urzędów certyfikacji.

### <a name="create-a-pfx-file"></a>Tworzenie pliku PFX

Wykonaj następujące polecenie z tego samego okna, w którym wykonano makecert; użyj tego samego hasła, które zostało użyte do utworzenia certyfikatu:

   ```cmd
   pvk2pfx -pvk MyCert.pvk -spc MyCert.cer -pfx MyCert.pfx -pi <password>
   ```

### <a name="import-the-client-certificate-into-the-personal-store"></a>Importowanie certyfikatu klienta do magazynu osobistego

1. W Eksploratorze Windows kliknij dwukrotnie pozycję *MyCert.pfx*.
2. W **Kreatorze importu certyfikatów** wybierz pozycję **Bieżący użytkownik** i kliknij przycisk **Dalej**.
3. Potwierdź ścieżkę pliku i kliknij przycisk **Dalej**.
4. Wpisz hasło, **pozostaw Uwzględnij wszystkie zaznaczone właściwości rozszerzone** i kliknij przycisk **Dalej**.
5. Pozostaw **Automatycznie wybierz sejdę certyfikatów i** kliknij przycisk **Dalej**.
6. Kliknij **przycisk Zakończ** i **OK**.

### <a name="upload-the-pfx-file-to-the-cloud-service"></a>Przekazywanie pliku PFX do usługi w chmurze

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Wybierz pozycję **Usługi w chmurze**.
3. Wybierz usługę w chmurze utworzoną powyżej dla usługi Podziału/Scalania.
4. W górnym menu kliknij **pozycję Certyfikaty.**
5. Kliknij **pozycję Przekaż** na dolnym pasku.
6. Wybierz plik PFX i wprowadź to samo hasło, co powyżej.
7. Po zakończeniu skopiuj odcisk palca certyfikatu z nowego wpisu na liście.

### <a name="update-the-service-configuration-file"></a>Aktualizowanie pliku konfiguracji usługi

Wklej odcisk palca certyfikatu skopiowany powyżej do atrybutu odcisk palca/wartość tych ustawień.
Dla roli pracownika:

   ```xml
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Dla roli sieci Web:

   ```xml
    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
    <Setting name="AllowedClientCertificateThumbprints" value="" />
    <Setting name="DataEncryptionPrimaryCertificateThumbprint" value="" />
    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
   ```

Należy pamiętać, że w przypadku wdrożeń produkcyjnych dla urzędu certyfikacji powinny być używane oddzielne certyfikaty do szyfrowania, certyfikatów serwera i certyfikatów klientów. Aby uzyskać szczegółowe instrukcje dotyczące tej kwestii, zobacz [Konfiguracja zabezpieczeń](sql-database-elastic-scale-split-merge-security-configuration.md).

## <a name="deploy-your-service"></a>Wdrażanie usługi

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz usługę w chmurze, która została utworzona wcześniej.
3. Kliknij pozycję **Przegląd**.
4. Wybierz środowisko przejściowe, a następnie kliknij przycisk **Przekaż**.
5. W oknie dialogowym wprowadź etykietę wdrożenia. W przypadku opcji "Pakiet" i "Konfiguracja" kliknij "Z lokalnego" i wybierz plik *SplitMergeService.cspkg* i plik cscfg skonfigurowany wcześniej.
6. Upewnij się, że pole wyboru z etykietą **Deploy, nawet jeśli jedna lub więcej ról zawiera pojedyncze wystąpienie** jest zaznaczone.
7. Naciśnij przycisk zaznaczyć w prawym dolnym rogu, aby rozpocząć wdrażanie. Spodziewaj się, że zajmie to kilka minut.

## <a name="troubleshoot-the-deployment"></a>Rozwiązywanie problemów z wdrażaniem

Jeśli rola sieci Web nie przejdzie do trybu online, prawdopodobnie wystąpił problem z konfiguracją zabezpieczeń. Sprawdź, czy ssl jest skonfigurowany w sposób opisany powyżej.

Jeśli rola pracownika nie przejdzie do trybu online, ale rola sieci web zakończy się pomyślnie, najprawdopodobniej występuje problem z połączeniem się z wcześniej utworzoną bazą danych o stanie.

- Upewnij się, że parametry połączenia w cscfg są dokładne.
- Sprawdź, czy serwer i baza danych istnieją oraz czy identyfikator użytkownika i hasło są poprawne.
- W przypadku usługi Azure SQL DB parametry połączenia powinny mieć postać:

   `Server=<serverName>.database.windows.net; Database=<databaseName>;User ID=<user>; Password=<password>; Encrypt=True; Connection Timeout=30`

- Upewnij się, że nazwa serwera nie zaczyna się od **https://**.
- Upewnij się, że serwer usługi Azure SQL DB umożliwia usługom Platformy Azure łączenie się z nim. Aby to zrobić, otwórz bazę danych w portalu i upewnij się, że ustawienie **Zezwalaj na dostęp do usług platformy Azure** jest ustawione na **On****.

## <a name="test-the-service-deployment"></a>Testowanie wdrożenia usługi

### <a name="connect-with-a-web-browser"></a>Łączenie się z przeglądarką internetową

Określ punkt końcowy sieci Web usługi scalania podziału. Można to znaleźć w portalu, przechodząc do **przeglądu** usługi w chmurze i patrząc w obszarze **Adres URL witryny** po prawej stronie. Zastąp **http://** **https://,** ponieważ domyślne ustawienia zabezpieczeń wyłączają punkt końcowy HTTP. Załaduj stronę dla tego adresu URL do przeglądarki.

### <a name="test-with-powershell-scripts"></a>Testowanie za pomocą skryptów programu PowerShell

Wdrożenie i środowisko można przetestować, uruchamiając dołączone przykładowe skrypty programu PowerShell.

Pliki skryptów zawarte są:

1. *SetupSampleSplitMergeEnvironment.ps1* - konfiguruje warstwę danych testowych dla podziału/scalania (szczegółowy opis znajduje się w poniższej tabeli)
2. *ExecuteSampleSplitMerge.ps1* - wykonuje operacje testowe w warstwie danych testowych (szczegółowy opis znajduje się w poniższej tabeli)
3. *GetMappings.ps1* — przykładowy skrypt najwyższego poziomu, który drukuje bieżący stan mapowań niezależnego fragmentu.
4. *ShardManagement.psm1* - skrypt pomocniczy, który otacza interfejs API obrażeń odłamków
5. *SqlDatabaseHelpers.psm1* - skrypt pomocniczy do tworzenia baz danych SQL i zarządzania nimi
   
   <table style="width:100%">
     <tr>
       <th>Plik programu PowerShell</th>
       <th>Kroki</th>
     </tr>
     <tr>
       <th rowspan="5">KonfiguracjaSampleSplitMergeEnvironment.ps1</th>
       <td>1. Tworzy bazę danych menedżera map fragmentów</td>
     </tr>
     <tr>
       <td>2. Tworzy 2 bazy danych niezależnego fragmentu.
     </tr>
     <tr>
       <td>3. Tworzy mapę niezależnego fragmentu dla tych baz danych (usuwa wszystkie istniejące mapy niezależnego fragmentu w tych bazach danych). </td>
     </tr>
     <tr>
       <td>4. Tworzy małą tabelę przykładu w obu fragmentów i wypełnia tabelę w jednym z fragmentów.</td>
     </tr>
     <tr>
       <td>5. Deklaruje SchemaInfo dla podzielonej tabeli.</td>
     </tr>
   </table>
   <table style="width:100%">
     <tr>
       <th>Plik programu PowerShell</th>
       <th>Kroki</th>
     </tr>
   <tr>
       <th rowspan="4">WykonujeSampleSplitMerge.ps1 </th>
       <td>1. Wysyła żądanie podziału do frontonu sieci web usługi dzielenia scalania, który dzieli połowę danych z pierwszego niezależnego fragmentu do drugiego niezależnego fragmentu.</td>
     </tr>
     <tr>
       <td>2. Sonduje frontend sieci web dla stanu żądania podziału i czeka na zakończenie żądania.</td>
     </tr>
     <tr>
       <td>3. Wysyła żądanie scalania do frontonu sieci web usługi dzielenia i scalania, który przenosi dane z drugiego niezależnego fragmentu z powrotem do pierwszego niezależnego fragmentu.</td>
     </tr>
     <tr>
       <td>4. Sonduje fronton sieci Web dla stanu żądania korespondencji seryjnej i czeka na zakończenie żądania.</td>
     </tr>
   </table>
   
## <a name="use-powershell-to-verify-your-deployment"></a>Weryfikowanie wdrożenia za pomocą programu PowerShell

1. Otwórz nowe okno programu PowerShell i przejdź do katalogu, w którym pobrano pakiet Scalanie podziału, a następnie przejdź do katalogu "powershell".

2. Utwórz serwer bazy danych SQL Azure (lub wybierz istniejący serwer), na którym zostanie utworzony menedżer map niezależnego fragmentu i fragmenty.

   > [!NOTE]
   > Skrypt *SetupSampleSplitMergeEnvironment.ps1* domyślnie tworzy wszystkie te bazy danych na tym samym serwerze, aby zachować prostotę skryptu. Nie jest to ograniczenie samej usługi scalania podziału.

   Logowanie uwierzytelniania SQL z dostępem do odczytu/zapisu do DB będą potrzebne dla usługi Scalanie podziału, aby przenieść dane i zaktualizować mapę niezależnego fragmentu. Ponieważ usługa scalania podziału jest uruchamiana w chmurze, obecnie nie obsługuje zintegrowanego uwierzytelniania.

   Upewnij się, że serwer SQL platformy Azure jest skonfigurowany tak, aby zezwalać na dostęp z adresu IP komputera z uruchomionymi tymi skryptami. To ustawienie można znaleźć w witrynie Serwer SQL Azure / konfiguracja / dozwolone adresy IP.

3. Wykonaj skrypt *SetupSampleSplitMergeEnvironment.ps1,* aby utworzyć przykładowe środowisko.

   Uruchomienie tego skryptu spowoduje wymazanie istniejących struktur danych zarządzania mapami niezależnego fragmentu w bazie danych menedżera map niezależnego fragmentu i fragmentów. Może być przydatne do ponownego uruchomienia skryptu, jeśli chcesz ponownie zainicjować mapy niezależnego fragmentu lub fragmenty.

   Przykładowy wiersz polecenia:

   ```cmd
   .\SetupSampleSplitMergeEnvironment.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

4. Wykonaj skrypt Getmappings.ps1, aby wyświetlić mapowania, które obecnie istnieją w przykładowym środowisku.

   ```cmd
   .\GetMappings.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' -ShardMapManagerServerName 'abcdefghij.database.windows.net'
   ```

5. Wykonaj *skrypt ExecuteSampleSplitMerge.ps1,* aby wykonać operację podziału (przeniesienie połowy danych na pierwszy fragment do drugiego fragmentu), a następnie operację scalania (przeniesienie danych z powrotem na pierwszy fragment). Jeśli skonfigurowano protokół SSL i wyłączono punkt końcowy http, upewnij się, że zamiast tego używasz https:// punktu końcowego.

   Przykładowy wiersz polecenia:

   ```cmd
   .\ExecuteSampleSplitMerge.ps1
    -UserName 'mysqluser' -Password 'MySqlPassw0rd' 
    -ShardMapManagerServerName 'abcdefghij.database.windows.net' 
    -SplitMergeServiceEndpoint 'https://mysplitmergeservice.cloudapp.net' 
    -CertificateThumbprint '0123456789abcdef0123456789abcdef01234567'
   ```

   Jeśli zostanie wyświetlony poniższy błąd, najprawdopodobniej występuje problem z certyfikatem punktu końcowego sieci Web. Spróbuj połączyć się z punktem końcowym sieci Web za pomocą ulubionej przeglądarki sieci Web i sprawdź, czy wystąpił błąd certyfikatu.

     `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLSsecure channel.`

   Jeśli to się udało, dane wyjściowe powinny wyglądać jak poniżej:

   ```output
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

6. Eksperymentuj z innymi typami danych! Wszystkie te skrypty mają opcjonalny parametr -ShardKeyType, który umożliwia określenie typu klucza. Wartość domyślna to Int32, ale można również określić Int64, Guid lub Binary.

## <a name="create-requests"></a>Tworzenie żądań

Usługa może być używana za pomocą interfejsu użytkownika sieci Web lub importując i używając modułu Programu PowerShell SplitMerge.psm1, który będzie przesyłał żądania za pośrednictwem roli sieci web.

Usługa może przenosić dane zarówno w tabelach podzielonych na fragmenty, jak i w tabelach referencyjnych. Tabela podzielona na fragmenty ma kolumnę klucza dzielenia na fragmenty i ma różne dane wiersza na każdym niezależnuze. Tabela odwołań nie jest podzielona na fragmenty, więc zawiera te same dane wiersza na każdym niezależnuze. Tabele odwołań są przydatne dla danych, które nie zmieniają się często i jest używany do łączenia z tabel podzielonych na fragmenty w kwerendach.

Aby wykonać operację scalania podziału, należy zadeklarować podzielone tabele i tabele odwołań, które mają zostać przeniesione. Jest to realizowane za pomocą interfejsu API **SchemaInfo.** Ten interfejs API znajduje się w obszarze nazw **Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.Schema.**

1. Dla każdej tabeli podzielonej na fragmenty utwórz obiekt **ShardedTableInfo** opisujący nadrzędną nazwę schematu tabeli (opcjonalnie, domyślnie "dbo"), nazwę tabeli i nazwę kolumny w tej tabeli, która zawiera klucz dzielenia na fragmenty.
2. Dla każdej tabeli odwołań należy utworzyć obiekt **ReferenceTableInfo** opisujący nadrzędną nazwę schematu tabeli (opcjonalnie, domyślnie "dbo") i nazwę tabeli.
3. Dodaj powyższe obiekty TableInfo do nowego obiektu **SchemaInfo.**
4. Uzyskaj odwołanie do obiektu **ShardMapManager** i wywołaj **getschemaInfoCollection**.
5. Dodaj **SchemaInfo** do **SchemaInfoCollection**, podając nazwę mapy niezależnego fragmentu.

Przykładem tego można zobaczyć w dziale SetupSampleSplitMergeEnvironment.ps1.

Usługa scalania podziału nie tworzy docelowej bazy danych (lub schematu dla wszystkich tabel w bazie danych) dla Ciebie. Muszą one być wstępnie utworzone przed wysłaniem żądania do usługi.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Po uruchomieniu przykładowych skryptów programu PowerShell może zostać wyświetlony poniższy komunikat:

   `Invoke-WebRequest : The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

Ten błąd oznacza, że certyfikat SSL nie jest poprawnie skonfigurowany. Postępuj zgodnie z instrukcjami w sekcji "Łączenie się z przeglądarką internetową".

Jeśli nie możesz przesłać żądań, możesz zobaczyć:

   `[Exception] System.Data.SqlClient.SqlException (0x80131904): Could not find stored procedure 'dbo.InsertRequest'.`

W takim przypadku sprawdź plik konfiguracyjny, w szczególności ustawienie **WorkerRoleSynchronizationStorageAccountConnectionString**. Ten błąd zazwyczaj wskazuje, że rola procesu roboczego nie może pomyślnie zainicjować bazy danych metadanych przy pierwszym użyciu.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/allowed-services.png
[2]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/manage.png
[3]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/staging.png
[4]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/upload.png
[5]: ./media/sql-database-elastic-scale-configure-deploy-split-and-merge/storage.png
