---
title: Pobieranie narzędzia Azure stosu z serwisu GitHub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać narzędzia niezbędne do pracy z stosu Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 28F360AD-789A-488D-965F-FC6E6CCF3329
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: f4c4d907fe76c5e6ee5893b5b4dd1b2d766877a7
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287261"
---
# <a name="download-azure-stack-tools-from-github"></a>Pobieranie narzędzia Azure stosu z usługi GitHub

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Narzędzia AzureStack to repozytorium GitHub obsługującego moduły programu PowerShell, które służy do zarządzania i wdrażania zasobów Azure stosu.

## <a name="download-targets"></a>Pobierz elementy docelowe

Możesz pobrać i użyć tych modułów programu PowerShell Azure stosu Development Kit lub aby klient zewnętrznych z systemem Windows, który używa połączenia sieci VPN.

## <a name="how-to-get-the-tools"></a>Jak uzyskać narzędzi

Aby uzyskać te narzędzia, klonowanie repozytorium GitHub AzureStack narzędzia lub folder AzureStack narzędzia pobierania za pomocą następującego skryptu:

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

Repozytorium narzędzia AzureStack zawiera moduły programu PowerShell, które obsługuje następujące funkcje Azure stosu:

| Funkcjonalność | Opis | kto może używać tego modułu? |
| --- | --- | --- |
| [Funkcje dotyczące chmury](azure-stack-validate-templates.md) | Ten moduł służy do skorzystaj z możliwości chmury chmury. Na przykład możesz też uzyskać funkcji chmury, takich jak wersja interfejsu API, zasobów usługi Azure Resource Manager, rozszerzeń maszyny Wirtualnej itp. stos Azure i chmury Azure za pomocą tego modułu. | Chmura administratorów i użytkowników. |
| [Zasada Menedżera zasobów Azure stosu](azure-stack-policy-module.md) | Ten moduł służy do konfigurowania subskrypcji platformy Azure lub grupy zasobów platformy Azure z tej samej wersji i usługi dostępności jako stosu Azure. | Użytkowników i administratorów chmury |
| [Łączenie z Azure stosu](azure-stack-connect-azure-stack.md) | Używaj tego modułu, aby połączyć się z wystąpieniem stosu Azure za pomocą programu PowerShell i do konfigurowania połączeń sieci VPN Azure stosu. | Użytkowników i administratorów chmury |
| [Moduł sprawdzania poprawności szablonu](azure-stack-validate-templates.md) | Używaj tego modułu, aby sprawdzić, czy istniejącego lub nowego szablonu można wdrożyć do stosu Azure. | Użytkowników i administratorów chmury |

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie środowiska PowerShell użytkownika Azure stosu](azure-stack-powershell-configure-user.md)
* [Nawiązywanie połączeń zestaw deweloperski stosu Azure za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md)
