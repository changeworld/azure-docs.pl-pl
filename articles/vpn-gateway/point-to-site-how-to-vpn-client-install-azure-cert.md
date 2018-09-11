---
title: 'Instalowanie certyfikatu klienta punkt-lokacja: Azure | Dokumentacja firmy Microsoft'
description: Zainstaluj certyfikat klienta do uwierzytelniania certyfikatów P2S — Windows, Mac i Linux.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: eec15b84e4bdb8df3fe84a53909d5da4b39545ff
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44294446"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>Instalowanie certyfikatów klienta dla połączeń uwierzytelniania certyfikatu P2S

Wszyscy klienci, łączących się z siecią wirtualną przy użyciu uwierzytelniania certyfikatu platformy Azure Point-to-Site wymagają certyfikatu klienta. Ten artykuł pomoże Ci zainstalować certyfikat klienta, który jest używany do uwierzytelniania podczas nawiązywania połączenia z siecią wirtualną przy użyciu P2S.

## <a name="generate"></a>Uzyskiwanie certyfikatu klienta

Niezależnie od tego, jakiego systemu operacyjnego klienta chcesz się połączyć z musi zawsze mieć certyfikat klienta. Możesz wygenerować certyfikat klienta z certyfikatu głównego, który został wygenerowany przy użyciu rozwiązania urzędu certyfikacji przedsiębiorstwa lub certyfikatu głównego z podpisem własnym. Zobacz [PowerShell](vpn-gateway-certificates-point-to-site.md), [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md), lub [Linux](vpn-gateway-certificates-point-to-site-linux.md) instrukcje dotyczące kroków wygenerować certyfikat klienta. 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>Klienci sieci VPN Mac są obsługiwane tylko modelu wdrażania usługi Resource Manager. Nie są obsługiwane dla klasycznego modelu wdrażania.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

Certyfikat klienta systemu Linux jest zainstalowany na komputerze klienckim w ramach konfiguracji klienta. Zobacz [konfiguracji klienta - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) instrukcje.

## <a name="next-steps"></a>Kolejne kroki

Wykonaj kroki konfiguracji punkt-lokacja, aby [tworzenie i instalowanie plików konfiguracji klienta sieci VPN](point-to-site-vpn-client-configuration-azure-cert.md).