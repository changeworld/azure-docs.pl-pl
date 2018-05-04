---
title: Tworzenie obrazu niestandardowego Azure DevTest Labs na podstawie pliku VHD za pomocą programu PowerShell | Dokumentacja firmy Microsoft
description: Zautomatyzować tworzenie obraz niestandardowy w usłudze Azure DevTest Labs z pliku VHD za pomocą programu PowerShell
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: v-craic
ms.openlocfilehash: 6bfb34c05a03ed02694210a5901f4f367e8a2fd3
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Tworzenie niestandardowego obrazu z pliku VHD za pomocą programu PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instrukcje krok po kroku

W poniższych krokach objaśniono przez proces tworzenia niestandardowego obrazu z pliku VHD za pomocą programu PowerShell:

1. W wierszu polecenia programu PowerShell Zaloguj się do konta platformy Azure z następujące wywołanie do **Connect-AzureRmAccount** polecenia cmdlet.  
    
    ```PowerShell
    Connect-AzureRmAccount
    ```

1.  Wybierz odpowiednią subskrypcję Azure przez wywołanie metody **Select-AzureRmSubscription** polecenia cmdlet. Zastąp symbol zastępczy następujące dla **$subscriptionId** zmiennej z identyfikatorem ważnej subskrypcji platformy Azure. 

    ```PowerShell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    ```

1.  Pobierz obiekt laboratorium przez wywołanie metody **Get-AzureRmResource** polecenia cmdlet. Zastąp symbole zastępcze następujące dla **$labRg** i **$labName** zmiennych z odpowiednimi wartościami dla danego środowiska. 

    ```PowerShell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzureRmResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```
 
1.  Pobiera laboratorium magazynu konto i laboratorium magazynu konta klucza wartości z obiektu laboratorium. 

    ```PowerShell
    $labStorageAccount = Get-AzureRmResource -ResourceId $lab.Properties.defaultStorageAccount 
    $labStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value
    ```

1.  Zastąp symbol zastępczy następujące dla **$vhdUri** zmiennej o identyfikatorze URI do przekazany plik wirtualnego dysku twardego. Identyfikator URI pliku wirtualnego dysku twardego można uzyskać z konta magazynu obiektów blob bloku w portalu Azure.

    ```PowerShell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Tworzenie przy użyciu niestandardowego obrazu **AzureRmResourceGroupDeployment nowy** polecenia cmdlet. Zastąp symbole zastępcze następujące dla **$customImageName** i **$customImageDescription** zmienne łatwy do rozpoznania nazwy dla danego środowiska.

    ```PowerShell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzureRmResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Skrypt programu PowerShell, aby utworzyć niestandardowy obraz z pliku VHD

Poniższy skrypt programu PowerShell, można utworzyć niestandardowy obraz z pliku VHD. Zastąp symbole zastępcze (początkową i końcową z nawiasy) z odpowiednimi wartościami dla potrzeb. 

```PowerShell
# Log in to your Azure account.  
Connect-AzureRmAccount

# Select the desired Azure subscription. 
$subscriptionId = '<Specify your subscription ID here>'
Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzureRmResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the lab storage account and lab storage account key values.
$labStorageAccount = Get-AzureRmResource -ResourceId $lab.Properties.defaultStorageAccount 
$labStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value

# Set the URI of the VHD file.  
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image. 
New-AzureRmResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Wpisy na blogu pokrewne

- [Niestandardowe obrazy lub formuł?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Kopiowanie obrazów niestandardowych między Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Kolejne kroki

- [Dodaj Maszynę wirtualną do laboratorium](devtest-lab-add-vm.md)
