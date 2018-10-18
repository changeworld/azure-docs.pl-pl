---
title: Używanie warunków w szablonach usługi Azure Resource Manager | Microsoft Docs
description: Dowiedz się, jak wdrażać zasoby platformy Azure na podstawie warunków.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/02/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 216e474f519e57352b017dc3e6bcdd74d48b03de
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238650"
---
# <a name="tutorial-use-condition-in-azure-resource-manager-templates"></a>Samouczek: używanie warunków w szablonach usługi Azure Resource Manager

Dowiedz się, jak wdrażać zasoby platformy Azure na podstawie warunków. 

Scenariusz w tym samouczku jest podobny do użytego w części [Samouczek: tworzenie szablonów usługi Azure Resource Manager z zasobami zależnymi](./resource-manager-tutorial-create-templates-with-dependent-resources.md). W tym samouczku utworzysz konto magazynu, maszynę wirtualną, sieć wirtualną i trochę innych zasobów zależnych. Zamiast tworzyć nowe konto magazynu, zezwalasz użytkownikom na utworzenie nowego konta magazynu lub użycie istniejącego. Aby osiągnąć ten cel, definiujesz dodatkowy parametr. Jeśli wartość parametru to „new”, jest tworzone nowe konto magazynu.

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Otwieranie szablonu szybkiego startu
> * Modyfikowanie szablonu
> * Wdrożenie szablonu
> * Oczyszczanie zasobów

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć pracę z tym artykułem, potrzebne są następujące zasoby:

* Program [Visual Studio Code](https://code.visualstudio.com/) z rozszerzeniem [Resource Manager Tools](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>Otwieranie szablonu szybkiego startu

Szablony szybkiego startu platformy Azure to repozytorium na potrzeby szablonów usługi Resource Manager. Zamiast tworzyć szablon od podstaw, możesz znaleźć szablon przykładowy i zmodyfikować go. Szablon używany w tym samouczku nazywa się [Wdrożenie prostej maszyny wirtualnej z systemem Windows](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. W programie Visual Studio Code wybierz pozycję **File (Plik)**>**Open File (Otwórz plik)**.
2. W polu **File name (Nazwa pliku)** wklej następujący adres URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Wybierz pozycję **Open (Otwórz)**, aby otworzyć plik.
4. Wybierz pozycję **Plik**>**Zapisz jako**, aby zapisać kopię pliku o nazwie **azuredeploy.json** na komputerze lokalnym.

## <a name="modify-the-template"></a>Modyfikowanie szablonu

Wprowadź dwie zmiany do istniejącego szablonu:

* Dodaj parametr służący do podawania nazwy konta magazynu. Ten parametr daje użytkownikowi możliwość podania nazwy istniejącego konta magazynu. Może również służyć jako nazwa nowego konta magazynu.
* Dodaj nowy parametr o nazwie **newOrExisting**. Ten parametr jest używany przy wdrożeniu do określenia, czy utworzyć nowe konto magazynu, czy użyć istniejącego.

1. Otwórz plik **azuredeploy.json** w programie Visual Studio Code.
2. W całym szablonie zastąp fragment **variables('storageAccountName')** fragmentem **parameters('storageAccountName')**.  Fragment **variables('storageAccountName')** pojawia się w trzech miejscach.
3. Usuń następującą definicję zmiennej:

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",
    ```
4. Dodaj następujące dwa parametry do szablonu:

    ```json
    "newOrExisting": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    ```
    Zaktualizowana definicja parametrów wygląda następująco:

    ![Warunek użycia w usłudze Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

5. Dodaj następujący wiersz na początku definicji konta magazynu.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'yes')]",
    ```

    Warunek sprawdza wartość parametru o nazwie **newOrExisting**. Jeśli wartość parametru to **new**, przy wdrażaniu tworzone jest konto magazynu.

    Zaktualizowana definicja konta magazynu wygląda następująco:

    ![Warunek użycia w usłudze Resource Manager](./media/resource-manager-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)

6. Zapisz zmiany.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Postępuj zgodnie z instrukcjami przedstawionymi w sekcji [Wdrożenie szablonu](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template), aby wdrożyć szablon.

Wdrażając szablon przy użyciu programu Azure PowerShell, należy określić jeden dodatkowy parametr:

```powershell
$resourceGroupName = "<Enter the resource group name>"
$storageAccountName = "Enter the storage account name>"
$location = "<Enter the Azure location>"
$vmAdmin = "<Enter the admin username>"
$vmPassword = "<Enter the password>"
$dnsLabelPrefix = "<Enter the prefix>"

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
$vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
    -dnsLabelPrefix $dnsLabelPrefix -storageAccountName $storageAccountName -newOrExisting "new"
```

> [!NOTE]
> Wdrożenie zakończy się niepowodzeniem, jeśli parametr **newOrExisting** będzie mieć wartość **new**, ale konto magazynu o podanej nazwie będzie już istnieć.

Spróbuj przeprowadzić kolejne wdrożenie, tym razem z parametrem **newOrExisting** ustawionym na wartość „existing”, i podaj nazwę istniejącego konta magazynu. Aby wcześniej utworzyć konto magazynu, zobacz [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy zasoby platformy Azure nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów.

1. W witrynie Azure Portal wybierz pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.  W grupie zasobów zostanie wyświetlonych łącznie sześć zasobów.
4. Wybierz pozycję **Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzysz szablon, który pozwoli użytkownikom wybrać między utworzeniem nowego konta magazynu a użyciem istniejącego konta magazynu. Maszyna wirtualna tworzona w tym samouczku wymaga nazwy użytkownika i hasła administratora. Zamiast przekazywać hasło podczas wdrażania, możesz wstępnie zapisać je w usłudze Azure Key Vault i pobrać podczas wdrażania. Aby dowiedzieć się, jak pobierać wpisy tajne z usługi Azure Key Vault i używać ich przy wdrożeniach na podstawie szablonu, zobacz:

> [!div class="nextstepaction"]
> [Integrowanie usługi Key Vault z wdrożeniem z użyciem szablonu](./resource-manager-tutorial-use-key-vault.md)
