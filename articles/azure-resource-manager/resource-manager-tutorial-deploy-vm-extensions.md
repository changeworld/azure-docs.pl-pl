---
title: Wdróż rozszerzenia maszyny wirtualnej za pomocą szablonów Azure Resource Manager | Microsoft Docs
description: Dowiedz się, jak wdrażać rozszerzenia maszyn wirtualnych za pomocą szablonów Azure Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 338054aadbf04c6c6e2b496677476c2c5634b6ba
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169297"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Samouczek: Wdrażanie rozszerzeń maszyny wirtualnej za pomocą szablonów Azure Resource Manager

Dowiedz się, jak używać [rozszerzeń maszyny wirtualnej platformy Azure](../virtual-machines/extensions/features-windows.md) do wykonywania zadań konfiguracyjnych po wdrożeniu i automatyzacji na maszynach wirtualnych platformy Azure. Wiele różnych rozszerzeń maszyn wirtualnych jest dostępnych do użycia z maszynami wirtualnymi platformy Azure. W tym samouczku wdrażasz niestandardowe rozszerzenie skryptu z szablonu Azure Resource Manager, aby uruchomić skrypt programu PowerShell na maszynie wirtualnej z systemem Windows.  Skrypt instaluje serwer sieci Web na maszynie wirtualnej.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotuj skrypt programu PowerShell
> * Otwórz szablon szybkiego startu
> * Edytuj szablon
> * Wdróż szablon
> * Weryfikowanie wdrożenia

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania tego artykułu są potrzebne następujące czynności:

* [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem narzędzi Menedżer zasobów Tools. Zobacz [Instalowanie rozszerzenia](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Aby zwiększyć bezpieczeństwo, należy użyć wygenerowanego hasła dla konta administratora maszyny wirtualnej. Oto przykład generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Azure Key Vault jest przeznaczony do ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integrowanie Azure Key Vault w Menedżer zasobów Template Deployment](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="prepare-a-powershell-script"></a>Przygotuj skrypt programu PowerShell

Skrypt programu PowerShell z następującą zawartością jest udostępniany w witrynie [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

W przypadku wybrania opcji opublikowania pliku we własnej lokalizacji należy zaktualizować element `fileUri` w szablonie w dalszej części tego samouczka.

## <a name="open-a-quickstart-template"></a>Otwórz szablon szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium szablonów Menedżer zasobów. Zamiast tworzyć szablon od podstaw, można znaleźć przykładowy szablon i dostosować go. Szablon używany w tym samouczku jest nazywany [wdrożeniem prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W Visual Studio Code wybierz pozycję **plik** > **Otwórz plik**.
1. W polu **Nazwa pliku** wklej następujący adres URL: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. Aby otworzyć plik, wybierz pozycję **Otwórz**.
    Szablon definiuje pięć zasobów:

   * **Microsoft. Storage/storageAccounts**. Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft. Network/adresów publicipaddress**. Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft. Network/virtualNetworks**. Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft. Network/networkInterfaces**. Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft. COMPUTE/virtualMachines**. Zobacz [odwołanie do szablonu](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Warto zapoznać się z podstawową wiedzą na temat szablonu przed jego dostosowaniem.

1. Zapisz kopię pliku na komputerze lokalnym o nazwie *azuredeploy. JSON* , wybierając pozycję **plik** > **Zapisz jako**.

## <a name="edit-the-template"></a>Edytuj szablon

Dodaj zasób rozszerzenia maszyny wirtualnej do istniejącego szablonu o następującej zawartości:

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Aby uzyskać więcej informacji na temat tej definicji zasobu, zobacz [odwołanie do rozszerzenia](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Poniżej przedstawiono kilka ważnych elementów:

* **Nazwa**: ponieważ zasób rozszerzenia jest zasobem podrzędnym obiektu maszyny wirtualnej, nazwa musi mieć prefiks nazwy maszyny wirtualnej. Zobacz [Set Name i Type dla zasobów podrzędnych](child-resource-name-type.md).
* **dependsOn**: Utwórz zasób rozszerzenia po utworzeniu maszyny wirtualnej.
* **fileUris**: lokalizacje, w których są przechowywane pliki skryptów. W przypadku wybrania opcji nie używaj podanej lokalizacji należy zaktualizować wartości.
* **sekcji commandtoexecute**: to polecenie wywołuje skrypt.

## <a name="deploy-the-template"></a>Wdróż szablon

Procedurę wdrażania można znaleźć w sekcji "Wdrażanie szablonu" w [samouczku: Tworzenie szablonów Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Zalecamy używanie wygenerowanego hasła dla konta administratora maszyny wirtualnej. Zapoznaj się z sekcją [wymagania wstępne](#prerequisites) artykułu.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. W Azure Portal wybierz maszynę wirtualną.
1. Na stronie Przegląd maszyny wirtualnej Skopiuj adres IP, wybierając **pozycję kliknij, aby skopiować**, a następnie wkleić ją na karcie przeglądarki. Zostanie otwarta strona powitalna domyślna Internet Information Services (IIS):

![Strona powitalna Internet Information Services](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy wdrożone zasoby platformy Azure nie są już potrzebne, wyczyść je, usuwając grupę zasobów.

1. W Azure Portal w okienku po lewej stronie wybierz pozycję **Grupa zasobów**.
2. W polu **Filtruj według nazwy** wprowadź nazwę grupy zasobów.
3. Wybierz nazwę grupy zasobów.
    W grupie zasobów są wyświetlane sześć zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono maszynę wirtualną i rozszerzenie maszyny wirtualnej. Rozszerzenie zainstalowało serwer sieci Web usług IIS na maszynie wirtualnej. Aby dowiedzieć się, jak zaimportować plik BACPAC przy użyciu rozszerzenia Azure SQL Database, zobacz:

> [!div class="nextstepaction"]
> [Wdróż rozszerzenia SQL](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)
