---
title: 'Generowanie i eksportowanie certyfikatów dla P2S: PowerShell'
titleSuffix: Azure VPN Gateway
description: Utwórz certyfikat główny z podpisem własnym, wyeksportuj klucz publiczny i wygeneruj certyfikaty klientów przy użyciu programu PowerShell w systemie Windows 10 lub Windows Server 2016.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: cherylmc
ms.openlocfilehash: f28e76e9dcaf1331fd26a2321cd4deca1027e693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279340"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generowanie i eksportowanie certyfikatów dla point-to-site przy użyciu programu PowerShell

Połączenia typu "punkt-lokacja" używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i wygenerować certyfikaty klientów przy użyciu programu PowerShell w systemie Windows 10 lub Windows Server 2016. Jeśli szukasz różnych instrukcji certyfikatów, zobacz [Certyfikaty - Linux](vpn-gateway-certificates-point-to-site-linux.md) lub [Certyfikaty - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Kroki opisane w tym artykule należy wykonać na komputerze z systemem Windows 10 lub Windows Server 2016. Polecenia cmdlet programu PowerShell używane do generowania certyfikatów są częścią systemu operacyjnego i nie działają w innych wersjach systemu Windows. Komputer z systemem Windows 10 lub Windows Server 2016 jest potrzebny tylko do wygenerowania certyfikatów. Po wygenerowaniu certyfikatów można je przekazać lub zainstalować w dowolnym obsługiwanym systemie operacyjnym klienta.

Jeśli nie masz dostępu do komputera z systemem Windows 10 lub Windows Server 2016, możesz użyć [funkcji MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) do generowania certyfikatów. Certyfikaty generowane przy użyciu dowolnej metody można zainstalować w dowolnym [obsługiwanym](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) systemie operacyjnym klienta.

[!INCLUDE [generate and export certificates](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="install-an-exported-client-certificate"></a><a name="install"></a>Instalowanie wyeksportowanego certyfikatu klienta

Każdy klient, który łączy się z siecią wirtualną za pomocą połączenia P2S wymaga certyfikatu klienta, który ma być zainstalowany lokalnie.

Aby zainstalować certyfikat klienta, zobacz [Instalowanie certyfikatu klienta dla połączeń typu punkt-lokacja](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="next-steps"></a>Następne kroki

Kontynuuj konfigurację punktu do lokacji.

* Aby **zapoznać się z** instrukcjami procesu wdrażania usługi Resource Manager, zobacz [Konfigurowanie systemu P2S przy użyciu natywnego uwierzytelniania certyfikatów platformy Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Aby zapoznać się z **klasycznymi** krokami modelu wdrażania, zobacz [Konfigurowanie połączenia sieci VPN typu punkt-lokacja do sieci wirtualnej (klasycznej).](vpn-gateway-howto-point-to-site-classic-azure-portal.md)