---
title: Usługa Azure Monitor stanu v2 szczegółowe instrukcje | Dokumentacja firmy Microsoft
description: Aby uzyskać szczegółowe instrukcje dla klientów zaczynających się od monitora stanu w wersji 2. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami internetowymi platformy ASP.NET hostowanymi lokalnie na maszynach wirtualnych lub platformie Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 6eca2b47c2362f34415db8b4f335f3089babc58b
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255878"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Stan monitora v2 szczegółowe instrukcje

Ten dokument szczegółowe informacje, jak dołączyć do galerii programu PowerShell i pobrać moduł ApplicationMonitor. Zapisaliśmy najbardziej typowe parametry wymagane, aby rozpocząć pracę.
Dołączono również ręcznej instrukcje w przypadku, gdy dostęp do Internetu jest niedostępna.

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="instrumentation-key"></a>Klucz Instrumentacji

Aby rozpocząć pracę, musi mieć klucz instrumentacji. Aby uzyskać więcej informacji, przeczytaj [Utwórz zasób usługi Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-administrator-with-an-elevated-execution-policy"></a>Uruchom program PowerShell jako administrator z zasadami wykonywanie z podwyższonym poziomem uprawnień

**Uruchom jako Administrator**: 
- Opis: Program PowerShell, należy poziomu uprawnień administratora, aby wprowadzić zmiany do komputera.

**Zasady wykonywania**:
- Opis: Domyślnie uruchomionych skryptów PowerShell, zostaną wyłączone. Firma Microsoft zaleca, umożliwiając skrypty RemoteSigned dla bieżącego zakresu.
- Informacje ogólne: [O zasadach wykonania](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) i [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
)
- Cmd: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`
- Następujące parametry opcjonalne:
    - `-Force` To pozwoli na pominięcie monitu o potwierdzenie.

**Przykładowe błędy:**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Wymagania wstępne dotyczące programu PowerShell

Przeprowadź inspekcję bieżącej wersji programu PowerShell, uruchamiając polecenie: `$PSVersionTable`.
Polecenie tworzy następujące wyniki:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Te instrukcje zostały napisane i przetestować na komputerze z systemem Windows 10 w wersjach wymienionych powyżej.

## <a name="prerequisites-for-powershell-gallery"></a>Wymagania wstępne dotyczące galerii programu PowerShell

Te kroki zostaną Przygotowywanie serwera do pobrania moduły z galerii programu PowerShell.

> [!NOTE] 
> Obsługa galerii programu PowerShell znajduje się na system Windows 10, Windows Server 2016 i programu PowerShell 6. Starsze wersje można znaleźć w tym dokumencie: [Instalowanie modułu PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)


1. Uruchom program PowerShell jako Administrator, za pomocą zasad wykonania z podwyższonym poziomem uprawnień.
2. Dostawca pakietu NuGet 
    - Opis: Ten dostawca jest wymagany do interakcji z repozytoriami oparte na pakietach NuGet, takich jak galerii PowerShellGallery
    - Informacje ogólne: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6)
    - Cmd: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`
    - Następujące parametry opcjonalne:
        - `-Proxy` Określa serwer proxy dla żądania.
        - `-Force` To pozwoli na pominięcie monitu o potwierdzenie. 
    
    Otrzymasz ten monit, jeśli nie została skonfigurowana NuGet:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Zaufany repozytoriów
    - Opis: Domyślnie galerii PowerShellGallery jest niezaufany repozytorium.
    - Informacje ogólne: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6)
    - Cmd: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`
    - Następujące parametry opcjonalne:
        - `-Proxy` Określa serwer proxy dla żądania.

    Otrzymasz ten monit, jeśli w galerii programu PowerShell nie jest zaufana:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    - Można potwierdzić tę zmianę i inspekcji wszystkich PSRepositories przez uruchomienie polecenia: `Get-PSRepository`

4. Wersja modułu PowerShellGet 
    - Opis: Ten moduł zawiera narzędzia używane do uzyskania inne moduły z galerii programu PowerShell. V1.0.0.1 jest dostarczany z systemem Windows 10 i Windows Server. Minimalna wersja wymagana jest v1.6.0. Aby przeprowadzić inspekcję, która wersja jest zainstalowana, uruchom polecenie: `Get-Command -Module PowerShellGet`
    - Informacje ogólne: [Instalowanie modułu PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget)
    - Cmd: `Install-Module -Name PowerShellGet`
    - Następujące parametry opcjonalne:
        - `-Proxy` Określa serwer proxy dla żądania.
        - `-Force` Spowoduje to zignorować to ostrzeżenie "już zainstalowane" i zainstaluj najnowszą wersję.

    Jeśli nie używasz najnowszej wersji modułu PowerShellGet, otrzymasz ten błąd:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Ponowne uruchomienie programu PowerShell. Nie można załadować nowej wersji w bieżącej sesji. Wszystkie nowe sesje programu Powershell będzie miał najnowszy moduł PowerShellGet załadowane.

## <a name="download--install-via-powershell-gallery"></a>Pobieranie i instalowanie za pomocą galerii programu PowerShell

Te kroki będą pobierać modułu Az.ApplicationMonitor z galerii programu PowerShell.

1. Wymagania wstępne dotyczące galerii programu PowerShell są wymagane.
2. Uruchom program PowerShell jako Administrator, za pomocą zasad wykonania z podwyższonym poziomem uprawnień.
3. Zainstaluj moduł Az.ApplicationMonitor
    - Informacje ogólne: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6)
    - Cmd: `Install-Module -Name Az.ApplicationMonitor`
    - Następujące parametry opcjonalne:
        - `-Proxy` Określa serwer proxy dla żądania.
        - `-AllowPrerelease` Umożliwi to zainstalowanie wersji alfa i beta.
        - `-AcceptLicense` To pozwoli na pominięcie wiersza "Zaakceptuj licencję"
        - `-Force` Spowoduje to zignorować to ostrzeżenie "Niezaufanych repozytorium"

## <a name="download--install-manually-offline-option"></a>Pobierz i zainstaluj ręcznie (opcja w trybie offline)

Jeśli z jakiegoś powodu nie można nawiązać połączenia modułu programu PowerShell, mogą ręcznie Pobierz i zainstaluj moduł Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg"></a>Ręcznie pobrać najnowsze nupkg

1. Przejdź do: https://www.powershellgallery.com/packages/Az.ApplicationMonitor
2. Wybierz najnowszą wersję z historii wersji.
3. Znajdź "Opcje instalacji" i wybierz opcję "Pobierz Podręcznik".

### <a name="option-1-install-into-powershell-modules-directory"></a>Opcja 1: Zainstaluj do katalogu modułów programu PowerShell
Zainstaluj ręcznie pobranego modułu programu PowerShell do katalogu programu PowerShell, dzięki czemu można go odnaleźć w sesji programu PowerShell.
Aby uzyskać więcej informacji, zobacz: [Instalowanie modułu programu PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)


#### <a name="unzip-nupkg-as-zip-using-expand-archive-v1010"></a>Rozpakuj nupkg w formie pliku zip, za pomocą Expand-archiwum (v1.0.1.0)

- Opis: Wersję podstawową Microsoft.PowerShell.Archive (v1.0.1.0) nie można rozpakować nupkg pliki. Zmień nazwę pliku z rozszerzeniem ".zip".
- Informacje ogólne: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6)
- Cmd: 

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-using-expand-archive-v1100"></a>Rozpakuj nupkg przy użyciu Expand-archiwum (v1.1.0.0).

- Opis: Aby rozpakować nupkgs bez zmieniania rozszerzenie, należy użyć bieżącą wersję rozwiń archiwum. 
- Informacje ogólne: [Rozwiń archiwum](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) i [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0)
- Cmd:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-manually"></a>Opcja 2: Rozpakowywanie i zaimportować ręcznie
Zainstaluj ręcznie pobranego modułu programu PowerShell do katalogu programu PowerShell, dzięki czemu można go odnaleźć w sesji programu PowerShell.
Aby uzyskać więcej informacji, zobacz: [Instalowanie modułu programu PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module)

Instalacji do innego katalogu, należy ręcznie zaimportować przy użyciu modułu [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6)

> [!IMPORTANT] 
> Zostanie zainstalowany w ramach instalacji biblioteki DLL za pomocą ścieżek względnych. Store zawartość tego pakietu do katalogu zamierzony środowiska uruchomieniowego i upewnij się, że zezwalają na uprawnienia dostępu, odczytu, ale nie zapisu.

1. Zmień rozszerzenie na ".zip" i Wyodrębnij zawartość pakietu w katalogu instalacji zamierzone.
2. Znajdź ścieżkę pliku do "Az.ApplicationMonitor.psd1".
3. Uruchom program PowerShell jako Administrator, za pomocą zasad wykonania z podwyższonym poziomem uprawnień. 
4. Ładowanie modułu za pomocą polecenia: `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="proxy"></a>Serwer proxy

