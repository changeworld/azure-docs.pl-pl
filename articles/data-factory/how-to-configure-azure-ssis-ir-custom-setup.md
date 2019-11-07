---
title: 'Dostosowywanie Instalatora dla środowiska Azure-SSIS Integration Runtime '
description: W tym artykule opisano sposób używania niestandardowego interfejsu Instalatora dla środowiska Azure-SSIS Integration Runtime w celu zainstalowania dodatkowych składników lub zmiany ustawień
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 1/25/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 222672a93ccde7464ec1f37212f18996033a1460
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674859"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Dostosowywanie Instalatora dla środowiska Azure-SSIS Integration Runtime

Niestandardowy interfejs instalacji Azure-SSIS Integration Runtime udostępnia interfejs umożliwiający dodanie własnych kroków instalacyjnych podczas aprowizacji lub ponownej konfiguracji Azure-SSIS IR. Konfiguracja niestandardowa pozwala zmienić domyślną konfigurację lub środowisko pracy (na przykład w celu uruchomienia dodatkowych usług systemu Windows lub utrzymać poświadczenia dostępu do udziałów plików) lub zainstalować dodatkowe składniki (na przykład zestawy, sterowniki lub rozszerzenia). w każdym węźle Azure-SSIS IR.

Konfigurację niestandardową można skonfigurować przez przygotowanie skryptu i skojarzonych z nim plików, a następnie przekazanie ich do kontenera obiektów BLOB na koncie usługi Azure Storage. Podajesz Uniform Resource Identifier sygnatury dostępu współdzielonego (SAS) dla kontenera podczas aprowizacji lub zmiany konfiguracji Azure-SSIS IR. Każdy węzeł Azure-SSIS IR następnie pobiera skrypt i skojarzone z nim pliki z kontenera i uruchamia konfigurację niestandardową z podniesionymi uprawnieniami. Po zakończeniu instalacji niestandardowej każdy węzeł przekazuje standardowe dane wyjściowe wykonywania i innych dzienników do kontenera.

