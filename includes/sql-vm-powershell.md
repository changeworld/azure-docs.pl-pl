---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183189"
---
## <a name="start-your-powershell-session"></a>Uruchamianie sesji programu PowerShell
 

Uruchom polecenie cmdlet [**konta Connect-Az,**](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) a zostanie wyświetlony ekran logowania, aby wprowadzić poświadczenia. Użyj tych samych poświadczeń, których używasz do logowania w witrynie Azure Portal.

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji, użyj polecenia cmdlet [**Set-AzContext,**](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) aby wybrać subskrypcję, której powinna używać sesja programu PowerShell. Aby zobaczyć, jakiej subskrypcji jest obsługiwana bieżąca sesja programu PowerShell, uruchom [**program Get-AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Aby wyświetlić wszystkie subskrypcje, uruchom [**get-azsubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

