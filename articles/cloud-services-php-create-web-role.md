---
title: Tworzenie ról sieci Web i pracowników platformy Azure dla PHP
description: Przewodnik dotyczący tworzenia ról sieci web i procesów roboczych PHP w usłudze w chmurze platformy Azure oraz konfigurowania środowiska wykonawczego PHP.
services: ''
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 54410e1e70a2ec0d3a9e2f853dc9556cd05996ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297258"
---
# <a name="create-php-web-and-worker-roles"></a>Tworzenie roli internetowej i procesu roboczego PHP

## <a name="overview"></a>Omówienie

W tym przewodniku pokazano, jak utworzyć role sieci web lub pracownika PHP w środowisku programistycznym systemu Windows, wybrać określoną wersję PHP z dostępnych wersji "wbudowanych", zmienić konfigurację PHP, włączyć rozszerzenia i wreszcie wdrożyć na platformie Azure. Opisano w nim również sposób konfigurowania roli sieci Web lub procesu roboczego do używania środowiska wykonawczego PHP (z niestandardową konfiguracją i rozszerzeniami), które udostępniasz.

Platforma Azure udostępnia trzy modele obliczeniowe dla uruchomionych aplikacji: Usługa Azure App Service, maszyny wirtualne platformy Azure i usługi w chmurze platformy Azure. Wszystkie trzy modele obsługują PHP. Usługi w chmurze, które obejmują role sieci web i pracowników, zapewniają *platformę jako usługę (PaaS).* W ramach usługi w chmurze rola sieci web udostępnia dedykowany serwer sieci Web internetowych internetowych usług informacyjnych (IIS) do obsługi aplikacji sieci web frontu. Rola procesu roboczego może uruchamiać zadania asynchroniczne, długotrwałe lub wieczyste niezależnie od interakcji użytkownika lub danych wejściowych.

