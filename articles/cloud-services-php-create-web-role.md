---
title: Utwórz role sieć web i procesu roboczego platformy Azure dla języka PHP
description: Podręcznik tworzenia ról sieć web i proces roboczy języka PHP w usłudze w chmurze platformy Azure w celu konfigurowania środowiska uruchomieniowego języka PHP.
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
ms.openlocfilehash: 83834104dd73e4381947903196ad35c3497b64a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337565"
---
# <a name="create-php-web-and-worker-roles"></a>Tworzenie roli internetowej i procesu roboczego PHP

## <a name="overview"></a>Omówienie

Tym przewodniku pokazano sposób tworzenia ról sieć web i proces roboczy języka PHP w środowisku programowania Windows, wybierz określoną wersję programu PHP z wersji "wbudowane", która jest dostępna, zmiany konfiguracji PHP, Włącz rozszerzenia i na koniec wdrażanie na platformie Azure. On również zawiera opis sposobu konfigurowania roli sieci web lub proces roboczy do użycia środowiska uruchomieniowego języka PHP (przy użyciu niestandardowej konfiguracji i rozszerzenia) przez Ciebie.

Platforma Azure udostępnia trzy modele obliczeniowe na potrzeby uruchamiania aplikacji: Usługa Azure App Service, usługa Azure Virtual Machines i usług Azure Cloud Services. Wszystkie trzy modele obsługują PHP. Oferuje usługi w chmurze, które obejmują role sieć web i proces roboczy, *platforma jako usługa (PaaS)*. W ramach usługi w chmurze rola internetowa zapewnia dedykowany serwer sieci web usług Internet Information Services (IIS) do hostowania aplikacji frontonu sieci web. Rola procesu roboczego, można uruchomić asynchroniczne, długotrwałe lub ciągłe zadania niezależne od interakcji z użytkownikiem lub danych wejściowych.

