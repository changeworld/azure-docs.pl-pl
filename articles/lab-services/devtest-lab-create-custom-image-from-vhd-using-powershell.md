---
title: Tworzenie obrazu niestandardowego w usłudze Azure DevTest Labs z pliku VHD za pomocą programu PowerShell | Dokumentacja firmy Microsoft
description: Automatyczne tworzenie obrazu niestandardowego w usłudze Azure DevTest Labs z pliku VHD za pomocą programu PowerShell
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: c1cdb64e4c8c99eeca4cc66c0d0ad2b755144917
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60201967"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Tworzenie obrazu niestandardowego z pliku VHD za pomocą programu PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

W poniższych krokach objaśniono proces tworzenia obrazu niestandardowego z pliku VHD za pomocą programu PowerShell:

1. W wierszu polecenia programu PowerShell Zaloguj się do konta platformy Azure przy użyciu następujące wywołanie do **Connect AzAccount** polecenia cmdlet.

    ```powershell
    Connect-AzAccount
    ```

1.  Wybierz odpowiednią subskrypcję platformy Azure przez wywołanie metody **AzSubscription wybierz** polecenia cmdlet. Zastąp następujące symbol zastępczy dla **$subscriptionId** zmiennej z identyfikatorem ważnej subskrypcji platformy Azure.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Pobierz obiekt laboratorium, wywołując **Get AzResource** polecenia cmdlet. Zastąp następujące symbole zastępcze dla **$labRg** i **$labName** zmienne odpowiednimi wartościami dla danego środowiska.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Uzyskiwanie laboratorium magazynu konta i laboratorium z magazynu konta wartości klucza obiektu laboratorium.

    ```powershell
    $labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount
    $labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value
    ```

1.  Zastąp następujące symbol zastępczy dla **$vhdUri** zmiennej za pomocą identyfikatora URI do przekazanego pliku wirtualnego dysku twardego. Identyfikator URI pliku wirtualnego dysku twardego może pobrać z bloku obiektu blob konta magazynu w witrynie Azure portal.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Tworzenie przy użyciu niestandardowego obrazu **New AzResourceGroupDeployment** polecenia cmdlet. Zastąp następujące symbole zastępcze dla **$customImageName** i **$customImageDescription** zmienne do nazw opisowych dla środowiska.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Skrypt programu PowerShell, aby utworzyć niestandardowy obraz z pliku wirtualnego dysku twardego

Poniższy skrypt programu PowerShell, można utworzyć niestandardowy obraz z pliku wirtualnego dysku twardego. Zastąp symbole zastępcze (uruchamianie i kończy z nawiasami) odpowiednie wartości dla Twoich potrzeb.

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the lab storage account and lab storage account key values.
$labStorageAccount = Get-AzResource -ResourceId $lab.Properties.defaultStorageAccount
$labStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Wpisy w blogu pokrewne

- [Obrazy niestandardowe lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między usłudze Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Kolejne kroki

- [Dodaj Maszynę wirtualną do środowiska laboratoryjnego](devtest-lab-add-vm.md)
