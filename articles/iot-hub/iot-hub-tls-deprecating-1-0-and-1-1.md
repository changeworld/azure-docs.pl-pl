---
title: Wycofanie protokołu TLS 1,0 i 1,1 w IoT Hub i usłudze Device Provisioning Service (DPS) | Microsoft Docs
description: Wytyczne dotyczące wycofania protokołu TLS 1,0 i 1,1 oraz obsługiwane szyfry w IoT Hub i DPS.
author: rezasherafat
ms.author: rezas
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d61ca8fe7c6f5e7cc400714d7c31a0a7e50b8a88
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402792"
---
# <a name="deprecation-of-tls-10-and-11-in-iot-hub-and-device-provisioning-service"></a>Wycofanie protokołu TLS 1,0 i 1,1 w usłudze IoT Hub i usługi Device Provisioning

Aby zapewnić najlepszą w swojej klasie funkcję szyfrowania, IoT Hub i usługi Device Provisioning Service (DPS) są przenoszone do Transport Layer Security (TLS) 1,2 jako mechanizm szyfrowania wybrany dla urządzeń i usług IoT. 

## <a name="supported-ciphers"></a>Obsługiwane szyfry

Oś czasu dostępności różnych szyfrów używanych w uzgadnianiu protokołu TLS jest następująca:

* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 (obecnie obsługiwane)
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 (będzie obsługiwana w drugiej połowie 2020)
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 (będzie obsługiwana w drugiej połowie 2020)
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 (będzie obsługiwana w drugiej połowie 2020)


## <a name="customer-feedback"></a>Opinie klientów

Chociaż Wymuszanie protokołu TLS 1,2 to Najlepsza w swojej klasie funkcja szyfrowania, która zostanie włączona zgodnie z harmonogramem, nadal będziemy chcieli poznać klientów odnośnie do ich określonych wdrożeń i problemów, które przyjmą protokół TLS 1,2. W tym celu można wysłać komentarze do [iot_tls1_deprecation@microsoft.com](mailto:iot_tls1_deprecation@microsoft.com).
