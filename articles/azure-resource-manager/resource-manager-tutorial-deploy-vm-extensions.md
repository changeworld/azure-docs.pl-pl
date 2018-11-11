---
title: Wdrażanie rozszerzeń maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager | Microsoft Docs
description: Dowiedz się, jak wdrożyć rozszerzenia maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ca07f044a423a4ea1b5dee484c56f457c7d785de
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239735"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Samouczek: wdrażanie rozszerzeń maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager

Dowiedz się, jak używać [rozszerzeń maszyny wirtualnej platformy Azure](../virtual-machines/extensions/features-windows.md) do wykonywania konfiguracji po wdrożeniu oraz zadań automatyzacji na maszynach wirtualnych platformy Azure. Wielu różnych rozszerzeń maszyny wirtualnej można używać z maszynami wirtualnymi platformy Azure. W tym samouczku wdrożysz rozszerzenie niestandardowego skryptu z szablonu usługi Resource Manager, aby uruchomić skrypt programu PowerShell na maszynie wirtualnej z systemem Windows.  Skrypt instaluje serwer internetowy na maszynie wirtualnej.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowywanie skryptu programu PowerShell
> * Otwieranie szablonu szybkiego startu
> * Edytowanie szablonu
> * Wdrożenie szablonu
> * Weryfikowanie wdrożenia

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem Resource Manager Tools.  Zobacz [Instalowanie rozszerzenia](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Poniżej przedstawiono przykład służący do generowania hasła:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: integracja z usługą Azure Key Vault podczas wdrażania szablonu usługi Resource Manager](./resource-manager-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="prepare-a-powershell-script"></a>Przygotowywanie skryptu programu PowerShell

Skrypt programu PowerShell o następującej zawartości jest udostępniany z [konta usługi Azure Storage z dostępem publicznym](https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

W przypadku wybrania publikowania pliku do własnej lokalizacji musisz zaktualizować element [fileUri] w szablonie w dalszej części tego samouczka.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium na potrzeby szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **File (Plik)**>**Open File (Otwórz plik)**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Istnieje pięć zasobów definiowanych przez szablon:

    * `Microsoft.Storage/storageAccounts`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * `Microsoft.Network/publicIPAddresses`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * `Microsoft.Network/virtualNetworks`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * `Microsoft.Network/networkInterfaces`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * `Microsoft.Compute/virtualMachines`. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Warto uzyskać podstawową wiedzę na temat szablonu przed rozpoczęciem jego dostosowywania.
5. Wybierz pozycję **Plik**>**Zapisz jako**, aby zapisać kopię pliku o nazwie **azuredeploy.json** na komputerze lokalnym.

## <a name="edit-the-template"></a>Edytowanie szablonu

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
                "https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Zobacz [odwołanie do rozszerzenia](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions), jeśli potrzebujesz więcej informacji na temat tej definicji zasobu. Poniżej przedstawiono niektóre ważne elementy:

* **name**: ponieważ zasób rozszerzenia jest zasobem podrzędnym obiektu maszyny wirtualnej, nazwa musi mieć prefiks nazwy maszyny wirtualnej. Zobacz [Zasoby podrzędne](./resource-manager-templates-resources.md#child-resources).
* **dependsOn**: rozszerzenie zasobu musi zostać utworzone po utworzeniu maszyny wirtualnej.
* **fileUris**: lokalizacje, w których są przechowywane pliki skryptów. Jeśli nie chcesz używać dostarczonych, musisz zaktualizować wartości.
* **commandToExecute**: polecenie służące do wywołania skryptu.  

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Zapoznaj się sekcją [Wdrażanie szablonu](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template), gdzie znajduje się procedura wdrażania. Zalecane jest użycie wygenerowanego hasła dla konta administratora maszyny wirtualnej. Zobacz [Wymagania wstępne](#prerequisites).

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

W portalu wybierz maszynę wirtualną, a następnie w przeglądzie maszyny wirtualnej użyj przycisku **Kliknij, aby skopiować** po prawej stronie adresu IP w celu skopiowania go i później wklejenia w karcie przeglądarki. Zostanie otwarta domyślna strona powitalna usług IIS, która powinna wyglądać następująco:

![Usługa Azure Resource Manager wdraża serwer internetowy usług IIS skryptu klienta rozszerzeń maszyny wirtualnej](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono maszynę wirtualną i rozszerzenie maszyny wirtualnej. Rozszerzenie zainstalowało serwer internetowy usług IIS na maszynie wirtualnej. Aby dowiedzieć się, jak zaimportować plik BACPAC za pomocą rozszerzenia usługi SQL Database, zobacz:

> [!div class="nextstepaction"]
> [](./resource-manager-tutorial-deploy-vm-extensions.md)
