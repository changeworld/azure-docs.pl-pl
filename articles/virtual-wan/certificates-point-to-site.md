---
title: Generowanie i eksportowanie certyfikatów dla połączeń sieci VPN użytkowników | Wirtualna sieć WAN platformy Azure
description: Utwórz certyfikat główny z podpisem własnym, wyeksportuj klucz publiczny i wygeneruj certyfikaty klientów przy użyciu programu PowerShell w systemie Windows 10 lub Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: 0303bac88f34c895a4a680cd5bff0e9d1513d2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059961"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Generowanie i eksportowanie certyfikatów dla połączeń sieci VPN użytkowników

Połączenia sieci VPN użytkowników (point-to-site) używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i wygenerować certyfikaty klientów przy użyciu programu PowerShell w systemie Windows 10 lub Windows Server 2016.

Kroki opisane w tym artykule należy wykonać na komputerze z systemem Windows 10 lub Windows Server 2016. Polecenia cmdlet programu PowerShell używane do generowania certyfikatów są częścią systemu operacyjnego i nie działają w innych wersjach systemu Windows. Komputer z systemem Windows 10 lub Windows Server 2016 jest potrzebny tylko do wygenerowania certyfikatów. Po wygenerowaniu certyfikatów można je przekazać lub zainstalować w dowolnym obsługiwanym systemie operacyjnym klienta.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Następne kroki

Kontynuuj [kroki wirtualnej sieci WAN dla połączenia sieci VPN użytkownika](virtual-wan-about.md)
