---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926219"
---
Zainstaluj najnowsze wersje modułu usługi ExpressRoute i modułów programu PowerShell usługi Azure Service Management (SM). Do uruchamiania modułów SM nie można używać środowiska CloudShell platformy Azure.

1. Aby zainstalować moduł zarządzania usługami platformy Azure, wykonaj instrukcje opisane w artykule [Instalowanie modułu zarządzania usługami](/powershell/azure/servicemanagement/install-azure-ps) . Jeśli masz już zainstalowany moduł AZ lub RM, upewnij się, że używasz elementu "-AllowClobber".
2. Zaimportuj zainstalowane moduły. W poniższym przykładzie Dostosuj ścieżkę, aby odzwierciedlić lokalizację i wersję zainstalowanych modułów programu PowerShell.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Aby zalogować się do konta platformy Azure, Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i Połącz się ze swoim kontem. Skorzystaj z następującego przykładu, aby nawiązać połączenie przy użyciu modułu zarządzania usługami:

   ```powershell
   Add-AzureAccount
   ```