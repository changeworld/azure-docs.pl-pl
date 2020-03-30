---
title: 'Brama sieci VPN platformy Azure: instalowanie certyfikatu klienta typu punkt-lokacja'
description: Zainstaluj certyfikat klienta do uwierzytelniania certyfikatów P2S - Windows, Mac, Linux.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/10/2020
ms.author: cherylmc
ms.openlocfilehash: 787b8a34ed4b232b9e6cc033e67b1a8162c85f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902847"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalowanie certyfikatów klientów dla połączeń uwierzytelniania certyfikatów P2S

Wszyscy klienci, którzy łączą się z siecią wirtualną przy użyciu uwierzytelniania certyfikatu platformy Azure typu Point-to-Site, wymagają certyfikatu klienta. Ten artykuł ułatwia instalowanie certyfikatu klienta używanego do uwierzytelniania podczas łączenia się z siecią wirtualną przy użyciu usługi P2S.

## <a name="acquire-a-client-certificate"></a><a name="generate"></a>Uzyskiwanie certyfikatu klienta

Bez względu na to, z jakiego systemu operacyjnego klienta chcesz się połączyć, zawsze musisz mieć certyfikat klienta. Można wygenerować certyfikat klienta z certyfikatu głównego wygenerowanego przy użyciu rozwiązania urzędu certyfikacji przedsiębiorstwa lub z certyfikatem głównym z podpisem własnym. Zobacz instrukcje [programu PowerShell](vpn-gateway-certificates-point-to-site.md), [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)lub [Linux,](vpn-gateway-certificates-point-to-site-linux.md) aby uzyskać instrukcje generowania certyfikatu klienta. 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Klienci sieci VPN dla komputerów Mac są obsługiwani tylko dla modelu wdrażania Menedżera zasobów. Nie są one obsługiwane dla klasycznego modelu wdrażania.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Certyfikat klienta systemu Linux jest zainstalowany na kliencie jako część konfiguracji klienta. Zobacz [konfiguracja klienta - Linux,](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) aby uzyskać instrukcje.

## <a name="next-steps"></a>Następne kroki

Przejdź do czynności związanych z instrukcjami konfiguracji Punkt-lokacja, aby [utworzyć i zainstalować pliki konfiguracyjne klienta sieci VPN](point-to-site-vpn-client-configuration-azure-cert.md).