Aby uzyskać więcej informacji o tych opcjach, zobacz [udostępnianych przez platformę Azure opcji hostowania obliczeń](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Pobierz zestaw Azure SDK dla języka PHP

[Zestawu Azure SDK dla języka PHP](php-download-sdk.md) składa się z kilku składników. W tym artykule użyje dwa z nich: Program Azure PowerShell i emulatory systemu Azure. Te dwa składniki można zainstalować za pomocą Instalatora platformy sieci Web firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Tworzenie projektu usługi w chmurze

Pierwszym krokiem w tworzeniu rola sieć web lub proces roboczy języka PHP jest utworzenie projektu usługi platformy Azure. projekt usługi Azure służy jako kontener logiczny dla ról sieć web i procesu roboczego i zawiera projektu [usługi (csdef) definicji] i [Konfiguracja usługi (cscfg)] plików.

Aby utworzyć nowy projekt usługi Azure, uruchomić programu Azure PowerShell jako administrator i uruchom następujące polecenie:

    PS C:\>New-AzureServiceProject myProject

To polecenie spowoduje utworzenie nowego katalogu (`myProject`) do którego można dodać ról sieć web i proces roboczy.

## <a name="add-php-web-or-worker-roles"></a>Dodawanie ról sieć web i proces roboczy języka PHP

Aby dodać rolę sieci web języka PHP do projektu, uruchom następujące polecenie w katalogu głównym projektu:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Dla roli procesu roboczego Użyj tego polecenia:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> `roleName` Parametr jest opcjonalny. Jeśli zostanie pominięty, nazwa roli zostanie wygenerowany automatycznie. Będzie pierwsza rola sieci web utworzone `WebRole1`, drugi będzie `WebRole2`i tak dalej. Będzie pierwsza rola proces roboczy utworzony `WorkerRole1`, drugi będzie `WorkerRole2`i tak dalej.
>
>

## <a name="specify-the-built-in-php-version"></a>Określ wbudowanych wersji języka PHP

Po dodaniu roli sieci web lub proces roboczy języka PHP do projektu, pliki konfiguracji projektu są modyfikowane, aby PHP zostanie zainstalowany na każdym wystąpieniu sieci web lub proces roboczy w aplikacji podczas jej wdrażania. Aby sprawdzić wersję programu PHP, które będą instalowane domyślnie, uruchom następujące polecenie:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Dane wyjściowe polecenia powyżej będzie wyglądać podobnie do przedstawionego poniżej. W tym przykładzie `IsDefault` flaga jest ustawiona na `true` dla języka PHP 5.3.17, wskazujący, że będzie ona domyślną wersję języka PHP zainstalowane.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

Można ustawić wersji środowiska uruchomieniowego języka PHP do dowolnej wersji języka PHP, które są wyświetlane. Na przykład Ustawianie wersji języka PHP (dla roli o nazwie `roleName`) do 5.4.0, użyj następującego polecenia:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Dostępne wersje PHP mogą ulec zmianie w przyszłości.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Dostosowywanie wbudowane środowiska uruchomieniowego języka PHP

Masz pełną kontrolę nad konfiguracją środowiska uruchomieniowego języka PHP, który jest instalowany, gdy wykonujesz powyższe kroki, w tym modyfikowanie `php.ini` ustawienia i włączania rozszerzeń.

Aby dostosować wbudowane środowiska uruchomieniowego języka PHP, wykonaj następujące kroki:

1. Dodaj nowy folder o nazwie `php`, `bin` katalogu roli sieci web. Dla roli procesu roboczego należy go dodać do roli katalogu głównego.
2. W `php` folderze utwórz inny folder o nazwie `ext`. Umieszczaj żadnego `.dll` rozszerzenia plików (np. `php_mongo.dll`), którą chcesz włączyć w tym folderze.
3. Dodaj `php.ini` plik `php` folderu. Włącz jakichkolwiek rozszerzeń niestandardowych, a następnie ustaw wszystkie dyrektywy języka PHP w tym pliku. Na przykład, jeśli chcesz włączyć `display_errors` na i włączyć `php_mongo.dll` rozszerzenia, zawartość swojej `php.ini` pliku będzie następująca:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Wszystkie ustawienia, które nie została jawnie ustawiona w `php.ini` pliku zapewniają zostanie automatycznie można ustawić wartości domyślne. Jednak należy pamiętać o tym, że można dodawać kompletna `php.ini` pliku.
>
>

## <a name="use-your-own-php-runtime"></a>Użyj własnego środowiska uruchomieniowego języka PHP

W niektórych przypadkach zamiast wybierając wbudowanych środowiska uruchomieniowego języka PHP i skonfigurowanie go zgodnie z opisem powyżej można podać własne środowiska uruchomieniowego języka PHP. Na przykład można użyć tego samego środowiska uruchomieniowego języka PHP w roli sieci web lub proces roboczy, używanego w środowisku programistycznym. Ułatwia zapewnienie, że aplikacja nie spowoduje zmiany zachowania w środowisku produkcyjnym.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Skonfiguruj rolę sieci web, aby użyć własnego środowiska uruchomieniowego języka PHP

Aby skonfigurować rolę sieci web, aby użyć środowiska uruchomieniowego języka PHP, który podasz, wykonaj następujące kroki:

1. Tworzenie projektu usługi Azure i Dodaj rolę sieci web PHP, jak opisano wcześniej w tym temacie.
2. Tworzenie `php` folderu w `bin` folder, który znajduje się w katalogu głównym roli sieci web, a następnie dodaj swoje środowiska uruchomieniowego języka PHP (wszystkie pliki binarne, pliki konfiguracji, podfoldery itp.), aby `php` folderu.
3. (OPCJONALNIE) Jeśli Twoje środowisko uruchomieniowe języka PHP [Drivers firmy Microsoft dla programów PHP i SQL Server][sqlsrv drivers], musisz skonfigurować roli sieci web, aby zainstalować [programu SQL Server Native Client 2012] [ sql native client] po jej zaaprowizowaniu. Aby to zrobić, Dodaj [Instalatora sqlncli.msi x64] do `bin` folder w katalogu głównym roli sieci web. Skrypt uruchamiania opisany w następnym kroku zostanie dyskretnie uruchom Instalatora, po zaaprowizowaniu roli. Jeśli Twoje środowiska uruchomieniowego języka PHP nie korzysta z Drivers firmy Microsoft dla programu PHP dla programu SQL Server, należy usunąć następujący wiersz w skrypcie pokazano w następnym kroku:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Zdefiniuj zadania uruchamiania, który konfiguruje [Internet Information Services (IIS)] [ iis.net] na potrzeby obsługi żądań dla Twojego środowiska uruchomieniowego języka PHP `.php` stron. Aby to zrobić, otwórz `setup_web.cmd` pliku (w `bin` plik katalogu głównego roli sieci web) w edytorze tekstów i zastąp jego zawartość za pomocą następującego skryptu:

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
5. Dodawania plików aplikacji do katalogu głównego roli sieci web. Jest to katalog główny serwera sieci web.
6. Publikuj swoje aplikacje, zgodnie z opisem w [publikować aplikację](#publish-your-application) poniższej sekcji.

> [!NOTE]
> `download.ps1` Skryptu (w `bin` folder katalogu głównego roli sieci web) mogą być usuwane po należy wykonać czynności opisane powyżej, aby przy użyciu własnego środowiska uruchomieniowego języka PHP.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurowanie roli procesu roboczego, aby użyć własnego środowiska uruchomieniowego języka PHP

Aby skonfigurować rolę procesu roboczego do użycia środowiska uruchomieniowego języka PHP, które podasz, wykonaj następujące kroki:

1. Tworzenie projektu usługi Azure, a następnie dodaj rolę procesu roboczego języka PHP, jak opisano wcześniej w tym temacie.
2. Tworzenie `php` folder w katalogu głównym roli procesu roboczego, a następnie dodaj swoje środowiska uruchomieniowego języka PHP (wszystkie pliki binarne, pliki konfiguracji, podfoldery itp.) do `php` folderu.
3. (OPCJONALNIE) Jeśli Twoje środowisko uruchomieniowe języka PHP [Drivers firmy Microsoft dla programów PHP i SQL Server][sqlsrv drivers], musisz skonfigurować swojej roli procesu roboczego, aby zainstalować [programu SQL Server Native Client 2012] [ sql native client] po jej zaaprowizowaniu. Aby to zrobić, Dodaj [Instalatora sqlncli.msi x64] do katalogu głównego roli procesu roboczego. Skrypt uruchamiania opisany w następnym kroku zostanie dyskretnie uruchom Instalatora, po zaaprowizowaniu roli. Jeśli Twoje środowiska uruchomieniowego języka PHP nie korzysta z Drivers firmy Microsoft dla programu PHP dla programu SQL Server, należy usunąć następujący wiersz w skrypcie pokazano w następnym kroku:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Zdefiniuj zadania uruchamiania, który dodaje swoje `php.exe` pliku wykonywalnego do zmiennej środowiskowej PATH roli procesu roboczego po zaaprowizowaniu roli. Aby to zrobić, otwórz `setup_worker.cmd` pliku (w katalogu głównym roli proces roboczy) w edytorze tekstów i zastąp jego zawartość za pomocą następującego skryptu:

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
5. Dodawania plików aplikacji do katalogu głównego swojej roli procesu roboczego.
6. Publikuj swoje aplikacje, zgodnie z opisem w [publikować aplikację](#publish-your-application) poniższej sekcji.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Uruchom aplikację w emulatorach zasobów obliczeniowych i magazynu

Emulatory systemu Azure zapewnia środowisku lokalnym, można przetestować aplikację platformy Azure, przed wdrożeniem jej w chmurze. Istnieją pewne różnice między emulatorów i środowisko platformy Azure. Aby lepiej zrozumieć to, zobacz [korzystanie z emulatora usługi Azure storage do programowania i testowania](storage/common/storage-use-emulator.md).

Należy pamiętać, że użytkownik musi mieć zainstalowane lokalnie na korzystanie z emulatora obliczeń w języku PHP. Emulator obliczeń użyje lokalnej instalacji języka PHP do uruchamiania aplikacji.

Aby uruchomić projekt w emulatorów, uruchom następujące polecenie z katalogu głównego projektu:

    PS C:\MyProject> Start-AzureEmulator

Zostaną wyświetlone dane wyjściowe podobne do tego:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Możesz zobaczyć działanie aplikacji w emulatorze, należy otworzyć przeglądarkę sieci web i przejście do adresów lokalnych w danych wyjściowych (`http://127.0.0.1:81` w przykładowych danych wyjściowych powyżej).

Aby zatrzymać emulatorów, należy wykonać to polecenie:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publikowanie aplikacji

Aby opublikować aplikację, należy najpierw zaimportować swoje ustawienia publikowania za pomocą [AzurePublishSettingsFile importu](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) polecenia cmdlet. Następnie można opublikować aplikację za pomocą [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) polecenia cmdlet. Aby dowiedzieć się, jak logowania, zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji, zobacz [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[usługi (csdef) definicji]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[Konfiguracja usługi (cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[Instalatora sqlncli.msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648