Podczas monitorowania komputera w prywatnej sieci intranet, będzie to konieczne przekierować ruch http przez serwer proxy.

Polecenia programu PowerShell, aby pobrać i zainstalować Az.ApplicationMonitor z galerii programu PowerShell obsługują `-Proxy` parametru.
Zapoznaj się z powyższymi instrukcjami podczas pisania skryptów instalacji.

Zestaw SDK usługi Application Insights będzie konieczne wysyłanie danych telemetrycznych aplikacji do firmy Microsoft. Firma Microsoft zaleca skonfigurowanie ustawień serwera proxy dla aplikacji w z pliku web.config. Zobacz [usługi Application Insights — często zadawane pytania: Przekazywanie proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough) Aby uzyskać więcej informacji.


## <a name="enable-monitoring"></a>Włączanie monitorowania 

Cmd: `Enable-ApplicationInsightsMonitoring`

Przejrzyj nasze [dokumentacja interfejsu API](status-monitor-v2-api-enable-monitoring.md) szczegółowy opis sposobu użycia tego polecenia cmdlet. 



## <a name="next-steps"></a>Kolejne kroki

 Wyświetlanie telemetrii:

- [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md) w celu monitorowania wydajności i użycia
- [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) do diagnozowania problemów
- [Analiza](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md)

 Dodawanie kolejnych funkcji telemetrii:

- [Tworzenie testów sieci web](monitor-web-app-availability.md) aby upewnić się, że witryna pozostaje aktywna.
- [Dodawanie telemetrii klienta sieci web](../../azure-monitor/app/javascript.md) aby zobaczyć wyjątki pochodzące z kodu strony sieci web i umożliwić wstawianie wywołań śladu.
- [Dodawanie zestawu SDK usługi Application Insights do kodu](../../azure-monitor/app/asp-net.md) tak, aby wstawić ślad i rejestrować wywołania

Wykonuj więcej zadań dzięki v2 Monitora stanu:

- Użyj zapoznaj się z przewodnikiem [rozwiązywanie](status-monitor-v2-troubleshoot.md) Monitora stanu w wersji 2.
