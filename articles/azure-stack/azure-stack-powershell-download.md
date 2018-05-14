---
title: Pobieranie narzędzia Azure stosu z serwisu GitHub | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać narzędzia, które są wymagane do pracy z stosu Azure.
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
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: a116b7a048ff95ca601a65633cdc63f98fefee9d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="download-azure-stack-tools-from-github"></a>Pobieranie narzędzia Azure stosu z usługi GitHub

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

**Narzędzia AzureStack** to repozytorium GitHub obsługującego moduły programu PowerShell do zarządzania i wdrażanie zasobów Azure stosu. Jeśli planujesz nawiązać połączenie z siecią VPN, możesz pobrać te moduły programu PowerShell Azure stosu Development Kit lub klient zewnętrznych z systemem Windows. Aby uzyskać te narzędzia, klonowanie repozytorium GitHub lub pobrać **AzureStack narzędzia** folderu za pomocą następującego skryptu:

```PowerShell
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

## <a name="functionality-provided-by-the-modules"></a>Funkcje zapewniane przez moduły

**AzureStack narzędzia** repozytorium zawiera moduły programu PowerShell, które obsługuje następujące funkcje Azure stosu:  

| Funkcjonalność | Opis | kto może używać tego modułu? |
| --- | --- | --- |
| [Funkcje dotyczące chmury](user/azure-stack-validate-templates.md) | Ten moduł służy do skorzystaj z możliwości chmury chmury. Na przykład za pomocą tego modułu, można uzyskać funkcji chmury, takich jak wersja interfejsu API i zasoby usługi Azure Resource Manager. Można także uzyskać rozszerzeń maszyny Wirtualnej Azure stosu i chmury Azure za pomocą tego modułu. | Operatorzy chmur i użytkowników |
| [Zasada Menedżera zasobów Azure stosu](user/azure-stack-policy-module.md) | Ten moduł służy do konfigurowania subskrypcji platformy Azure lub grupy zasobów platformy Azure z tej samej wersji i usługi dostępności jako stosu Azure. | Operatorzy chmur i użytkowników |
| [Zarejestrować w usłudze Azure](azure-stack-register.md) | Ten moduł służy do zarejestrowania wystąpienia development kit z platformy Azure. Po zarejestrowaniu można pobrać elementów marketplace z platformy Azure i używać ich w stosie Azure. | Operatorzy chmury |
| [Azure wdrożenia stosu](azure-stack-run-powershell-script.md) | Ten moduł służy do przygotowania stosu Azure komputer do wdrożenia i wdrożenie za pomocą obrazu wirtualnego dysku twardego (VHD) Azure stosu. | Operatorzy chmury|
| [Łączenie z Azure stosu](azure-stack-connect-powershell.md) | Ten moduł służy do konfigurowania połączeń sieci VPN Azure stosu. | Operatorzy chmur i użytkowników |
| [Moduł sprawdzania poprawności szablonu](user/azure-stack-validate-templates.md) | Używaj tego modułu, aby sprawdzić, czy istniejącego lub nowego szablonu można wdrożyć do stosu Azure. | Operatorzy chmur i użytkowników|


## <a name="next-steps"></a>Kolejne kroki
* [Konfigurowanie środowiska PowerShell użytkownika Azure stosu](user/azure-stack-powershell-configure-user.md)   
* [Nawiązywanie połączeń zestaw deweloperski stosu Azure za pośrednictwem sieci VPN](azure-stack-connect-azure-stack.md)  
