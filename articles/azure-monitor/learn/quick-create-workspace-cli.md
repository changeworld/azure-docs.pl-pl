---
title: Utwórz obszar roboczy usługi Log Analytics przy użyciu wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć obszar roboczy usługi Log Analytics umożliwia zarządzanie rozwiązaniami i gromadzenia danych z środowiska w chmurze i lokalnych przy użyciu wiersza polecenia platformy Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2019
ms.openlocfilehash: 89d397574c423e28bcbb0fec5ddd45959a737a93
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77659888"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Utwórz obszar roboczy usługi Log Analytics przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0

Interfejs wiersza polecenia platformy Azure 2.0 umożliwia tworzenie zasobów platformy Azure i zarządzanie nimi z poziomu wiersza polecenia lub za pomocą skryptów. Ten przewodnik Szybki Start przedstawia sposób wdrażania obszaru roboczego Log Analytics w programie Azure Monitor przy użyciu interfejsu wiersza polecenia platformy Azure 2,0. Obszar roboczy Log Analytics jest unikatowym środowiskiem dla Azure Monitor danych dziennika. Każdy obszar roboczy ma własne repozytorium danych i konfigurację, a źródła danych i rozwiązania są skonfigurowane do przechowywania danych w określonym obszarze roboczym. Musisz mieć Log Analytics obszar roboczy, jeśli zamierzasz zbierać dane z następujących źródeł:

* Zasoby platformy Azure w ramach subskrypcji  
* Lokalnych komputerów monitorowanych przez program System Center Operations Manager  
* Kolekcje urządzeń z Configuration Manager  
* Dane diagnostyczne lub dziennika z usługi Azure Storage  

W przypadku innych źródeł, takie jak maszyny wirtualne platformy Azure i Windows lub maszyny wirtualne systemu Linux w środowisku zobacz następujące tematy:

* [Zbieranie danych z usługi Azure Virtual Machines](../learn/quick-collect-azurevm.md)
* [Zbieranie danych z hybrydowego komputera z systemem Linux](../learn/quick-collect-linux-computer.md)
* [Zbieranie danych z hybrydowego komputera z systemem Windows](quick-collect-windows-computer.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
Utwórz obszar roboczy za pomocą [AZ Group Deployment Create](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create). Poniższy przykład tworzy obszar roboczy w lokalizacji *Wschodnie* przy użyciu szablonu Menedżer zasobów z komputera lokalnego. Szablon JSON jest skonfigurowany do tylko wyświetlenie monitu o nazwę obszaru roboczego i określenie wartości domyślnej dla innych parametrów, które prawdopodobnie będzie służyć jako standardowej konfiguracji w danym środowisku. Możesz także przechowywać szablon na koncie magazynu platformy Azure w celu zapewnienia dostępu współdzielonego w Twojej organizacji. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../../azure-resource-manager/templates/deploy-cli.md)

Aby uzyskać informacje o obsługiwanych regionach, zobacz [regiony log Analytics jest dostępny w](https://azure.microsoft.com/regions/services/) i Wyszukaj Azure monitor w polu **Wyszukaj produkt** .

Następujące parametry ustawiona wartość domyślna:

* Lokalizacja — wartość domyślna to wschodnie stany USA
* Jednostka SKU — wartość domyślna to nowej warstwy cenowej na GB, wydana w kwietniu 2018 r., model cen

>[!WARNING]
>W przypadku tworzenia lub konfigurowania obszaru roboczego Log Analytics w ramach subskrypcji, która została wybrana w nowym modelu cen 2018 kwietnia, jedyną prawidłową warstwą cenową Log Analytics jest **PerGB2018**.
>

### <a name="create-and-deploy-template"></a>Tworzenie i wdrażanie szablonu

1. Skopiuj i wklej następującą składnię JSON do pliku:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Edytuj szablon do własnych wymagań. Zapoznaj się z tematem dokumentacja [szablonu Microsoft. OperationalInsights/Workspaces](https://docs.microsoft.com/azure/templates/microsoft.operationalinsights/workspaces) , aby dowiedzieć się, jakie właściwości i wartości są obsługiwane.
3. Zapisz ten plik jako **deploylaworkspacetemplate. JSON** w folderze lokalnym.   
4. Wszystko jest teraz gotowe do wdrożenia tego szablonu. Użyj następujących poleceń z folderu zawierającego szablon. Po wyświetleniu monitu o nazwę obszaru roboczego Podaj nazwę globalnie unikatową we wszystkich subskrypcjach platformy Azure.

    ```azurecli
    az group deployment create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat podobny do poniższego, który zawiera wynik:

![Przykład wyniku, gdy wdrożenie jest ukończone](media/quick-create-workspace-cli/template-output-01.png)

## <a name="next-steps"></a>Następne kroki
Teraz, gdy masz dostępnego obszaru roboczego, możesz skonfigurować zbieranie danych telemetrycznych monitorowania, uruchamiają przeszukiwanie dzienników w celu analizowania danych i dodać rozwiązanie do zarządzania w celu zapewnienia dodatkowych danych i szczegółowych informacji analitycznych.  

* Aby włączyć zbieranie danych z zasobów platformy Azure za pomocą usługi Diagnostyka Azure lub Azure Storage, zobacz [zbieranie dzienników usług platformy Azure i metryki do użycia w programie log Analytics](../platform/collect-azure-metrics-logs.md).  
* Dodaj [System Center Operations Manager jako źródło danych](../platform/om-agents.md) , aby zebrać dane z agentów zgłaszających Operations Manager grupy zarządzania i zapisać je w obszarze roboczym log Analytics.  
* Połącz [Configuration Manager](../platform/collect-sccm.md) , aby zaimportować komputery należące do kolekcji w hierarchii.  
* Przejrzyj dostępne [rozwiązania do monitorowania](../insights/solutions.md) oraz sposób dodawania lub usuwania rozwiązania z obszaru roboczego.
