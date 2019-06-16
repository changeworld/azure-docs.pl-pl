---
title: Dostosowywanie ustawień środowiska Azure-SSIS integration Runtime | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób instalowania dodatkowych składników, lub zmienić ustawienia za pomocą interfejsu niestandardowego Instalatora dla środowiska Azure-SSIS integration runtime
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
ms.openlocfilehash: cfa9d6a1a287281bec91facf04c73506db81f84a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64711558"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Dostosowywanie ustawień środowiska Azure-SSIS integration Runtime

Interfejs niestandardowego Instalatora dla środowiska Azure-SSIS Integration Runtime zapewnia interfejs do dodawania własnych kroki konfiguracji podczas inicjowania obsługi lub ponownej konfiguracji z usługi Azure-SSIS IR. Instalacja niestandardowa pozwala zmienić domyślne operacyjnych, konfiguracji lub środowisku (na przykład, aby uruchomić dodatkowe usługi Windows lub utrwalić poświadczenia dostępu do udziałów plików) lub zainstalowania dodatkowych składników (na przykład, zestawy sterowników i rozszerzenia) w każdym węźle usługi Azure-SSIS IR.

Przygotowywanie skryptu i skojarzone z nią pliki i przekazujemy je do kontenera obiektów blob na koncie usługi Azure Storage, należy skonfigurować ustawienia niestandardowe. Podaj sygnatury dostępu współdzielonego (SAS) jednolite zasobów identyfikator (URI) dla kontenera usługi aprowizacji lub zmienić konfigurację usługi Azure-SSIS IR. Każdy węzeł środowiska IR Azure-SSIS następnie pobrać skrypt i skojarzone z nią pliki z kontenera i Twoje niestandardowy program instalacyjny zostanie uruchomiony z podwyższonym poziomem uprawnień. Po zakończeniu instalacji niestandardowej każdy węzeł wysyła standardowe dane wyjściowe wykonywania i innych dzienników do kontenera.

Można zainstalować składniki bezpłatnej i bez licencji i płatna lub licencjonowanych składników. Jeśli jesteś niezależnym dostawcą oprogramowania, zobacz [sposobu tworzenia wersji płatnej lub licencjonowane składniki dla środowiska Azure-SSIS IR](how-to-develop-azure-ssis-ir-licensed-components.md).

> [!IMPORTANT]
> Węzły w wersji 2 z serii Azure-SSIS IR nie są odpowiednie dla ustawień niestandardowych, aby zamiast tego użyj węzły serii v3.  Jeśli używasz już węzły serii v2, Przełącz używają węzłów serii v3 tak szybko, jak to możliwe.

## <a name="current-limitations"></a>Bieżące ograniczenia

-   Jeśli chcesz używać `gacutil.exe` do instalowania zestawów w globalnej pamięci podręcznej zestawów (GAC), musisz podać `gacutil.exe` jako część instalacji niestandardowej lub użyj kopii podane w kontenerze publicznej wersji zapoznawczej.