Aby uzyskać więcej informacji na temat tych opcji, zobacz [Opcje hostingu obliczeń udostępniane przez platformę Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Pobierz zestaw Azure SDK dla PHP

Zestaw [SDK platformy Azure dla PHP](https://github.com/Azure/azure-sdk-for-php) składa się z kilku składników. W tym artykule użyje dwóch z nich: Azure PowerShell i emulatorów platformy Azure. Te dwa składniki można zainstalować za pośrednictwem instalatora platformy Microsoft Web Platform. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Tworzenie projektu usług w chmurze

Pierwszym krokiem w tworzeniu roli sieci web lub pracownika PHP jest utworzenie projektu usługi Azure. Projekt usługi Azure służy jako kontener logiczny dla ról sieci web i procesu roboczego i zawiera pliki definicji usługi projektu [(csdef)] i [konfiguracji usługi (cscfg).]

Aby utworzyć nowy projekt usługi Azure, uruchom program Azure PowerShell jako administrator i wykonaj następujące polecenie:

    PS C:\>New-AzureServiceProject myProject

To polecenie utworzy nowy`myProject`katalog ( ), do którego można dodać role sieci Web i procesu roboczego.

## <a name="add-php-web-or-worker-roles"></a>Dodawanie ról sieci web lub pracownika PHP

Aby dodać rolę sieci web PHP do projektu, uruchom następujące polecenie z katalogu głównego projektu:

    PS C:\myProject> Add-AzurePHPWebRole roleName

W przypadku roli procesu roboczego należy użyć tego polecenia:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> Parametr `roleName` jest opcjonalny. Jeśli zostanie pominięta, nazwa roli zostanie wygenerowana automatycznie. Pierwszą utworzoną `WebRole1`rolą internetową `WebRole2`będzie , druga będzie , i tak dalej. Pierwszą utworzoną `WorkerRole1`rolą pracownika `WorkerRole2`będzie , druga będzie i tak dalej.
>
>

## <a name="use-your-own-php-runtime"></a>Korzystaj z własnego środowiska wykonawczego PHP

W niektórych przypadkach zamiast wybierać wbudowany czas pracy PHP i konfigurować go w sposób opisany powyżej, możesz podać własny czas pracy PHP. Na przykład można użyć tego samego środowiska wykonawczego PHP w roli sieci web lub procesu roboczego, który jest używany w środowisku programistycznym. Ułatwia to upewnienie się, że aplikacja nie zmieni zachowania w środowisku produkcyjnym.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Konfigurowanie roli sieci Web do korzystania z własnego środowiska wykonawczego PHP

Aby skonfigurować rolę sieci Web do używania środowiska wykonawczego PHP, które podasz, wykonaj następujące kroki:

1. Utwórz projekt usługi Azure i dodaj rolę sieci web PHP, jak opisano wcześniej w tym temacie.
2. Utwórz `php` folder `bin` w folderze, który znajduje się w katalogu głównym roli sieci Web, a następnie dodaj do folderu `php` środowisko wykonawcze PHP (wszystkie pliki binarne, pliki konfiguracyjne, podfoldery itp.).
3. (OPCJONALNIE) Jeśli środowisko wykonawcze PHP używa [sterowników Microsoft Drivers for PHP dla programu SQL Server,][sqlsrv drivers]należy skonfigurować rolę sieci Web w celu zainstalowania [klienta macierzystego programu SQL Server 2012][sql native client] podczas jego obsługi administracyjnej. Aby to zrobić, dodaj [instalator sqlncli.msi x64] do folderu `bin` w katalogu głównym roli sieci Web. Skrypt startowy opisany w następnym kroku będzie dyskretnie uruchamiać instalatora po zainicjowaniu obsługi administracyjnej roli. Jeśli środowisko wykonawcze PHP nie używa sterowników microsoft dla php dla programu SQL Server, można usunąć następujący wiersz ze skryptu pokazanego w następnym kroku:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Zdefiniuj zadanie uruchamiania, które konfiguruje [internetowe usługi informacyjne (IIS)][iis.net] do używania środowiska wykonawczego PHP do obsługi żądań stron. `.php` Aby to zrobić, `setup_web.cmd` otwórz plik `bin` (w pliku katalogu głównego roli sieci Web) w edytorze tekstu i zastąp jego zawartość następującym skryptem:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Dodaj pliki aplikacji do katalogu głównego roli sieci Web. Będzie to katalog główny serwera sieci web.
6. Opublikuj aplikację zgodnie z opisem w sekcji [Publikuj aplikację](#publish-your-application) poniżej.

> [!NOTE]
> Skrypt `download.ps1` (w `bin` folderze katalogu głównego roli sieci web) można usunąć po zastosowaniu kroków opisanych powyżej w celu użycia własnego środowiska wykonawczego PHP.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurowanie roli procesu roboczego do korzystania z własnego środowiska wykonawczego PHP

Aby skonfigurować rolę procesu roboczego do używania środowiska wykonawczego PHP, które podasz, wykonaj następujące kroki:

1. Utwórz projekt usługi Azure i dodaj rolę pracownika PHP zgodnie z opisem opisanym wcześniej w tym temacie.
2. Utwórz `php` folder w katalogu głównym roli procesu roboczego, a następnie dodaj do folderu `php` środowisko uruchomieniowe PHP (wszystkie pliki binarne, pliki konfiguracyjne, podfoldery itp.).
3. (OPCJONALNIE) Jeśli środowisko wykonawcze PHP używa [sterowników microsoft dla PHP dla programu SQL Server,][sqlsrv drivers]należy skonfigurować rolę procesu roboczego, aby zainstalować [klienta macierzystego programu SQL Server 2012,][sql native client] gdy jest aprowizacji. Aby to zrobić, dodaj [instalator sqlncli.msi x64] do katalogu głównego roli procesu roboczego. Skrypt startowy opisany w następnym kroku będzie dyskretnie uruchamiać instalatora po zainicjowaniu obsługi administracyjnej roli. Jeśli środowisko wykonawcze PHP nie używa sterowników microsoft dla php dla programu SQL Server, można usunąć następujący wiersz ze skryptu pokazanego w następnym kroku:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Zdefiniuj zadanie `php.exe` uruchamiania, które dodaje plik wykonywalny do zmiennej środowiskowej PATH roli procesu roboczego, gdy rola jest aprowizowana. Aby to zrobić, `setup_worker.cmd` otwórz plik (w katalogu głównym roli procesu roboczego) w edytorze tekstu i zastąp jego zawartość następującym skryptem:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Dodaj pliki aplikacji do katalogu głównego roli procesu roboczego.
6. Opublikuj aplikację zgodnie z opisem w sekcji [Publikuj aplikację](#publish-your-application) poniżej.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Uruchamianie aplikacji w emulatorach obliczeniowych i pamięci masowej

Emulatory platformy Azure zapewniają środowisko lokalne, w którym można przetestować aplikację platformy Azure przed wdrożeniem jej w chmurze. Istnieją pewne różnice między emulatorów i środowiska platformy Azure. Aby lepiej to zrozumieć, zobacz [Używanie emulatora magazynu platformy Azure do tworzenia i testowania.](storage/common/storage-use-emulator.md)

Należy pamiętać, że aby korzystać z emulatora obliczeniowego, musisz mieć zainstalowany php lokalnie. Emulator obliczeniowy użyje lokalnej instalacji PHP do uruchomienia aplikacji.

Aby uruchomić projekt w emulatorach, wykonaj następujące polecenie z katalogu głównego projektu:

    PS C:\MyProject> Start-AzureEmulator

Zostaną wyświetlone dane wyjściowe podobne do tych: 

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Możesz zobaczyć aplikację uruchomiona w emulatorze, otwierając przeglądarkę sieci web i`http://127.0.0.1:81` przeglądając adres lokalny wyświetlany w danych wyjściowych (w powyższym przykładzie danych wyjściowych).

Aby zatrzymać emulatory, wykonaj następujące polecenie:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publikowanie aplikacji

Aby opublikować aplikację, należy najpierw zaimportować ustawienia publikowania przy użyciu polecenia cmdlet [Import-AzurePublishSettingsFile.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) Następnie można opublikować aplikację przy użyciu polecenia cmdlet [Publish-AzureServiceProject.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) Aby uzyskać informacje dotyczące logowania, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Centrum programistów PHP](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definicja usługi (csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[konfiguracja usługi (cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[instalator sqlncli.msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648
