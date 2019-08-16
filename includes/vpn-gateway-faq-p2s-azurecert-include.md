---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 93f6bc8533218af7f0e6dcd1c5f7be6fe8c00e29
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520865"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Czy można użyć własnego głównego urzędu certyfikacji PKI do generowania certyfikatów dla połączeń punkt-lokacja?

Tak. Wcześniej można było używać tylko certyfikatów głównych z podpisem własnym. Nadal można przesłać 20 certyfikatów głównych.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Czy mogę używać certyfikatów z Azure Key Vault?

Nie.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Jakie narzędzia umożliwiają tworzenie certyfikatów?

Możesz użyć rozwiązania infrastruktury kluczy publicznych przedsiębiorstwa (wewnętrznej infrastruktury kluczy publicznych), programu Azure PowerShell, narzędzia MakeCert lub protokołu OpenSSL.

### <a name="certsettings"></a>Czy są dostępne instrukcje dotyczące ustawień i parametrów certyfikatów?

* **Wewnętrzna infrastruktura PKI/korporacyjne rozwiązanie PKI:** Zapoznaj się z instrukcjami [generowania certyfikatów](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Zapoznaj się z artykułem [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) .

* **MakeCert:** Zapoznaj się z artykułem [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) .

* **OpenSSL:** 

    * W przypadku eksportowania certyfikatów należy przekonwertować certyfikat główny na format Base64.

    * Certyfikat klienta:

      * W przypadku tworzenia klucza prywatnego należy określić długość równą 4096.
      * W przypadku tworzenia certyfikatu dla parametru *-extensions* należy określić wartość *usr_cert*.
