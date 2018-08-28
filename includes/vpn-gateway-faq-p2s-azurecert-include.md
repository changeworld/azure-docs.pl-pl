---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 97d33bfcc8251b10ba121b7fb013800904450563
ms.sourcegitcommit: baed5a8884cb998138787a6ecfff46de07b8473d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "30197133"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Czy w przypadku połączenia punkt-lokacja można użyć własnego głównego urzędu certyfikacji PKI?

Tak. Wcześniej można było używać tylko certyfikatów głównych z podpisem własnym. Nadal można przesłać 20 certyfikatów głównych.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Jakie narzędzia umożliwiają tworzenie certyfikatów?

Możesz użyć rozwiązania infrastruktury kluczy publicznych przedsiębiorstwa (wewnętrznej infrastruktury kluczy publicznych), programu Azure PowerShell, narzędzia MakeCert lub protokołu OpenSSL.

### <a name="certsettings"></a>Czy są dostępne instrukcje dotyczące ustawień i parametrów certyfikatów?

* **Wewnętrzne rozwiązanie infrastruktury kluczy publicznych/rozwiązanie infrastruktury kluczy publicznych w przedsiębiorstwie:** zobacz procedurę [generowania certyfikatów](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** procedurę można znaleźć w artykule dotyczącym programu [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md).

* **MakeCert:** procedurę można znaleźć w artykule dotyczącym narzędzia [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md).

* **OpenSSL:** 

    * W przypadku eksportowania certyfikatów należy przekonwertować certyfikat główny na format Base64.

    * Certyfikat klienta:

      * W przypadku tworzenia klucza prywatnego należy określić długość równą 4096.
      * W przypadku tworzenia certyfikatu dla parametru *-extensions* należy określić wartość *usr_cert*.