-   Jeśli chcesz odwoływać się do podfolderu w skrypcie, `msiexec.exe` nie obsługuje `.\` notacji, aby odwoływać się do folderu głównego. Użyj polecenia podobnego `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` zamiast `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`.

-   Jeśli zachodzi potrzeba dołączania środowiska IR Azure-SSIS przy użyciu instalacji niestandardowej do sieci wirtualnej, jest obsługiwane tylko sieć wirtualną usługi Azure Resource Manager. Klasyczna sieć wirtualna nie jest obsługiwane.

-   Udział administracyjny nie jest obecnie obsługiwana na platformie Azure-SSIS IR.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dostosowywanie środowiska IR Azure-SSIS, potrzebne są następujące elementy:

-   [Subskrypcja platformy Azure](https://azure.microsoft.com/)

-   [Serwer usługi Azure SQL Database lub wystąpienia zarządzanego](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Aprowizowanie środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Konto usługi Azure Storage](https://azure.microsoft.com/services/storage/). Dla niestandardowego instalatora przekazywanie i przechowywanie skrypt instalacji niestandardowej i skojarzone z nią pliki w kontenerze obiektów blob. Proces instalacji niestandardowej również przekazuje jej dzienniki wykonywania do tego samego kontenera obiektów blob.

## <a name="instructions"></a>Instrukcje

1. Pobierz i zainstaluj [programu Azure PowerShell](/powershell/azure/install-az-ps).

1. Przygotuj skrypt instalacji niestandardowej i jego skojarzone pliki (na przykład, .bat, .cmd, .exe, .dll, msi lub ps1).

   1.  Konieczne jest posiadanie plik skryptu o nazwie `main.cmd`, który jest punktem wejścia ustawień niestandardowych.

   1.  Jeśli chcesz, aby dodatkowe dzienniki generowane przez innych narzędzi (na przykład `msiexec.exe`) do przekazania do kontenera, określ zmienną środowiskową wstępnie zdefiniowanych `CUSTOM_SETUP_SCRIPT_LOG_DIR` jako folder dziennika w skryptach (na przykład `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. Pobieranie, instalowanie i uruchamianie [Eksploratora usługi Azure Storage](https://storageexplorer.com/).

   1. W obszarze **(lokalne i dołączone)** po prawej stronie wybierz **kont magazynu** i wybierz **łączenie z usługą Azure storage**.

      ![Łączenie z usługą Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. Wybierz **Użyj klucza i nazwy konta magazynu** i wybierz **dalej**.

      ![Używanie nazwy i klucza konta magazynu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Wprowadź nazwę konta usługi Azure Storage i klucz, wybierz opcję **dalej**, a następnie wybierz pozycję **Connect**.

      ![Podaj nazwę konta magazynu i klucza](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. W ramach konta usługi Azure Storage, usługi połączone, kliknij prawym przyciskiem myszy **kontenery obiektów Blob**, wybierz opcję **Utwórz kontener obiektów Blob**i nadaj nazwę nowego kontenera.

      ![Tworzenie kontenera obiektów blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. Wybierz nowy kontener, a następnie Przekaż skrypt instalacji niestandardowej oraz skojarzone z nią pliki. Upewnij się, że przekazujesz `main.cmd` na najwyższym poziomie kontenera, nie w dowolnym folderze. Ponadto upewnij się, że kontener zawiera tylko pliki wymagane ustawienia niestandardowe, więc pobierania ich do środowiska IR Azure-SSIS później nie będzie zająć dużo czasu. Maksymalny czas instalacji niestandardowej aktualnie jest ustawiony na 45 minut, zanim upłynie limit czasu i obejmuje to czas, aby pobrać wszystkie pliki z kontenera i zainstalowania go na platformie Azure-SSIS IR. Jeśli potrzebne jest dłuższy okres, zgłoś bilet pomocy technicznej.

      ![Przekazywanie plików do kontenera obiektów blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. Kliknij prawym przyciskiem myszy kontener, a następnie wybierz pozycję **Uzyskaj sygnaturę dostępu współdzielonego**.

      ![Uzyskaj sygnaturę dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. Z czasem wygaśnięcia wystarczająco długi i Odczyt, tworzenie identyfikatora URI sygnatury dostępu Współdzielonego kontenera, zapisu i listy uprawnień. Należy pobrać i uruchomić skrypt instalacji niestandardowej i skojarzone z nią pliki, zawsze wtedy, gdy dowolny węzeł środowiska IR Azure-SSIS odtwarzany z obrazu ponownym identyfikator URI sygnatury dostępu Współdzielonego. Musisz mieć uprawnienia zapisu można przekazać dzienniki wykonywania instalacji.

      > [!IMPORTANT]
      > Upewnij się, że identyfikator URI sygnatury dostępu Współdzielonego nie wygasa i Instalacja niestandardowa zasoby są zawsze dostępne podczas całego cyklu życia środowiska IR Azure-SSIS, od tworzenia do usunięcia, zwłaszcza, jeśli regularnie zatrzymujesz i uruchamiasz środowiska IR Azure-SSIS w tym okresie.

      ![Generuj sygnaturę dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. Skopiuj i Zapisz identyfikator URI sygnatury dostępu Współdzielonego kontenera.

      ![Skopiuj i Zapisz sygnatura dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

   1. Podczas aprowizowania lub ponownie skonfigurować środowiska IR Azure-SSIS przy użyciu interfejsu użytkownika usługi Data Factory, przed rozpoczęciem korzystania z usługi Azure-SSIS IR, wprowadź identyfikator URI sygnatury dostępu Współdzielonego kontenera w odpowiednim polu na **Zaawansowane ustawienia** panelu:

      ![Wprowadź sygnaturę dostępu współdzielonego](media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

      W przypadku udostępniania lub zmienić konfigurację środowiska IR Azure-SSIS przy użyciu programu PowerShell, przed rozpoczęciem korzystania z usługi Azure-SSIS IR, uruchomić `Set-AzDataFactoryV2IntegrationRuntime` polecenia cmdlet z identyfikatora URI sygnatury dostępu Współdzielonego kontenera jako wartość dla nowych `SetupScriptContainerSasUri` parametru. Na przykład:

      ```powershell
      Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                -Name $MyAzureSsisIrName `
                                                -ResourceGroupName $MyResourceGroupName `
                                                -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

      Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName
      ```

   1. Po zakończeniu ustawień niestandardowych i uruchamia środowiska IR Azure-SSIS, można znaleźć standardowe dane wyjściowe `main.cmd` i loguje się do niego inne wykonywania `main.cmd.log` folderu kontenera magazynu.

1. Aby zobaczyć inne przykłady Instalacja niestandardowa, należy połączyć się z kontenerem publicznej wersji zapoznawczej, za pomocą Eksploratora usługi Azure Storage.

   a.  W obszarze **(lokalne i dołączone)** , kliknij prawym przyciskiem myszy **kont magazynu**, wybierz opcję **łączenie z usługą Azure storage**, wybierz opcję **za pomocą parametrów połączenia lub dostępu współdzielonego identyfikatora URI sygnatury**, a następnie wybierz pozycję **dalej**.

      ![Łączenie się z magazynem platformy Azure przy użyciu sygnatura dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b.  Wybierz **korzystania z identyfikatora URI sygnatury dostępu Współdzielonego** i wprowadź następujący identyfikator URI sygnatury dostępu Współdzielonego dla kontenera publicznej wersji zapoznawczej. Wybierz **dalej**, a następnie wybierz **Connect**.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![Podaj sygnaturę dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. Wybierz kontener połączonych publicznej wersji zapoznawczej, a następnie kliknij dwukrotnie ikonę `CustomSetupScript` folderu. W tym folderze są następujące elementy:

      1. A `Sample` folder zawierający niestandardowe Instalatora, aby zainstalować podstawowe zadania w każdym węźle usługi Azure-SSIS IR. Zadanie nie działa jednak uśpienia przez kilka sekund. Folder zawiera także `gacutil` folder, którego cała zawartość (`gacutil.exe`, `gacutil.exe.config`, i `1033\gacutlrc.dll`) mogą być kopiowane się do kontenera. Ponadto `main.cmd` zawiera komentarze, aby utrwalić poświadczenia dostępu do udziałów plików.

      1. A `UserScenarios` folder, który zawiera kilka niestandardowych ustawień dla rzeczywiste scenariusze użytkowników.

   ![Zawartość kontenerów publicznej wersji zapoznawczej](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   d. Kliknij dwukrotnie `UserScenarios` folderu. W tym folderze są następujące elementy:

      1. A `.NET FRAMEWORK 3.5` folder zawierający niestandardowe Instalatora, aby zainstalować starszą wersję programu .NET Framework, które mogą być wymagane dla niestandardowych składników w każdym węźle usługi Azure-SSIS IR.

      1. A `BCP` folder zawierający niestandardowe Instalatora, aby zainstalować narzędzia wiersza polecenia programu SQL Server (`MsSqlCmdLnUtils.msi`), w tym programu do kopiowania zbiorczego (`bcp`), w każdym węźle usługi Azure-SSIS IR.

      1. `EXCEL` Folder, który zawiera niestandardowe ustawienia do instalowania zestawów typu open source (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`, i `ExcelDataReader.dll`) w każdym węźle usługi Azure-SSIS IR.

      1. `ORACLE ENTERPRISE` Folder, który zawiera skrypt instalacji niestandardowej (`main.cmd`) i plik konfiguracji instalacji dyskretnej (`client.rsp`) Aby zainstalować łączniki bazy danych Oracle i OCI sterownika w każdym węźle Twojego środowiska Azure-SSIS IR Enterprise Edition. Ta konfiguracja pozwala Użyj Menedżera połączeń bazy danych Oracle, źródłowym i docelowym. Najpierw pobierz v5.0 Connectors firmy Microsoft na oprogramowanie Oracle (`AttunitySSISOraAdaptersSetup.msi` i `AttunitySSISOraAdaptersSetup64.msi`) z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) i najnowszych klienta Oracle — na przykład `winx64_12102_client.zip` — od [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html), następnie przekazać je wszystkie razem z `main.cmd` i `client.rsp` do kontenera. Jeśli używasz TNS nawiązać połączenia z programem Oracle, należy również pobrać `tnsnames.ora`, edytować go i przekaż go do kontenera, dzięki czemu mogą być kopiowane do folderu instalacyjnego programu Oracle podczas instalacji.

      1. `ORACLE STANDARD ADO.NET` Folder, który zawiera skrypt instalacji niestandardowej (`main.cmd`) do zainstalowania sterownika Oracle ODP.NET w każdym węźle usługi Azure-SSIS IR. Ta konfiguracja pozwala Użyj Menedżera połączeń ADO.NET, źródłowym i docelowym. Najpierw pobierz najnowszy sterownik Oracle ODP.NET — na przykład `ODP.NET_Managed_ODAC122cR1.zip` — od [Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), a następnie przekaż go razem z `main.cmd` do kontenera.
       
      1. `ORACLE STANDARD ODBC` Folder, który zawiera skrypt instalacji niestandardowej (`main.cmd`) Aby zainstalować sterownik ODBC dla bazy danych Oracle i skonfigurować DSN w każdym węźle usługi Azure-SSIS IR. Ta konfiguracja pozwala za pomocą Menedżera połączeń ODBC/źródłowa/docelowa lub połączenia zapytania menedżera/źródła zasilania rodzaj źródła danych ODBC do łączenia się z serwerem Oracle. Najpierw pobierz najnowsze błyskawiczne wersję klienta Oracle (Pakiet podstawowy lub podstawowe pakiet Lite) i pakiet ODBC — na przykład pakietów 64-bitowym z [tutaj](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (pakiet podstawowego: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, podstawowe Lite pakietu: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, pakiet ODBC : `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) lub pakietów 32-bitowy z [tutaj](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (pakiet podstawowego: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, podstawowe Lite pakietu: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, pakiet ODBC: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`), a następnie przekaż je razem z `main.cmd` do kontener.

      1. `SAP BW` Folder, który zawiera skrypt instalacji niestandardowej (`main.cmd`) Aby zainstalować zestaw łącznika SAP .NET (`librfc32.dll`) w każdym węźle Twojego środowiska Azure-SSIS IR Enterprise Edition. Ta konfiguracja pozwala Użyj Menedżera połączeń systemu SAP BW, źródłowym i docelowym. Najpierw należy przekazać 64-bitowe czy 32-bitową wersję `librfc32.dll` z folderu instalacyjnego programu SAP do kontenera, wraz z `main.cmd`. Skrypt następnie kopiuje zestawu SAP do `%windir%\SysWow64` lub `%windir%\System32` folderu podczas instalacji.

      1. A `STORAGE` folder, który zawiera niestandardowe ustawienia instalacji programu Azure PowerShell w każdym węźle usługi Azure-SSIS IR. Ta konfiguracja umożliwia wdrażanie, a następnie uruchom SSIS umieszcza uruchamianą [skryptów programu PowerShell do manipulowania konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Kopiuj `main.cmd`, próbkę `AzurePowerShell.msi` (lub zainstaluj najnowszą wersję) i `storage.ps1` do kontenera. Na użytek PowerShell.dtsx jako szablon pakietów. Szablon pakietu łączy [zadania pobierania obiektów Blob Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), jakie pliki do pobrania `storage.ps1` jako skrypt programu PowerShell można modyfikować i [wykonać zadanie procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , który jest wykonywany skrypt w każdym węźle.

      1. A `TERADATA` folder, który zawiera skrypt instalacji niestandardowej (`main.cmd`), jego skojarzonego pliku (`install.cmd`) i pakiety instalacyjne (`.msi`). Te pliki zainstalować Teradata łączników interfejsu API TPT i sterownik ODBC w każdym węźle Twojego środowiska Azure-SSIS IR Enterprise Edition. Ta konfiguracja umożliwia używanie, Teradata Menedżera połączeń, źródłowym i docelowym. Najpierw Pobierz plik zip 15.x Teradata narzędzia i programy narzędziowe (TTU) (na przykład `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) z [Teradata](http://partnerintelligence.teradata.com), a następnie przekaż go wraz z powyższymi `.cmd` i `.msi` plików do kontenera.

   ![Folderów w folderze scenariusze użytkownika](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   e. Aby wypróbować te przykłady Instalacja niestandardowa, skopiuj i Wklej zawartość z wybrany folder do kontenera. Po aprowizacji lub zmienić konfigurację środowiska IR Azure-SSIS przy użyciu programu PowerShell, uruchom `Set-AzDataFactoryV2IntegrationRuntime` polecenia cmdlet z identyfikatora URI sygnatury dostępu Współdzielonego kontenera jako wartość dla nowych `SetupScriptContainerSasUri` parametru.

## <a name="next-steps"></a>Kolejne kroki

-   [Enterprise Edition, środowiska Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Jak tworzyć aplikacje płatne lub licencjonowane niestandardowych składników dla środowiska Azure-SSIS integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
