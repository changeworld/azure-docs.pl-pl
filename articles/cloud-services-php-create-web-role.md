---
title: Tworzenie Azure role sieci web i proces roboczy dla PHP
description: Przewodnik dotyczący tworzenia ról sieci web i proces roboczy PHP w usłudze w chmurze platformy Azure i konfigurowania środowiska uruchomieniowego języka PHP.
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
ms.openlocfilehash: b9f350870dde71666d269aaae9cb7c14aaac5aad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="how-to-create-php-web-and-worker-roles"></a>Jak utworzyć role sieci web i proces roboczy języka PHP
## <a name="overview"></a>Przegląd
W tym przewodniku opisano sposób tworzyć role sieci web lub procesu roboczego PHP w środowisku projektowym systemu Windows, wybierz określonej wersji programu PHP w wersjach "wbudowanych" dostępne zmiany konfiguracji PHP, Włącz rozszerzenia i na koniec wdrażanie na platformie Azure. Zawiera również opis sposobu konfigurowania roli sieci web lub procesu roboczego do używania środowiska uruchomieniowego języka PHP (z konfiguracji niestandardowej i rozszerzenia) podane.

## <a name="what-are-php-web-and-worker-roles"></a>Co to są role sieć web i proces roboczy PHP?
Platforma Azure udostępnia trzy modele obliczeniowe na potrzeby uruchamiania aplikacji: Azure App Service, maszynach wirtualnych platformy Azure i usługi w chmurze Azure. Wszystkie trzy modele obsługują PHP. Usługi w chmurze, które obejmują role sieć web i proces roboczy, zapewnia *platforma jako usługa (PaaS)*. W ramach usługi w chmurze rola sieci web zapewnia dedykowany serwer sieci web usług Internet Information Services (IIS) umożliwia obsługę aplikacji frontonu sieci web. Rola proces roboczy można uruchamiać asynchroniczne, długotrwałe lub ciągłe zadania niezależne interakcji z użytkownikiem lub danych wejściowych.

