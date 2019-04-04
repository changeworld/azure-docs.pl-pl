---
title: Pobierz narzędzia usługi Azure Stack z repozytorium GitHub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać narzędzia, które są wymagane do pracy z usługą Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 9eb8b3aefedb3e3330715408595a21596f33a413
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480943"
---
# <a name="download-azure-stack-tools-from-github"></a>Pobierz narzędzia usługi Azure Stack z usługi GitHub

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

**Narzędzia AzureStack** jest [repozytorium GitHub](https://github.com/Azure/AzureStack-Tools) obsługujący modułów programu PowerShell do zarządzania i wdrażania zasobów w usłudze Azure Stack. Jeśli zamierzasz nawiązać połączenie z siecią VPN, możesz pobrać te moduły programu PowerShell usługi Azure Stack Development Kit lub klienckich zewnętrznych z systemem Windows. Aby uzyskać tych narzędzi, sklonuj repozytorium GitHub lub pobrać **AzureStack narzędzia** folderu, uruchamiając następujący skrypt:

```powershell
# Change directory to the root directory. 
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>Funkcje udostępniane przez moduły

**AzureStack narzędzia** repozytorium zawiera moduły programu PowerShell, które obsługują następujące funkcje dla usługi Azure Stack:  

| Funkcjonalność | Opis | Kto może używać tego modułu? |
| --- | --- | --- |
| [Możliwości chmury](user/azure-stack-validate-templates.md) | Ten moduł umożliwia dostęp do możliwości chmury, chmury. Na przykład za pomocą tego modułu, można uzyskać możliwości chmury, takich jak wersja interfejsu API i zasoby usługi Azure Resource Manager. Możesz również uzyskać rozszerzeń maszyny Wirtualnej dla usługi Azure Stack i chmury platformy Azure za pomocą tego modułu. | Operatorzy chmur i użytkowników |
| [Zasady usługi Resource Manager dla usługi Azure Stack](user/azure-stack-policy-module.md) | Ten moduł służy do konfigurowania subskrypcji platformy Azure lub grupę zasobów platformy Azure przy użyciu tej samej wersji i usługi dostępności jako usługi Azure Stack. | Operatorzy chmur i użytkowników |
| [Rejestrowania na platformie Azure](azure-stack-register.md) | Ten moduł służy do rejestrowania wystąpienia development kit na platformie Azure. Po zarejestrowaniu możesz pobieranie elementów portalu marketplace z platformy Azure i używać ich w usłudze Azure Stack. | Operatorzy chmury |
| [Wdrożenie usługi Azure Stack](azure-stack-run-powershell-script.md) | Ten moduł służy do przygotowania komputera hosta usługi Azure Stack, aby wdrożyć i ponowne wdrażanie przy użyciu usługi Azure Stack obrazu wirtualnego dysku twardego (VHD). | Operatorzy chmury|
| [Nawiązywanie połączenia z usługi Azure Stack](azure-stack-connect-powershell.md) | Ten moduł służy do konfigurowania połączeń sieci VPN do usługi Azure Stack. | Operatorzy chmur i użytkowników |
| [Moduł sprawdzania poprawności szablonu](user/azure-stack-validate-templates.md) | Ten moduł służy do Sprawdź, jeśli istniejącego lub nowego szablonu można wdrożyć w usłudze Azure Stack. | Operatorzy chmur i użytkowników|


## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie środowiska PowerShell użytkownika usługi Azure Stack](user/azure-stack-powershell-configure-user.md)   
* [Łączenie usługi Azure Stack Development Kit za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md)  
