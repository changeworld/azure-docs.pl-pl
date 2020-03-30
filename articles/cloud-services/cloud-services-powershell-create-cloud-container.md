---
title: Tworzenie kontenera usługi w chmurze za pomocą programu PowerShell | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak utworzyć kontener usługi w chmurze za pomocą programu PowerShell. Kontener obsługuje role sieci Web i procesu roboczego.
services: cloud-services
documentationcenter: .net
author: cawaMS
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: ef9d3c7c479df9c71a855f0a243b5b9d0da947d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359505"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Tworzenie pustego kontenera usługi w chmurze za pomocą polecenia programu Azure PowerShell

W tym artykule wyjaśniono, jak szybko utworzyć kontener usług w chmurze przy użyciu poleceń cmdlet programu Azure PowerShell. Wykonaj poniższe kroki:

1. Zainstaluj polecenie cmdlet programu Microsoft Azure PowerShell ze strony [pobierania programu Azure PowerShell.](https://aka.ms/webpi-azps)
2. Otwórz wiersz polecenia programu PowerShell.
3. Użyj [add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) do logowania.

   > [!NOTE]
   > Aby uzyskać dalsze instrukcje dotyczące instalowania polecenia cmdlet programu Azure PowerShell i łączenia się z subskrypcją platformy Azure, zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Użyj polecenia cmdlet **New-AzureService,** aby utworzyć pusty kontener usługi w chmurze platformy Azure.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. W tym przykładzie należy wywołać polecenie cmdlet:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Aby uzyskać więcej informacji na temat tworzenia usługi w chmurze platformy Azure, uruchom:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Następne kroki

* Aby zarządzać wdrożeniem usługi w chmurze, zapoznaj się z poleceniami [Get-AzureService](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0)i [Set-AzureService.](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) Więcej informacji można również znaleźć w temacie [Jak skonfigurować usługi w chmurze.](cloud-services-how-to-configure-portal.md)
* Aby opublikować projekt usługi w chmurze na platformie Azure, zapoznaj się z przykładem kodu **PublishCloudService.ps1** z [zarchiwizowanego repozytorium usług w chmurze](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).