Można zainstalować zarówno składniki bezpłatne, jak i nielicencjonowane oraz składniki płatne lub licencjonowane. Jeśli jesteś niezależnym dostawcą oprogramowania, zapoznaj się z tematem [jak opracowywać składniki płatne lub licencjonowane dla Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Węzły z serii v2 Azure-SSIS IR nie są odpowiednie dla konfiguracji niestandardowej, dlatego należy zamiast tego użyć węzłów serii v3.  Jeśli korzystasz już z węzłów z serii v2, przełącz się do korzystania z węzłów z serii v3 najszybciej, jak to możliwe.

## <a name="current-limitations"></a>Bieżące ograniczenia

-   Jeśli chcesz używać `gacutil.exe` do instalowania zestawów w globalnej pamięci podręcznej zestawów (GAC), musisz podać `gacutil.exe` w ramach instalacji niestandardowej lub użyć kopii udostępnionej w kontenerze publicznej wersji zapoznawczej.

-   Jeśli chcesz odwołać się do podfolderu w skrypcie, `msiexec.exe` nie obsługuje notacji `.\` w celu odwoływania się do folderu głównego. Użyj polecenia, takiego jak `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`, a nie `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

-   Jeśli zachodzi potrzeba dołączenia do Azure-SSIS IR z instalacją niestandardową do sieci wirtualnej, obsługiwana jest tylko Azure Resource Manager Sieć wirtualna. Klasyczna Sieć wirtualna nie jest obsługiwana.

-   Udział administracyjny nie jest obecnie obsługiwany w Azure-SSIS IR.

-   Sterownik ODBC IBM iSeries Access nie jest obsługiwany w Azure-SSIS IR. Podczas instalacji niestandardowej może zostać wyświetlony błąd instalacji. Skontaktuj się z pomocą techniczną firmy IBM, aby uzyskać pomoc.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby dostosować Azure-SSIS IR, potrzebne są następujące elementy:

-   [Subskrypcja platformy Azure](https://azure.microsoft.com/)

-   [Azure SQL Database lub serwer wystąpienia zarządzanego](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Inicjowanie obsługi administracyjnej Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Konto usługi Azure Storage](https://azure.microsoft.com/services/storage/). W przypadku instalacji niestandardowej można przekazać i zapisać niestandardowy skrypt instalacyjny oraz skojarzone z nim pliki w kontenerze obiektów BLOB. Proces instalacji niestandardowej umożliwia również przekazanie dzienników wykonywania do tego samego kontenera obiektów BLOB.

## <a name="instructions"></a>Instrukcje

1. Pobierz i zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps).

1. Przygotowanie niestandardowego skryptu instalacji i skojarzonych z nim plików (na przykład. bat,. cmd,. exe,. dll,. msi lub pliki ps1).

   1.  Musisz mieć plik skryptu o nazwie `main.cmd`, który jest punktem wejścia konfiguracji niestandardowej.

   1.  Należy upewnić się, że skrypt może być wykonywany dyskretnie, zaleca się najpierw przetestować skrypt na maszynie lokalnej.

   1.  Jeśli potrzebujesz dodatkowych dzienników generowanych przez inne narzędzia (na przykład `msiexec.exe`) do przekazania do kontenera, określ wstępnie zdefiniowaną zmienną środowiskową, `CUSTOM_SETUP_SCRIPT_LOG_DIR` jako folder dziennika w skryptach (na przykład `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

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

   1. W przypadku inicjowania obsługi administracyjnej lub zmiany konfiguracji Azure-SSIS IR przy użyciu Data Factory interfejsu użytkownika przed rozpoczęciem Azure-SSIS IR wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera w odpowiednim polu na panelu **ustawień zaawansowanych** :

      ![Wprowadź sygnaturę dostępu współdzielonego](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      Podczas aprowizacji lub zmiany konfiguracji Azure-SSIS IR przy użyciu programu PowerShell przed rozpoczęciem Azure-SSIS IR Uruchom polecenie cmdlet `Set-AzDataFactoryV2IntegrationRuntime` z identyfikatorem URI sygnatury dostępu współdzielonego kontenera jako wartość nowego parametru `SetupScriptContainerSasUri`. Na przykład:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. Po zakończeniu instalacji niestandardowej i uruchomieniu Azure-SSIS IR można znaleźć standardowe dane wyjściowe `main.cmd` i innych dzienników wykonywania w folderze `main.cmd.log` kontenera magazynu.

1. Aby wyświetlić inne przykłady instalacji niestandardowej, Połącz się z kontenerem publicznej wersji zapoznawczej przy użyciu Eksplorator usługi Azure Storage.

   a.  W obszarze **(lokalne i dołączone)** kliknij prawym przyciskiem myszy pozycję **konta magazynu**, wybierz pozycję **Połącz z usługą Azure Storage**, wybierz polecenie **Użyj parametrów połączenia lub identyfikatora URI sygnatury dostępu współdzielonego**, a następnie wybierz przycisk **dalej**.

      ![Nawiązywanie połączenia z usługą Azure Storage za pomocą sygnatury dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  Wybierz pozycję **Użyj identyfikatora URI sygnatury dostępu współdzielonego** i wprowadź następujący identyfikator URI sygnatury dostępu współdzielonego dla publicznego kontenera podglądu. Wybierz pozycję **dalej**, a następnie wybierz pozycję **Połącz**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Podaj sygnaturę dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   d. Wybierz połączony publiczny kontener w wersji zapoznawczej i kliknij dwukrotnie folder `CustomSetupScript`. W tym folderze znajdują się następujące elementy:

      1. Folder `Sample` zawierający niestandardową konfigurację służącą do instalowania podstawowego zadania na każdym węźle Azure-SSIS IR. Zadanie nie wykonuje żadnych operacji, ale w stanie uśpienia przez kilka sekund. Folder zawiera również folder `gacutil`, całą zawartość, którą (`gacutil.exe`, `gacutil.exe.config`i `1033\gacutlrc.dll`) można skopiować jako element do kontenera. Ponadto `main.cmd` zawiera komentarze do utrwalania poświadczeń dostępu do udziałów plików.

      1. Folder `UserScenarios`, który zawiera kilka niestandardowych ustawień dla scenariuszy rzeczywistych użytkowników.

   ![Zawartość kontenera publicznej wersji zapoznawczej](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. Kliknij dwukrotnie folder `UserScenarios`. W tym folderze znajdują się następujące elementy:

      1. Folder `.NET FRAMEWORK 3.5`, który zawiera konfigurację niestandardową, aby zainstalować wcześniejszą wersję .NET Framework, która może być wymagana dla składników niestandardowych w każdym węźle Azure-SSIS IR.

      1. Folder `BCP` zawierający konfigurację niestandardową służącą do instalowania SQL Server narzędzi wiersza polecenia (`MsSqlCmdLnUtils.msi`), w tym program kopiowania zbiorczego (`bcp`), na każdym węźle Azure-SSIS IR.

      1. Folder `EXCEL` zawierający niestandardową konfigurację służącą do instalowania zestawów typu Open Source (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`i `ExcelDataReader.dll`) w każdym węźle Azure-SSIS IR.

      1. Folder `ORACLE ENTERPRISE`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`) i cichy plik konfiguracyjny instalacji (`client.rsp`) do zainstalowania łączników Oracle i sterownika OCI w każdym węźle Azure-SSIS IR Enterprise Edition. Ta konfiguracja pozwala korzystać z Menedżera połączeń Oracle, źródła i miejsca docelowego. Najpierw pobierz łączniki Microsoft Konektors v 5.0 for Oracle (`AttunitySSISOraAdaptersSetup.msi` i `AttunitySSISOraAdaptersSetup64.msi`) z [Centrum pobierania Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) i najnowszego klienta Oracle — na przykład `winx64_12102_client.zip`-from [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), a następnie przekazać je wszystkie razem z `main.cmd` i `client.rsp` kontener. Jeśli używasz TNS do łączenia się z bazą danych Oracle, musisz również pobrać `tnsnames.ora`, edytować go i przekazać do kontenera, aby można go było skopiować do folderu instalacyjnego programu Oracle podczas instalacji.

      1. Folder `ORACLE STANDARD ADO.NET`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), aby zainstalować sterownik Oracle ODP.NET na każdym węźle Azure-SSIS IR. Ta konfiguracja pozwala korzystać z Menedżera połączeń ADO.NET, źródła i miejsca docelowego. Najpierw Pobierz najnowszy sterownik Oracle ODP.NET — na przykład `ODP.NET_Managed_ODAC122cR1.zip`-from [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), a następnie Przekaż go wraz z `main.cmd` do kontenera.
       
      1. Folder `ORACLE STANDARD ODBC`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), aby zainstalować sterownik Oracle ODBC i skonfigurować nazwę DSN w każdym węźle Azure-SSIS IR. Ta konfiguracja pozwala na łączenie się z serwerem Oracle przy użyciu Menedżera połączeń ODBC/źródła/lokalizacji docelowej lub Power Query Connection Manager/Source ze źródłem danych ODBC. Najpierw pobierz najnowszą wersję programu Oracle Instant Client (pakiet podstawowy lub podstawowy pakiet uproszczony) oraz pakiet ODBC — na przykład pakiety 64-bitowe z tego [miejsca](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pakiet podstawowy: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, podstawowy pakiet w wersji lite: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, pakiet ODBC: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) lub 32-bitowy pakiety z tego [miejsca](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pakiet podstawowy: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, podstawowy pakiet w języku Lite: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, pakiet ODBC: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`), a następnie Przekaż je razem z `main.cmd` do kontenera.

      1. Folder `SAP BW`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), aby zainstalować zestaw SAP .NET Connector (`librfc32.dll`) w każdym węźle Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia korzystanie z Menedżera połączeń SAP BW, źródła i miejsca docelowego. Najpierw przekaż 64-bitową lub 32-bitową wersję `librfc32.dll` z folderu instalacji SAP do kontenera wraz z `main.cmd`. Skrypt kopiuje zestaw SAP do folderu `%windir%\SysWow64` lub `%windir%\System32` podczas instalacji.

      1. Folder `STORAGE`, który zawiera konfigurację niestandardową służącą do instalowania Azure PowerShell w każdym węźle Azure-SSIS IR. Dzięki tej konfiguracji można wdrażać i uruchamiać pakiety usług SSIS, które uruchamiają [skrypty programu PowerShell w celu manipulowania kontem usługi Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Skopiuj `main.cmd`, `AzurePowerShell.msi` próbkę (lub zainstaluj najnowszą wersję) i `storage.ps1` do kontenera. Użyj programu PowerShell. dtsx jako szablonu dla pakietów. Szablon pakietu łączy [zadanie pobierania obiektów blob platformy Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), które pobiera `storage.ps1` jako modyfikowalny skrypt programu PowerShell i [zadanie wykonywania procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , które wykonuje skrypt w każdym węźle.

      1. Folder `TERADATA`, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), skojarzony z nim plik (`install.cmd`) i pakiety Instalatora (`.msi`). Te pliki instalują łączniki programu Teradata, interfejs API TPT oraz sterownik ODBC na każdym węźle Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia korzystanie z Menedżera połączeń programu Teradata, źródła i miejsca docelowego. Najpierw Pobierz plik zip Tools i Utilities programu Teradata (TTU) 15. x (na przykład `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) z programu [Teradata](http://partnerintelligence.teradata.com), a następnie Przekaż go razem z powyższymi `.cmd` i `.msi` plikami do kontenera.

   ![Foldery w folderze scenariusze użytkownika](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. Aby wypróbować te próbki instalacji niestandardowej, skopiuj i wklej zawartość z wybranego folderu do kontenera. W przypadku aprowizacji lub zmiany konfiguracji Azure-SSIS IR przy użyciu programu PowerShell uruchom polecenie cmdlet `Set-AzDataFactoryV2IntegrationRuntime` z identyfikatorem URI sygnatury dostępu współdzielonego kontenera jako wartość nowego parametru `SetupScriptContainerSasUri`.

## <a name="next-steps"></a>Następne kroki

-   [Wersja Enterprise Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Jak opracowywać płatne lub licencjonowane składniki niestandardowe dla środowiska Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
