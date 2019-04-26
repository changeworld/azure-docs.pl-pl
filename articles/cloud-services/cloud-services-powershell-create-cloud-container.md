---
title: Tworzenie kontenera usługi w chmurze przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia kontenera usługi w chmurze przy użyciu programu PowerShell. Kontener hostuje role sieć web i proces roboczy.
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: ''
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: 771f93edfee8f7b48fb7d0d2c98419f9427f6338
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60527327"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Polecenie programu Azure PowerShell umożliwia tworzenie pustego usługi kontenera w chmurze

W tym artykule wyjaśniono, jak szybko utworzyć kontener usług w chmurze przy użyciu poleceń cmdlet programu Azure PowerShell. Wykonaj poniższe kroki:

1. Zainstaluj polecenia cmdlet programu Microsoft Azure PowerShell z [programu Azure PowerShell pobierze](https://aka.ms/webpi-azps) strony.
2. Otwórz wiersz polecenia programu PowerShell.
3. Użyj [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) do logowania.

   > [!NOTE]
   > Aby uzyskać dalsze instrukcje dotyczące instalowania programu Azure PowerShell polecenia cmdlet i nawiązywania połączenia z subskrypcją platformy Azure, zapoznaj się [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Użyj **New-AzureService** polecenia cmdlet, aby utworzyć kontener usługi pusty chmury platformy Azure.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Postępuj zgodnie z tym przykładzie do wywołania polecenia cmdlet:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Aby uzyskać więcej informacji na temat tworzenia usługi w chmurze platformy Azure Uruchom polecenie:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Kolejne kroki

* Aby zarządzać wdrażania usługi w chmurze, zobacz [Get-AzureService](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0), i [Set-AzureService](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) poleceń. Może również dotyczyć [sposób konfigurowania usługi w chmurze](cloud-services-how-to-configure-portal.md) Aby uzyskać więcej informacji.
* Aby opublikować projekt usługi w chmurze na platformie Azure, zapoznaj się **PublishCloudService.ps1** przykładowy kod z [repozytorium services chmurze zarchiwizowane](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).