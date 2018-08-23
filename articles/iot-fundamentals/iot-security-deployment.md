---
title: Zabezpieczanie wdrożenia Internet of Things | Dokumentacja firmy Microsoft
description: W tym artykule szczegółowo przedstawiono sposób Zabezpieczanie wdrożenia IoT
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: 00acb08f567dbd50522d0e8a0b7b9a18a6658000
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42059538"
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-solution-accelerator-cipher-suites"></a>Mechanizmy szyfrowania akcelerator rozwiązań IoT

Akceleratory rozwiązań IoT obsługują następujące mechanizmy szyfrowania w podanej kolejności.

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

Przeczytaj na temat zabezpieczeń usługi IoT Hub w [kontrolować dostęp do usługi IoT Hub](../iot-hub/iot-hub-devguide-security.md) w przewodniku dla deweloperów usługi IoT Hub. 
