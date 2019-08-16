---
title: Dostosowywanie Instalatora dla środowiska Azure-SSIS Integration Runtime | Microsoft Docs
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
ms.openlocfilehash: 4962070d69af98d0c7b10dc6f931612766529dce
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515704"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Dostosowywanie Instalatora dla środowiska Azure-SSIS Integration Runtime

Niestandardowy interfejs Instalatora platformy Azure-SSIS Integration Runtime udostępnia interfejs umożliwiający dodanie własnych kroków instalacyjnych podczas aprowizacji lub ponownej konfiguracji środowiska Azure-SSIS IR. Konfiguracja niestandardowa pozwala zmienić domyślną konfigurację lub środowisko pracy (na przykład w celu uruchomienia dodatkowych usług systemu Windows lub utrzymać poświadczenia dostępu do udziałów plików) lub zainstalować dodatkowe składniki (na przykład zestawy, sterowniki lub rozszerzenia). w każdym węźle środowiska Azure-SSIS IR.

Konfigurację niestandardową można skonfigurować przez przygotowanie skryptu i skojarzonych z nim plików, a następnie przekazanie ich do kontenera obiektów BLOB na koncie usługi Azure Storage. Podajesz Uniform Resource Identifier sygnatury dostępu współdzielonego (SAS) dla kontenera podczas aprowizacji lub ponownego konfigurowania środowiska Azure-SSIS IR. Każdy węzeł środowiska Azure-SSIS IR Pobiera skrypt i skojarzone z nim pliki, a następnie uruchamia konfigurację niestandardową z podniesionymi uprawnieniami. Po zakończeniu instalacji niestandardowej każdy węzeł przekazuje standardowe dane wyjściowe wykonywania i innych dzienników do kontenera.

