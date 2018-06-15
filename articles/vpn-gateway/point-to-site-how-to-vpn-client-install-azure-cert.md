---
title: Zainstaluj certyfikat klienta P2S | Azure
description: Instalowanie certyfikatu klienta Mac lub Windows P2S uwierzytelniania certyfikatu.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager, azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: cherylmc
ms.openlocfilehash: bf2788fff64ab8b3a5ccf75b8a80f2bd5aba5151
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30317997"
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>Zainstaluj certyfikat klienta dla połączeń uwierzytelniania certyfikatu Azure punkt-lokacja

Wszyscy klienci, którzy połączyć sieć wirtualną przy użyciu uwierzytelniania certyfikatu Azure punkt-lokacja wymagają certyfikatu klienta. Ten artykuł pomaga zainstalować certyfikat klienta, który jest używany do uwierzytelniania podczas nawiązywania połączenia przy użyciu P2S sieci wirtualnej.

## <a name="generate"></a>Generowanie i wyeksportować certyfikat klienta

Możesz wygenerować certyfikat klienta na podstawie certyfikatu głównego, który został wygenerowany za pomocą rozwiązania urzędu certyfikacji przedsiębiorstwa lub certyfikatu głównego z podpisem własnym. Zobacz [PowerShell](vpn-gateway-certificates-point-to-site.md) lub [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) instrukcje dotyczące czynności. Po wygenerowaniu certyfikaty klienta, należy wyeksportować je jako pliki PFX. Należy uwzględnić całego łańcucha certyfikatów podczas eksportowania.

## <a name="installwin"></a>Zainstaluj certyfikat - systemu Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Zainstaluj certyfikat - Mac

Klienci Mac VPN są obsługiwane tylko modelu wdrażania Menedżera zasobów. Nie są obsługiwane dla klasycznym modelu wdrażania.

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>Kolejne kroki

Wykonaj kroki konfiguracji punkt-lokacja.

* [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Program PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Portal Azure (klasyczny)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