Aby uzyskać więcej informacji o tych opcjach, zobacz [obliczeniowe hosting opcji dostarczanych przez platformę Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Pobierz zestaw Azure SDK dla języka PHP
[Zestaw Azure SDK for PHP] składa się z kilku składników. W tym artykule będzie korzystać z dwóch z nich: Azure PowerShell i emulatory platformy Azure. Te dwa składniki można zainstalować za pomocą Instalatora platformy sieci Web firmy Microsoft. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Tworzenie projektu usługi w chmurze
Pierwszym krokiem tworzenia roli sieci web lub procesu roboczego PHP jest tworzenie projektu usługi Azure. projekt usługi Azure służy jako kontener logicznej dla sieci web i proces roboczy ról i zawiera projektu [definicji (csdef) usługi] i [konfiguracji usługi (cscfg)] plików.

Aby utworzyć nowy projekt usługi Azure, uruchomienie programu Azure PowerShell jako administrator i uruchom następujące polecenie:

    PS C:\>New-AzureServiceProject myProject

To polecenie spowoduje utworzenie nowego katalogu (`myProject`) do której można dodać role sieci web i proces roboczy.

## <a name="add-php-web-or-worker-roles"></a>Dodawanie ról PHP sieci web lub procesu roboczego
Aby dodać rolę sieci web PHP na projekt, uruchom następujące polecenie w katalogu głównym projektu:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Dla roli proces roboczy Użyj tego polecenia:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> `roleName` Parametr jest opcjonalny. Jeśli zostanie pominięty, zostanie automatycznie wygenerowana nazwa roli. Pierwszy rola sieci web utworzony będzie `WebRole1`, drugi będzie `WebRole2`i tak dalej. Pierwszy roli procesu roboczego, utworzony zostanie `WorkerRole1`, drugi będzie `WorkerRole2`i tak dalej.
>
>

## <a name="specify-the-built-in-php-version"></a>Określ wbudowane wersję PHP
Po dodaniu roli PHP sieci web lub procesu roboczego do projektu pliki konfiguracji projektu są modyfikowane, dzięki czemu PHP zostanie zainstalowana na każde wystąpienie w sieci web lub procesu roboczego aplikacji po jej wdrożeniu. Aby wyświetlić wersję PHP, które będą instalowane domyślnie, uruchom następujące polecenie:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

Dane wyjściowe polecenia powyżej będzie wyglądać podobnie do co przedstawiono poniżej. W tym przykładzie `IsDefault` flaga jest ustawiona na `true` dla programów PHP 5.3.17, i wskazujący, że będzie domyślna wersja PHP zainstalowane.

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

Wersja środowiska uruchomieniowego PHP można ustawić na dowolnej wersji PHP, które są wymienione. Na przykład, aby ustawić wersję PHP (dla roli o nazwie `roleName`) do 5.4.0, użyj następującego polecenia:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Dostępne wersje PHP mogą ulec zmianie w przyszłości.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Dostosowywanie wbudowanej środowiska uruchomieniowego języka PHP
Masz pełną kontrolę nad konfiguracji środowiska uruchomieniowego języka PHP, instalowanego podczas wykonaj kroki opisane powyżej, w tym modyfikowanie `php.ini` ustawienia i włączania rozszerzeń.

Aby dostosować wbudowanych środowiska uruchomieniowego języka PHP, wykonaj następujące kroki:

1. Dodaj nowy folder o nazwie `php`, do `bin` katalogu swojej roli sieci web. Dla roli proces roboczy Dodaj go do katalogu głównego roli.
2. W `php` folderu, utworzyć inny folder o nazwie `ext`. Umieszczaj żadnego `.dll` rozszerzenia plików (np. `php_mongo.dll`), który chcesz włączyć w tym folderze.
3. Dodaj `php.ini` pliku `php` folderu. Włącz wszystkie rozszerzenia niestandardowe i ustaw wszystkie dyrektywy PHP w tym pliku. Na przykład, jeśli chcesz włączyć `display_errors` na i Włącz `php_mongo.dll` rozszerzenia, zawartość z `php.ini` pliku będzie następujące:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Wszystkie ustawienia, które nie zostanie jawnie ustawiona `php.ini` pliku Podaj zostanie automatycznie można ustawić wartości domyślne. Jednak należy pamiętać, że można dodać pełnego `php.ini` pliku.
>
>

## <a name="use-your-own-php-runtime"></a>Użyć własnego środowiska uruchomieniowego języka PHP
W niektórych przypadkach, zamiast wybierania wbudowanych środowiska uruchomieniowego języka PHP i konfigurowania go jak opisano powyżej możesz podać własne środowiska uruchomieniowego języka PHP. Na przykład można użyć tego samego środowiska uruchomieniowego języka PHP w roli sieci web lub procesu roboczego używanego w środowisku projektowania. Ułatwia to upewnij się, że aplikacja nie spowoduje zmiany zachowania w środowisku produkcyjnym.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Skonfiguruj rolę sieci web do używania własnego środowiska uruchomieniowego języka PHP
Aby skonfigurować rolę sieci web do używania środowiska uruchomieniowego języka PHP, który podasz, wykonaj następujące kroki:

1. Tworzenie projektu usługi Azure i Dodaj rolę sieci web PHP, jak opisano wcześniej w tym temacie.
2. Utwórz `php` folderu w `bin` folderu, który znajduje się w katalogu głównym roli sieci web, a następnie dodać do Twojego środowiska uruchomieniowego języka PHP (wszystkie pliki binarne, pliki konfiguracji, podfoldery itp.) `php` folderu.
3. (OPCJONALNIE) Jeśli korzysta z Twojego środowiska uruchomieniowego języka PHP [Drivers firmy Microsoft dla programów PHP i SQL Server][sqlsrv drivers], będzie konieczne skonfigurowanie roli sieci web do zainstalowania [programu SQL Server Native Client 2012] [ sql native client] po zainicjowaniu obsługi administracyjnej. Aby to zrobić, należy dodać [Instalator sqlncli.msi x64] do `bin` folderu w katalogu głównym roli sieci web. Po udostępnieniu roli, skryptu uruchomieniowego opisany w następnym kroku zostanie dyskretnie uruchom Instalatora. Jeśli Twojego środowiska uruchomieniowego języka PHP nie używa Drivers firmy Microsoft dla programów PHP i SQL Server, należy usunąć następujący wiersz w skrypcie pokazano w następnym kroku:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Zdefiniuj zadanie uruchamiania, który konfiguruje [Internet Information Services (IIS)] [ iis.net] na potrzeby obsługi żądań dla Twojego środowiska uruchomieniowego języka PHP `.php` stron. Aby to zrobić, otwórz `setup_web.cmd` pliku (w `bin` pliku katalog główny roli sieci web) w edytorze tekstów i zastąp jego zawartość następujący skrypt:

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
6. Publikowanie aplikacji, zgodnie z opisem w [opublikować aplikację](#publish-your-application) poniższej sekcji.

> [!NOTE]
> `download.ps1` Skryptu (w `bin` folder katalogu głównego roli sieci web) mogą być usuwane po wykonaj kroki opisane powyżej, aby przy użyciu własnego środowiska uruchomieniowego języka PHP.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Konfigurowanie roli proces roboczy, aby użyć własnego środowiska uruchomieniowego języka PHP
Aby skonfigurować rolę procesu roboczego, aby użyć środowiska uruchomieniowego języka PHP, który podasz, wykonaj następujące kroki:

1. Tworzenie projektu usługi Azure i Dodaj rolę procesu roboczego PHP, jak opisano wcześniej w tym temacie.
2. Utwórz `php` folderu w katalogu głównym roli procesu roboczego, a następnie dodać do Twojego środowiska uruchomieniowego języka PHP (wszystkie pliki binarne, pliki konfiguracji, podfoldery itp.) `php` folderu.
3. (OPCJONALNIE) Jeśli korzysta z Twojego środowiska uruchomieniowego języka PHP [Drivers firmy Microsoft dla programów PHP i SQL Server][sqlsrv drivers], musisz skonfigurować swojej roli procesu roboczego, aby zainstalować [programu SQL Server Native Client 2012] [ sql native client] po zainicjowaniu obsługi administracyjnej. Aby to zrobić, należy dodać [Instalator sqlncli.msi x64] do katalogu głównego roli procesu roboczego. Po udostępnieniu roli, skryptu uruchomieniowego opisany w następnym kroku zostanie dyskretnie uruchom Instalatora. Jeśli Twojego środowiska uruchomieniowego języka PHP nie używa Drivers firmy Microsoft dla programów PHP i SQL Server, należy usunąć następujący wiersz w skrypcie pokazano w następnym kroku:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Zdefiniuj zadanie uruchamiania, który dodaje użytkownika `php.exe` pliku wykonywalnego do zmiennej środowiskowej PATH roli procesu roboczego po udostępnieniu roli. Aby to zrobić, otwórz `setup_worker.cmd` plików (w katalogu głównym roli procesu roboczego) w edytorze tekstów i zastąp jego zawartość następujący skrypt:

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
6. Publikowanie aplikacji, zgodnie z opisem w [opublikować aplikację](#publish-your-application) poniższej sekcji.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Uruchom aplikację w emulatory zasobów obliczeniowych i magazynu
Emulatory Azure zapewniają lokalnego środowiska można przetestować aplikację Azure, przed wdrożeniem w chmurze. Istnieją pewne różnice między emulatorów i środowiska platformy Azure. Aby lepiej zrozumieć to, zobacz [użyć emulatora magazynu Azure do programowania i testowania](storage/common/storage-use-emulator.md).

Należy pamiętać, że użytkownik musi mieć zainstalowane lokalnie, aby użyć emulatora obliczeń PHP. Emulator obliczeń użyje lokalnej instalacji PHP do uruchamiania aplikacji.

Aby uruchomić projekt w emulatorów, uruchom następujące polecenie z katalogu głównego projektu:

    PS C:\MyProject> Start-AzureEmulator

Zostaną wyświetlone dane wyjściowe podobne do poniższego:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Widać działanie aplikacji w emulatorze przez otwarcie przeglądarki sieci web i przeglądania lokalny adres wyświetlany w danych wyjściowych (`http://127.0.0.1:81` w powyższym przykładowe dane wyjściowe).

Aby zatrzymać emulatorów, wykonanie tego polecenia:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publikowanie aplikacji
Aby opublikować aplikację, należy najpierw zaimportować Twoje ustawienia publikowania za pomocą [AzurePublishSettingsFile importu](https://msdn.microsoft.com/library/azure/dn790370.aspx) polecenia cmdlet. Następnie można opublikować aplikację przy użyciu [Publish-AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) polecenia cmdlet. Informacje logowania, zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, zobacz [Centrum deweloperów języka PHP](/develop/php/).

[Zestaw Azure SDK for PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definicji (csdef) usługi]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[konfiguracji usługi (cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[Instalator sqlncli.msi x64]: http://go.microsoft.com/fwlink/?LinkID=239648
