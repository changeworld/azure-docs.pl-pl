---
title: Azure monitor stanu v2 — wprowadzenie | Microsoft Docs
description: Przewodnik Szybki Start dotyczący monitor stanu v2. Monitorowanie wydajności witryny sieci Web bez ponownego wdrażania witryny sieci Web. Współpracuje z usługą ASP.NET Web Apps hostowaną lokalnie, na maszynach wirtualnych lub na platformie Azure.
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
ms.openlocfilehash: a0c836c8ef947e190a0090b3435eec1c53ded436
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326257"
---
# <a name="get-started-with-status-monitor-v2"></a>Wprowadzenie do monitor stanu v2

Ten artykuł zawiera polecenia szybkiego startu, które powinny być wykonywane dla większości środowisk.
Instrukcje są zależne od Galeria programu PowerShell do dystrybucji aktualizacji.
Te polecenia obsługują parametr programu `-Proxy` PowerShell.

Aby uzyskać wyjaśnienie tych poleceń, instrukcje dotyczące dostosowywania i informacje dotyczące rozwiązywania problemów, zobacz [szczegółowe instrukcje](status-monitor-v2-detailed-instructions.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="download-and-install-via-powershell-gallery"></a>Pobierz i zainstaluj za pomocą Galeria programu PowerShell

### <a name="install-prerequisites"></a>Instalacja wymagań wstępnych
Uruchom program PowerShell jako administrator.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Zamknij program PowerShell.

### <a name="install-status-monitor-v2"></a>Zainstaluj monitor stanu v2
Uruchom program PowerShell jako administrator.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Włączanie monitorowania
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Pobierz i zainstaluj ręcznie (opcja offline)
### <a name="download-the-module"></a>Pobierz moduł
Ręcznie Pobierz najnowszą wersję modułu z [Galeria programu PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-status-monitor-v2"></a>Rozpakuj i zainstaluj monitor stanu v2
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Włączanie monitorowania
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Następne kroki

 Wyświetlanie telemetrii:

- [Poznaj metryki](../../azure-monitor/app/metrics-explorer.md) , aby monitorować wydajność i użycie.
- [Wyszukaj zdarzenia i dzienniki](../../azure-monitor/app/diagnostic-search.md) , aby zdiagnozować problemy.
- [Użyj analizy](../../azure-monitor/app/analytics.md) , aby uzyskać bardziej zaawansowane zapytania.
- [Tworzenie pulpitów nawigacyjnych](../../azure-monitor/app/overview-dashboard.md).

 Dodawanie kolejnych funkcji telemetrii:

- [Utwórz testy sieci Web](monitor-web-app-availability.md) , aby upewnić się, że witryna pozostaje aktywna.
- [Dodaj telemetrię klienta sieci Web](../../azure-monitor/app/javascript.md) , aby zobaczyć wyjątki z kodu strony sieci Web i włączyć wywołania śledzenia.
- [Dodaj do kodu zestaw SDK Application Insights](../../azure-monitor/app/asp-net.md) , aby móc wstawiać wywołania śledzenia i rejestrowania.

Więcej informacji o monitor stanu v2:

- Zapoznaj się z [szczegółowymi instrukcjami](status-monitor-v2-detailed-instructions.md) dla wyjaśnienia poleceń znalezionych w tym miejscu.
- Skorzystaj z naszego przewodnika, aby [rozwiązywać problemy z](status-monitor-v2-troubleshoot.md) Monitor stanu v2.
