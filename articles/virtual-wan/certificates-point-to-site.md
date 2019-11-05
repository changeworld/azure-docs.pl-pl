---
title: Generowanie i eksportowanie certyfikatów dla połączeń sieci VPN użytkowników wirtualnej sieci WAN platformy Azure | Microsoft Docs
description: Utwórz certyfikat główny z podpisem własnym, wyeksportuj klucz publiczny i Wygeneruj certyfikaty klienta przy użyciu programu PowerShell w systemie Windows 10 lub Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 57d730a92c687a297a35b8cd6cccd955025694af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514916"
---
# <a name="generate-and-export-certificates-for-virtual-wan-user-vpn-connections"></a>Generowanie i eksportowanie certyfikatów dla połączeń sieci VPN użytkownika wirtualnego sieci WAN

Połączenia sieci VPN użytkowników używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i wygenerować certyfikaty klienta przy użyciu programu PowerShell w systemie Windows 10 lub Windows Server 2016.

Kroki opisane w tym artykule należy wykonać na komputerze z systemem Windows 10 lub Windows Server 2016. Polecenia cmdlet programu PowerShell używane do generowania certyfikatów są częścią systemu operacyjnego i nie działają w innych wersjach systemu Windows. Komputer z systemem Windows 10 lub Windows Server 2016 jest wymagany tylko do wygenerowania certyfikatów. Po wygenerowaniu certyfikatów można je przekazać lub zainstalować w dowolnym obsługiwanym systemie operacyjnym klienta.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Następne kroki

Kontynuuj wykonywanie [wirtualnych kroków sieci WAN dla połączenia sieci VPN użytkownika](virtual-wan-about.md)
