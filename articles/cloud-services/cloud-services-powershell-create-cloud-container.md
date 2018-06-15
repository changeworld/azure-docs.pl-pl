---
title: Utwórz kontener usługi chmury przy użyciu programu PowerShell | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak utworzyć kontener usługi chmury przy użyciu programu PowerShell. Kontener obsługuje role sieci web i proces roboczy.
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
ms.openlocfilehash: b55a0dd7800448c50897af784092b4a60fa7a25e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29376137"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Użyj polecenia programu Azure PowerShell, aby utworzyć kontener usługi chmury pusty
W tym artykule wyjaśniono, jak szybko utworzyć kontener usługi w chmurze przy użyciu poleceń cmdlet programu Azure PowerShell. Wykonaj poniższe kroki:

1. Zainstaluj polecenia cmdlet programu PowerShell usługi Microsoft Azure z [programu Azure PowerShell pobierze](http://aka.ms/webpi-azps) strony.
2. Otwórz wiersz polecenia programu PowerShell.
3. Użyj [Add-AzureAccount](/powershell/module/Azure/Add-AzureAccount?view=azuresmps-4.0.0) do logowania.

   > [!NOTE]
   > Dodatkowe instrukcje dotyczące instalowania polecenia cmdlet programu Azure PowerShell i nawiązywania połączenia z subskrypcją platformy Azure można znaleźć w temacie [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Użyj **New-AzureService** , aby utworzyć kontener usługi chmury Azure puste.

   ```powershell
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Wykonaj w tym przykładzie do wywołania polecenia cmdlet:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Aby uzyskać więcej informacji na temat tworzenia usługi w chmurze Azure Uruchom polecenie:

```
Get-help New-AzureService
```

### <a name="next-steps"></a>Kolejne kroki
* Aby zarządzać wdrażaniem usługi chmury, zapoznaj się [Get-AzureService](/powershell/module/Azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/Azure/Remove-AzureService?view=azuresmps-4.0.0), i [Set-AzureService](/powershell/module/azure/set-azureservice?view=azuresmps-4.0.0) poleceń. Mogą również dotyczyć [sposób konfigurowania usługi w chmurze](cloud-services-how-to-configure-portal.md) Aby uzyskać więcej informacji.
* Aby opublikować projekt usługi w chmurze na platformie Azure, zobacz **PublishCloudService.ps1** przykładowy kod z [repozytorium usługi w chmurze zarchiwizowane](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).
