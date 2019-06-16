---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: c6666f4417cde9e0f77cc965ded1d6bdb5dced34
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165810"
---
## <a name="start-your-powershell-session"></a>Uruchamianie sesji programu PowerShell
 

Uruchom [ **Connect Az Account** ](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount) polecenia cmdlet zostanie wyświetlony ekran logowania, w którym należy wprowadzić poświadczenia. Użyj tych samych poświadczeń, których używasz do logowania w witrynie Azure Portal.

```powershell
Connect-AzAccount
```

Jeśli masz wiele subskrypcji, użyj [ **AzContext zestaw** ](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) polecenia cmdlet, aby wybrać subskrypcję, która powinny być użyta w sesji programu PowerShell. Aby zobaczyć, jakie subskrypcji bieżącej sesji programu PowerShell jest używany, uruchom [ **Get AzContext**](https://docs.microsoft.com/powershell/module/az.accounts/get-azcontext). Aby wyświetlić wszystkie swoje subskrypcje, uruchom [ **Get AzSubscription**](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```

