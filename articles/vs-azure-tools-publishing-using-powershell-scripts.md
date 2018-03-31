---
title: Za pomocą skryptów programu Windows PowerShell do opublikowania dla deweloperów i środowisk testowych | Dokumentacja firmy Microsoft
description: Dowiedz się, jak za pomocą skryptów środowiska Windows PowerShell z programu Visual Studio do publikowania do programowania i testowania środowisk.
services: visual-studio-online
documentationcenter: na
author: ghogen
manager: douge
editor: ''
ms.assetid: 5fff1301-5469-4d97-be88-c85c30f837c1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: 58d1c8398e626544a7b02198ec0431203aedcc81
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="using-windows-powershell-scripts-to-publish-to-dev-and-test-environments"></a>Używanie skryptów programu Windows PowerShell w celu publikowania w środowisku deweloperskim i testowym

Podczas tworzenia aplikacji sieci web w programie Visual Studio, można wygenerować skrypt programu Windows PowerShell, który można później można zautomatyzować publikowanie witryny sieci Web na platformie Azure jako aplikacji sieci Web w usłudze Azure App Service lub maszynie wirtualnej. Można edytować i rozszerzyć skrypt programu Windows PowerShell w edytorze programu Visual Studio ze swoimi potrzebami lub zintegrować skryptu z istniejących kompilacji, testów i publikowanie skryptów.

Skrypty te można udostępnić dostosowane wersje (znanej także jako środowiskach programistycznych i testowych) lokacji do użytku tymczasowego. Na przykład skonfigurować konkretnej wersji witryny sieci Web na maszynie wirtualnej platformy Azure lub miejsce wystawiania z witryny sieci Web do uruchamiania zestawu testów, występuje błąd, przetestować poprawkę, wersja próbna proponowanych zmian lub skonfigurować niestandardowe środowisko pokaz lub prezentacji. Po utworzeniu skryptu, który publikuje projektu można odtworzyć identyczne środowisk zgodnie z potrzebami, uruchamiając skrypt lub uruchom skrypt z kompilację aplikacji sieci web, aby utworzyć niestandardowe środowisko do testowania.

## <a name="prerequisites"></a>Wymagania wstępne

