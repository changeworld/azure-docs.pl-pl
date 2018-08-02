---
title: Przykładowy skrypt programu Azure PowerShell — wiązanie niestandardowego certyfikatu SSL z aplikacją internetową | Microsoft Docs
description: Przykładowy skrypt programu Azure PowerShell — wiązanie niestandardowego certyfikatu SSL z aplikacją internetową
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 4ab6ad5e8a022c577e9382cde7bdcb3059a18a14
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39323810"
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Tworzenie powiązania niestandardowego certyfikatu SSL z aplikacją internetową

Ten przykładowy skrypt tworzy aplikację internetową w usłudze App Service z powiązanymi zasobami, a następnie tworzy powiązanie certyfikatu SSL nazwy domeny niestandardowej z tą aplikacją. 

W razie potrzeby zainstaluj program Azure PowerShell, korzystając z instrukcji w [przewodniku programu Azure PowerShell](/powershell/azure/overview), a następnie uruchom polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure. Ponadto upewnij się, że:

- Utworzono połączenie z platformą Azure za pomocą polecenia `az login`.
- Masz dostęp do strony konfiguracji serwera DNS rejestratora domen.
- Masz prawidłowy plik PFX i jego hasło na potrzeby certyfikatu SSL, który ma zostać przekazany i powiązany.

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia 

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów, aplikację internetową i wszystkie powiązane zasoby.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Tworzy plan usługi App Service. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Tworzy aplikację internetową. |
| [Set-AzureRmAppServicePlan](/powershell/module/azurerm.websites/set-azurermappserviceplan) | Modyfikuje plan usługi App Service, aby zmienić jej warstwę cenową. |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Modyfikuje konfigurację aplikacji internetowej. |
| [New-AzureRmWebAppSSLBinding](/powershell/module/azurerm.websites/new-azurermwebappsslbinding) | Tworzy powiązanie certyfikatu SSL dla aplikacji internetowej. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat modułu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](/powershell/azure/overview).

Więcej przykładów programu Azure PowerShell dla usługi Azure App Service Web Apps można znaleźć w [przykładach programu Azure PowerShell](../app-service-powershell-samples.md).
