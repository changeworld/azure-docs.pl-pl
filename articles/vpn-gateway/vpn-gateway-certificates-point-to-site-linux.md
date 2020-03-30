---
title: 'Generowanie i eksportowanie certyfikatów dla point-to-site: Linux: CLI'
description: Utwórz certyfikat główny z podpisem własnym, wyeksportuj klucz publiczny i wygeneruj certyfikaty klientów przy użyciu interfejsu wiersza polecenia systemu Linux (strongSwan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: a0f996ff2805da4dd5af400642eef2506c228d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779760"
---
# <a name="generate-and-export-certificates"></a>Generowanie i eksportowanie certyfikatów

Połączenia typu "punkt-lokacja" używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i wygenerować certyfikaty klienta przy użyciu interfejsu wiersza polecenia systemu Linux i strongSwan. Jeśli szukasz różnych instrukcji certyfikatów, zobacz artykuły [programu Powershell](vpn-gateway-certificates-point-to-site.md) lub [MakeCert.](vpn-gateway-certificates-point-to-site-makecert.md) Aby uzyskać informacje dotyczące sposobu instalowania strongSwan przy użyciu interfejsu użytkownika zamiast interfejsu wiersza polecenia, zobacz kroki w artykule [konfiguracji klienta.](point-to-site-vpn-client-configuration-azure-cert.md#install)

## <a name="install-strongswan"></a>Zainstaluj strongSwan

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Generowanie i eksportowanie certyfikatów

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Następne kroki

Kontynuuj konfigurowanie punktu do lokacji, aby [utworzyć i zainstalować pliki konfiguracyjne klienta sieci VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
