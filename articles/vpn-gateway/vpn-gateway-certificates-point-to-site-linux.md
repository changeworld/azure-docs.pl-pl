---
title: 'Generowanie i eksportowanie certyfikatów dla punktu na lokację: W systemie Linux: INTERFEJS Azure | Microsoft Docs'
description: Utwórz certyfikat główny z podpisem własnym, wyeksportuj klucz publiczny i Wygeneruj certyfikaty klienta przy użyciu interfejsu wiersza polecenia systemu Linux (klient strongswan).
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/31/2019
ms.author: alzam
ms.openlocfilehash: 79840010561746f2885909123012ffb4efbea5f6
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990310"
---
# <a name="generate-and-export-certificates"></a>Generowanie i eksportowanie certyfikatów

Połączenia punkt-lokacja używają certyfikatów do uwierzytelniania. W tym artykule pokazano, jak utworzyć certyfikat główny z podpisem własnym i wygenerować certyfikaty klienta przy użyciu interfejsu wiersza polecenia systemu Linux i klient strongswan. Jeśli szukasz różnych instrukcji dotyczących certyfikatów, zobacz artykuły dotyczące [programu PowerShell](vpn-gateway-certificates-point-to-site.md) lub [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) . Aby uzyskać informacje na temat sposobu instalowania klient strongswan przy użyciu graficznego interfejsu użytkownika zamiast interfejsu wiersza polecenia, zobacz kroki opisane w artykule dotyczącym [konfiguracji klienta](point-to-site-vpn-client-configuration-azure-cert.md#install) .

## <a name="generate-and-export"></a>Generuj i Eksportuj
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Następne kroki

Kontynuuj konfigurację typu punkt-lokacja, aby [tworzyć i instalować pliki konfiguracji klienta sieci VPN](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
