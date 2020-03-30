---
title: Włącz usługę Azure Monitor w środowisku hybrydowym | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób włączania usługi Azure Monitor dla maszyn wirtualnych dla środowiska chmury hybrydowej, które zawiera co najmniej jedną maszynę wirtualną.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: 734f61c2e96002516e9e15af88d2c6b0fce00e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480746"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-environment"></a>Włącz usługę Azure Monitor dla maszyn wirtualnych w środowisku hybrydowym

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule wyjaśniono, jak włączyć usługę Azure Monitor dla maszyn wirtualnych dla maszyn wirtualnych lub komputerów fizycznych hostowanych w centrum danych lub innym środowisku chmury. Po zakończeniu tego procesu pomyślnie rozpoczniesz monitorowanie maszyn wirtualnych w danym środowisku i dowiedzieć się, czy występują one problemy z wydajnością lub dostępnością.

Przed rozpoczęciem należy zapoznać się z [wymaganiami wstępnymi](vminsights-enable-overview.md) i sprawdzić, czy subskrypcja i zasoby spełniają wymagania. Przejrzyj wymagania i metody wdrażania [dla usługi Log Analytics Linux i windows agent](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Agent zależności mapy map usługi Azure Monitor dla maszyn wirtualnych nie przesyła żadnych danych i nie wymaga żadnych zmian w zapory lub portach. Dane mapy są zawsze przesyłane przez agenta usługi Log Analytics do usługi Azure Monitor bezpośrednio lub za pośrednictwem [bramy pakietu Operations Management Suite,](../../azure-monitor/platform/gateway.md) jeśli zasady zabezpieczeń IT nie zezwalają komputerom w sieci na łączenie się z Internetem.

Kroki, aby wykonać to zadanie są podsumowane w następujący sposób:

1. Zainstaluj agenta analizy dzienników dla systemu Windows lub Linux. Przed zainstalowaniem agenta zapoznaj się z [omówieniem agenta usługi Log Analytics,](../platform/log-analytics-agent.md) aby zapoznać się z wymaganiami wstępnymi systemu i metodami wdrażania.

2. Pobierz i zainstaluj agenta zależności mapy map usługi Azure Monitor dla maszyn wirtualnych dla [systemu Windows](https://aka.ms/dependencyagentwindows) lub [Linux](https://aka.ms/dependencyagentlinux).

3. Włącz zbieranie liczników wydajności.

4. Wdrażanie usługi Azure Monitor dla maszyn wirtualnych.

>[!NOTE]
>Informacje opisane w tym artykule dotyczące wdrażania agenta zależności ma również zastosowanie do [rozwiązania mapy usług.](service-map.md)  

## <a name="install-the-dependency-agent-on-windows"></a>Instalowanie agenta zależności w systemie Windows

Agent zależności można zainstalować ręcznie na komputerach `InstallDependencyAgent-Windows.exe`z systemem Windows, uruchamiając program . Jeśli ten plik wykonywalny zostanie uruchomiony bez żadnych opcji, zostanie uruchomiony kreator instalacji, który można wykonać, aby zainstalować agenta interaktywnie.

>[!NOTE]
>*Uprawnienia administratora* są wymagane do zainstalowania lub odinstalowania agenta.

W poniższej tabeli przedstawiono parametry obsługiwane przez instalatora agenta z wiersza polecenia.

| Parametr | Opis |
|:--|:--|
| /? | Zwraca listę opcji wiersza polecenia. |
| / S | Wykonuje instalację dyskretną bez interakcji z użytkownikiem. |

Na przykład, aby uruchomić program `/?` instalacyjny z parametrem, wprowadź **InstallDependencyAgent-Windows.exe /?**.

Pliki agenta zależności systemu Windows są domyślnie instalowane w *programie C:\Program Files\Microsoft Dependency Agent.* Jeśli agent zależności nie uruchamia się po zakończeniu instalacji, sprawdź dzienniki, aby uzyskać szczegółowe informacje o błędzie. Katalog dziennika to *%Programfiles%\Agent zależności firmy Microsoft\logi*.

## <a name="install-the-dependency-agent-on-linux"></a>Instalowanie agenta zależności w systemie Linux

Agent zależności jest zainstalowany na serwerach Linux z *InstallDependencyAgent-Linux64.bin*, skrypt powłoki z samorozdyshowing binarny. Plik można uruchomić za `sh` pomocą lub dodać uprawnienia do wykonywania do samego pliku.

>[!NOTE]
> Aby zainstalować lub skonfigurować agenta, wymagany jest dostęp na poziomie administratora.
>

| Parametr | Opis |
|:--|:--|
| — Pomoc | Pobierz listę opcji wiersza polecenia. |
| -s | Przeprowadź instalację cichą bez monitowania użytkownika. |
| --sprawdź | Sprawdź uprawnienia i system operacyjny, ale nie instaluj agenta. |

Na przykład, aby uruchomić program `-help` instalacyjny z parametrem, wprowadź **InstallDependencyAgent-Linux64.bin -help**.

Zainstaluj agenta zależności linuksa jako `sh InstallDependencyAgent-Linux64.bin`root, uruchamiając polecenie .

Jeśli agent zależności nie może uruchomić, sprawdź dzienniki szczegółowe informacje o błędzie. W agentach systemu Linux katalog dziennika to */var/opt/microsoft/dependency-agent/log*.

Pliki agenta zależności są umieszczane w następujących katalogach:

| Pliki | Lokalizacja |
|:--|:--|
| Pliki jądra | /opt/microsoft/dependency-agent |
| Pliki dziennika | /var/opt/microsoft/dependency-agent/log |
| Plik konfiguracji | /etc/opt/microsoft/dependency-agent/config |
| Pliki wykonywalne usługi | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Pliki binarne magazynu | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>Przykłady skryptów instalacji

Aby łatwo wdrożyć agenta zależności na wielu serwerach jednocześnie, poniższy przykład skryptu jest dostarczany do pobrania i zainstalowania agenta zależności w systemie Windows lub Linux.

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

## <a name="desired-state-configuration"></a>Desired State Configuration

Aby wdrożyć agenta zależności przy użyciu żądanej konfiguracji stanu (DSC), można użyć modułu xPSDesiredStateConfiguration z następującym przykładowym kodem:

```powershell
configuration VMInsights {

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

## <a name="enable-performance-counters"></a>Włączanie liczników wydajności

Jeśli obszar roboczy usługi Log Analytics, do którego odwołuje się rozwiązanie, nie jest jeszcze skonfigurowany do zbierania liczników wydajności wymaganych przez rozwiązanie, należy je włączyć. Można to zrobić na jeden z dwóch sposobów:
* Ręcznie, zgodnie z opisem w [źródłach danych wydajności systemu Windows i Linux w usłudze Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Pobierając i uruchamiając skrypt programu PowerShell dostępny w [galerii programu Azure PowerShell](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Wdrażanie usługi Azure Monitor dla maszyn wirtualnych

Ta metoda zawiera szablon JSON, który określa konfigurację włączania składników rozwiązania w obszarze roboczym usługi Log Analytics.

Jeśli nie wiesz, jak wdrożyć zasoby przy użyciu szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/templates/deploy-powershell.md)
* [Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Aby korzystać z interfejsu wiersza polecenia platformy Azure, należy najpierw zainstalować i używać interfejsu wiersza polecenia lokalnie. Musi być uruchomiony interfejsu wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować swoją `az --version`wersję, uruchom polecenie . Aby zainstalować lub uaktualnić platformę Azure CLI, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                        "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('VMInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'VMInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Zapisz ten plik jako *installsolutionsforvminsights.json* w folderze lokalnym.

1. Przechwytywanie wartości *nazw workspace,* *resourcegroupname*i *miejsca pracy*. Wartość *WorkspaceName* jest nazwą obszaru roboczego usługi Log Analytics. Wartość *obszaru roboczegoLokalizacja* jest regionem zdefiniowanym w obszarze roboczym.

1. Możesz przystąpić do wdrożenia tego szablonu przy użyciu następującego polecenia programu PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Zmiana konfiguracji może potrwać kilka minut, aby zakończyć. Po zakończeniu zostanie wyświetlony komunikat podobny do następującego i zawiera wynik:

    ```powershell
    provisioningState       : Succeeded
    ```
   Po włączeniu monitorowania może upłynąć około 10 minut, zanim będzie można wyświetlić stan kondycji i metryki dla komputera hybrydowego.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="vm-doesnt-appear-on-the-map"></a>Maszyna wirtualna nie jest wyświetlana na mapie

Jeśli instalacja agenta zależności powiodła się, ale nie widać komputera na mapie, zdiagnozować problem, wykonując następujące kroki.

1. Czy agent zależności jest pomyślnie zainstalowany? Można to sprawdzić, sprawdzając, czy usługa jest zainstalowana i uruchomiona.

    **Windows**: Poszukaj usługi o nazwie "Agent zależności firmy Microsoft".

    **Linux**: Poszukaj uruchomionego procesu "microsoft-dependency-agent".

2. Czy korzystasz z [bezpłatnej warstwy cenowej Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) Bezpłatny plan pozwala na maksymalnie pięć unikalnych komputerów. Kolejne komputery nie pojawią się na mapie, nawet jeśli poprzednie pięć nie wysyła już danych.

3. Czy komputer wysyła dane dziennika i perf do dzienników usługi Azure Monitor? Wykonaj następujące zapytanie dotyczące komputera:

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    Czy zwrócił jeden lub więcej wyników? Czy dane są najnowsze? Jeśli tak, agent usługi Log Analytics działa poprawnie i komunikuje się z usługą. Jeśli nie, sprawdź agenta na serwerze: [Agent analizy dzienników dla systemu Windows rozwiązywania problemów](../platform/agent-windows-troubleshoot.md) lub [agenta analizy dzienników dla systemu Linux rozwiązywania problemów](../platform/agent-linux-troubleshoot.md).

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>Komputer pojawia się na mapie, ale nie ma żadnych procesów

Jeśli serwer jest widoczny na mapie, ale nie ma danych procesowych ani danych połączenia, oznacza to, że agent zależności jest zainstalowany i uruchomiony, ale sterownik jądra nie został załadowany.

Sprawdź plik C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file (Windows) lub /var/opt/microsoft/dependency-agent/log/service.log (Linux). Ostatnie wiersze pliku powinny wskazywać, dlaczego jądro nie załadowane. Na przykład jądro może nie być obsługiwane w systemie Linux, jeśli jądro zostanie zaktualizowane.


## <a name="next-steps"></a>Następne kroki

Teraz, gdy monitorowanie jest włączone dla maszyn wirtualnych, te informacje są dostępne do analizy za pomocą usługi Azure Monitor dla maszyn wirtualnych.

- Aby wyświetlić odnalezione zależności aplikacji, zobacz [Wyświetlanie usługi Azure Monitor dla map maszyn wirtualnych.](vminsights-maps.md)

- Aby zidentyfikować wąskie gardła i ogólne wykorzystanie wydajności maszyny wirtualnej, zobacz [Wyświetlanie wydajności maszyny Wirtualnej platformy Azure.](vminsights-performance.md)
