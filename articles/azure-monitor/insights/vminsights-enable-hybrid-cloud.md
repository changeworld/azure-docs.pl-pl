---
title: Włącz Azure Monitor (wersja zapoznawcza) dla środowiska hybrydowego | Microsoft Docs
description: W tym artykule opisano sposób włączania Azure Monitor dla maszyn wirtualnych dla hybrydowego środowiska chmury, które zawiera co najmniej jedną maszynę wirtualną.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: bd44eebf8aceaf7fe32cf8cf1b1152db32acb344
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362274"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Włącz Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) dla środowiska hybrydowego

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule wyjaśniono, jak włączyć Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) dla maszyn wirtualnych lub komputerów fizycznych hostowanych w centrum danych lub w innym środowisku chmury. Po zakończeniu tego procesu pomyślnie uruchomiono monitorowanie maszyn wirtualnych w środowisku i Dowiedz się, czy występują problemy z wydajnością lub dostępnością.

Przed rozpoczęciem należy zapoznać się z wymaganiami [wstępnymi](vminsights-enable-overview.md) i upewnić się, że Twoja subskrypcja i zasoby spełniają wymagania. Zapoznaj się z wymaganiami i metodami wdrażania dla [log Analytics Linux i Windows Agent](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych nie przesyłać żadnych danych, a nie wymaga wprowadzania zmian w zapory i porty. Dane mapy są zawsze przesyłane przez agenta Log Analytics do usługi Azure Monitor, bezpośrednio lub przez [bramę pakietu Operations Management Suite](../../azure-monitor/platform/gateway.md) , jeśli zasady zabezpieczeń IT nie zezwalają komputerom w sieci na łączenie się z Internetem.

Kroki umożliwiające wykonanie tego zadania są podsumowane w następujący sposób:

1. Zainstaluj Log Analytics agenta dla systemu Windows lub Linux. Przed zainstalowaniem agenta zapoznaj się z artykułem [Omówienie agenta log Analytics](../platform/log-analytics-agent.md) , aby poznać wymagania wstępne i metody wdrażania systemu.

2. Pobierz i Zainstaluj agenta Azure Monitor dla maszyn wirtualnych zależności mapy dla [systemu Windows](https://aka.ms/dependencyagentwindows) lub [Linux](https://aka.ms/dependencyagentlinux).

3. Należy włączyć zbieranie liczników wydajności.

4. Wdrażanie usługi Azure Monitor dla maszyn wirtualnych.

>[!NOTE]
>Informacje opisane w tym artykule dotyczące wdrażania agenta zależności dotyczą również [rozwiązania Service map](service-map.md).  

## <a name="install-the-dependency-agent-on-windows"></a>Instalowanie agenta zależności na Windows

Agenta zależności można zainstalować ręcznie na komputerach z systemem Windows, uruchamiając `InstallDependencyAgent-Windows.exe`. Po uruchomieniu tego pliku wykonywalnego bez żadnych opcji, uruchamia Kreatora instalacji, które można wykonać, aby zainstalować agenta interaktywnie.

>[!NOTE]
>Do zainstalowania lub odinstalowania agenta wymagane są uprawnienia *administratora* .

W poniższej tabeli wymieniono parametry, które są obsługiwane przez Instalatora agenta z wiersza polecenia.

| Parametr | Opis |
|:--|:--|
| /? | Zwraca listę opcji wiersza polecenia. |
| /S | Wykonanie dyskretnej instalacji bez interakcji użytkownika. |

Na przykład aby uruchomić program instalacyjny z parametrem `/?`, wprowadź **InstallDependencyAgent-Windows. exe/?** .

Pliki dla agenta zależności systemu Windows są domyślnie instalowane w *katalogu C:\Program Files\Microsoft Agent zależności* . Jeśli nie można uruchomić agenta zależności po zakończeniu instalacji, zapoznaj się z dziennikami, aby uzyskać szczegółowe informacje o błędzie. Katalog dziennika to *%ProgramFiles%\Microsoft Dependency Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Instalowanie agenta zależności w systemie Linux

Agent zależności jest instalowany na serwerach z systemem Linux z *InstallDependencyAgent-linux64. bin*, skryptu powłoki z samowyodrębniającym się plikiem binarnym. Plik można uruchomić za pomocą `sh` lub dodać uprawnienia wykonywania do samego pliku.

>[!NOTE]
> Aby zainstalować lub skonfigurować agenta, wymagany jest dostęp na poziomie administratora.
>

| Parametr | Opis |
|:--|:--|
| -help | Pobierz listę opcji wiersza polecenia. |
| -s | Przeprowadź instalację cichą bez monitowania użytkownika. |
| — Sprawdź | Sprawdzanie uprawnień i systemu operacyjnego, ale nie należy instalować agenta. |

Na przykład aby uruchomić program instalacyjny z parametrem `-help`, wprowadź **InstallDependencyAgent-linux64. bin-help**.

Zainstaluj agenta zależności systemu Linux jako element główny, uruchamiając polecenie `sh InstallDependencyAgent-Linux64.bin`.

Jeśli agenta zależności nie powiedzie się, sprawdź dzienniki Aby uzyskać szczegółowe informacje o błędzie. W przypadku agentów systemu Linux katalog dziennika to */var/opt/Microsoft/Dependency-Agent/log*.

Pliki agenta zależności są umieszczone w następujących katalogach:

| Pliki | Lokalizacja |
|:--|:--|
| Pliki jądra | /opt/microsoft/dependency-agent |
| Pliki dziennika | /var/opt/microsoft/dependency-agent/log |
| Plik konfiguracji | /etc/opt/microsoft/dependency-agent/config |
| Pliki wykonywalne usługi | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Pliki binarne magazynu | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Przykłady skryptów instalacji

Łatwe wdrażanie agenta zależności na wiele serwerów na raz, w poniższym przykładzie skrypt znajduje się pobrać i zainstalować agenta zależności w systemie Windows lub Linux.

### <a name="powershell-script-for-windows"></a>Skrypt programu PowerShell dla systemu Windows

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Skrypt powłoki dla systemu Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Konfiguracja żądanego stanu

Aby wdrożyć agenta zależności za pomocą Desired State Configuration (DSC), możesz użyć modułu xPSDesiredStateConfiguration z poniższy przykład kodu:

```powershell
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>Włącz liczniki wydajności

Jeśli obszar roboczy usługi Log Analytics, który odwołuje się do niej rozwiązanie nie jest już skonfigurowany można zebrać liczników wydajności wymaganych przez to rozwiązanie, należy je włączyć. Można to zrobić na jeden z dwóch sposobów:
* Ręczne, zgodnie z opisem w temacie [źródła danych wydajności systemu Windows i Linux w log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Pobierając i uruchamiając skrypt programu PowerShell, który jest dostępny z [galerii Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Wdrażanie usługi Azure Monitor dla maszyn wirtualnych

Ta metoda obejmuje szablon JSON, który określa konfigurację włączanie składników rozwiązania w Twoim obszarze roboczym usługi Log Analytics.

Jeśli nie wiesz, jak wdrażać zasoby przy użyciu szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Aby korzystać z interfejsu wiersza polecenia platformy Azure, należy najpierw zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie. Musi być uruchomiona wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować swoją wersję, uruchom `az --version`. Aby zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Tworzenie i wykonywanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Zapisz ten plik jako *installsolutionsforvminsights. JSON* w folderze lokalnym.

1. Przechwyć wartości dla *obszarów roboczych*, *ResourceGroupName*i *WorkspaceLocation*. Wartość dla *obszaru roboczegoname* jest nazwą obszaru roboczego log Analytics. Wartość parametru *WorkspaceLocation* to region, w którym jest zdefiniowany obszar roboczy.

1. Możesz wdrożyć ten szablon przy użyciu następującego polecenia programu PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Zmiana konfiguracji może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego i zawiera wynik:

    ```powershell
    provisioningState       : Succeeded
    ```
   Po włączeniu monitorowania może zająć około 10 minut, zanim będzie można wyświetlić stan kondycji i metryki dla komputera hybrydowych.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="vm-doesnt-appear-on-the-map"></a>Maszyna wirtualna nie jest wyświetlana na mapie

Jeśli instalacja agenta zależności zakończyła się pomyślnie, ale nie widzisz komputera na mapie, należy zdiagnozować problem, wykonując następujące kroki.

1. Agent zależności zainstalowano pomyślnie? Aby to sprawdzić przez sprawdzanie, czy usługa jest zainstalowana i uruchomiona.

    **Windows**: Wyszukaj usługę o nazwie "Microsoft Dependency Agent".

    **Linux**: poszukaj uruchomionego procesu "Microsoft-Dependency-Agent".

2. Czy korzystasz z [warstwy cenowej bezpłatna log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)? Plan bezpłatny pozwala na maksymalnie pięć unikatowych komputerów. Wszystkie kolejne komputery nie będą widoczne na mapie, nawet jeśli poprzednie pięć nie wyśle już danych.

3. Czy komputer wysyła dane dziennika i wydajności do dzienników Azure Monitor? Wykonaj następujące zapytanie dotyczące komputera:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Czy zwraca co najmniej jeden wynik? To najnowsze dane? W takim przypadku Agent Log Analytics działa prawidłowo i komunikuje się z usługą. Jeśli nie, sprawdź, czy Agent na serwerze: [log Analytics Agent na potrzeby rozwiązywania problemów z systemem Windows](../platform/agent-windows-troubleshoot.md) lub [agenta log Analytics do rozwiązywania problemów z systemem Linux](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Komputer jest wyświetlany na mapie, ale nie ma procesów

Jeśli widzisz serwer na mapie, ale nie ma on danych procesu ani połączenia, oznacza to, że Agent zależności został zainstalowany i uruchomiony, ale Sterownik jądra nie został załadowany.

Sprawdź plik C:\Program Files\Microsoft zależności Agent\logs\wrapper.log (Windows) lub pliku /var/opt/microsoft/dependency-agent/log/service.log (Linux). Ostatnie wiersze pliku powinno wskazywać, dlaczego jądra nie została załadowana. Na przykład jądra mogą nie być obsługiwane w systemie Linux, jeśli zaktualizowane swoje jądra.


## <a name="next-steps"></a>Następne kroki

Po włączeniu monitorowania dla maszyn wirtualnych te informacje są dostępne do analizy za pomocą Azure Monitor dla maszyn wirtualnych.

- Aby wyświetlić odnalezione zależności aplikacji, zobacz [view Azure monitor dla maszyn wirtualnych map](vminsights-maps.md).

- Aby identyfikować wąskie gardła i ogólne wykorzystanie z wydajnością maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny wirtualnej platformy Azure](vminsights-performance.md).
