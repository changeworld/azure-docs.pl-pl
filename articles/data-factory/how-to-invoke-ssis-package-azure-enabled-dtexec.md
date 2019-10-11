---
title: Wykonywanie pakietów SQL Server Integration Services (SSIS) za pomocą narzędzia dtexec z obsługą platformy Azure | Microsoft Docs
description: Dowiedz się, jak wykonywać pakiety SQL Server Integration Services (SSIS) za pomocą narzędzia dtexec z włączoną obsługą platformy Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 740e53728356755bcc42e1e0aafb64992b30e113
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249032"
---
# <a name="run-sql-server-integration-services-ssis-packages-with-azure-enabled-dtexec-utility"></a>Uruchamianie pakietów SQL Server Integration Services (SSIS) za pomocą narzędzia dtexec z obsługą platformy Azure
W tym artykule opisano narzędzie wiersza polecenia **dtexec** (**AzureDTExec**) z obsługą platformy Azure.  Służy do uruchamiania pakietów SSIS na Azure-SSIS Integration Runtime (IR) w Azure Data Factory (ADF).

Tradycyjne narzędzie **dtexec** zawiera SQL Server, zobacz dokumentację [Narzędzia dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) , aby uzyskać więcej informacji.  Jest często wywoływany przez koordynatorów lub harmonogramów innych firm, takich jak Active Batch, Control-M itp., do uruchamiania pakietów usług SSIS w środowisku lokalnym.  Nowoczesne narzędzie **AzureDTExec** zawiera narzędzie SQL Server Management Studio (SSMS).  Może być również wywoływana przez koordynatorów lub harmonogramów innych firm do uruchamiania pakietów usług SSIS na platformie Azure.  Ułatwia ona podnoszenie & przenoszenie/Migrowanie pakietów SSIS do chmury.  Po migracji, jeśli chcesz nadal korzystać z koordynatorów lub harmonogramów innych firm w codziennych operacjach, można teraz wywołać **AzureDTExec** zamiast **dtexec**.

