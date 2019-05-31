---
title: Usługa Azure Monitor stanu v2 wprowadzenie | Dokumentacja firmy Microsoft
description: Przewodnik Szybki Start dla monitora stanu w wersji 2. Monitorowanie wydajności witryny sieci Web bez konieczności ponownego wdrażania witryny sieci Web. Działa z aplikacjami internetowymi platformy ASP.NET hostowanymi lokalnie na maszynach wirtualnych lub platformie Azure.
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
ms.openlocfilehash: 3dcd50c3aa516f2af40c1e28a36a8039773e069c
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255057"
---
# <a name="getting-started-with-status-monitor-v2"></a>Wprowadzenie do Monitora stanu w wersji 2

Ten dokument zawiera poleceń szybkiego startu, powinny działać w przypadku większości środowisk. Te instrukcje są zależne od galerii programu PowerShell w celu przeprowadzania dystrybucji aktualizacji. Te polecenia obsługi programu PowerShell `-Proxy` parametru.

Przejrzyj nasze [szczegółowe instrukcje](status-monitor-v2-detailed-instructions.md) strony objaśnienia dotyczące tych poleceń, instrukcje dotyczące sposobu dostosowywania i jak rozwiązywać problemy.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

> [!IMPORTANT]
> Monitor stanu w wersji 2 jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="download--install-via-powershell-gallery"></a>Pobieranie i instalowanie za pomocą galerii programu PowerShell

### <a name="install-prerequisites"></a>Instalacja wymagań wstępnych
Uruchom program PowerShell jako Administrator
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Exit PowerShell

### <a name="install-status-monitor-v2"></a>Stan instalacji monitorowanie v2
Uruchom program PowerShell jako Administrator
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Włączanie monitorowania
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download--install-manually-offline-option"></a>Pobierz i zainstaluj ręcznie (opcja w trybie offline)
### <a name="manual-download"></a>Pobieranie ręczne
Ręcznie Pobierz najnowszą wersję modułu na podstawie: https://www.powershellgallery.com/packages/Az.ApplicationMonitor

### <a name="unzip-and-install-status-monitor-v2"></a>Rozpakowywanie i instalowanie Monitora stanu w wersji 2
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.2.1-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Włączanie monitorowania
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



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

- Przegląd [szczegółowe instrukcje](status-monitor-v2-detailed-instructions.md) objaśnienia dotyczące poleceń, w tym przewodniku.
- Użyj zapoznaj się z przewodnikiem [rozwiązywanie](status-monitor-v2-troubleshoot.md) Monitora stanu w wersji 2.
