---
title: Dostosowywanie Instalatora dla środowiska Azure-SSIS Integration Runtime
description: W tym artykule opisano sposób używania niestandardowego interfejsu Instalatora dla środowiska Azure-SSIS Integration Runtime w celu zainstalowania dodatkowych składników lub zmiany ustawień
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 02/01/2020
ms.openlocfilehash: e85ef22542fc162648dbfc637892cf7e580c6aac
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964553"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Dostosowywanie Instalatora dla środowiska Azure-SSIS Integration Runtime

Niestandardowy interfejs instalacji Azure-SSIS Integration Runtime udostępnia interfejs umożliwiający dodanie własnych kroków instalacyjnych podczas aprowizacji lub ponownej konfiguracji Azure-SSIS IR. 

Konfiguracja niestandardowa pozwala zmienić domyślną konfigurację lub środowisko pracy (na przykład w celu uruchomienia dodatkowych usług systemu Windows lub utrzymać poświadczenia dostępu do udziałów plików) lub zainstalować dodatkowe składniki (na przykład zestawy, sterowniki lub rozszerzenia). w każdym węźle Azure-SSIS IR.

Istnieją dwa sposoby przeprowadzania niestandardowych konfiguracji na Azure-SSIS IR: Express Custom Setups bez skryptów i standardowych konfiguracji niestandardowych ze skryptami.

W przypadku instalacji niestandardowych Express można uruchamiać niektóre typowe konfiguracje systemu/polecenia systemu Windows lub instalować niektóre popularne/zalecane dodatkowe składniki bez użycia żadnych skryptów.  

W przypadku standardowych konfiguracji niestandardowych należy przygotować skrypt i skojarzone z nim pliki, a następnie przekazać je ze sobą do kontenera obiektów BLOB na koncie usługi Azure Storage. Następnie należy podać Uniform Resource Identifier sygnatury dostępu współdzielonego (SAS) dla kontenera podczas aprowizacji lub zmiany konfiguracji Azure-SSIS IR. Każdy węzeł Azure-SSIS IR następnie pobierze skrypt i skojarzone z nim pliki z kontenera i uruchomi konfigurację niestandardową z podniesionymi uprawnieniami. Po zakończeniu instalacji niestandardowej każdy węzeł przekaże standardowe dane wyjściowe wykonywania i innych dzienników do kontenera.

