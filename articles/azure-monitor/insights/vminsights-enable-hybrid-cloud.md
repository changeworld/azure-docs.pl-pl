---
title: Włączanie usługi Azure Monitor (wersja zapoznawcza) w przypadku środowiska hybrydowego z | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak włączyć usługi Azure Monitor dla maszyn wirtualnych dla środowisko chmury hybrydowej, który zawiera co najmniej jednej maszyny wirtualnej.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2019
ms.author: magoedte
ms.openlocfilehash: bc26cc0654aac9416bf31ffccf426648e3a8b8d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122542"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Włączanie usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) dla środowisk hybrydowych

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

W tym artykule opisano sposób włączania usługi Azure Monitor dla maszyn wirtualnych (wersja zapoznawcza) dla maszyn wirtualnych lub fizycznych komputerów, hostowanej w centrum danych lub w innym środowisku chmury. Po zakończeniu tego procesu zostanie pomyślnie rozpoczęto monitorowanie maszyn wirtualnych w danym środowisku i Dowiedz się, jeśli występują one wszelkie problemy z wydajnością lub dostępnością. 

Przed rozpoczęciem należy przejrzeć [wymagania wstępne](vminsights-enable-overview.md) i sprawdź, czy Twoja subskrypcja i zasoby spełniają wymagania. Przegląd wymagań i metod wdrażania [agenta Log Analytics w systemie Linux i Windows](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych nie przesyłać żadnych danych, a nie wymaga wprowadzania zmian w zapory i porty. Dane mapy są zawsze przesyłane przez agenta usługi Log Analytics w usłudze Azure Monitor, bezpośrednio lub za pośrednictwem [bramy pakietu Operations Management Suite](../../azure-monitor/platform/gateway.md) Jeśli Twoje informatyczne zasady zabezpieczeń nie pozwalają komputerom w sieci połączenie z Internetem.

Kroki do wykonania tego zadania są podsumować w następujący sposób:

1. Zainstaluj agenta usługi Log Analytics dla Windows lub Linux. Przed zainstalowaniem agenta, przejrzyj [omówienie agenta usługi Log Analytics](../platform/log-analytics-agent.md) artykuł, aby zrozumieć wymagania wstępne systemu i metod wdrażania.

2. Pobierz i zainstaluj usługi Azure Monitor dla agenta zależności mapy maszyn wirtualnych dla [Windows](https://aka.ms/dependencyagentwindows) lub [Linux](https://aka.ms/dependencyagentlinux).

3. Należy włączyć zbieranie liczników wydajności.

4. Wdrażanie usługi Azure Monitor dla maszyn wirtualnych.

## <a name="install-the-dependency-agent-on-windows"></a>Instalowanie agenta zależności na Windows
Można zainstalować agenta zależności ręcznie na komputerach Windows przez uruchomienie `InstallDependencyAgent-Windows.exe`. Po uruchomieniu tego pliku wykonywalnego bez żadnych opcji, uruchamia Kreatora instalacji, które można wykonać, aby zainstalować agenta interaktywnie.

>[!NOTE]
>*Administrator* uprawnienia są wymagane do zainstalowania lub odinstalowania agenta.

W poniższej tabeli wymieniono parametry, które są obsługiwane przez Instalatora agenta z wiersza polecenia.

| Parametr | Opis |
|:--|:--|
| /? | Zwraca listę opcji wiersza polecenia. |
| /S | Wykonanie dyskretnej instalacji bez interakcji użytkownika. |

Na przykład, aby uruchomić program instalacyjny z `/?` parametrów, wprowadź **InstallDependencyAgent Windows.exe /?** .

Pliki agenta zależności Windows są zainstalowane w *C:\Program Files\Microsoft Dependency Agent* domyślnie. Jeśli agenta zależności nie powiedzie się po zakończeniu instalacji, sprawdź dzienniki Aby uzyskać szczegółowe informacje o błędzie. Katalog dziennika jest *%Programfiles%\Microsoft Agent\logs zależności*.

## <a name="install-the-dependency-agent-on-linux"></a>Instalowanie agenta zależności w systemie Linux
Agent zależności jest zainstalowany na serwery z systemem Linux z *InstallDependencyAgent Linux64.bin*, skrypt powłoki, za pomocą samorozpakowujący się plik binarny. Można uruchomić go za pomocą `sh` lub Dodaj uprawnienia do wykonywania do pliku sam.

>[!NOTE]
> Aby zainstalować lub skonfigurować agenta, wymagany jest dostęp na poziomie administratora.
>

| Parametr | Opis |
|:--|:--|
| -help | Pobierz listę opcji wiersza polecenia. |
| -s | Przeprowadź instalację cichą bez monitowania użytkownika. |
| — Sprawdź | Sprawdzanie uprawnień i systemu operacyjnego, ale nie należy instalować agenta. |

Na przykład, aby uruchomić program instalacyjny z `-help` parametrów, wprowadź **InstallDependencyAgent Linux64.bin — Pomoc**.

Instalowanie agenta zależności systemu Linux jako użytkownik główny, uruchamiając polecenie `sh InstallDependencyAgent-Linux64.bin`.

Jeśli agenta zależności nie powiedzie się, sprawdź dzienniki Aby uzyskać szczegółowe informacje o błędzie. Na agentach systemu Linux jest katalog dziennika */var/opt/microsoft/dependency-agent/log*.

Pliki agenta zależności są umieszczone w następujących katalogach:

| Pliki | Lokalizacja |
|:--|:--|
| Pliki jądra | /opt/microsoft/dependency-agent |
| Pliki dziennika | /var/opt/microsoft/dependency-agent/log |
| Plik konfiguracji | /etc/opt/microsoft/dependency-agent/config |
| Pliki wykonywalne usługi | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Pliki binarne magazynu | /var/opt/microsoft/dependency-agent/storage |

## <a name="enable-performance-counters"></a>Włącz liczniki wydajności
Jeśli obszar roboczy usługi Log Analytics, który odwołuje się do niej rozwiązanie nie jest już skonfigurowany można zebrać liczników wydajności wymaganych przez to rozwiązanie, należy je włączyć. Możesz to zrobić na dwa sposoby:
* Ręcznie, zgodnie z opisem w [Windows i Linux źródła danych dotyczących wydajności w usłudze Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Pobierając i uruchamiając skrypt programu PowerShell, który jest dostępny z [galerii programu PowerShell systemu Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Wdrażanie usługi Azure Monitor dla maszyn wirtualnych
Ta metoda obejmuje szablon JSON, który określa konfigurację włączanie składników rozwiązania w Twoim obszarze roboczym usługi Log Analytics.

Jeśli nie wiesz, jak wdrażać zasoby przy użyciu szablonu, zobacz:
* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../../azure-resource-manager/resource-group-template-deploy.md)
* [Wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Aby użyć wiersza polecenia platformy Azure, należy najpierw zainstalować i korzystać z interfejsu wiersza polecenia lokalnie. Musi być uruchomiona wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Aby zidentyfikować wersję, uruchom `az --version`. Aby zainstalować lub uaktualnić wiersza polecenia platformy Azure, zobacz [zainstalować interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Zapisz ten plik jako *installsolutionsforvminsights.json* do folderu lokalnego.

1. Przechwytywanie wartości *WorkspaceName*, *ResourceGroupName*, i *WorkspaceLocation*. Wartość *WorkspaceName* to nazwa obszaru roboczego usługi Log Analytics. Wartość *WorkspaceLocation* jest region, w obszarze roboczym jest zdefiniowany w.

1. Możesz wdrożyć ten szablon przy użyciu następującego polecenia programu PowerShell:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Zmiana konfiguracji może potrwać kilka minut. Po jej zakończeniu zostanie wyświetlony komunikat podobny do poniższego, która zawiera wynik:

    ```powershell
    provisioningState       : Succeeded
    ```
   Po włączeniu monitorowania może zająć około 10 minut, zanim będzie można wyświetlić stan kondycji i metryki dla komputera hybrydowych.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy monitorowanie jest włączone dla maszyn wirtualnych, te informacje są dostępne dla analizy dzięki usłudze Azure Monitor dla maszyn wirtualnych.
 
- Aby dowiedzieć się, jak korzystać z funkcji Health, zobacz [widok usługi Azure Monitor kondycji maszyn wirtualnych](vminsights-health.md).
- Aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md).
- Aby zidentyfikować wąskie gardła i ogólnego użycia z wydajnością maszyny Wirtualnej, zobacz [maszyny Wirtualnej platformy Azure w widoku wydajności](vminsights-performance.md).
- Aby obejrzeć zależności odnalezionych aplikacji, zobacz [widok usługi Azure Monitor dla maszyn wirtualnych mapy](vminsights-maps.md).