* Zestaw Azure SDK 2.3 lub nowszej. Zobacz [pobierania programu Visual Studio](http://go.microsoft.com/fwlink/?LinkID=624384). (Nie trzeba zestawu Azure SDK, aby wygenerować skryptów dla projektów sieci web. Ta funkcja jest dla projektów sieci web, nie role sieci web usług w chmurze).
* Program Azure PowerShell 0.7.4 lub nowszym. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* [Program Windows PowerShell 3.0](http://go.microsoft.com/?linkid=9811175) lub nowszym.

## <a name="additional-tools"></a>Dodatkowe narzędzia

Dodatkowych narzędzi i zasobów do pracy z programu PowerShell w programie Visual Studio do tworzenia aplikacji platformy Azure są dostępne. Zobacz [narzędzia programu PowerShell dla programu Visual Studio](http://go.microsoft.com/fwlink/?LinkId=404012).

## <a name="generating-the-publish-scripts"></a>Generowanie skryptów publikowania

Możesz wygenerować skrypty publikowania do maszyny wirtualnej, który jest hostem witryny sieci Web, podczas tworzenia nowego projektu wykonując [tych instrukcji](virtual-machines/windows/classic/web-app-visual-studio.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Możesz również [Generowanie publikowanie skryptów dla aplikacji sieci web w usłudze Azure App Service](app-service/app-service-web-get-started-dotnet.md).

## <a name="scripts-that-visual-studio-generates"></a>Skrypty, które generuje Visual Studio

Visual Studio generuje folder rozwiązania poziomu o nazwie **PublishScripts** zawiera dwa pliki programu Windows PowerShell, skrypt publikowania dla maszyny wirtualnej lub witryny sieci Web i moduł, który zawiera funkcje, których można używać w skryptach. Visual Studio także generuje plik w formacie JSON, który określa szczegółowe informacje dotyczące wdrażania projektu.

### <a name="windows-powershell-publish-script"></a>Skrypt publikowanie programu Windows PowerShell

Skrypt publikowania zawiera kroki publikowania określonych w celu wdrożenia do witryny sieci Web lub maszyny wirtualnej. Program Visual Studio udostępnia kolorowania rozwoju środowiska Windows PowerShell. Pomoc dla funkcji jest dostępna i funkcji w skrypcie ze swoimi potrzebami zmieniających się za darmo można edytować.

### <a name="windows-powershell-module"></a>Moduł programu Windows PowerShell

Moduł programu Windows PowerShell, który generuje Visual Studio zawiera funkcje, które używa skryptu publikowania. Te funkcje programu Azure PowerShell nie mają być modyfikowane. Zobacz artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

### <a name="json-configuration-file"></a>Plik JSON konfiguracji
Plik JSON jest tworzony w **konfiguracje** folderu i zawiera dane konfiguracji, które określa dokładnie zasoby, które zostaną wdrażanie na platformie Azure. Nazwa pliku, który generuje Visual Studio jest projektu name-WAWS-dev.json Jeśli utworzono witrynę sieci Web lub projektu Nazwa-maszyny Wirtualnej — dev.json Jeśli utworzono maszynę wirtualną. Oto przykładowy plik JSON konfiguracji, który jest generowany podczas tworzenia witryny sieci Web. Większość wartości nie wymaga wyjaśnień. Nazwa witryny sieci Web jest generowany przez platformę Azure, dlatego mogą być niezgodne nazwę projektu.

```json
{
    "environmentSettings": {
        "webSite": {
            "name": "WebApplication26632",
            "location": "West US"
        },
        "databases": [{
            "connectionStringName": "DefaultConnection",
            "databaseName": "WebApplication26632_db",
            "serverName": "YourDatabaseServerName",
            "user": "sqluser2",
            "password": "",
            "edition": "",
            "size": "",
            "collation": "",
            "location": "West US"
        }]
    }
}
```

Podczas tworzenia maszyny wirtualnej, plik JSON konfiguracji podobny do następującego. Usługi w chmurze jest tworzony jako kontener dla maszyny wirtualnej. Maszyna wirtualna zawiera zwykle punktów końcowych dla dostępu do sieci web za pośrednictwem protokołu HTTP i HTTPS, a także punktów końcowych dla narzędzia Web Deploy, który umożliwia publikowanie witryny sieci Web z komputera lokalnego, pulpitu zdalnego i środowiska Windows PowerShell.

```json
{
    "environmentSettings": {
        "cloudService": {
            "name": "myusernamevm1",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myusernamevm1",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Win2K8R2SP1-Datacenter-201403.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [{
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [{
            "connectionStringName": "",
            "databaseName": "",
            "serverName": "",
            "user": "",
            "password": ""
        }],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Można edytować konfigurację JSON, aby zmienić, co się stanie po uruchomieniu skrypty publikowania. `cloudService` i `virtualMachine` sekcje są wymagane, ale można usunąć `databases` sekcji, jeśli nie jest potrzebna. Właściwości, które są puste w Visual Studio generuje domyślny plik konfiguracji jest opcjonalna. te właściwości, które mają wartości domyślny plik konfiguracji są wymagane.

Jeśli masz witrynę sieci Web, który ma wiele środowisk wdrażania (nazywanych miejscami) zamiast lokacji pojedynczego produkcji na platformie Azure, może zawierać nazwy witryny sieci Web z nazwą miejsca w pliku konfiguracji JSON. Na przykład, jeśli masz witrynę sieci Web o nazwie **witryna** z miejscem dla niego o nazwie **test** , a następnie identyfikator URI jest `mysite-test.cloudapp.net`, ale mysite(test) jest prawidłowa nazwa do użycia w pliku konfiguracji. Można wykonać tylko w przypadku tej witryny sieci Web i gniazd już istnieją w Twojej subskrypcji. Jeśli nie istnieją, należy utworzyć witrynę sieci Web, uruchamiając skrypt bez określenia miejsca, a następnie utwórz miejsce w [portalu Azure](https://portal.azure.com/), a następnie uruchom skrypt o nazwie modyfikacji witryny sieci Web. Aby uzyskać więcej informacji dotyczących miejsc wdrożenia dla aplikacji sieci web, zobacz [Konfigurowanie środowiska dla aplikacji sieci web w usłudze Azure App Service przejściowe](app-service/web-sites-staged-publishing.md).

## <a name="how-to-run-the-publish-scripts"></a>Jak uruchomić skrypty publikowania

Jeśli nigdy nie zostało uruchomione przed skrypt programu Windows PowerShell, należy najpierw ustawić zasady wykonywania, aby umożliwić uruchamianie skryptów. Zasady jest to funkcja zabezpieczeń, aby uniemożliwić użytkownikom uruchamiania skryptów środowiska Windows PowerShell, jeśli są narażone na przed złośliwym oprogramowaniem i wirusami, które obejmują wykonywanie skryptów.

### <a name="run-the-script"></a>Uruchom skrypt

1. Tworzenie pakietu Narzędzia Web Deploy w dla projektu. Pakiet Web Deploy jest skompresowanego archiwum (pliku .zip), który zawiera pliki, które ma zostać skopiowany do witryny sieci Web lub maszyny wirtualnej. Pakiety Web Deploy można tworzyć w programie Visual Studio dla dowolnej aplikacji sieci web.

![Tworzenie sieci Web wdrażanie pakietu](./media/vs-azure-tools-publishing-using-powershell-scripts/IC767885.png)

Aby uzyskać więcej informacji, zobacz [jak: utworzyć pakiet wdrożeniowy sieci Web w programie Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx). Można również zautomatyzować tworzenie pakietu Narzędzia Web Deploy, zgodnie z opisem w [dostosowywanie i rozszerzanie scripts[(#customizing-and-extending-publish-scripts) publikowania]

1. W **Eksploratora rozwiązań**, otwórz menu kontekstowe dla skryptu, a następnie wybierz **otworzyć za pomocą programu PowerShell ISE**.
2. Jeśli uruchamianie skryptów środowiska Windows PowerShell na tym komputerze po raz pierwszy, Otwórz okno wiersza polecenia z uprawnieniami administratora i wpisz następujące polecenie:

    ```powershell
    Set-ExecutionPolicy RemoteSigned
    ```

3. Zaloguj się na platformie Azure przy użyciu następującego polecenia.

    ```powershell
    Add-AzureAccount
    ```

    Po wyświetleniu monitu podaj nazwę użytkownika i hasło.

    Należy pamiętać, że podczas automatyzacji skrypt tej metody podawania poświadczeń platformy Azure nie działa. Zamiast tego należy używać `.publishsettings` pliku o podanie poświadczeń. Jeden raz, użyto polecenia **Get-AzurePublishSettingsFile** Pobierz plik z platformy Azure, a następnie użyć **AzurePublishSettingsFile importu** do zaimportowania pliku. Aby uzyskać szczegółowe informacje, zobacz [How to install and configure Azure PowerShell (Jak zainstalować i skonfigurować program Azure PowerShell)](/powershell/azure/overview).

4. (Opcjonalnie) Jeśli chcesz utworzyć zasobów platformy Azure, takich jak maszyny wirtualnej, użyj bazy danych i witryny sieci Web bez publikowania aplikacji sieci web **WebApplication.ps1 publikowania** z **— konfiguracji** argument wartość plik JSON konfiguracji. Ten wiersz polecenia używa plik JSON konfiguracji do określenia, które zasoby do utworzenia. Ponieważ używa ustawień domyślnych dla innych argumentów wiersza polecenia, tworzy zasoby, ale nie publikowania aplikacji sieci web. Verbose — opcja zapewnia więcej informacji o tym, co dzieje.

    ```powershell
    Publish-WebApplication.ps1 -Verbose –Configuration C:\Path\WebProject-WAWS-dev.json
    ```

5. Użyj **WebApplication.ps1 publikowania** polecenia, jak pokazano w jednej z poniższych przykładach wywołania skrypt i opublikować aplikację sieci web. Jeśli potrzebujesz zastępują ustawienia domyślne dla każdego inne argumenty, takie jak nazwa subskrypcji, Opublikuj nazwę pakietu, poświadczenia maszyny wirtualnej lub poświadczenia serwera bazy danych, można określić tych parametrów. Użyj **— pełne** opcję, aby uzyskać więcej informacji o postęp procesu publikowania.

    ```powershell
    Publish-WebApplication.ps1 –Configuration C:\Path\WebProject-WAWS-dev-json `
    –SubscriptionName Contoso `
    -WebDeployPackage C:\Documents\Azure\ADWebApp.zip `
    -DatabaseServerPassword @{Name="dbServerName";Password="adminPassword"} `
    -Verbose
    ```

    Jeśli tworzysz maszynę wirtualną, polecenie wygląda następująco. W tym przykładzie przedstawiono również sposób określ poświadczenia dla wielu baz danych. Dla maszyn wirtualnych utworzonych przez te skrypty certyfikat SSL nie pochodzi z zaufanego głównego urzędu. W związku z tym należy użyć **— AllowUntrusted** opcji.

    ```powershell
    Publish-WebApplication.ps1 `
    -Configuration C:\Path\ADVM-VM-test.json `
    -SubscriptionName Contoso `
    -WebDeployPackage C:\Path\ADVM.zip `
    -AllowUntrusted `
    -VMPassword @{name = "vmUserName"; password = "YourPasswordHere"} `
    -DatabaseServerPassword @{Name="server1";Password="adminPassword1"}, @{Name="server2";Password="adminPassword2"} `
    -Verbose
    ```

    Skrypt można utworzyć bazy danych, ale nie tworzy serwerów baz danych. Jeśli chcesz utworzyć serwer bazy danych, możesz użyć **AzureSqlDatabaseServer nowy** funkcji w module usługi Azure.

## <a name="customizing-and-extending-the-publish-scripts"></a>Dostosowywanie i rozszerzanie skrypty publikowania
Można dostosować skrypt publikowania i pliku konfiguracji JSON. Funkcje w module środowiska Windows PowerShell **AzureWebAppPublishModule.psm1** nie mają być modyfikowane. Jeśli chcesz określić inną bazę danych lub zmiany niektórych właściwości maszyny wirtualnej, Edytuj plik JSON konfiguracji. Jeśli chcesz rozszerzyć funkcjonalność programu skryptów do automatyzacji tworzenia i testowania projektu, można zaimplementować klas zastępczych funkcji w **WebApplication.ps1 publikowania**.

Aby zautomatyzować tworzenie projektu, Dodaj kod, który wywołuje MSBuild, aby `New-WebDeployPackage` opisane w tym przykładzie kodu. Ścieżka do polecenia programu MSBuild różni się w zależności od wersji programu Visual Studio został zainstalowany. Aby uzyskać poprawną ścieżkę, można użyć funkcji **Get-MSBuildCmd**, jak pokazano w poniższym przykładzie.

### <a name="to-automate-building-your-project"></a>Aby zautomatyzować tworzenie projektu
1. Dodaj `$ProjectFile` parametru w sekcji parametrów globalnych.

    ```powershell
    [Parameter(Mandatory = $false)]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [String]
    $ProjectFile,
    ```

2. Skopiuj funkcję `Get-MSBuildCmd` do pliku skryptu.

    ```powershell
    function Get-MSBuildCmd
    {
            process
    {

                $path =  Get-ChildItem "HKLM:\SOFTWARE\Microsoft\MSBuild\ToolsVersions\" |
                                    Sort-Object {[double]$_.PSChildName} -Descending |
                                    Select-Object -First 1 |
                                    Get-ItemProperty -Name MSBuildToolsPath |
                                    Select -ExpandProperty MSBuildToolsPath

                $path = (Join-Path -Path $path -ChildPath 'msbuild.exe')

            return Get-Item $path
        }
    }
    ```

3. Zastąp `New-WebDeployPackage` z następujący kod i zastąp symbole zastępcze podczas tworzenia wiersza `$msbuildCmd`. Ten kod jest dla programu Visual Studio 2017 r. Jeśli używasz programu Visual Studio 2015, zmień **VisualStudioVersion** właściwości `14.0` (`12.0` dla programu Visual Studio 2013).

    ```powershell
    function New-WebDeployPackage
    {
        #Write a function to build and package your web application
    ```

    Aby utworzyć aplikację sieci web, należy użyć MsBuild.exe. Aby uzyskać Pomoc Zobacz MSBuild wiersza polecenia pod adresem: [http://go.microsoft.com/fwlink/?LinkId=391339](http://go.microsoft.com/fwlink/?LinkId=391339)

    ```powershell
    Write-VerboseWithTime 'Build-WebDeployPackage: Start'

    $msbuildCmd = '"{0}" "{1}" /T:Rebuild;Package /P:VisualStudioVersion=15.0 /p:OutputPath="{2}\MSBuildOutputPath" /flp:logfile=msbuild.log,v=d' -f (Get-MSBuildCmd), $ProjectFile, $scriptDirectory

    Write-VerboseWithTime ('Build-WebDeployPackage: ' + $msbuildCmd)
    ```

### <a name="start-execution-of-the-build-command"></a>Uruchamia wykonywanie polecenia kompilacji

```powershell
$job = Start-Process cmd.exe -ArgumentList('/C "' + $msbuildCmd + '"') -WindowStyle Normal -Wait -PassThru

if ($job.ExitCode -ne 0) {
    throw('MsBuild exited with an error. ExitCode:' + $job.ExitCode)
}

#Obtain the project name
$projectName = (Get-Item $ProjectFile).BaseName

#Construct the path to web deploy zip package
$DeployPackageDir =  '.\MSBuildOutputPath\_PublishedWebsites\{0}_Package\{0}.zip' -f $projectName

#Get the full path for the web deploy zip package. This is required for MSDeploy to work
$WebDeployPackage = Resolve-Path –LiteralPath $DeployPackageDir

Write-VerboseWithTime 'Build-WebDeployPackage: End'

return $WebDeployPackage
}
```

1. Wywołanie `New-WebDeployPackage` funkcja przed wierszem: `$Config = Read-ConfigFile $Configuration` dla aplikacji sieci web lub `$Config = Read-ConfigFile $Configuration -HasWebDeployPackage:([Bool]$WebDeployPackage)` dla maszyn wirtualnych.

    ```powershell
    if($ProjectFile)
    {
    $WebDeployPackage = New-WebDeployPackage
    }
    ```

2. Wywołanie dostosowanego skryptu z wiersza polecenia przy użyciu przekazywanie `$Project` argumentu, jak w poniższym przykładzie:

    ```powershell
    .\Publish-WebApplicationVM.ps1 -Configuration .\Configurations\WebApplication5-VM-dev.json `
    -ProjectFile ..\WebApplication5\WebApplication5.csproj `
    -VMPassword @{Name="VMUser";Password="Test.123"} `
    -AllowUntrusted `
    -Verbose
    ```

    Aby zautomatyzować testowania aplikacji, Dodaj kod, aby `Test-WebApplication`. Należy koniecznie Usuń komentarz linii w **WebApplication.ps1 publikowania** gdzie te funkcje są wywoływane. Jeśli nie zapewniać implementację, należy ręcznie utworzyć projektu za pomocą programu Visual Studio, a następnie uruchom skrypt publish do publikowania na platformie Azure.

## <a name="publishing-function-summary"></a>Podsumowanie funkcji publikowania
Aby uzyskać pomoc dotyczącą funkcje, których można użyć w wierszu polecenia programu Windows PowerShell, użyj polecenia `Get-Help function-name`. Pomoc zawiera parametr pomocy i przykłady. Ten sam tekst pomocy jest również w plikach źródłowych skryptu **AzureWebAppPublishModule.psm1** i **WebApplication.ps1 publikowania**. Skrypt i pomocy są zlokalizowane w języku Visual Studio.

**AzureWebAppPublishModule**

| Nazwa funkcji | Opis |
| --- | --- |
| Add-AzureSQLDatabase |Tworzy nową bazę danych Azure SQL. |
| Add-AzureSQLDatabases |Tworzy baz danych Azure SQL z wartości w pliku konfiguracji JSON, który generuje Visual Studio. |
| Add-AzureVM |Tworzy maszynę wirtualną platformy Azure i zwraca adres URL wdrożonej maszyny wirtualnej. Funkcja konfiguruje wymagania wstępne, a następnie wywołuje **AzureVM nowy** funkcji (moduł Azure), aby utworzyć nową maszynę wirtualną. |
| Add-AzureVMEndpoints |Dodaje nowe wejściowych punktów końcowych do maszyny wirtualnej i przywraca maszynę wirtualną z nowego punktu końcowego. |
| Add-AzureVMStorage |Tworzy nowe konto magazynu Azure w bieżącej subskrypcji. Nazwa konta zaczyna się od "devtest", po której następują unikatowy ciąg alfanumeryczny. Funkcja zwraca nazwę nowego konta magazynu. Określ lokalizację lub grupę koligacji dla nowego konta magazynu. |
| Add-AzureWebsite |Tworzy witrynę sieci Web o określonej nazwie i lokalizacji. Ta funkcja wymaga **AzureWebsite nowy** funkcji w module usługi Azure. Jeśli subskrypcja nie zawiera już witryna sieci Web o określonej nazwie, ta funkcja tworzy witryny sieci Web i zwraca obiekt witryny sieci Web. W przeciwnym razie zwraca `$null`. |
| Subskrypcji kopii zapasowej |Zapisuje bieżący subskrypcji platformy Azure w `$Script:originalSubscription` zmiennej w zakresie skryptu. Ta funkcja jest zapisywany bieżącej subskrypcji platformy Azure (uzyskanych przez `Get-AzureSubscription -Current`) i jego konta magazynu i subskrypcji, która zostanie zmieniona przez ten skrypt (przechowywana w zmiennej `$UserSpecifiedSubscription`), a jego konto magazynu w zakresie skryptu. Po zapisaniu wartości, można użyć funkcji, takich jak `Restore-Subscription`, aby przywrócić oryginalne bieżącego konta subskrypcji i magazynu bieżący stan, jeśli bieżący stan został zmieniony. |
| Find-AzureVM |Pobiera określonej maszyny wirtualnej platformy Azure. |
| Format-DevTestMessageWithTime |Dołącza datę i godzinę do wiadomości. Ta funkcja jest przeznaczona dla komunikatów zapisywanych strumienie błąd i pełne. |
| Get-AzureSQLDatabaseConnectionString |Zbierane parametry połączenia do łączenia z bazą danych Azure SQL. |
| Get-AzureVMStorage |Zwraca nazwę pierwsze konto magazynu przy użyciu wzorca nazwy "devtest*" (bez uwzględniania wielkości liter) w określonej lokalizacji lub grupie koligacji. Jeśli "devtest*" konta magazynu jest niezgodne lokalizacja lub grupa koligacji, funkcja ignoruje go. Określ lokalizację lub grupę koligacji. |
| Get-MSDeployCmd |Zwraca polecenie do uruchomienia narzędzia MsDeploy.exe. |
| New-AzureVMEnvironment |Wyszukuje lub tworzy maszynę wirtualną w subskrypcji, która odpowiada wartości w pliku konfiguracji JSON. |
| Publish-WebPackage |Używa MsDeploy.exe i sieci web publikowania pakietu. Plik zip do wdrażania zasobów do witryny sieci Web. Ta funkcja nie generuje żadnego wyniku. Jeśli wywołanie MSDeploy.exe zakończy się niepowodzeniem, funkcja zwraca wyjątek. Aby uzyskać bardziej szczegółowe dane wyjściowe, należy użyć **-Verbose** opcji. |
| Publish-WebPackageToVM |Sprawdza, czy wartości parametrów, a następnie wywołuje **WebPackage publikowania** funkcji. |
| Read-ConfigFile |Sprawdza poprawność pliku konfiguracji JSON i zwraca tablicy skrótów, wybranych wartości. |
| Przywracanie subskrypcji |Resetuje bieżącej subskrypcji do pierwotnej subskrypcji. |
| Test-AzureModule |Zwraca `$true` Jeśli wersja zainstalowanego modułu Azure jest 0.7.4 lub nowszym. Zwraca `$false` Jeśli moduł nie jest zainstalowany lub jest starsza wersja. Ta funkcja nie ma parametrów. |
| Test-AzureModuleVersion |Zwraca `$true` w przypadku wersji modułu Azure 0.7.4 lub nowszym. Zwraca `$false` Jeśli moduł nie jest zainstalowany lub jest starsza wersja. Ta funkcja nie ma parametrów. |
| Test-HttpsUrl |Konwertuje wejściowy adres URL do obiektu System.Uri. Zwraca `$True` jest bezwzględny adres URL, jeśli jego schemat jest typu https. Zwraca `$false` adres URL jest względny, jego schematu nie HTTPS, czy ciąg wejściowy nie można przekonwertować na adres URL. |
| Test-Member |Zwraca `$true` Jeśli właściwość lub metoda jest elementem członkowskim obiektu. W przeciwnym razie zwraca `$false`. |
| ErrorWithTime zapisu |Zapisuje komunikat o błędzie z prefiksem bieżącego czasu. Ta funkcja wymaga **Format DevTestMessageWithTime** funkcji dołączy czasu przed zapisaniem komunikat do strumienia błędów. |
| Write-HostWithTime |Zapisuje komunikat w programie hosta (**Write-Host**) i jest poprzedzony prefiksem bieżącego czasu. Efekt zapisywanie w programie hosta może być różna. Większość programów obsługujących środowiska Windows PowerShell zapisu te komunikaty do wyjścia standardowego. |
| Write-VerboseWithTime |Zapisuje komunikat trybu informacji pełnej prefiksem bieżącego czasu. Ponieważ wywołuje **Write-Verbose**, komunikat jest wyświetlany tylko wtedy, gdy skrypt jest uruchamiany z **pełne** parametru lub gdy **VerbosePreference** ma ustawioną wartość preferencji **Kontynuuj**. |

**Publish-WebApplication**

| Nazwa funkcji | Opis |
| --- | --- |
| New-AzureWebApplicationEnvironment |Tworzy zasobów platformy Azure, takich jak witryny sieci Web lub maszyny wirtualnej. |
| New-WebDeployPackage |Ta funkcja nie jest zaimplementowana. Można dodać polecenia w tej funkcji, aby skompilować projekt. |
| Publish-AzureWebApplication |Publikowanie aplikacji sieci web na platformie Azure. |
| Publish-WebApplication |Tworzy i wdraża aplikacje sieci Web, maszyn wirtualnych, baz danych i konta magazynu dla projektu sieci web programu Visual Studio. |
| Test-WebApplication |Ta funkcja nie jest zaimplementowana. W tej funkcji, aby przetestować aplikację, można dodać polecenia. |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat skryptów środowiska PowerShell, odczytując [skryptów programu Windows PowerShell](https://technet.microsoft.com/library/bb978526.aspx) zobaczyć inne skrypty programu PowerShell systemu Azure w [Centrum skryptów](https://azure.microsoft.com/documentation/scripts/).
