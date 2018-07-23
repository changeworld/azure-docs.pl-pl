---
title: Pobierz narzędzia usługi Azure Stack z repozytorium GitHub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać narzędzia wymagane do pracy z usługą Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: mabrigg
ms.openlocfilehash: 9c1e4abe50b06db58a4ca05a99e1ae4a531b2294
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187455"
---
# <a name="download-azure-stack-tools-from-github"></a>Pobierz narzędzia usługi Azure Stack z usługi GitHub

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Narzędzia AzureStack jest repozytorium GitHub, który jest hostem modułów programu PowerShell, które służą do zarządzania i wdrażania zasobów w usłudze Azure Stack.

## <a name="download-targets"></a>Pobierz elementy docelowe

Możesz pobrać i używać tych modułów programu PowerShell do usługi Azure Stack Development Kit lub aby klienckich zewnętrznych z systemem Windows, który używa połączenia sieci VPN.

## <a name="how-to-get-the-tools"></a>Jak uzyskać narzędzia

Aby uzyskać te narzędzia, sklonuj repozytorium GitHub AzureStack narzędzia lub Pobierz folder AzureStack narzędzia, uruchamiając następujący skrypt:

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>Funkcje udostępniane przez moduły

Repozytorium narzędzia AzureStack zawiera moduły programu PowerShell, które obsługują następujące funkcje dla usługi Azure Stack:

| Funkcjonalność | Opis | Kto może używać tego modułu? |
| --- | --- | --- |
| [Możliwości chmury](https://github.com/Azure/AzureStack-Tools/tree/master/CloudCapabilities) | Ten moduł umożliwia dostęp do możliwości chmury, chmury. Na przykład możesz uzyskać możliwości chmury, takie jak wersja interfejsu API, zasobów usługi Azure Resource Manager, rozszerzenia maszyn wirtualnych itd., dla usługi Azure Stack i chmury platformy Azure za pomocą tego modułu. | Chmura Administratorzy i użytkownicy. |
| [Zasady usługi Resource Manager dla usługi Azure Stack](azure-stack-policy-module.md) | Ten moduł służy do konfigurowania subskrypcji platformy Azure lub grupę zasobów platformy Azure przy użyciu tej samej wersji i usługi dostępności jako usługi Azure Stack. | Użytkowników i administratorów chmury |
| [Nawiązywanie połączenia z usługi Azure Stack](azure-stack-connect-azure-stack.md) | Używaj tego modułu, aby połączyć się z wystąpieniem usługi Azure Stack za pośrednictwem programów PowerShell i do skonfigurowania połączenia sieci VPN w usłudze Azure Stack. | Użytkowników i administratorów chmury |
| [Moduł sprawdzania poprawności szablonu](azure-stack-validate-templates.md) | Ten moduł służy do Sprawdź, jeśli istniejącego lub nowego szablonu można wdrożyć w usłudze Azure Stack. | Użytkowników i administratorów chmury |

## <a name="next-steps"></a>Kolejne kroki

- [Konfigurowanie środowiska PowerShell użytkownika usługi Azure Stack](azure-stack-powershell-configure-user.md)
- [Łączenie usługi Azure Stack Development Kit za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md)