Można zainstalować zarówno składniki bezpłatne, jak i nielicencjonowane oraz składniki płatne lub licencjonowane. Jeśli jesteś niezależnym dostawcą oprogramowania, zapoznaj się z tematem [jak opracowywać składniki płatne lub licencjonowane dla środowiska Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Węzły z serii v2 platformy Azure-SSIS IR nie są odpowiednie do konfiguracji niestandardowej, dlatego należy zamiast tego użyć węzłów serii v3.  Jeśli korzystasz już z węzłów z serii v2, przełącz się do korzystania z węzłów z serii v3 najszybciej, jak to możliwe.

## <a name="current-limitations"></a>Bieżące ograniczenia

-   Jeśli chcesz użyć `gacutil.exe` programu do instalacji zestawów w globalnej pamięci podręcznej zestawów (GAC), musisz podać `gacutil.exe` jako część konfiguracji niestandardowej lub użyć kopii udostępnionej w kontenerze publicznej wersji zapoznawczej.

-   Jeśli chcesz odwołać się do podfolderu w skrypcie, `msiexec.exe` program nie `.\` obsługuje notacji, aby odwołać się do folderu głównego. Użyj polecenia, takiego `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` jak `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`zamiast.

-   Jeśli musisz dołączyć do sieci wirtualnej środowisko Azure-SSIS IR z instalacją niestandardową, obsługiwana jest tylko Azure Resource Manager sieci wirtualnej. Klasyczna Sieć wirtualna nie jest obsługiwana.

-   Udział administracyjny nie jest obecnie obsługiwany przez środowisko Azure-SSIS IR.

-   Sterownik ODBC programu IBM iSeries Access nie jest obsługiwany przez środowisko Azure-SSIS IR. Podczas instalacji niestandardowej może zostać wyświetlony błąd instalacji. Skontaktuj się z pomocą techniczną firmy IBM, aby uzyskać pomoc.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aby dostosować środowisko Azure-SSIS IR, potrzebne są następujące elementy:

-   [Subskrypcja platformy Azure](https://azure.microsoft.com/)

-   [Azure SQL Database lub serwer wystąpienia zarządzanego](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Inicjowanie obsługi środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Konto usługi Azure Storage](https://azure.microsoft.com/services/storage/). W przypadku instalacji niestandardowej można przekazać i zapisać niestandardowy skrypt instalacyjny oraz skojarzone z nim pliki w kontenerze obiektów BLOB. Proces instalacji niestandardowej umożliwia również przekazanie dzienników wykonywania do tego samego kontenera obiektów BLOB.

## <a name="instructions"></a>Instrukcje

1. Pobierz i zainstaluj [Azure PowerShell](/powershell/azure/install-az-ps).

1. Przygotowanie niestandardowego skryptu instalacji i skojarzonych z nim plików (na przykład. bat,. cmd,. exe,. dll,. msi lub pliki ps1).

   1.  Musisz mieć plik skryptu o nazwie `main.cmd`, który jest punktem wejścia instalacji niestandardowej.

   1.  Jeśli chcesz, aby dodatkowe dzienniki wygenerowane przez inne narzędzia (na `msiexec.exe`przykład) zostały przekazane do kontenera, określ wstępnie zdefiniowaną `CUSTOM_SETUP_SCRIPT_LOG_DIR` zmienną środowiskową jako folder dziennika w `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`skryptach (na przykład).

1. Pobierz, zainstaluj i uruchom [Eksplorator usługi Azure Storage](https://storageexplorer.com/).

   1. W obszarze **(lokalne i dołączone)** kliknij prawym przyciskiem myszy pozycję **konta magazynu** i wybierz pozycję **Połącz z usługą Azure Storage**.

      ![Łączenie z usługą Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Wybierz pozycję **Użyj nazwy i klucza konta magazynu** , a **następnie**wybierz pozycję Dalej.

      ![Używanie nazwy i klucza konta magazynu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Wprowadź nazwę i klucz konta usługi Azure Storage, wybierz pozycję **dalej**, a następnie wybierz pozycję **Połącz**.

      ![Podaj nazwę i klucz konta magazynu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. W obszarze połączone konto usługi Azure Storage kliknij prawym przyciskiem myszy **kontenery obiektów BLOB**, wybierz pozycję **Utwórz kontener obiektów BLOB**i nazwij nowy kontener.

      ![Tworzenie kontenera obiektów blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Wybierz nowy kontener i Przekaż swój niestandardowy skrypt instalacji i skojarzone z nim pliki. Upewnij się, że przekazujesz `main.cmd` na najwyższym poziomie kontenera, a nie w żadnym folderze. Upewnij się również, że kontener zawiera tylko niezbędne pliki instalacji niestandardowej, więc pobranie ich do środowiska Azure-SSIS IR w przyszłości nie zajmie dużo czasu. Maksymalny okres instalacji niestandardowej jest obecnie ustawiony na 45 minut przed przekroczeniem limitu czasu, co obejmuje czas pobierania wszystkich plików z kontenera i instalowania go na platformie Azure-SSIS IR. Jeśli potrzebujesz dłuższego okresu, zgłoś bilet pomocy technicznej.

      ![Przekazywanie plików do kontenera obiektów BLOB](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Kliknij prawym przyciskiem myszy kontener i wybierz polecenie **Pobierz sygnaturę dostępu**współdzielonego.

      ![Uzyskaj sygnaturę dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Utwórz identyfikator URI sygnatury dostępu współdzielonego dla kontenera o wystarczająco długim czasie ważności i z uprawnieniami do odczytu i zapisu i wyświetlania listy. Wymagany jest identyfikator URI sygnatury dostępu współdzielonego, który umożliwia pobranie i uruchomienie niestandardowego skryptu instalacji i skojarzonych z nim plików za każdym razem, gdy dowolny węzeł środowiska Azure-SSIS IR zostanie ponownie utworzony/uruchomiony. Musisz mieć uprawnienia do zapisu w celu przekazania dzienników wykonywania Instalatora.

      > [!IMPORTANT]
      > Upewnij się, że identyfikator URI sygnatury dostępu współdzielonego nie wygasa, a niestandardowe zasoby instalacyjne są zawsze dostępne w całym cyklu życia środowiska Azure-SSIS IR, od tworzenia do usunięcia, zwłaszcza jeśli regularnie zatrzymujesz i uruchamiasz środowisko Azure-SSIS IR w tym okresie.

      ![Generowanie sygnatury dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Skopiuj i Zapisz identyfikator URI sygnatury dostępu współdzielonego kontenera.

      ![Kopiowanie i zapisywanie sygnatury dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

   1. W przypadku aprowizacji lub ponownego konfigurowania środowiska Azure-SSIS IR przy użyciu interfejsu użytkownika Data Factory przed rozpoczęciem działania środowiska Azure-SSIS IR wprowadź identyfikator URI sygnatury dostępu współdzielonego kontenera w odpowiednim polu na panelu **ustawień zaawansowanych** :

      ![Wprowadź sygnaturę dostępu współdzielonego](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      Podczas aprowizacji lub zmiany konfiguracji środowiska Azure-SSIS IR przy użyciu programu PowerShell przed rozpoczęciem pracy z usługą Azure-SSIS IR Uruchom `Set-AzDataFactoryV2IntegrationRuntime` polecenie cmdlet z identyfikatorem URI sygnatury dostępu współdzielonego kontenera `SetupScriptContainerSasUri` jako wartość nowego parametru. Na przykład:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. Po zakończeniu instalacji niestandardowej i uruchomieniu środowiska Azure-SSIS IR można znaleźć standardowe dane wyjściowe `main.cmd` i inne dzienniki wykonywania `main.cmd.log` w folderze kontenera magazynu.

1. Aby wyświetlić inne przykłady instalacji niestandardowej, Połącz się z kontenerem publicznej wersji zapoznawczej przy użyciu Eksplorator usługi Azure Storage.

   a.  W obszarze **(lokalne i dołączone)** kliknij prawym przyciskiem myszy pozycję **konta magazynu**, wybierz pozycję **Połącz z usługą Azure Storage**, wybierz polecenie **Użyj parametrów połączenia lub identyfikatora URI sygnatury dostępu**współdzielonego, a następnie wybierz przycisk **dalej**.

      ![Nawiązywanie połączenia z usługą Azure Storage za pomocą sygnatury dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  Wybierz pozycję **Użyj identyfikatora URI sygnatury** dostępu współdzielonego i wprowadź następujący identyfikator URI sygnatury dostępu współdzielonego dla publicznego kontenera podglądu. Wybierz pozycję **dalej**, a następnie wybierz pozycję **Połącz**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Podaj sygnaturę dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Wybierz połączony publiczny kontener w wersji zapoznawczej i kliknij `CustomSetupScript` go dwukrotnie. W tym folderze znajdują się następujące elementy:

      1. `Sample` Folder, który zawiera niestandardową konfigurację służącą do instalowania podstawowego zadania na każdym węźle środowiska Azure-SSIS IR. Zadanie nie wykonuje żadnych operacji, ale w stanie uśpienia przez kilka sekund. Folder zawiera `gacutil` również folder, cała zawartość (`gacutil.exe`, `gacutil.exe.config`i `1033\gacutlrc.dll`), którą można skopiować do kontenera. Ponadto program `main.cmd` zawiera komentarze dotyczące utrwalania poświadczeń dostępu dla udziałów plików.

      1. `UserScenarios` Folder, który zawiera kilka niestandardowych ustawień dla scenariuszy rzeczywistych użytkowników.

   ![Zawartość kontenera publicznej wersji zapoznawczej](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. Kliknij `UserScenarios` dwukrotnie folder. W tym folderze znajdują się następujące elementy:

      1. `.NET FRAMEWORK 3.5` Folder, który zawiera konfigurację niestandardową, aby zainstalować wcześniejszą wersję .NET Framework, która może być wymagana dla składników niestandardowych w każdym węźle środowiska Azure-SSIS IR.

      1. Folder, który zawiera konfigurację niestandardową, aby zainstalować SQL Server narzędzia wiersza polecenia (`MsSqlCmdLnUtils.msi`), w tym program kopiowania zbiorczego`bcp`(), na każdym węźle środowiska Azure-SSIS IR. `BCP`

      1. Folder, który zawiera konfigurację niestandardową, aby zainstalować zestawy typu open source`DocumentFormat.OpenXml.dll`( `ExcelDataReader.DataSet.dll`, i `ExcelDataReader.dll`) w każdym węźle środowiska Azure-SSIS IR. `EXCEL`

      1. Folder, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`) i cichy plik konfiguracyjny instalacji (`client.rsp`) w celu zainstalowania łączników Oracle i sterownika OCI na każdym węźle platformy Azure-SSIS IR Enterprise Edition. `ORACLE ENTERPRISE` Ta konfiguracja pozwala korzystać z Menedżera połączeń Oracle, źródła i miejsca docelowego. Najpierw pobierz łączniki Microsoft Connector w wersji 5.0 for`AttunitySSISOraAdaptersSetup.msi` Oracle `AttunitySSISOraAdaptersSetup64.msi`(i) z [Centrum pobierania Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=55179) i `winx64_12102_client.zip` najnowszego klienta Oracle — na przykład — od firmy [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), a następnie Przekaż je razem z usługą `main.cmd` i`client.rsp` do kontenera. Jeśli używasz TNS do łączenia się z bazą danych Oracle, musisz również pobrać `tnsnames.ora`, edytować i przekazać go do kontenera, aby można go było skopiować do folderu instalacyjnego programu Oracle podczas instalacji.

      1. Folder, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), aby zainstalować sterownik Oracle ODP.NET na każdym węźle środowiska Azure-SSIS IR. `ORACLE STANDARD ADO.NET` Ta konfiguracja pozwala korzystać z Menedżera połączeń ADO.NET, źródła i miejsca docelowego. Najpierw Pobierz najnowszy sterownik Oracle ODP.NET — na przykład `ODP.NET_Managed_ODAC122cR1.zip` — z bazy danych [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), a następnie `main.cmd` Przekaż go do swojego kontenera.
       
      1. Folder, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), aby zainstalować sterownik Oracle ODBC i skonfigurować nazwę DSN w każdym węźle środowiska Azure-SSIS IR. `ORACLE STANDARD ODBC` Ta konfiguracja pozwala na łączenie się z serwerem Oracle przy użyciu Menedżera połączeń ODBC/źródła/lokalizacji docelowej lub Power Query Connection Manager/Source ze źródłem danych ODBC. Najpierw pobierz najnowszą wersję programu Oracle Instant Client (pakiet podstawowy lub podstawowy pakiet Basic) oraz pakiet ODBC — na przykład pakiety 64-bitowe z tego [miejsca](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pakiet podstawowy: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, podstawowy pakiet w wersji Lite: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, pakiet ODBC `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`:) lub pakiety 32-bitowe z tego [miejsca](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pakiet podstawowy: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, podstawowy pakiet języka Lite `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`:, pakiet ODBC `instantclient-odbc-nt-18.3.0.0.0dbru.zip`:), a następnie Przekaż je razem `main.cmd` do kontenera.

      1. Folder, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), aby zainstalować zestaw SAP .NET Connector (`librfc32.dll`) w każdym węźle platformy Azure-SSIS IR Enterprise Edition. `SAP BW` Ta konfiguracja umożliwia korzystanie z Menedżera połączeń SAP BW, źródła i miejsca docelowego. Najpierw przekaż 64-bitową lub 32-bitową wersję `librfc32.dll` z folderu instalacji SAP do kontenera, w połączeniu z. `main.cmd` Skrypt kopiuje zestaw SAP do `%windir%\SysWow64` folderu lub `%windir%\System32` podczas instalacji.

      1. `STORAGE` Folder, który zawiera konfigurację niestandardową służącą do instalowania Azure PowerShell na każdym węźle środowiska Azure-SSIS IR. Dzięki tej konfiguracji można wdrażać i uruchamiać pakiety usług SSIS, które uruchamiają [skrypty programu PowerShell w celu manipulowania kontem usługi Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Skopiuj `main.cmd`, a przykład `AzurePowerShell.msi` (lub zainstaluj najnowszą wersję) i `storage.ps1` do kontenera. Użyj programu PowerShell. dtsx jako szablonu dla pakietów. Szablon pakietu łączy [zadanie pobierania obiektów blob platformy Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), które jest `storage.ps1` pobierane jako modyfikowalny skrypt programu PowerShell, oraz [zadanie wykonywania procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , które wykonuje skrypt w każdym węźle.

      1. Folder, który zawiera niestandardowy skrypt instalacyjny (`main.cmd`), skojarzony z nim plik (`install.cmd`) i pakiety Instalatora (`.msi`). `TERADATA` Pliki te instalują łączniki programu Teradata, interfejs API TPT oraz sterownik ODBC na każdym węźle platformy Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia korzystanie z Menedżera połączeń programu Teradata, źródła i miejsca docelowego. Najpierw Pobierz plik zip Tools i Utilities (TTU) 15. `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`x (na przykład) z programu [Teradata](http://partnerintelligence.teradata.com), a następnie Przekaż go razem z powyższymi `.cmd` i `.msi` plikami do kontenera.

   ![Foldery w folderze scenariusze użytkownika](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. Aby wypróbować te próbki instalacji niestandardowej, skopiuj i wklej zawartość z wybranego folderu do kontenera. Podczas aprowizacji lub zmiany konfiguracji środowiska Azure-SSIS IR przy użyciu programu PowerShell uruchom `Set-AzDataFactoryV2IntegrationRuntime` polecenie cmdlet z identyfikatorem URI sygnatury dostępu współdzielonego kontenera jako wartość nowego `SetupScriptContainerSasUri` parametru.

## <a name="next-steps"></a>Następne kroki

-   [Enterprise Edition Integration Runtime platformy Azure — SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Jak opracowywać płatne lub licencjonowane składniki niestandardowe dla środowiska Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
