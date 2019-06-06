---
title: Usługa Azure Monitor stanu v2 szczegółowe instrukcje | Dokumentacja firmy Microsoft
description: Aby uzyskać szczegółowe instrukcje dla klientów zaczynających się od monitora stanu w wersji 2. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami sieci web platformy ASP.NET hostowanej lokalnie, na maszynach wirtualnych lub na platformie Azure.
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
ms.openlocfilehash: d0960c749d74903acc778c0f21d5c49f380195ae
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734197"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Stan monitora v2: Szczegółowe instrukcje

W tym artykule opisano sposób dołączyć do galerii programu PowerShell i pobrać moduł ApplicationMonitor.
Opisano w nim typowe parametry, które należy rozpocząć pracę.
Funkcja ta zawiera również instrukcje ręcznego w przypadku, gdy nie masz dostępu do Internetu.

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Tej wersji zapoznawczej jest oferowana bez umowy dotyczącej poziomu usług, i firma Microsoft nie jest to zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, a niektóre mogą mieć ograniczone możliwości.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-an-instrumentation-key"></a>Uzyskiwanie klucza Instrumentacji

Aby rozpocząć pracę, musisz mieć klucz instrumentacji. Aby uzyskać więcej informacji, zobacz [Utwórz zasób usługi Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Uruchom program PowerShell jako administrator z zasadami wykonywanie z podwyższonym poziomem uprawnień

**Uruchom jako administrator**

Program PowerShell wymaga uprawnień na poziomie administratora do wprowadzania zmian w komputerze.

**Zasady wykonywania**
- Opis: Domyślnie uruchamiania skryptów programu PowerShell jest wyłączona. Firma Microsoft zaleca, umożliwiając skrypty RemoteSigned w bieżącym zakresie.
- Informacje ogólne: [O zasadach wykonania](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) i [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Polecenie: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Opcjonalny parametr:
    - `-Force`. Pomija monit o potwierdzenie.

**Przykładowe błędy**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Wymagania wstępne dotyczące programu PowerShell

Inspekcja wystąpienia programu PowerShell, uruchamiając `$PSVersionTable` polecenia.
To polecenie tworzy następujące wyniki:


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

Te instrukcje zostały napisane i przetestować na komputerze z systemem Windows 10 i wymienionych powyżej.

## <a name="prerequisites-for-powershell-gallery"></a>Wymagania wstępne dotyczące galerii programu PowerShell

Te kroki przygotuje serwerowi Pobierz moduły z galerii programu PowerShell.

> [!NOTE] 
> Galeria programu PowerShell jest obsługiwana na Windows 10, Windows Server 2016 i programu PowerShell 6.
> Aby uzyskać informacje o starszych wersjach, zobacz [instalacji PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Uruchom program PowerShell jako administrator, za pomocą zasad wykonania z podwyższonym poziomem uprawnień.
2. Zainstaluj dostawcę pakietu NuGet.
    - Opis: Należy tego dostawcy do interakcji z oparte na pakietach NuGet repozytoriów, takich jak Galeria programu PowerShell.
    - Informacje ogólne: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Polecenie: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Następujące parametry opcjonalne:
        - `-Proxy`. Określa serwer proxy dla żądania.
        - `-Force`. Pomija monit o potwierdzenie.
    
    Otrzymasz ten monit, jeśli nie została skonfigurowana NuGet:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Skonfiguruj galerii programu PowerShell jako zaufane repozytorium.
    - Opis: Domyślnie galerii programu PowerShell jest niezaufany repozytorium.
    - Informacje ogólne: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Polecenie: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Opcjonalny parametr:
        - `-Proxy`. Określa serwer proxy dla żądania.

    Otrzymasz ten monit, jeśli w galerii programu PowerShell nie jest zaufana:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Można potwierdzić tę zmianę i inspekcji wszystkich PSRepositories, uruchamiając `Get-PSRepository` polecenia.

4. Zainstaluj najnowszą wersję modułu PowerShellGet.
    - Opis: Ten moduł zawiera narzędzia używane do uzyskania inne moduły z galerii programu PowerShell. Wersja 1.0.0.1 jest dostarczana z systemem Windows 10 i Windows Server. W wersji 1.6.0 lub nowszy jest wymagany. Aby ustalić, jaka wersja jest zainstalowana, uruchom `Get-Command -Module PowerShellGet` polecenia.
    - Informacje ogólne: [Instalowanie modułu PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Polecenie: `Install-Module -Name PowerShellGet`.
    - Następujące parametry opcjonalne:
        - `-Proxy`. Określa serwer proxy dla żądania.
        - `-Force`. Pomija ostrzeżenie "już zainstalowane" i zainstalowanie najnowszej wersji.

    Jeśli nie używasz najnowszej wersji modułu PowerShellGet, otrzymasz ten błąd:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Ponowne uruchomienie programu PowerShell. Nie można załadować nowej wersji w bieżącej sesji. Nowej sesji programu PowerShell zostanie załadowany najnowszą wersję modułu PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Pobierz i zainstaluj moduł za pomocą galerii programu PowerShell

Te kroki będą pobierać modułu Az.ApplicationMonitor z galerii programu PowerShell.

1. Upewnij się, że spełniono wszystkie wymagania wstępne dotyczące galerii programu PowerShell.
2. Uruchom program PowerShell jako administrator, za pomocą zasad wykonania z podwyższonym poziomem uprawnień.
3. Zainstaluj moduł Az.ApplicationMonitor.
    - Informacje ogólne: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Polecenie: `Install-Module -Name Az.ApplicationMonitor`.
    - Następujące parametry opcjonalne:
        - `-Proxy`. Określa serwer proxy dla żądania.
        - `-AllowPrerelease`. Umożliwia instalację w wersji alfa i beta.
        - `-AcceptLicense`. Pomija monit "Zaakceptuj licencję"
        - `-Force`. Pomija ostrzeżenie "Niezaufanych repozytorium".

## <a name="download-and-install-the-module-manually-offline-option"></a>Pobierz i zainstaluj moduł ręcznie (opcja w trybie offline)

Jeśli z jakiegoś powodu nie można nawiązać połączenia modułu programu PowerShell, należy ręcznie Pobierz i zainstaluj moduł Az.ApplicationMonitor.

### <a name="manually-download-the-latest-nupkg-file"></a>Ręcznie Pobierz najnowszy plik nupkg

1. Przejdź do pozycji https://www.powershellgallery.com/packages/Az.ApplicationMonitor (Plik > Nowy > Inny).
2. Wybierz najnowszą wersję pliku w **historię wersji** tabeli.
3. W obszarze **opcje instalacji**, wybierz opcję **pobieranie ręczne**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Opcja 1: Zainstaluj w katalogu modułów programu PowerShell
Zainstaluj ręcznie pobranego modułu programu PowerShell do katalogu programu PowerShell, będą wykrywalny przez sesji programu PowerShell.
Aby uzyskać więcej informacji, zobacz [Instalowanie modułu programu PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Rozpakuj nupkg jako plik zip przy użyciu Expand-archiwum (v1.0.1.0)

- Opis: Wersję podstawową Microsoft.PowerShell.Archive (v1.0.1.0) nie można rozpakować nupkg pliki. Zmień nazwę pliku z rozszerzeniem zip.
- Informacje ogólne: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Polecenie:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Rozpakuj nupkg przy użyciu Expand-archiwum (v1.1.0.0)

- Opis: Rozpakowywanie plików nupkg bez konieczności zmieniania rozszerzenie, należy użyć bieżącą wersję rozwiń archiwum.
- Informacje ogólne: [Rozwiń archiwum](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) i [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Polecenie:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opcja 2: Rozpakuj i ręcznie zaimportować nupkg
Zainstaluj ręcznie pobranego modułu programu PowerShell do katalogu programu PowerShell, będą wykrywalny przez sesji programu PowerShell.
Aby uzyskać więcej informacji, zobacz [Instalowanie modułu programu PowerShell](https://docs.mircrosoft.com/powershell/developer/module/installing-a-powershell-module).

Instalowania modułu do żadnego innego katalogu, należy ręcznie zaimportować moduł za pomocą [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> Instaluje biblioteki DLL za pomocą ścieżek względnych.
> Store zawartość pakietu w katalogu zamierzony środowiska uruchomieniowego i upewnij się, że zezwalają na uprawnienia dostępu, odczytu, ale nie zapisu.

1. Zmień rozszerzenie na ".zip" i Wyodrębnij zawartość pakietu w katalogu instalacji zamierzone.
2. Znajdź Az.ApplicationMonitor.psd1 ścieżkę pliku.
3. Uruchom program PowerShell jako administrator, za pomocą zasad wykonania z podwyższonym poziomem uprawnień.
4. Ładowanie modułu za pomocą `Import-Module Az.ApplicationMonitor.psd1` polecenia.
    

## <a name="route-traffic-through-a-proxy"></a>Kierowanie ruchu za pośrednictwem serwera proxy

Podczas monitorowania komputera w prywatnej sieci intranet, należy przekierować ruch HTTP przez serwer proxy.

Polecenia programu PowerShell do pobrania i zainstalowania Az.ApplicationMonitor z galerii programu PowerShell obsługują `-Proxy` parametru.
Podczas pisania skryptów instalacji, przejrzyj poprzednich instrukcji.

Zestaw SDK usługi Application Insights będzie konieczne wysyłanie danych telemetrycznych aplikacji do firmy Microsoft. Firma Microsoft zaleca, skonfiguruj ustawienia serwera proxy dla aplikacji w pliku web.config. Aby uzyskać więcej informacji, zobacz [Insights aplikacji często zadawane pytania: Przekazywanie proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Włączanie monitorowania

Użyj `Enable-ApplicationInsightsMonitoring` polecenie, aby włączyć monitorowanie.

Zobacz [dokumentacja interfejsu API](status-monitor-v2-api-enable-monitoring.md) szczegółowy opis sposobu użycia tego polecenia cmdlet.



## <a name="next-steps"></a>Kolejne kroki

 Wyświetlanie telemetrii:

- [Eksplorowanie metryk](../../azure-monitor/app/metrics-explorer.md) do monitorowania wydajności i użycia.
- [Wyszukiwanie zdarzeń i dzienników](../../azure-monitor/app/diagnostic-search.md) do diagnozowania problemów.
- [Korzystanie z analizy](../../azure-monitor/app/analytics.md) dla bardziej zaawansowanych zapytań.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).

 Dodawanie kolejnych funkcji telemetrii:

- [Tworzenie testów sieci web](monitor-web-app-availability.md) aby upewnić się, że witryna pozostaje aktywna.
- [Dodawanie telemetrii klienta sieci web](../../azure-monitor/app/javascript.md) aby zobaczyć wyjątki pochodzące z kodu strony sieci web i umożliwić wywołania śledzenia.
- [Dodaj zestaw Application Insights SDK do kodu](../../azure-monitor/app/asp-net.md) dzięki czemu można wstawić ślad i rejestrować wywołania.

Wykonuj więcej zadań dzięki v2 Monitora stanu:

- Użyj zapoznaj się z przewodnikiem [Rozwiązywanie problemów z](status-monitor-v2-troubleshoot.md) Monitora stanu w wersji 2.
