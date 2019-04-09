---
title: Wdrażanie szablonów przy użyciu programu PowerShell w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Wdrażanie szablonu do usługi Azure Stack przy użyciu programu PowerShell.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9c1df99557293030dc0b1c0693b0bbc517a3f0ff
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262299"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Wdrażanie szablonu do usługi Azure Stack przy użyciu programu PowerShell

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Program PowerShell służy do wdrażania szablonów usługi Azure Resource Manager do usługi Azure Stack. W tym artykule opisano, jak wdrożyć szablon przy użyciu programu PowerShell.

## <a name="run-azurerm-powershell-cmdlets"></a>Uruchom polecenia cmdlet usługi AzureRM PowerShell

W tym przykładzie użyto **AzureRM** poleceń cmdlet programu PowerShell i szablonu przechowywanego w witrynie GitHub. Ten szablon tworzy maszynę wirtualną systemu Windows Server 2012 R2 Datacenter.

>[!NOTE]
> Przed podjęciem próby w tym przykładzie, upewnij się, że masz [skonfigurowaniu programu PowerShell](azure-stack-powershell-configure-user.md) dla użytkownika usługi Azure Stack.

1. Przeglądaj [repozytorium AzureStackGitHub](https://aka.ms/AzureStackGitHub) i Znajdź **101-simple-windows-vm** szablonu. Zapisz szablon do tej lokalizacji: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Otwórz wiersz polecenia programu PowerShell.
3. Zastąp `username` i `password` w poniższym skrypcie za pomocą nazwy użytkownika i hasło, a następnie uruchom skrypt:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "local"

    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
        -NewStorageAccountName mystorage$myNum `
        -DnsNameForPublicIP mydns$myNum `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
        -VmName myVM$myNum `
        -WindowsOSVersion 2012-R2-Datacenter
    ```

    >[!IMPORTANT]
    > Za każdym razem, gdy ten skrypt można uruchomić, należy zwiększyć wartość `$myNum` parametru, aby zapobiec zastąpieniu wdrożenia.

4. Otwórz portal, wybierz pozycję Azure Stack **Przeglądaj**, a następnie wybierz pozycję **maszyn wirtualnych** można znaleźć swoją nową maszyną wirtualną (**myDeployment001**).

## <a name="next-steps"></a>Kolejne kroki

- [Wdrażanie szablonów za pomocą programu Visual Studio](azure-stack-deploy-template-visual-studio.md)