Możesz zainstalować zarówno składniki bezpłatnej/nielicencjonowanej, jak i płatne/licencjonowane ze standardowymi konfiguracjami w języku Express/Standard. Jeśli jesteś niezależnym dostawcą oprogramowania, zapoznaj się z naszą dokumentacją dotyczącą [tworzenia płatnych lub licencjonowanych składników dla Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Węzły z serii v2 Azure-SSIS IR nie są odpowiednie dla konfiguracji niestandardowej, dlatego należy zamiast tego użyć węzłów serii v3.  Jeśli korzystasz już z węzłów z serii v2, przełącz się do korzystania z węzłów z serii v3 najszybciej, jak to możliwe.

## <a name="current-limitations"></a>Bieżące ograniczenia

Następujące ograniczenia mają zastosowanie tylko do standardowych konfiguracji niestandardowych:

- Jeśli chcesz użyć `gacutil.exe` w skrypcie do zainstalowania zestawów w globalnej pamięci podręcznej zestawów (GAC), musisz podać `gacutil.exe` w ramach konfiguracji niestandardowej lub użyć kopii udostępnionej w naszym kontenerze publicznej wersji zapoznawczej poniżej.

- Jeśli chcesz odwołać się do podfolderu w skrypcie, `msiexec.exe` nie obsługuje notacji `.\` w celu odwoływania się do folderu głównego. Użyj polecenia, takiego jak `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`, a nie `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

- Udziały administracyjne, czyli ukryte udziały sieciowe tworzone automatycznie przez system Windows, nie są obecnie obsługiwane w Azure-SSIS IR.

- Sterownik ODBC IBM iSeries Access nie jest obsługiwany w Azure-SSIS IR. Podczas instalacji niestandardowej mogą pojawić się błędy instalacji. Skontaktuj się z pomocą techniczną firmy IBM, aby uzyskać pomoc.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby dostosować Azure-SSIS IR, potrzebne są następujące elementy:

- [Subskrypcja platformy Azure](https://azure.microsoft.com/)

- [Inicjowanie obsługi Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Konto usługi Azure Storage](https://azure.microsoft.com/services/storage/). Niewymagane w przypadku instalacji niestandardowych Express. W przypadku standardowych konfiguracji niestandardowych można przekazać i zapisać niestandardowy skrypt instalacyjny oraz skojarzone z nim pliki w kontenerze obiektów BLOB. Proces instalacji niestandardowej umożliwia również przekazanie dzienników wykonywania do tego samego kontenera obiektów BLOB.

## <a name="instructions"></a>Instrukcje

1. Jeśli chcesz zainicjować lub zmienić konfigurację Azure-SSIS IR przy użyciu programu PowerShell, Pobierz i zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps). W przypadku instalacji niestandardowych Express przejdź do kroku 4.

1. Przygotowanie niestandardowego skryptu instalacji i skojarzonych z nim plików (na przykład. bat,. cmd,. exe,. dll,. msi lub pliki ps1).

   1. Musisz mieć plik skryptu o nazwie `main.cmd`, który jest punktem wejścia konfiguracji niestandardowej.

   1. Należy upewnić się, że skrypt może być wykonywany dyskretnie, zaleca się najpierw przetestować skrypt na maszynie lokalnej.

   1. Jeśli potrzebujesz dodatkowych dzienników generowanych przez inne narzędzia (na przykład `msiexec.exe`) do przekazania do kontenera, określ wstępnie zdefiniowaną zmienną środowiskową, `CUSTOM_SETUP_SCRIPT_LOG_DIR` jako folder dziennika w skryptach (na przykład `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. Pobierz, zainstaluj i uruchom [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

   1. W obszarze **(lokalne i dołączone)** kliknij prawym przyciskiem myszy pozycję **konta magazynu** i wybierz pozycję **Połącz z usługą Azure Storage**.

      ![Łączenie z usługą Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Wybierz pozycję **Użyj nazwy i klucza konta magazynu** , a **następnie**wybierz pozycję Dalej.

      ![Używanie nazwy i klucza konta magazynu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Wprowadź nazwę i klucz konta usługi Azure Storage, wybierz pozycję **dalej**, a następnie wybierz pozycję **Połącz**.

      ![Podaj nazwę i klucz konta magazynu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. W obszarze połączone konto usługi Azure Storage kliknij prawym przyciskiem myszy **kontenery obiektów BLOB**, wybierz pozycję **Utwórz kontener obiektów BLOB**i nazwij nowy kontener.

      ![Tworzenie kontenera obiektów blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Wybierz nowy kontener i Przekaż swój niestandardowy skrypt instalacji i skojarzone z nim pliki. Upewnij się, że przekazano `main.cmd` na najwyższym poziomie kontenera, a nie w żadnym folderze. Upewnij się również, że kontener zawiera tylko niezbędne pliki instalacji niestandardowej, więc pobranie ich na Azure-SSIS IR później nie zajmie dużo czasu. Maksymalny okres instalacji niestandardowej jest obecnie ustawiony na 45 minut przed przekroczeniem limitu czasu, co obejmuje czas pobierania wszystkich plików z kontenera i instalowania go na Azure-SSIS IR. Jeśli potrzebujesz dłuższego okresu, zgłoś bilet pomocy technicznej.

      ![Przekazywanie plików do kontenera obiektów BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Kliknij prawym przyciskiem myszy kontener i wybierz polecenie **Pobierz sygnaturę dostępu współdzielonego**.

      ![Uzyskaj sygnaturę dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Utwórz identyfikator URI sygnatury dostępu współdzielonego dla kontenera o wystarczająco długim czasie ważności i z uprawnieniami do odczytu i zapisu i wyświetlania listy. Wymagany jest identyfikator URI sygnatury dostępu współdzielonego, który umożliwia pobranie i uruchomienie niestandardowego skryptu instalacji i skojarzonych z nim plików za każdym razem, gdy dowolny węzeł Azure-SSIS IR zostanie odgraficzny/uruchomiony ponownie. Musisz mieć uprawnienia do zapisu w celu przekazania dzienników wykonywania Instalatora.

      > [!IMPORTANT]
      > Upewnij się, że identyfikator URI sygnatury dostępu współdzielonego nie wygasa, a niestandardowe zasoby instalacyjne są zawsze dostępne w całym cyklu życia Azure-SSIS IR, od tworzenia do usunięcia, zwłaszcza jeśli regularnie zatrzymasz i uruchomisz Azure-SSIS IR w tym okresie.

      ![Generowanie sygnatury dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Skopiuj i Zapisz identyfikator URI sygnatury dostępu współdzielonego kontenera.

      ![Kopiowanie i zapisywanie sygnatury dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Podczas aprowizacji lub zmiany konfiguracji Azure-SSIS IR przy użyciu Data Factory interfejsu użytkownika można dodać lub usunąć niestandardowe ustawienia, zaznaczając pole wyboru **dostosuj Azure-SSIS Integration Runtime z dodatkowymi konfiguracjami systemu/instalacjami składników** w sekcji **Ustawienia zaawansowane** w panelu konfiguracja środowiska Integration Runtime. 

   Jeśli chcesz dodać standardowe konfiguracje niestandardowe, wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera w polu **Identyfikator URI sygnatury dostępu współdzielonego kontenera ustawień niestandardowych** . 
   
   Jeśli chcesz dodać niestandardowe ustawienia instalacji, wybierz pozycję **Nowy** , aby otworzyć panel **Dodaj ekspresową instalację niestandardową** , a następnie wybierz dowolne typy z menu rozwijanego **Typ instalacji niestandardowej** :

   1. W przypadku wybrania typu **polecenia Uruchom cmdkey** można zachować poświadczenia dostępu do udziałów plików/Azure Files na Azure-SSIS IR, wprowadzając odpowiednio nazwę komputera/domeny, nazwę konta/nazwę użytkownika i klucz konta/hasło w polach **/Add**, **/User**i **/Pass** . Jest to podobne do uruchamiania polecenia Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) na komputerze lokalnym.
   
   1. Jeśli zostanie wybrana opcja **Dodaj typ zmiennej środowiskowej** , można dodać zmienne środowiskowe systemu Windows do użycia w pakietach uruchamianych na Azure-SSIS IR, wprowadzając odpowiednio nazwę zmiennej środowiskowej i wartość w polach **Nazwa zmiennej** i **wartość zmiennej** . Jest to podobne do uruchamiania polecenia [Set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) systemu Windows na komputerze lokalnym.

   1. Jeśli wybierzesz opcję **Zainstaluj licencjonowany typ składnika** , możesz wybrać wszystkie składniki zintegrowane od partnerów niezależnego dostawcy oprogramowania z menu rozwijanego **Nazwa składnika** :

      1. W przypadku wybrania składnika **fabryki zadań SentryOne** można zainstalować pakiet [fabryk zadań](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) składników z SentryOne na Azure-SSIS IR, wprowadzając klucz licencji produktu zakupiony z nich w polu **klucz licencji** . Aktualna wersja zintegrowana to **2019.4.3**.

      1. W przypadku wybrania **OH22'S HEDDA. Składnik we/wy** , można zainstalować [HEDDA. ](https://hedda.io/ssis-component/)Składnik jakości/oczyszczania danych we/wy z oh22 na Azure-SSIS IR po zakupie usługi. Aktualna wersja zintegrowana to **1.0.13**.

      1. W przypadku wybrania składnika **oh22's SQLPhonetics.NET** można zainstalować składnik [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) Data/dopasowanie z oh22 na Azure-SSIS IR, wprowadzając klucz licencji produktu zakupiony z nich w polu **klucz licencji** . Aktualna wersja zintegrowana to **1.0.43**.
   
   Dodane ustawienia Express Custom Setup zostaną wyświetlone w sekcji **Ustawienia zaawansowane** . Aby je usunąć, zaznacz ich pola wyboru, a następnie wybierz pozycję **Usuń**.

   ![Ustawienia zaawansowane z konfiguracjami niestandardowymi](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. Podczas aprowizacji lub zmiany konfiguracji Azure-SSIS IR przy użyciu programu PowerShell można dodać/usunąć niestandardowe ustawienia, uruchamiając polecenie cmdlet `Set-AzDataFactoryV2IntegrationRuntime` przed rozpoczęciem Azure-SSIS IR.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   Po zakończeniu standardowej instalacji niestandardowej i rozpoczęciu Azure-SSIS IR można znaleźć standardowe dane wyjściowe `main.cmd` i innych dzienników wykonywania w folderze `main.cmd.log` kontenera magazynu.

1. Aby wyświetlić przykłady standardowych konfiguracji niestandardowych, Połącz się z naszym publicznym kontenerem w wersji zapoznawczej przy użyciu Eksplorator usługi Azure Storage.

   1. W obszarze **(lokalne i dołączone)** kliknij prawym przyciskiem myszy pozycję **konta magazynu**, wybierz pozycję **Połącz z usługą Azure Storage**, wybierz polecenie **Użyj parametrów połączenia lub identyfikatora URI sygnatury dostępu współdzielonego**, a następnie wybierz przycisk **dalej**.

      ![Nawiązywanie połączenia z usługą Azure Storage za pomocą sygnatury dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   1. Wybierz pozycję **Użyj identyfikatora URI sygnatury dostępu współdzielonego** i wprowadź następujący identyfikator URI sygnatury dostępu współdzielonego dla publicznego kontenera podglądu. Wybierz pozycję **dalej**, a następnie wybierz pozycję **Połącz**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Podaj sygnaturę dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   1. Wybierz połączony publiczny kontener w wersji zapoznawczej i kliknij dwukrotnie folder `CustomSetupScript`. W tym folderze znajdują się następujące elementy:

      1. Folder `Sample` zawierający niestandardową konfigurację służącą do instalowania podstawowego zadania na każdym węźle Azure-SSIS IR. Zadanie nie wykonuje żadnych operacji, ale w stanie uśpienia przez kilka sekund. Folder zawiera również folder `gacutil`, całą zawartość, którą (`gacutil.exe`, `gacutil.exe.config`i `1033\gacutlrc.dll`) można skopiować jako element do kontenera.

      1. Folder `UserScenarios`, który zawiera kilka niestandardowych przykładów instalacyjnych z rzeczywistych scenariuszy użytkownika.

      ![Zawartość kontenera publicznej wersji zapoznawczej](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   1. Kliknij dwukrotnie folder `UserScenarios`, aby znaleźć następujące elementy:

      1. Folder `.NET FRAMEWORK 3.5`, który zawiera konfigurację niestandardową, aby zainstalować wcześniejszą wersję .NET Framework, która może być wymagana dla składników niestandardowych w każdym węźle Azure-SSIS IR.

      1. Folder `BCP` zawierający konfigurację niestandardową służącą do instalowania SQL Server narzędzi wiersza polecenia (`MsSqlCmdLnUtils.msi`), w tym program kopiowania zbiorczego (`bcp`), na każdym węźle Azure-SSIS IR.

      1. Folder `EXCEL`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`) do instalowania C# zestawów/bibliotek, których można użyć w zadaniach skryptów do dynamicznego odczytywania/zapisywania plików programu Excel w każdym węźle Azure-SSIS IR. Najpierw pobierz `ExcelDataReader.dll` z tego [miejsca](https://www.nuget.org/packages/ExcelDataReader/) i `DocumentFormat.OpenXml.dll` z tego [miejsca](https://www.nuget.org/packages/DocumentFormat.OpenXml/), a następnie Przekaż je wszystkie wraz z `main.cmd` do kontenera. Alternatywnie, jeśli chcesz tylko użyć standardowego Menedżera połączeń programu Excel/źródła/miejsca docelowego, wymagany pakiet redystrybucyjny dostępu jest już wstępnie zainstalowany na Azure-SSIS IR, więc nie jest potrzebna żadna Konfiguracja niestandardowa.
      
      1. Folder `MYSQL ODBC`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`) do instalowania sterowników ODBC MySQL na każdym węźle Azure-SSIS IR. Ta konfiguracja umożliwia łączenie z serwerem MySQL za pomocą Menedżera połączeń ODBC/źródła/lokalizacji docelowej. Najpierw należy pobrać najnowszą wersję 64-bitową i 32-bitową instalacji sterowników ODBC programu MySQL — na przykład `mysql-connector-odbc-8.0.13-winx64.msi` i `mysql-connector-odbc-8.0.13-win32.msi` — od programu [MySQL](https://dev.mysql.com/downloads/connector/odbc/), a następnie przekazać je ze sobą za pomocą `main.cmd` do kontenera.

      1. Folder `ORACLE ENTERPRISE`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`) i cichy plik konfiguracyjny instalacji (`client.rsp`) do zainstalowania łączników Oracle i sterownika OCI w każdym węźle Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia łączenie się z serwerem Oracle przy użyciu Menedżera połączeń Oracle/źródła/lokalizacji docelowej. Najpierw pobierz łączniki Microsoft Connector 5.0 for Oracle (`AttunitySSISOraAdaptersSetup.msi` i `AttunitySSISOraAdaptersSetup64.msi`) z [Centrum pobierania Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) i najnowszego klienta Oracle — na przykład `winx64_12102_client.zip`-from [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), a następnie Przekaż je wszystkie wraz z `main.cmd` i `client.rsp` do kontenera. Jeśli używasz TNS do łączenia się z bazą danych Oracle, musisz również pobrać `tnsnames.ora`, edytować go i przekazać do kontenera, aby można go było skopiować do folderu instalacyjnego programu Oracle podczas instalacji.

      1. Folder `ORACLE STANDARD ADO.NET`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), aby zainstalować sterownik Oracle ODP.NET na każdym węźle Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem Oracle przy użyciu Menedżera połączeń ADO.NET/źródła/lokalizacji docelowej. Najpierw Pobierz najnowszy sterownik Oracle ODP.NET — na przykład `ODP.NET_Managed_ODAC122cR1.zip`-from [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), a następnie Przekaż go wraz z `main.cmd` do kontenera.
       
      1. Folder `ORACLE STANDARD ODBC`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), aby zainstalować sterownik Oracle ODBC i skonfigurować nazwę DSN w każdym węźle Azure-SSIS IR. Ta konfiguracja pozwala na łączenie się z serwerem Oracle przy użyciu Menedżera połączeń ODBC/źródła/lokalizacji docelowej lub Power Query Connection Manager/Source ze źródłem danych ODBC. Najpierw pobierz najnowszą wersję programu Oracle Instant Client (pakiet podstawowy lub podstawowy pakiet w wersji uproszczonej) oraz pakiet ODBC — na przykład pakiety 64-bitowe z tego [miejsca](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pakiet podstawowy: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, podstawowy pakiet w języku Lite: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, pakiet ODBC: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) lub pakiety 32-bitowe z tego [miejsca](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pakiet podstawowy: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, podstawowy pakiet w języku Lite: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, pakiet ODBC: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`), a następnie Przekaż je wszystkie wraz z `main.cmd` do kontenera.

      1. Folder `ORACLE STANDARD OLEDB`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), aby zainstalować sterownik Oracle OLEDB na każdym węźle Azure-SSIS IR. Ta konfiguracja umożliwia łączenie się z serwerem Oracle przy użyciu Menedżera połączeń OLEDB/źródła/lokalizacji docelowej. Najpierw Pobierz najnowszy sterownik Oracle OLEDB — na przykład `ODAC122010Xcopy_x64.zip`-from [Oracle](https://www.oracle.com/partners/campaign/index-090165.html), a następnie Przekaż go wraz z `main.cmd` do kontenera.

      1. Folder `POSTGRESQL ODBC`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), aby zainstalować sterowniki ODBC PostgreSQL na każdym węźle Azure-SSIS IR. Ta konfiguracja umożliwia nawiązanie połączenia z serwerem PostgreSQL za pomocą Menedżera połączeń ODBC/źródła/lokalizacji docelowej. Najpierw pobierz najnowsze 64-bitowe i 32-bitowe wersje instalatorów sterowników ODBC PostgreSQL — na przykład `psqlodbc_x64.msi` i `psqlodbc_x86.msi`-z [PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/), a następnie Przekaż je wszystkie ze `main.cmd` do kontenera.

      1. Folder `SAP BW`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), aby zainstalować zestaw SAP .NET Connector (`librfc32.dll`) w każdym węźle Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia łączenie się z serwerem SAP BW za pomocą Menedżera połączeń SAP BW/źródła/lokalizacji docelowej. Najpierw przekaż 64-bitową lub 32-bitową wersję `librfc32.dll` z folderu instalacji SAP wraz z `main.cmd` do kontenera. Skrypt kopiuje zestaw SAP do folderu `%windir%\SysWow64` lub `%windir%\System32` podczas instalacji.

      1. Folder `STORAGE`, który zawiera konfigurację niestandardową służącą do instalowania Azure PowerShell w każdym węźle Azure-SSIS IR. Dzięki tej konfiguracji można wdrażać i uruchamiać pakiety usług SSIS, które uruchamiają [skrypty programu PowerShell w celu manipulowania kontem usługi Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Skopiuj `main.cmd`, `AzurePowerShell.msi` próbkę (lub użyj najnowszej wersji) i `storage.ps1` do kontenera. Użyj programu PowerShell. dtsx jako szablonu dla pakietów. Szablon pakietu łączy [zadanie pobierania obiektów blob platformy Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), które pobiera `storage.ps1` jako modyfikowalny skrypt programu PowerShell i [zadanie wykonywania procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , które wykonuje skrypt w każdym węźle.

      1. Folder `TERADATA`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), skojarzony z nim plik (`install.cmd`) i pakiety Instalatora (`.msi`). Te pliki instalują łączniki programu Teradata, interfejs API TPT i sterownik ODBC na każdym węźle Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia nawiązanie połączenia z serwerem Teradata przy użyciu Menedżera połączeń programu Teradata/źródła/lokalizacji docelowej. Najpierw Pobierz narzędzia programu Teradata i pliki zip 15. x (na przykład `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) z programu [Teradata](http://partnerintelligence.teradata.com), a następnie Przekaż je razem z powyższymi `.cmd` i `.msi` plikami do kontenera.

      1. Folder `ZULU OPENJDK`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`) i plik programu PowerShell (`install_openjdk.ps1`) do instalowania Zulu OpenJDK na każdym węźle Azure-SSIS IR. Ta konfiguracja pozwala używać łączników plików Azure Data Lake Store/elastycznych do przetwarzania plików ORC/Parquet, zobacz [tutaj](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java) , aby uzyskać więcej informacji. Najpierw pobierz najnowszą Zulu OpenJDK — na przykład `zulu8.33.0.1-jdk8.0.192-win_x64.zip` — z tego [miejsca](https://www.azul.com/downloads/zulu/zulu-windows/), a następnie Przekaż go wraz z `main.cmd` i `install_openjdk.ps1` do kontenera.

      ![Foldery w folderze scenariusze użytkownika](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   1. Aby wypróbować te przykłady instalacji niestandardowej, skopiuj i wklej zawartość z wybranego folderu do kontenera.
   
      Podczas aprowizacji lub zmiany konfiguracji Azure-SSIS IR przy użyciu Data Factory interfejsu użytkownika zaznacz pole wyboru **dostosuj Azure-SSIS Integration Runtime z dodatkowymi konfiguracjami systemu/instalacjami składników** w sekcji **Ustawienia zaawansowane** i wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera w polu **Identyfikator URI sygnatury dostępu współdzielonego kontenera ustawień niestandardowych** .
   
      Podczas aprowizacji lub zmiany konfiguracji Azure-SSIS IR przy użyciu programu PowerShell uruchom polecenie cmdlet `Set-AzDataFactoryV2IntegrationRuntime` z identyfikatorem URI sygnatury dostępu współdzielonego kontenera jako wartością parametru `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Następne kroki

-   [Wersja Enterprise Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Jak opracowywać płatne lub licencjonowane składniki niestandardowe dla środowiska Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