**AzureDTExec** uruchomi pakiety jako działania pakietu SSIS w potokach ADF, zobacz artykuł [uruchamianie pakietów SSIS jako działania ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) , aby uzyskać więcej informacji.  Można ją skonfigurować za pośrednictwem programu SSMS, aby używać aplikacji Azure Active Directory (AAD), która generuje potoki w podajniku ADF.  Można go również skonfigurować w taki sposób, aby uzyskiwać dostęp do systemów plików/udziałów plików/Azure Files, w których przechowywane są pakiety.  Na podstawie wartości podanej dla opcji wywołania, **AzureDTExec** wygeneruje i uruchomi unikatowy potok ADF z uruchomionym działaniem pakietu SSIS.  Wywołanie **AzureDTExec** z tymi samymi wartościami dla jego opcji spowoduje ponowne uruchomienie istniejącego potoku.

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć **AzureDTExec**, Pobierz i zainstaluj najnowszy program SSMS (w wersji 18,3 lub nowszej) w [tym miejscu](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="configure-azuredtexec-utility"></a>Konfigurowanie narzędzia AzureDTExec
Zainstalowanie programu SSMS na komputerze lokalnym spowoduje również zainstalowanie **AzureDTExec**.  Aby skonfigurować ustawienia, uruchom program SSMS z opcją **Uruchom jako administrator** i wybierz pozycję Narzędzia kaskadowe menu rozwijane **— > Przeprowadź migrację do platformy Azure — > skonfigurować dtexec z obsługą platformy Azure**.

![Konfigurowanie menu dtexec z obsługą platformy Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

Ta akcja spowoduje wyczyszczenie okna **AzureDTExecConfig** , które należy otworzyć z uprawnieniami administracyjnymi do zapisu w pliku **AzureDTExec. Settings** .  Jeśli program SSMS nie został uruchomiony jako administrator, zostanie wyświetlone okno Kontrola konta użytkownika (UAC), aby wprowadzić hasło administratora w celu podniesienia poziomu uprawnień.

![Konfigurowanie ustawień dtexec z obsługą platformy Azure](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

W oknie **AzureDTExecConfig** można wprowadzić ustawienia konfiguracji w następujący sposób:

- Nazwa **aplikacji: wprowadź**unikatowy identyfikator aplikacji usługi AAD, która została utworzona przy użyciu odpowiednich uprawnień do generowania potoków w podajniku APD, zobacz artykuł [Tworzenie aplikacji usługi AAD i jej nazwy głównej za pośrednictwem Azure Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) artykułu, aby uzyskać więcej informacji.

- **AuthenticationKey**: Wprowadź klucz uwierzytelniania dla aplikacji usługi AAD.

- **TenantId**: wprowadź unikatowy identyfikator dzierżawy usługi AAD, pod którą zostanie utworzona aplikacja AAD.

- Subskrypcja **: wprowadź**unikatowy identyfikator subskrypcji platformy Azure, w ramach której został utworzony ADF.

- Grupa **zasobów: wprowadź**nazwę grupy zasobu platformy Azure, w której został utworzony ADF.

- **DataFactory**: Wprowadź nazwę Twojego podajnika, w którym są generowane unikatowe potoki z uruchomionym działaniem pakietu SSIS w oparciu o wartości opcji dostarczonych podczas wywoływania **AzureDTExec**.

- **IRName**: wprowadź nazwę Azure-SSIS IR w ADF, na której zostaną uruchomione pakiety określone w ścieżce Universal NAMING Convention (UNC) podczas wywoływania **AzureDTExec** .

- **PackageAccessDomain**: Wprowadź poświadczenia domeny w celu uzyskania dostępu do pakietów w ścieżce UNC określonej podczas wywoływania **AzureDTExec**.

- **PackageAccessUserName**: Wprowadź poświadczenia nazwy użytkownika w celu uzyskania dostępu do pakietów w ścieżce UNC określonej podczas wywoływania **AzureDTExec**.

- **PackageAccessPassword**: Wprowadź poświadczenia hasła, aby uzyskać dostęp do pakietów w ścieżce UNC określonej podczas wywoływania **AzureDTExec**.

- **LogPath**: wprowadź ścieżkę UNC folderu dziennika, w której będą zapisywane pliki dziennika z wykonywania pakietów na Azure-SSIS IR.

- **LogLevel**: Wprowadź wybrany zakres rejestrowania ze wstępnie zdefiniowanej **wartości null**/**podstawowa**/**pełne**opcje**wydajności** / dla wykonań pakietów na Azure-SSIS IR.

- **LogAccessDomain**: Wprowadź poświadczenia domeny w celu uzyskania dostępu do folderu dziennika w ścieżce UNC podczas zapisywania plików dziennika, wymagane, gdy określono **LogPath** i **LogLevel** nie ma **wartości null**.

- **LogAccessUserName**: Wprowadź poświadczenia nazwy użytkownika, aby uzyskać dostęp do folderu dziennika w jego ścieżce UNC podczas zapisywania plików dziennika, wymagane, gdy określono **LogPath** i **LogLevel** nie ma **wartości null**.

- **LogAccessPassword**: Wprowadź poświadczenia hasła, aby uzyskać dostęp do folderu dziennika w jego ścieżce UNC podczas zapisywania plików dziennika, wymagane, gdy określono **LogPath** i **LogLevel** nie ma **wartości null**.

- **PipelineNameHashStrLen**: Wprowadź długość ciągów skrótu do wygenerowania z wartości opcji, które są dostępne podczas wywoływania **AzureDTExec**.  Ciągi będą używane do tworzenia unikatowych nazw potoków ADF, które uruchamiają pakiety na Azure-SSIS IR.  Zwykle długość 32 znaków jest wystarczająca.

Jeśli planujesz przechowywanie pakietów i plików dziennika w lokalnych systemach plików/udziałach plików, należy przyłączyć Azure-SSIS IR do sieci wirtualnej połączonej z siecią lokalną, aby można było pobrać pakiety i napisać pliki dziennika, zobacz [Dołączanie Azure-SSIS IR do VNET](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) artykuł, aby uzyskać więcej informacji.

Aby uniknąć wyświetlania poufnych wartości zapisanych w pliku **AzureDTExec. Settings** w postaci zwykłego tekstu, firma Microsoft zakoduje je do ciągów kodowania base64.  Po wywołaniu **AzureDTExec**wszystkie ciągi kodowane algorytmem Base64 będą zdekodowane z powrotem do ich oryginalnych wartości.  Możesz zabezpieczyć plik **AzureDTExec. Settings** , ograniczając konta, które mają do niego dostęp.

## <a name="invoke-azuredtexec-utility"></a>Wywołaj narzędzie AzureDTExec
Możesz wywołać **AzureDTExec** w wierszu polecenia i podać odpowiednie wartości dla konkretnych opcji w scenariuszu przypadku użycia.

Narzędzie jest instalowane w `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`. Możesz dodać ścieżkę do zmiennej środowiskowej "PATH", aby można ją było wywołać z dowolnego miejsca.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

Wywołanie **AzureDTExec** oferuje podobne opcje jako wywołanie **dtexec**, zobacz dokumentację [Narzędzia dtexec](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) , aby uzyskać więcej informacji.  Poniżej przedstawiono opcje, które są obecnie obsługiwane:

- **/F [Iku]** : ładuje pakiet, który jest przechowywany w pliku system plików/udział plików/Azure Files.  Jako wartość tej opcji można określić ścieżkę UNC do pliku pakietu w systemie plików/udziale plików/Azure Files przy użyciu rozszerzenia dtsx.  Jeśli określona ścieżka UNC zawiera spację, należy umieścić znaki cudzysłowu wokół całej ścieżki.

- **/Conf [igFile]** : Określa plik konfiguracji, z którego mają zostać wyodrębnione wartości.  Korzystając z tej opcji, można ustawić konfigurację wykonawczą dla pakietu, która różni się od określonej w czasie projektowania.  Można przechowywać różne ustawienia w pliku konfiguracji XML, a następnie ładować je przed wykonaniem pakietu.  Aby uzyskać więcej informacji, zobacz artykuł dotyczący [konfiguracji pakietów usług SSIS](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017) .  Jako wartość tej opcji można określić ścieżkę UNC do pliku konfiguracji w systemie plików/udziale plików/Azure Files z rozszerzeniem dtsConfig.  Jeśli określona ścieżka UNC zawiera spację, należy umieścić znaki cudzysłowu wokół całej ścieżki.

- **/Conn [zenia]** : określa parametry połączenia dla istniejących menedżerów połączeń w pakiecie.  Za pomocą tej opcji można ustawić parametry połączenia w czasie wykonywania dla istniejących menedżerów połączeń w pakiecie, które różnią się od określonych w czasie projektowania.  Jako wartość tej opcji można określić ją w następujący sposób: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`.

- **/Set**: zastępuje konfigurację parametru, zmiennej, właściwości, kontenera, dostawcy dziennika, modułu wyliczającego foreach lub połączenia w pakiecie.  Tę opcję można określić wiele razy.  Jako wartość tej opcji można określić ją w następujący sposób: `property_path;value`, na przykład `\package.variables[counter].Value;1` zastępuje wartość zmiennej `counter` jako 1.  Możesz użyć Kreatora konfiguracji pakietu, aby znaleźć, skopiować i wkleić wartość `property_path` dla elementów w pakiecie, których wartość ma zostać przesłonięta, zobacz dokumentację [Kreatora konfiguracji pakietów](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014) , aby uzyskać więcej informacji.

- **/De [Crypt]** : ustawia hasło odszyfrowywania dla pakietu, który jest skonfigurowany przy użyciu **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** poziomu ochrony.

> [!NOTE]
> Wywołanie **AzureDTExec** z nowymi wartościami dla jego opcji spowoduje wygenerowanie nowego potoku z wyjątkiem opcji **/de [kryptu]** .

## <a name="next-steps"></a>Następne kroki

Po wypełnieniu unikatowych potoków z działaniem pakietu usług SSIS i uruchomieniu ich po wywołaniu **AzureDTExec**można monitorować je w portalu ADF. Aby uzyskać więcej informacji, zobacz artykuł [uruchamianie pakietów usług SSIS jako działania ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) .

> [!WARNING]
> Wygenerowany potok powinien być używany tylko przez **AzureDTExec**. Jego właściwości/parametry mogą ulec zmianie w przyszłości, dlatego nie należy ich modyfikować ani ponownie używać do innych celów, co może spowodować uszkodzenie **AzureDTExec**. W takim przypadku można zawsze usunąć potok, a **AzureDTExec** wygeneruje nowy potok przy następnym wywołaniu.
