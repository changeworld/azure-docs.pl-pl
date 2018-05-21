---
title: Zabezpieczanie wdrożenia Internetu rzeczy | Dokumentacja firmy Microsoft
description: W tym artykule szczegółowo sposobu zabezpieczania wdrożenia IoT
services: iot-suite
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 95c23341-16b0-4954-b3f2-d2e82ab7b367
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: 995759cf4831deedc642c0850603947b22ee1222
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-solution-accelerator-cipher-suites"></a>Mechanizmy szyfrowania akceleratora rozwiązania IoT

Akceleratorów rozwiązania IoT obsługuje następujące mechanizmy szyfrowania, w tej kolejności.

| Mechanizmy szyfrowania | Długość |
| --- | --- |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 secp384r1 ECDH (0xc028) (korektora 7680 bits RSA) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 secp256r1 ECDH (0xc027) (korektora 3072 bits RSA) FS |128 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (korektora 7680 bits RSA) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (korektora 3072 bits RSA) FS |128 |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="see-also"></a>Zobacz także
Możesz także wypróbować niektóre inne funkcje i możliwości akceleratorów rozwiązań IoT:

* [Akceleratora rozwiązania predykcyjne konserwacji — omówienie][lnk-predictive-overview]
* [Często zadawane pytania dotyczące akceleratorów rozwiązań IoT][lnk-faq]

Możesz przeczytać o zabezpieczeniach Centrum IoT w [kontrolować dostęp do Centrum IoT] [ lnk-devguide-security] w Podręczniku dewelopera Centrum IoT.

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]:../iot-accelerators/iot-accelerators-faq.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md
