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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 445628679a09a1884f63cdce446adec476af39af
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42057683"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Wdrażanie szablonu do usługi Azure Stack przy użyciu programu PowerShell

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Program PowerShell służy do wdrażania szablonów usługi Azure Resource Manager do usługi Azure Stack. Ten artykuł pokazuje, jak wdrożyć szablon przy użyciu programu PowerShell.

## <a name="run-azurerm-powershell-cmdlets"></a>Uruchom polecenia cmdlet usługi AzureRM PowerShell

W tym przykładzie użyto polecenia cmdlet usługi AzureRM PowerShell i szablonu przechowywanego w witrynie GitHub. Ten szablon tworzy maszynę wirtualną systemu Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Przed podjęciem próby w tym przykładzie, upewnij się, że masz [skonfigurowaniu programu PowerShell](azure-stack-powershell-configure-user.md) dla użytkownika usługi Azure Stack.

1. Przejdź do <http://aka.ms/AzureStackGitHub> i Znajdź **101-simple-windows-vm** szablonu. Zapisz szablon do tej lokalizacji: C:\\szablony\\azuredeploy-101-simple-windows-vm.json.
2. Otwórz wiersz polecenia programu PowerShell.
3. Zastąp *username* i *hasło* w poniższym skrypcie za pomocą nazwy użytkownika i hasło, a następnie uruchom skrypt.

   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
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
   >Za każdym, gdy ten skrypt można uruchomić zwiększyć wartość parametru "$myNum", aby zapobiec zastąpieniu wdrożenia.

4. Otwórz portal, wybierz pozycję Azure Stack **Przeglądaj**, a następnie wybierz pozycję **maszyn wirtualnych** można znaleźć swoją nową maszyną wirtualną (*myDeployment001*).

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie szablonów za pomocą programu Visual Studio](azure-stack-deploy-template-visual-studio.md)
