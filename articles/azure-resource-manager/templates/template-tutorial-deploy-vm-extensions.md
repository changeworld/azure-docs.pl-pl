---
title: Wdrażanie rozszerzeń maszyn wirtualnych z szablonem
description: Dowiedz się, jak wdrożyć rozszerzenia maszyny wirtualnej przy użyciu szablonów usługi Azure Resource Manager
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7397e9387fe3354a926ed607a9132ab6ddc7e785
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477592"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Samouczek: Wdrażanie rozszerzeń maszyn wirtualnych za pomocą szablonów ARM

Dowiedz się, jak używać [rozszerzeń maszyny wirtualnej platformy Azure](../../virtual-machines/extensions/features-windows.md) do wykonywania konfiguracji po wdrożeniu oraz zadań automatyzacji na maszynach wirtualnych platformy Azure. Wielu różnych rozszerzeń maszyny wirtualnej można używać z maszynami wirtualnymi platformy Azure. W tym samouczku wdrożyć rozszerzenie skryptu niestandardowego z szablonu usługi Azure Resource Manager (ARM) do uruchomienia skryptu programu PowerShell na maszynie Wirtualnej systemu Windows.  Skrypt instaluje serwer internetowy na maszynie wirtualnej.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Przygotowywanie skryptu programu PowerShell
> * Otwieranie szablonu szybkiego startu
> * Edytowanie szablonu
> * Wdrożenie szablonu
> * Weryfikowanie wdrożenia

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program Visual Studio Code z rozszerzeniem Resource Manager Tools. Zobacz [Tworzenie szablonów ARM za pomocą programu Visual Studio](use-vs-code-to-create-template.md).
* Aby zwiększyć bezpieczeństwo, użyj wygenerowanego hasła dla konta administratora maszyny wirtualnej. Poniżej przedstawiono przykład służący do generowania hasła:

    ```console
    openssl rand -base64 32
    ```

    Usługa Azure Key Vault została zaprojektowana w celu ochrony kluczy kryptograficznych i innych wpisów tajnych. Aby uzyskać więcej informacji, zobacz [Samouczek: Integrowanie usługi Azure Key Vault we wdrażaniu szablonu ARM](./template-tutorial-use-key-vault.md). Zalecamy również aktualizowanie hasła co trzy miesiące.

## <a name="prepare-a-powershell-script"></a>Przygotowywanie skryptu programu PowerShell

Skrypt programu PowerShell z następującą zawartością jest udostępniany z [gitHub:](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1)

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

W przypadku wybrania publikowania pliku do własnej lokalizacji musisz zaktualizować element `fileUri` w szablonie w dalszej części tego samouczka.

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybki start platformy Azure to repozytorium szablonów ARM. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **Plik** > **otwórz plik**.
1. W polu **Nazwa pliku** wklej następujący adres URL: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. Wybierz pozycję **Otwórz**, aby otworzyć plik.
    Szablon definiuje pięć zasobów:

   * **Microsoft.Storage/storageAccounts**. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft.Network/publicIPAddresses**. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft.Network/virtualNetworks**. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft.Network/networkInterfaces**. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft.Compute/virtualMachines**. Zobacz [dokumentację szablonu](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Warto uzyskać podstawową wiedzę na temat szablonu przed rozpoczęciem jego dostosowywania.

1. Aby zapisać kopię pliku o nazwie *azuredeploy.json* na komputerze lokalnym, wybierz pozycję **Plik** > **Zapisz jako**.

## <a name="edit-the-template"></a>Edytowanie szablonu

Dodaj zasób rozszerzenia maszyny wirtualnej do istniejącego szablonu o następującej zawartości:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2018-06-01",
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

Zobacz [informacje szczegółowe o rozszerzeniu](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions), jeśli potrzebujesz więcej informacji na temat tej definicji zasobu. Poniżej przedstawiono niektóre ważne elementy:

* **name**: ponieważ zasób rozszerzenia jest zasobem podrzędnym obiektu maszyny wirtualnej, nazwa musi mieć prefiks nazwy maszyny wirtualnej. Zobacz [Ustawianie nazwy i typu zasobów podrzędnych](child-resource-name-type.md).
* **dependsOn**: Utwórz zasób rozszerzenia po utworzeniu maszyny wirtualnej.
* **fileUris**: Lokalizacje, w których przechowywane są pliki skryptów. Jeśli nie chcesz używać podanej lokalizacji, musisz zaktualizować wartości.
* **commandToExecute**: To polecenie wywołuje skrypt.

Należy również otworzyć port HTTP, aby mieć dostęp do serwera sieci web.

1. Znajdź **securityRules** w szablonie.
1. Dodaj następującą regułę obok **domyślnej dozwolonej-3389**.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Aby zapoznać się z procedurą wdrażania, zobacz sekcję "Wdrażanie szablonu" [w samouczku: Tworzenie szablonów ARM z zasobami zależnymi](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Zalecamy użycie wygenerowanego hasła dla konta administratora maszyny wirtualnej. Zobacz sekcję [Wymagania wstępne](#prerequisites) tego artykułu.

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

1. W witrynie Azure Portal wybierz maszynę wirtualną.
1. W omówieniu maszyny Wirtualnej skopiuj adres IP, wybierając **pozycję Kliknij, aby skopiować**, a następnie wklej go na karcie przeglądarki. Zostanie otwarta domyślna strona powitalna internetowych usług informacyjnych (IIS):

![Strona powitalna usług Internet Information Services](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie potrzebujesz już zasobów platformy Azure wdrożonych przez Ciebie, wyczyść je, usuwając grupę zasobów.

1. W witrynie Azure Portal w okienku po lewej wybierz pozycję **Grupa zasobów**.
2. W polu **Filtruj według nazwy** podaj nazwę grupy zasobów.
3. Wybierz nazwę grupy zasobów.
    W grupie zasobów jest wyświetlanych sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** w górnym menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrożono maszynę wirtualną i rozszerzenie maszyny wirtualnej. Rozszerzenie zainstalowało serwer internetowy usług IIS na maszynie wirtualnej. Aby dowiedzieć się, jak zaimportować plik BACPAC za pomocą rozszerzenia usługi Azure SQL Database, zobacz:

> [!div class="nextstepaction"]
> [Wdrażanie rozszerzeń SQL](./template-tutorial-deploy-sql-extensions-bacpac.md)
