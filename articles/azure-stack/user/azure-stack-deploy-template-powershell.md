---
title: Wdrażanie szablonów w stosie Azure przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: Wdrażanie szablonu stos Azure przy użyciu programu PowerShell.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4af82deef029120aa2699e7c69c501ae61a1e8bd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Wdrażanie szablonu stos Azure przy użyciu programu PowerShell

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

PowerShell umożliwia wdrażanie szablonów usługi Azure Resource Manager stos Azure. W tym artykule przedstawiono sposób wdrożyć szablon przy użyciu programu PowerShell.

## <a name="run-azurerm-powershell-cmdlets"></a>Uruchom polecenia cmdlet programu AzureRM PowerShell

W tym przykładzie używane są polecenia cmdlet programu AzureRM PowerShell i szablon przechowywany w serwisie GitHub. Szablon tworzy maszynę wirtualną systemu Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Przed podjęciem próby w tym przykładzie, upewnij się, że znasz [skonfigurowane PowerShell](azure-stack-powershell-configure-user.md) dla użytkownika w usłudze Azure stosu.

1. Przejdź do <http://aka.ms/AzureStackGitHub> i Znajdź **101-prosty windows-vm** szablonu. Zapisz szablon do tej lokalizacji: C:\\szablony\\azuredeploy-101-prosty windows-vm.json.
2. Otwórz wiersz polecenia programu PowerShell z podwyższonym poziomem uprawnień.
3. Zastąp *username* i *hasło* w poniższym skrypcie o nazwę użytkownika i hasło, a następnie uruchom skrypt.

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
   >Zawsze, gdy Uruchom ten skrypt, zwiększ wartość parametru "$myNum", aby zapobiec zastąpieniu wdrożenia.

4. Otwieranie portalu, wybierz pozycję Azure stosu **Przeglądaj**, a następnie wybierz **maszyn wirtualnych** można znaleźć nowej maszyny wirtualnej (*myDeployment001*).

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie szablonów za pomocą programu Visual Studio](azure-stack-deploy-template-visual-studio.md)
