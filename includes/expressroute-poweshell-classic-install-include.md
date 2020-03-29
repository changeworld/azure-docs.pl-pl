---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74926219"
---
Zainstaluj najnowsze wersje modułów programu PowerShell usługi Azure Service Management (SM) i modułu Usługi ExpressRoute. Nie można używać środowiska Usługi Azure CloudShell do uruchamiania modułów SM.

1. Użyj instrukcji w [instalowaniu modułu zarządzania usługami,](/powershell/azure/servicemanagement/install-azure-ps) aby zainstalować moduł zarządzania usługami Azure. Jeśli masz już zainstalowany moduł Az lub RM, należy użyć '-AllowClobber'.
2. Zaimportuj zainstalowane moduły. Korzystając z poniższego przykładu, dostosuj ścieżkę, aby odzwierciedlić lokalizację i wersję zainstalowanych modułów programu PowerShell.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Aby zalogować się do konta platformy Azure, otwórz konsolę programu PowerShell z podwyższonymi prawami i połącz się z kontem. Poniższy przykład ułatwia łączenie się przy użyciu modułu Zarządzanie usługami:

   ```powershell
   Add-AzureAccount
   ```