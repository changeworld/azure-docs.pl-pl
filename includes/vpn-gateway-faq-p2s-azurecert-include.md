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
ms.openlocfilehash: e2e91dc91cf0fbe6827808785a4c3cc25b06542b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66151045"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>Czy w przypadku połączenia punkt-lokacja można użyć własnego głównego urzędu certyfikacji PKI?

Tak. Wcześniej można było używać tylko certyfikatów głównych z podpisem własnym. Nadal można przesłać 20 certyfikatów głównych.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Jakie narzędzia umożliwiają tworzenie certyfikatów?

Możesz użyć rozwiązania infrastruktury kluczy publicznych przedsiębiorstwa (wewnętrznej infrastruktury kluczy publicznych), programu Azure PowerShell, narzędzia MakeCert lub protokołu OpenSSL.

### <a name="certsettings"></a>Czy są dostępne instrukcje dotyczące ustawień i parametrów certyfikatów?

* **Wewnętrznej infrastruktury kluczy publicznych/Enterprise PKI rozwiązania:** Zobacz kroki umożliwiające [generowanie certyfikatów](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** Zobacz [programu Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) artykuł, aby kroki.

* **MakeCert:** Zobacz [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) artykuł, aby kroki.

* **OpenSSL:** 

    * W przypadku eksportowania certyfikatów należy przekonwertować certyfikat główny na format Base64.

    * Certyfikat klienta:

      * W przypadku tworzenia klucza prywatnego należy określić długość równą 4096.
      * W przypadku tworzenia certyfikatu dla parametru *-extensions* należy określić wartość *usr_cert*.