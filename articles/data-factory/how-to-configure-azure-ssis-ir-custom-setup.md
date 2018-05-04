---
title: Instalacja niestandardowa środowiska uruchomieniowego integracji usług SSIS Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia interfejsu niestandardowego Instalatora dla środowiska uruchomieniowego integracji usług SSIS Azure
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: douglasl
ms.openlocfilehash: af92eec8b6461563a366805d5eb4cbb964b028a5
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="custom-setup-for-the-azure-ssis-integration-runtime"></a>Instalacja niestandardowa środowiska uruchomieniowego integracji usług SSIS Azure

Instalacja niestandardowa interfejs środowiska uruchomieniowego integracji usług SSIS Azure umożliwia zmianę domyślnego, konfiguracji lub środowisku (na przykład do uruchomienia dodatkowych usług systemu Windows) lub zainstalowanie dodatkowych składników (na przykład, zestawy, sterowniki, lub Extensions) na każdym węźle sieci podczerwieni Azure SSIS. Ogólnie rzecz biorąc zapewnia interfejs do dodania własnych kroków konfiguracji podczas inicjowania obsługi administracyjnej lub zmiana konfiguracji sieci IR. Azure SSIS

Skonfiguruj ustawienia niestandardowe poprzez przygotowanie skrypt i skojarzone z nią pliki i przekazywanie ich do kontenera obiektów blob na koncie magazynu Azure. Podaj dostępu sygnatury dostępu Współdzielonego zasobu identyfikator URI (Uniform) dla kontenera programu obsługi administracyjnej lub ponownie skonfigurować Twoje podczerwieni Azure SSIS. Każdy węzeł z IR Azure SSIS następnie pobiera skrypt i skojarzone z nią pliki z Twojej kontenera i uruchamia niestandardowych ustawień z podwyższonym poziomem uprawnień. Po zakończeniu instalacji niestandardowej, każdy węzeł przekazuje standardowe dane wyjściowe wykonania a inne dzienniki do Twojej kontenera.

Można zainstalować składniki wolne lub użytkowaniem i płatną lub licencjonowanych składników. Jeśli jesteś niezależnym dostawcą oprogramowania, zobacz [tworzenia płatnej lub licencjonowanych składników do IR Azure SSIS](how-to-develop-azure-ssis-ir-licensed-components.md).


## <a name="current-limitations"></a>Bieżące ograniczenia

-   Jeśli chcesz użyć `gacutil.exe` do zainstalowania zestawów w globalnej pamięci podręcznej zestawów (GAC), należy podać go jako część ustawień niestandardowych, lub użyj kopii w kontenerze publicznej wersji zapoznawczej.

-   Należy przyłączyć Twojej IR Azure SSIS z niestandardowych ustawień do sieci wirtualnej, tylko usługi Azure Resource Manager wirtualnej jest obsługiwana. Klasyczne sieci wirtualnej nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Aby dostosować Twojej IR Azure SSIS, należy następujących czynności:

-   [Subskrypcja platformy Azure](https://azure.microsoft.com/)

-   [Serwer bazy danych SQL Azure lub zarządzane wystąpienia](https://ms.portal.azure.com/#create/Microsoft.SQLServer)

-   [Udostępnianie Twojego IR Azure SSIS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

-   [Konto usługi Azure Storage](https://azure.microsoft.com/services/storage/). Do instalacji niestandardowej przekazywanie i przechowywania skrypt instalacji niestandardowych i skojarzone z nią pliki w kontenerze obiektów blob. Proces instalacji niestandardowej przekazuje również jego dzienniki wykonywania do tego samego kontenera obiektów blob.

## <a name="instructions"></a>Instrukcje

2.  Pobierz i zainstaluj [programu Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) (w wersji 5.4 lub nowszej).

3.  Przygotuj skrypt instalacji niestandardowych i jego skojarzone pliki (na przykład, .bat, .cmd, .exe, dll, msi lub .ps1).

    1.  Musi mieć plik skryptu o nazwie `main.cmd`, która jest punkt wejścia ustawienia niestandardowe.

    2.  Jeśli chcesz, dodatkowe dzienniki generowane przez inne narzędzia (na przykład `msiexec.exe`), które zostaną przekazane do programu kontenera należy określić zmienną środowiskową wstępnie zdefiniowanych `CUSTOM_SETUP_SCRIPT_LOG_DIR` jako folder dziennika w skryptach (na przykład `msiexec /i xxx.msi /quiet
        /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

4.  Pobieranie, instalowanie i uruchamianie [Eksploratora usługi Storage Azure](http://storageexplorer.com/).

    1.  W obszarze **(lokalny i dołączonego)** wybierz prawa **kont magazynu** i wybierz **nawiązywanie połączenia z usługą Azure storage**.

       ![Łączenie z usługą Azure Storage](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

    2.  Wybierz **użyć nazwy konta magazynu i klucza** i wybierz **dalej**.

       ![Używanie nazwy i klucza konta magazynu](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

    3.  Wprowadź nazwę konta usługi Azure Storage i klucz, zaznacz **dalej**, a następnie wybierz **Connect**.

       ![Podaj nazwę konta magazynu i klucz](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

    4.  W obszarze połączone konta magazynu Azure, kliknij prawym przyciskiem myszy **kontenerów obiektów Blob**, wybierz pozycję **Tworzenie kontenera obiektów Blob**oraz nazwę nowego kontenera.

       ![Tworzenie kontenera obiektów blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

    5.  Wybierz nowy kontener i przesłać skrypt instalacji niestandardowych i skojarzone z nią pliki. Upewnij się, że przekazywanie `main.cmd` na najwyższym poziomie kontenera, nie znajduje się w dowolnym folderze. 

       ![Przekazywanie plików do kontenera obiektów blob](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

    6.  Kliknij prawym przyciskiem myszy kontener, a następnie wybierz **Uzyskaj sygnaturę dostępu współdzielonego**.

       ![Pobierz sygnatura dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

    7.  Tworzenie identyfikatora URI połączenia SAS dla kontenera sieci i czas wygaśnięcia wystarczająco długi, a odczytu + zapisu + listę uprawnień. Potrzebujesz identyfikatora URI połączenia SAS, aby pobrać i uruchamiania skryptu instalacji niestandardowej i skojarzone z nią pliki w przypadku dowolnego węzła sieci IR Azure SSIS zostanie odtworzone z obrazu. Musisz mieć uprawnienia zapisu do przekazania dzienniki wykonywania instalacji.

       ![Generowanie sygnatura dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

    8.  Skopiuj i Zapisz identyfikatora URI połączenia SAS z kontenera.

       ![Skopiuj i Zapisz sygnatura dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

    9.  Podczas obsługi administracyjnej lub ponownie skonfigurować Twoje IR Azure SSIS przy użyciu programu PowerShell, przed rozpoczęciem pracy z IR Azure SSIS Uruchom `Set-AzureRmDataFactoryV2IntegrationRuntime` polecenia cmdlet z identyfikatora URI połączenia SAS z kontenera jako wartość nowych `SetupScriptContainerSasUri` parametru. Na przykład:

       ```powershell
       Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                  -Name $MyAzureSsisIrName `
                                                  -ResourceGroupName $MyResourceGroupName `
                                                  -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

       Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                                    -Name $MyAzureSsisIrName `
                                                    -ResourceGroupName $MyResourceGroupName
       ```

    10.  Po zakończeniu niestandardowych ustawień i uruchamia Twojej IR Azure SSIS, można znaleźć standardowego wyjścia `main.cmd` i wykonywania innych dzienniki w `main.cmd.log` folder z kontenera magazynu.

2.  Aby wyświetlić inne przykłady Instalacja niestandardowa, połączyć się z kontenerem publicznej wersji zapoznawczej Eksplorator magazynu Azure.

    a.  W obszarze **(lokalny i dołączonego)**, kliknij prawym przyciskiem myszy **kont magazynu**, wybierz pozycję **nawiązywanie połączenia z usługą Azure storage**, wybierz pozycję **Użyj ciągu połączenia lub dostępu współdzielonego Identyfikator URI sygnatury**, a następnie wybierz **dalej**.

       ![Łączenie się z magazynem Azure z sygnaturą dostępu współdzielonego](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

    b.  Wybierz **korzystania z identyfikatora URI sygnatury dostępu Współdzielonego** i wprowadź następujący identyfikator URI sygnatury dostępu Współdzielonego dla kontenera, w publicznej wersji zapoznawczej. Wybierz **dalej**, a następnie wybierz polecenie **Connect**.

       `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

       ![Podaj sygnatura dostępu współdzielonego dla kontenera](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

    c. Wybierz kontener połączonych w publicznej wersji zapoznawczej, a następnie kliknij dwukrotnie ikonę `CustomSetupScript` folderu. W tym folderze są następujące elementy:

       1. A `Sample` folder zawierający niestandardowe Instalatora, aby zainstalować podstawowe zadania w każdym węźle sieci IR. Azure SSIS Zadanie nie działa jednak uśpienia przez kilka sekund. Folder zawiera także `gacutil` folder, który zawiera `gacutil.exe`.

       2. A `UserScenarios` folder, który zawiera osiem konfiguracje niestandardowe dla scenariuszy rzeczywistego użytkownika.

    ![Zawartość kontenera publicznej wersji zapoznawczej](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

    d. Kliknij dwukrotnie `UserScenarios` folderu. W tym folderze są następujące elementy:

       1. A `.NET FRAMEWORK 3.5` folder, który zawiera Instalacja niestandardowa, aby zainstalować wcześniejszą wersję programu .NET Framework, które mogą być wymagane dla składników niestandardowych w każdym węźle sieci podczerwieni Azure SSIS.

       2. A `BCP` folder zawierający niestandardowe Instalatora, aby zainstalować narzędzia wiersza polecenia programu SQL Server (`MsSqlCmdLnUtils.msi`), w tym kopiowania zbiorczego (`bcp`), w każdym węźle sieci podczerwieni Azure SSIS.

       3. `EXCEL` Folder zawierający niestandardowe Instalatora, aby zainstalować zestawy open source (`DocumentFormat.OpenXml.dll`, `ExcelDataReader.DataSet.dll`, i `ExcelDataReader.dll`) w każdym węźle sieci podczerwieni Azure SSIS.

       4. `MSDTC` Folder zawierający niestandardowe ustawienia do modyfikowania konfiguracji sieci i zabezpieczeń dla wystąpienia koordynatora transakcji rozproszonych (MSDTC) na każdym węźle sieci IR. Azure SSIS.

       5. `ORACLE ENTERPRISE` Folder, który zawiera skrypt instalacji niestandardowej (`main.cmd`) i pliku konfiguracji instalacji dyskretnej (`client.rsp`) do zainstalowania sterownika Oracle OCI w każdym węźle z Azure SSIS IR Enterprise Edition (wersja zapoznawcza prywatnych). Ta konfiguracja pozwala używać Menedżera połączeń Oracle, źródłowym i docelowym. Najpierw należy pobrać `winx64_12102_client.zip` z [Oracle](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) , a następnie przekazać go razem z `main.cmd` i `client.rsp` do Twojej kontenera. Jeśli używasz nazwę nawiązać połączenia z programem Oracle, należy pobrać `tnsnames.ora`, edytowania i przekaż go do Twojego kontenera, mogą zostać skopiowane do folderu instalacji programu Oracle podczas instalacji.

       6. `ORACLE STANDARD` Folder, który zawiera skrypt instalacji niestandardowej (`main.cmd`) do zainstalowania sterownika Oracle ODP.NET w każdym węźle sieci podczerwieni Azure SSIS. Ta konfiguracja pozwala używać Menedżera połączeń ADO.NET, źródłowym i docelowym. Najpierw pobierz `ODP.NET_Managed_ODAC122cR1.zip` z [Oracle](http://www.oracle.com/technetwork/database/windows/downloads/index-090165.html), a następnie przekazać go razem z `main.cmd` do Twojej kontenera.

       7. `SAP BW` Folder, który zawiera skrypt instalacji niestandardowej (`main.cmd`) Aby zainstalować zestaw łącznik .NET programu SAP (`librfc32.dll`) w każdym węźle z Azure SSIS IR Enterprise Edition (wersja zapoznawcza prywatnych). Ta konfiguracja pozwala używać Menedżera połączeń programu SAP BW, źródłowym i docelowym. Najpierw należy przekazać 64-bitowy lub 32-bitowej wersji systemu `librfc32.dll` z folderu instalacyjnego programu SAP do kontenera, wraz z `main.cmd`. Skrypt następnie kopiuje zestawu SAP do `%windir%\SysWow64` lub `%windir%\System32` folderu podczas instalacji.

       8. A `STORAGE` folder zawierający niestandardowe ustawienia do zainstalowania programu Azure PowerShell w każdym węźle sieci podczerwieni Azure SSIS. Ta konfiguracja pozwala wdrażać i wykonywania SSIS pakietów uruchomionymi [skryptów programu PowerShell do manipulowania konta magazynu Azure](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell). Kopiuj `main.cmd`, próbkę `AzurePowerShell.msi` (lub zainstaluj najnowszą wersję) i `storage.ps1` z kontenerem. Użyj PowerShell.dtsx jako szablon dla pakietów. Szablon pakietu łączy [zadań pobieranie obiektów Blob Azure](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task), które pliki do pobrania `storage.ps1` jako skrypt programu PowerShell można modyfikować i [wykonania zadanie procesu](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) , który jest wykonywany skrypt w każdym węźle.

       9. A `TERADATA` folder, który zawiera skrypt instalacji niestandardowej (`main.cmd)`, jego skojarzony plik (`install.cmd`) i pakietów Instalatora (`.msi`). Te pliki Zainstaluj łączniki programu Teradata, interfejsu API TPT i sterownik ODBC w każdym węźle sieci Azure SSIS IR Enterprise Edition (podglądzie prywatnym). Ta konfiguracja pozwala używać programu Teradata Menedżera połączeń, źródłowym i docelowym. Najpierw Pobierz plik zip 15.x Teradata narzędzi i narzędzia (TTU) (na przykład `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`) z [Teradata](http://partnerintelligence.teradata.com)i przekazać go razem z powyższych `.cmd` i `.msi` plików do sieci kontenera.

    ![Foldery w folderze scenariusze użytkowników](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

    e. Aby wypróbować te przykłady Instalacja niestandardowa, skopiuj i Wklej zawartość z wybranego folderu do kontenera sieci. Podczas obsługi administracyjnej lub ponownie skonfigurować Twoje IR Azure SSIS przy użyciu programu PowerShell, uruchom `Set-AzureRmDataFactoryV2IntegrationRuntime` polecenia cmdlet z identyfikatora URI połączenia SAS z kontenera jako wartość nowych `SetupScriptContainerSasUri` parametru.

## <a name="next-steps"></a>Kolejne kroki

-   [Środowisko uruchomieniowe integracji usług SSIS Azure w wersji Enterprise](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Jak wdrażać płatnej lub licencji niestandardowych składników środowiska uruchomieniowego integracji usług SSIS Azure](how-to-develop-azure-ssis-ir-licensed-components.md)